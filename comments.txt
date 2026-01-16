import pandas as pd
import matplotlib.pyplot as plt
import numpy as np

# --- 1. SET FONT AND COLORS ---
# Set Matplotlib font to Arial
try:
    plt.rcParams['font.sans-serif'] = ['Arial', 'Helvetica', 'sans-serif']
    plt.rcParams['font.family'] = 'sans-serif'
except Exception:
    # Fallback if Arial is not installed
    pass

# Define the user's requested colors
DARK_BLUE = '#012855' # Dark Blue (for all text and the TAM segment)
LIGHT_BLUE = '#6495ED' # Lighter Blue (for the Penetration Gap segment)

# --- 2. DATA PREPARATION ---
data = {
    'Year': [2025, 2026, 2027, 2028, 2029, 2030, 2031, 2032, 2033, 2034, 2035],
    'PAM': [27480900, 27527936, 27575054, 27622252, 27669531, 27716890, 27702481, 27688080, 27673686, 27659300, 27644921],
    'TAM': [2119949, 2321745, 2528592, 2740511, 2957529, 3179668, 3399365, 3623215, 3851212, 4083349, 4319620]
}
df = pd.DataFrame(data)

# Calculations
df['PAM_Millions'] = df['PAM'] / 1000000
df['TAM_Millions'] = df['TAM'] / 1000000
df['Gap_Millions'] = df['PAM_Millions'] - df['TAM_Millions']
df['Penetration_Percent'] = (df['TAM'] / df['PAM']) * 100

# --- 3. VISUALIZATION: STACKED BAR CHART ---
# Increased figure size for proper spacing (15x8)
plt.figure(figsize=(15, 8))

# Plot the bottom segment (TAM)
plt.bar(
    df['Year'],
    df['TAM_Millions'],
    color=DARK_BLUE,
    label='TAM (Target Addressable Market)'
)

# Plot the top segment (Penetration Gap), stacked on TAM
plt.bar(
    df['Year'],
    df['Gap_Millions'],
    bottom=df['TAM_Millions'],
    color=LIGHT_BLUE,
    label='Market Penetration Gap (Non-Targeted PAM)'
)

# Add Labels (data points and penetration percentages)
for i in range(len(df)):
    year = df['Year'].iloc[i]
    tam_val = df['TAM_Millions'].iloc[i]
    gap_val = df['Gap_Millions'].iloc[i]
    penetration = df['Penetration_Percent'].iloc[i]
    total_val = tam_val + gap_val

    # Label for TAM value (White text on dark bar)
    plt.text(year, tam_val / 2, f'{tam_val:.2f}M', ha='center', va='center', fontsize=9, color='white', fontweight='bold')

    # Label for Gap value (Black text on light bar)
    plt.text(year, tam_val + gap_val / 2, f'{gap_val:.2f}M', ha='center', va='center', fontsize=9, color='black', fontweight='bold')

    # Label for Penetration Percentage (Dark Blue on top)
    plt.text(year, total_val + 0.5, f'{penetration:.1f}%', ha='center', va='bottom', fontsize=10, color=DARK_BLUE, fontweight='bold')


# --- 4. APPLY FORMATTING AND COLOR TO TEXT ---
# Title
plt.title('TAM Penetration within PAM (2025-2035)', fontsize=16, color=DARK_BLUE)

# X-Label and Ticks
plt.xlabel('Year', fontsize=12, color=DARK_BLUE)
plt.xticks(df['Year'], color=DARK_BLUE)

# Y-Label and Ticks
plt.ylabel('Units Sold (Millions of Cars)', fontsize=12, color=DARK_BLUE)
plt.yticks(np.arange(0, df['PAM_Millions'].max() + 5, 5), color=DARK_BLUE)

# Legend (positioned outside to the right for correct spacing)
plt.legend(loc='upper left', bbox_to_anchor=(1.02, 1), fontsize=10, title="Market Segmentation", frameon=True)

# Use tight_layout for optimal spacing
plt.tight_layout()

# Save the final image file
plt.savefig('final_tam_penetration_chart.png')
plt.close()