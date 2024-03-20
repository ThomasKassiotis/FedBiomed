# Preprocessing from Researcher Node
The extension created for FedBiomed in Node enables uploading multiple CSV files of varying row counts and features. It consolidates these files into a single Excel document with multiple sheets, each sheet representing a different CSV file. Researchers can access information about this comprehensive Excel file from their node.

## Node Side
### Upload multiple csv files from a given directory (Option 7 - Safepolymed)

![alt text](/images/image-2.png)

### In a given directory we have uploaded 3 different csv files
- organizations-100.csv
- people-100.csv
- customers-100.csv

### Final excel file in Node

![alt text](/images/image.png)

## Researcher Side
### Preprocessing from Research Node

Preprocessing occurs within our custom training plan, specifically in the **training_data** function. Researchers can access and modify sheet names, column details, and values. They also have the capability to create a new CSV file with selected features and save it directly on the node side. All the preprocessing can be done with the Pandas library.

```python
    def training_data(self):
        
        ################# Start of Preprocessing ######################################
        
        # Get a list of sheet names
        data_frame = pd.ExcelFile(self.dataset_path, engine='openpyxl')
        print(data_frame.sheet_names)
        # Get content from a specific sheet as dataframe 
        sheet_name ='organizations-100'
        df = pd.read_excel(self.dataset_path, sheet_name=sheet_name, engine='openpyxl')
        # Print first five rows
        print(df.head())
        # Get headers/ column names
        print(df.keys())
        # Get content from other sheet
        sheet_name_two = 'people-100'
        df1 = pd.read_excel(self.dataset_path, sheet_name=sheet_name_two, engine='openpyxl')
        # Create column with name Gender and replace these values Male-0 - Female-1
        df1['Gender'] = df1['Sex'].map({'Male': 0, 'Female': 1})
        # Drop a specific column
        df1.drop(columns=['Sex'], inplace=True)
        print(df1.head())
        # Declaire a path to save the new csv file
        output_csv_path = 'data/HAR/harmonized.csv'
        # Save the csv file
        df1.to_csv(output_csv_path, index=False)
        print(f"CSV saved to: {output_csv_path}")
        
        ################# End of Preprocessing ######################################
        
        x_dim = self.model_args()['in_features']
        x_train = df['Index']
        y_train = df['Index']
        train_kwargs = {'shuffle': True}
        data_manager = DataManager(dataset=x_train , target=y_train, **train_kwargs)
        return data_manager
```

### Data Manipulation 
- We can retrieve all the different sheet names (Researcher Side)
```python
data_frame = pd.ExcelFile(self.dataset_path, engine='openpyxl')
print(data_frame.sheet_names)
```
The print occurs in Node side
```cmd
['organizations-100', 'people-100', 'customers-100']
```
- We can print the first five rows from a specific sheet (Researcher Side)
```python
sheet_name ='organizations-100'
df = pd.read_excel(self.dataset_path, sheet_name=sheet_name, engine='openpyxl')
print(df.head())
```
The print occurs in Node side
```cmd
   Index  Organization Id                     Name                         Website  ...                                     Description Founded                     Industry Number of employees
0      1  FAB0d41d5b5d22c              Ferrell LLC              https://price.net/  ...             Horizontal empowering knowledgebase    1990                     Plastics                3498
1      2  6A7EdDEA9FaDC52  Mckinney, Riley and Day  http://www.hall-buchanan.info/  ...             User-centric system-worthy leverage    2015  Glass / Ceramics / Concrete                4952
2      3  0bFED1ADAE4bcC1               Hester Ltd       http://sullivan-reed.com/  ...                  Switchable scalable moratorium    1971                Public Safety                5287
3      4  2bFC1Be8a4ce42f           Holder-Sellers             https://becker.com/  ...  De-engineered systemic artificial intelligence    2004                   Automotive                 921
4      5  9eE8A6a4Eb96C24              Mayer Group          http://www.brewer.com/  ...              Synchronized needs-based challenge    1991               Transportation                7870

[5 rows x 9 columns]
```
- We can get only the column names (Researcher Side)
```python
sheet_name ='organizations-100'
df = pd.read_excel(self.dataset_path, sheet_name=sheet_name, engine='openpyxl')
print(df.keys())
```
The print occurs in Node side
```cmd
Index(['Index', 'Organization Id', 'Name', 'Website', 'Country', 'Description',
       'Founded', 'Industry', 'Number of employees'],
      dtype='object')
```
- We have the ability to either generate a new dataframe or augment an existing one with additional columns and their corresponding values. For instance, we could introduce a new column titled Gender and assign new values by mapping from an existing column named Sex, where "Male" is represented by 0 and "Female" by 1.
```python
sheet_name_two = 'people-100'
df1 = pd.read_excel(self.dataset_path, sheet_name=sheet_name_two, engine='openpyxl')
df1['Gender'] = df1['Sex'].map({'Male': 0, 'Female': 1})
```
- We can drop specific columns
```python
df1.drop(columns=['Sex'], inplace=True)
```
- We can save the csv file into a given directory
```python
output_csv_path = 'data/harmonized_files/harmonized.csv'
df1.to_csv(output_csv_path, index=False)
```
We created a new dataframe awith a new column titled Gender and assign new values by mapping from an existing column named Sex, where "Male" is represented by 0 and "Female" by 1.
![alt text](/images/image-3.png)
