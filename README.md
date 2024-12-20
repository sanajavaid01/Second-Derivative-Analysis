# README

## Overview

This script processes biomedical signal data from a CSV file to calculate the second derivative of a signal, identifies key points (local maxima and minima), and computes ratios between these points for further analysis.

## Requirements

The script requires the following Python libraries:

- `pandas`
- `numpy`
- `scipy`

Install these dependencies using pip if they are not already installed:

```bash
pip install pandas numpy scipy
```

## Input

- A CSV file named `Healthy_Normal.csv` is expected in the working directory.
- The CSV file should contain a column named `AC_RED`, which represents the signal to be analyzed.

## Output

The script calculates:

1. The second derivative of the `AC_RED` signal.
2. Local maxima and minima of the second derivative.
3. Five key points (A, B, C, D, E) derived from the local maxima and minima.
4. Ratios of the values at these key points to point A.

## Usage

1. Place the CSV file in the working directory.
2. Run the script.

```python
python script_name.py
```

## Code Details

### 1. Load Data

The script begins by loading the dataset into a Pandas DataFrame:

```python
file_path = 'Healthy_Normal.csv'
data = pd.read_csv(file_path)
data.head(), data.info()
```

### 2. Calculate the Second Derivative

The second derivative of the `AC_RED` signal is computed using NumPy:

```python
signal = data['AC_RED']
sec_derivative = np.gradient(np.gradient(signal, edge_order=2), edge_order=2)
data['Second_Derivative'] = sec_derivative
```

### 3. Identify Key Points

Local maxima (peaks) and minima (valleys) are identified using SciPy:

```python
from scipy.signal import find_peaks
peaks, _ = find_peaks(sec_derivative, distance=4)
valleys, _ = find_peaks(-sec_derivative, distance=4)
abcde_indices = sorted(list(peaks) + list(valleys))
abcde_values = sec_derivative[abcde_indices]
```

### 4. Calculate Ratios

The first five key points (A, B, C, D, E) are used to compute the following ratios:

- B/A
- C/A
- D/A
- E/A

```python
if len(abcde_values) >= 5:
    A, B, C, D, E = abcde_values[:5]
    b_a_ratio = B / A
    c_a_ratio = C / A
    d_a_ratio = D / A
    e_a_ratio = E / A
else:
    b_a_ratio = c_a_ratio = d_a_ratio = e_a_ratio = None
```

### 5. Display Results

The results, including the key points and their ratios, are stored in a dictionary:

```python
result = {
    "A": A if len(abcde_values) >= 1 else None,
    "B": B if len(abcde_values) >= 2 else None,
    "C": C if len(abcde_values) >= 3 else None,
    "D": D if len(abcde_values) >= 4 else None,
    "E": E if len(abcde_values) >= 5 else None,
    "b/a": b_a_ratio,
    "c/a": c_a_ratio,
    "d/a": d_a_ratio,
    "e/a": e_a_ratio
}
print(result)
```

## Example Output

If the script runs successfully, the output will look like this:

```python
{
    "A": 0.123,
    "B": 0.456,
    "C": 0.789,
    "D": 0.234,
    "E": 0.567,
    "b/a": 3.707,
    "c/a": 6.415,
    "d/a": 1.902,
    "e/a": 4.610
}
```

## Notes

- Ensure that the `AC_RED` column exists in the input file.
- Adjust the `distance` parameter in `find_peaks` based on the signal characteristics.
- If fewer than five key points are found, the script will set their values and ratios to `None`.
