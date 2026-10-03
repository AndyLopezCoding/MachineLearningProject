# Household Appliance Energy Prediction

CSCI 3052U: Machine Learning I

Andy Lopez, Hasan Nawaz, Eliseo Marinez

This project compares machine-learning models for estimating household appliance energy use in watt-hours (Wh). The Milestone 2 notebook covers the dataset, initial exploratory analysis, and modelling plan.

[GitHub repository](https://github.com/AndyLopezCoding/MachineLearningProject)

## Files

- `MachineLearningProject.ipynb`: project notebook.
- `energydata_complete.csv`: dataset used by the notebook.
- `Milestone-2.pdf`: updated project proposal.

## How to run

1. Install Python and the required packages:

   ```sh
   python -m pip install pandas numpy matplotlib jupyter
   ```

2. Open `MachineLearningProject.ipynb` in VS Code or Jupyter.
3. Keep the CSV in the same folder as the notebook, select a Python kernel, and run all cells in order.

## Data source

[UCI Appliances Energy Prediction](https://archive.ics.uci.edu/dataset/374/appliances+energy+prediction), contributed by Luis Candanedo (2017), is licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).

The notebook renames columns and creates hour and day-of-week features.
