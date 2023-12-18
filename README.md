# Medical Data Visualizer

This is the boilerplate for the Medical Data Visualizer project. Instructions for building your project can be found at https://www.freecodecamp.org/learn/data-analysis-with-python/data-analysis-with-python-projects/medical-data-visualizer

# My collaboration in the project:

This Python script visualizes and analyzes medical examination data using matplotlib, seaborn, and pandas. The dataset contains information about patients, including body measurements, blood test results, and lifestyle choices. The script performs various tasks to gain insights into the relationships between cardiac disease, body measurements, blood markers, and lifestyle choices.

## Tasks and Code Explanation

### 1 - Add 'overweight' column:

Calculates the Body Mass Index (BMI) by dividing weight in kilograms by the square of height in meters.
Adds an 'overweight' column with values 0 for NOT overweight and 1 for overweight.
```python
df['overweight'] = (df['weight'] / (df['height'] / 100) ** 2).apply(lambda x: 1 if x > 25 else 0)
```

### 2 - Normalize data:

Sets values of 'cholesterol' and 'gluc' to 0 if the value is 1; otherwise, sets it to 1.
```python
df['cholesterol'] = df['cholesterol'].apply(lambda x: 0 if x == 1 else 1)
df['gluc'] = df['gluc'].apply(lambda x: 0 if x == 1 else 1)
```

### 3 - Draw Categorical Plot:

 - Creates a DataFrame for the cat plot using pd.melt with values from 'cholesterol', 'gluc', 'smoke', 'alco', 'active', and 'overweight'.
 - Groups and reformats the data to split it by 'cardio' and shows the counts of each feature.
 - Draws a catplot with Seaborn's sns.catplot().
```python
df_cat = pd.melt(df, id_vars='cardio', value_vars=['cholesterol', 'gluc', 'smoke', 'alco', 'active', 'overweight'])
df_cat = df_cat.groupby(['cardio', 'variable', 'value']).size().reset_index(name='total')
fig = sns.catplot(x='variable', y='total', hue='value', col='cardio', data=df_cat, kind='bar')
```

### 4 - Draw Heat Map:

 - Cleans the data by filtering out incorrect data segments.
 - Calculates the correlation matrix and generates a mask for the upper triangle.
 - Draws a heatmap with Seaborn's sns.heatmap().
```python
df_heat = df[(df['ap_lo'] <= df['ap_hi']) & 
             (df['height'] >= df['height'].quantile(0.025)) & 
             (df['height'] <= df['height'].quantile(0.975)) & 
             (df['weight'] >= df['weight'].quantile(0.025)) & 
             (df['weight'] <= df['weight'].quantile(0.975))]
corr = df_heat.corr()
mask = np.triu(np.ones_like(corr, dtype=bool))
fig, ax = plt.subplots(figsize=(12, 9))
sns.heatmap(corr, mask=mask, annot=True, fmt=".1f", linewidths=.5, square=True, cmap='coolwarm', center=0, cbar_kws={"shrink": 0.8})
```
