import numpy as np
import matplotlib.pyplot as plt

# Constants
R = 8.314  # Gas constant in J/(mol·K)

# Antoine coefficients for water and alcohols (example values, replace with accurate data from the paper)
antoine_coefficients = {
    "water": {"A": 8.07131, "B": 1730.63, "C": 233.426},
    "methanol": {"A": 8.08097, "B": 1582.27, "C": 239.726},
    "ethanol": {"A": 8.20417, "B": 1642.89, "C": 230.300},
    "n_propanol": {"A": 8.24307, "B": 1598.67, "C": 226.43},
    "iso_propanol": {"A": 8.29206, "B": 1659.06, "C": 227.44},
}

# Function to calculate saturation pressure using Antoine equation
def saturation_pressure(antoine_params, T):
    """
    Calculate the saturation pressure (in mmHg) of a component at temperature T (in °C).
    :param antoine_params: Dictionary with Antoine coefficients (A, B, C).
    :param T: Temperature in Celsius.
    :return: Saturation pressure in mmHg.
    """
    A, B, C = antoine_params["A"], antoine_params["B"], antoine_params["C"]
    return 10 ** (A - (B / (T + C)))

# Function to calculate liquid phase composition using Raoult's law
def calculate_liquid_composition(temp_C, mole_fraction_alcohol, alcohol_type="methanol"):
    """
    Calculate the liquid phase composition of an alcohol-water mixture using Raoult's law.
    :param temp_C: Temperature in Celsius.
    :param mole_fraction_alcohol: Mole fraction of alcohol in the mixture.
    :param alcohol_type: Type of alcohol (e.g., "methanol", "ethanol").
    :return: Liquid phase composition (mole fractions of alcohol and water).
    """
    # Saturation pressures for water and the selected alcohol
    P_sat_water = saturation_pressure(antoine_coefficients["water"], temp_C)
    P_sat_alcohol = saturation_pressure(antoine_coefficients[alcohol_type], temp_C)

    # Partial pressures
    P_water = (1 - mole_fraction_alcohol) * P_sat_water
    P_alcohol = mole_fraction_alcohol * P_sat_alcohol

    # Total pressure
    P_total = P_water + P_alcohol

    # Liquid phase compositions
    x_water = P_water / P_total
    x_alcohol = P_alcohol / P_total

    return x_alcohol, x_water

# Example: Calculate liquid phase composition for methanol-water mixture
temp_C = 60  # Temperature in Celsius
mole_fraction_alcohol = 0.3  # Mole fraction of methanol

x_alcohol, x_water = calculate_liquid_composition(temp_C, mole_fraction_alcohol, alcohol_type="methanol")

print(f"At {temp_C}°C, mole fractions in the liquid phase:")
print(f"Methanol: {x_alcohol:.4f}")
print(f"Water: {x_water:.4f}")

# Plotting example compositions at various mole fractions
temperatures = np.linspace(40, 100, 50)
mole_fractions_alcohol = np.linspace(0, 1, 50)
alcohol_type = "ethanol"

composition_alcohol = []
composition_water = []

for mf_alcohol in mole_fractions_alcohol:
    x_alcohol, x_water = calculate_liquid_composition(60, mf_alcohol, alcohol_type)
    composition_alcohol.append(x_alcohol)
    composition_water.append(x_water)

plt.plot(mole_fractions_alcohol, composition_alcohol, label=f"{alcohol_type} in liquid phase")
plt.plot(mole_fractions_alcohol, composition_water, label="Water in liquid phase")
plt.xlabel("Mole Fraction of Alcohol")
plt.ylabel("Mole Fraction in Liquid Phase")
plt.title("Liquid Phase Composition at 60°C")
plt.legend()
plt.grid()
plt.show()
