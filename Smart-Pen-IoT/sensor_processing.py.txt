import numpy as np

def analyze_data(ax, ay, az, pressure):
    movement_magnitude = np.sqrt(ax**2 + ay**2 + az**2)
    
    if movement_magnitude < 5000 and pressure < 300:
        return "⚠️ Concentration lapse detected!"
    else:
        return "✅ Normal writing"

# Example Data
sensor_data = {"ax": 2000, "ay": 1500, "az": 1800, "pressure": 250}
print(analyze_data(**sensor_data))
