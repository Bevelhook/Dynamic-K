# Dynamic-K
 Dark matter and gravitational lensing simplified using a Dynamic K
import numpy as np
import matplotlib.pyplot as plt
from IPython.display import display, HTML

# Function to calculate gravitational lensing deflection
def gravitational_lensing(x, y, mass, impact_param):
    """
    Calculate the deflection of light due to gravitational lensing by a Singular Isothermal Sphere (SIS).
    
    Parameters:
        x, y (float): Coordinates of the light ray.
        mass (float): Mass of the lensing object.
        impact_param (float): Impact parameter of the light ray.
    
    Returns:
        dx, dy (float): Deflection in the x and y directions.
    """
    # Einstein radius for SIS model
    theta_E = 4 * np.pi * (mass**2) / impact_param
    # Distance from the center of the mass
    r = np.sqrt(x**2 + y**2)
    # Avoid division by zero
    r = np.maximum(r, 1e-10)
    # Deflection angle for SIS model
    alpha = theta_E / r
    # Calculate the deflection in x and y directions
    dx = -y * alpha / r
    dy = x * alpha / r
    return dx, dy

# Function to simulate multiple images
def simulate_multiple_images(mass, source_x, source_y, observer_x, observer_y, num_points, impact_param, num_images):
    """
    Simulate multiple images due to gravitational lensing.
    
    Parameters:
        mass (float): Mass of the lensing object.
        source_x, source_y (float): Position of the light source.
        observer_x, observer_y (float): Position of the observer.
        num_points (int): Number of points to discretize the light path.
        impact_param (float): Base impact parameter.
        num_images (int): Number of images to simulate.
    
    Returns:
        images (list): List of (x, y) coordinates for each image.
    """
    images = []
    for i in range(num_images):
        # Increase variation in impact parameter for more distinct paths
        current_impact_param = impact_param * (1 + 0.5 * i)  # Increased variation
        x = np.linspace(source_x, observer_x, num_points)
        y = np.linspace(source_y, observer_y, num_points)
        dx, dy = np.zeros(num_points), np.zeros(num_points)
        
        for j in range(num_points - 1):
            dx[j], dy[j] = gravitational_lensing(x[j], y[j], mass, current_impact_param)
            x[j + 1] = x[j] + dx[j]
            y[j + 1] = y[j] + dy[j]
        
        images.append((x, y))
    return images

# Function to run the simulation and plot results
def run_simulation():
    # Simulation parameters
    mass = 1  # Mass of the lensing object (in arbitrary units)
    source_x, source_y = -10, 0  # Position of the light source
    observer_x, observer_y = 10, 0  # Position of the observer
    num_points = 500  # Number of points to discretize the light path
    impact_param = 1  # Base impact parameter
    num_images = 3  # Number of images to simulate

    # Simulate multiple images
    images = simulate_multiple_images(mass, source_x, source_y, observer_x, observer_y, num_points, impact_param, num_images)

    # Plot the results in 4K resolution
    plt.figure(figsize=(16, 9), dpi=300)  # 4K resolution (3840x2160 pixels)
    for i, (x, y) in enumerate(images):
        plt.plot(x, y, label=f'Image {i+1}', linewidth=2)
    plt.scatter([source_x, observer_x], [source_y, observer_y], color='red', s=100, label='Source/Observer')
    plt.scatter([0], [0], color='black', s=200, label='Lens')
    plt.xlabel('x', fontsize=14)
    plt.ylabel('y', fontsize=14)
    plt.title('Gravitational Lensing Simulation (SIS Model)', fontsize=16)
    plt.legend(fontsize=12)
    plt.grid(True, linestyle='--', alpha=0.7)
    plt.show()

# Create a "Run" button (for Jupyter Notebook or Google Colab)
display(HTML('<button onclick="run_simulation()">Run Simulation</button>'))

# Run the simulation when the button is clicked
run_simulation()
