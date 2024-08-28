# Project_Code
!pip install tpot pymatgen
from google.colab import drive
drive.mount('/content/drive')
#importing all necessary libraries
import os
import pandas as pd
from tpot import TPOTRegressor
from pymatgen.ext.matproj import MPRester
from pymatgen.io.cif import CifParser
from sklearn.model_selection import train_test_split
# Function to extract features from CIF files
def extract_features_from_cif(cif_file):
    try:
        with open(cif_file, 'r') as file:
            lines = file.readlines()
            features = {}
            for line in lines:
                line = line.strip()
                if line.startswith('#'):
                    # Extract features from lines starting with '#'
                    parts = line.split(':', 1)
                    if len(parts) == 2:
                        key = parts[0].strip()[1:]
                        value = parts[1].strip()
                        features[key] = value
            return features
    except Exception as e:
        print(f"Error extracting features from {cif_file}: {e}")
        return None

# We specifically check if each line starts with '#' to identify lines containing features.
# We split each line by ':' to separate the key and value parts.
# We strip any leading or trailing whitespace characters from both the key and value.
# We then store the key-value pairs in the features dictionary.
# Only lines that contain both a key and a value (separated by ':') are considered as features, and other lines are ignored.

# Function to load CIF files from the cif_merge folder
def load_cif_files(data_path):
    cif_files = []
    for file_name in os.listdir(data_path):
        if file_name.endswith('.cif'):
            cif_files.append(os.path.join(data_path, file_name))
    return cif_files
    #load cif files from the folder
data_path = '/content/drive/MyDrive/Dataset from dryad/HOIP_cif/cif_merge'
cif_files = load_cif_files(data_path)
# Extract features from CIF files
features_list = []
for cif_file in cif_files:
    features = extract_features_from_cif(cif_file)
    if features:
        features_list.append(features)

print(len(features_list))
# Convert features_list to DataFrame
df = pd.DataFrame(features_list)
df = df.dropna(subset = [' Bandgap, HSE06 (eV)']) # Drop rows with bandgap values NaN
features = df.drop(' Bandgap, HSE06 (eV)', axis=1)

target = df[' Bandgap, HSE06 (eV)']

# Verify the DataFrame columns
print("Columns in DataFrame:\n", df.columns)
