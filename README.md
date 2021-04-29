# house_price_python
A sample data science project that uses a Lasso Linear Regression Python model to predict house price from the Ames Housing Data dataset. Specifically, this example is used to demonstrate the creating of ModelOp Center-compliant code.

## Assets:
- `lasso.pickle` is the trained model artifact.
- `train_encoded_columns.pickle` is a binarized list of final column names that the model will accept.
- `standard_scaler.pickle` is a `sklearn.preprocessing.StandardScaler` transformer object that is fit on the training data.
- The datasets used for **scoring** are `df_baseline.json` and `df_sample.json`. These datasets represent raw data that would first be run into a batch scoring job.
- The datasets used for **metrics** are `df_baseline_scored.json` and `df_sample_scored.json`. These datasets represent data that has gone through the scoring process, meaning that the data is already transformed into model-ready input and that the predictions for each row are stored in the `prediction` column. Furthermore, the `SalePrice` column is renamed to `ground_truth`, as the metrics job requires those columns be named specifically.
- The `df_baseline_scored_input_schema.avsc` is an AVRO-compliant json file that details the input schema, as needed for ModelOp Center functionality.
- The `df_sample_scored_input_schema.avsc` is the schema file for the sample dataset.

## Directions:
1. For a **scoring** job, please use the `df_baseline.json` and/or the `df_sample.json` files. The output is a dictionary of records that has the input data transformed, as well as the prediction for each row appended.
2. For a **metrics** job, please use the `df_baseline_scored.json` and/or the `df_sample_scored.json` files. The output is a dictionary of the `R2`, `RMSE`, and `MAE` metrics for dataset entered.

The input data to the **scoring** job is `df_sample.json`, which is a JSONS file (one-line JSON records). Here are the first three records:
```json
{"Id":564,"MSSubClass":50,"MSZoning":"RL","LotFrontage":66.0,"LotArea":21780,"Street":"Pave","Alley":null,"LotShape":"Reg","LandContour":"Lvl","Utilities":"AllPub","LotConfig":"Inside","LandSlope":"Gtl","Neighborhood":"Edwards","Condition1":"Norm","Condition2":"Norm","BldgType":"1Fam","HouseStyle":"1.5Fin","OverallQual":6,"OverallCond":7,"YearBuilt":1918,"YearRemodAdd":1950,"RoofStyle":"Gable","RoofMatl":"CompShg","Exterior1st":"Wd Sdng","Exterior2nd":"Wd Sdng","MasVnrType":"None","MasVnrArea":0.0,"ExterQual":"TA","ExterCond":"TA","Foundation":"BrkTil","BsmtQual":"Gd","BsmtCond":"TA","BsmtExposure":"Mn","BsmtFinType1":"Unf","BsmtFinSF1":0,"BsmtFinType2":"Unf","BsmtFinSF2":0,"BsmtUnfSF":1163,"TotalBsmtSF":1163,"Heating":"GasA","HeatingQC":"Ex","CentralAir":"Y","Electrical":"SBrkr","1stFlrSF":1163,"2ndFlrSF":511,"LowQualFinSF":0,"GrLivArea":1674,"BsmtFullBath":0,"BsmtHalfBath":0,"FullBath":2,"HalfBath":0,"BedroomAbvGr":4,"KitchenAbvGr":1,"KitchenQual":"TA","TotRmsAbvGrd":8,"Functional":"Typ","Fireplaces":1,"FireplaceQu":"Gd","GarageType":"Detchd","GarageYrBlt":1955.0,"GarageFinish":"Fin","GarageCars":2,"GarageArea":396,"GarageQual":"TA","GarageCond":"TA","PavedDrive":"N","WoodDeckSF":72,"OpenPorchSF":36,"EnclosedPorch":0,"3SsnPorch":0,"ScreenPorch":144,"PoolArea":0,"PoolQC":null,"Fence":null,"MiscFeature":null,"MiscVal":0,"MoSold":7,"YrSold":2008,"SaleType":"WD","SaleCondition":"Normal","SalePrice":185000}
{"Id":507,"MSSubClass":60,"MSZoning":"RL","LotFrontage":80.0,"LotArea":9554,"Street":"Pave","Alley":null,"LotShape":"IR1","LandContour":"Lvl","Utilities":"AllPub","LotConfig":"Inside","LandSlope":"Gtl","Neighborhood":"SawyerW","Condition1":"Norm","Condition2":"Norm","BldgType":"1Fam","HouseStyle":"2Story","OverallQual":8,"OverallCond":5,"YearBuilt":1993,"YearRemodAdd":1994,"RoofStyle":"Gable","RoofMatl":"CompShg","Exterior1st":"VinylSd","Exterior2nd":"VinylSd","MasVnrType":"BrkFace","MasVnrArea":125.0,"ExterQual":"Gd","ExterCond":"TA","Foundation":"PConc","BsmtQual":"Gd","BsmtCond":"TA","BsmtExposure":"No","BsmtFinType1":"GLQ","BsmtFinSF1":380,"BsmtFinType2":"Unf","BsmtFinSF2":0,"BsmtUnfSF":397,"TotalBsmtSF":777,"Heating":"GasA","HeatingQC":"Ex","CentralAir":"Y","Electrical":"SBrkr","1stFlrSF":1065,"2ndFlrSF":846,"LowQualFinSF":0,"GrLivArea":1911,"BsmtFullBath":0,"BsmtHalfBath":0,"FullBath":2,"HalfBath":1,"BedroomAbvGr":3,"KitchenAbvGr":1,"KitchenQual":"Gd","TotRmsAbvGrd":8,"Functional":"Typ","Fireplaces":1,"FireplaceQu":"TA","GarageType":"Attchd","GarageYrBlt":1993.0,"GarageFinish":"RFn","GarageCars":2,"GarageArea":471,"GarageQual":"TA","GarageCond":"TA","PavedDrive":"Y","WoodDeckSF":182,"OpenPorchSF":81,"EnclosedPorch":0,"3SsnPorch":0,"ScreenPorch":0,"PoolArea":0,"PoolQC":null,"Fence":null,"MiscFeature":null,"MiscVal":0,"MoSold":9,"YrSold":2006,"SaleType":"WD","SaleCondition":"Normal","SalePrice":215000}
{"Id":656,"MSSubClass":160,"MSZoning":"RM","LotFrontage":21.0,"LotArea":1680,"Street":"Pave","Alley":null,"LotShape":"Reg","LandContour":"Lvl","Utilities":"AllPub","LotConfig":"Inside","LandSlope":"Gtl","Neighborhood":"BrDale","Condition1":"Norm","Condition2":"Norm","BldgType":"Twnhs","HouseStyle":"2Story","OverallQual":6,"OverallCond":5,"YearBuilt":1971,"YearRemodAdd":1971,"RoofStyle":"Gable","RoofMatl":"CompShg","Exterior1st":"HdBoard","Exterior2nd":"ImStucc","MasVnrType":"BrkFace","MasVnrArea":381.0,"ExterQual":"TA","ExterCond":"TA","Foundation":"CBlock","BsmtQual":"TA","BsmtCond":"TA","BsmtExposure":"No","BsmtFinType1":"Unf","BsmtFinSF1":0,"BsmtFinType2":"Unf","BsmtFinSF2":0,"BsmtUnfSF":525,"TotalBsmtSF":525,"Heating":"GasA","HeatingQC":"TA","CentralAir":"Y","Electrical":"SBrkr","1stFlrSF":525,"2ndFlrSF":567,"LowQualFinSF":0,"GrLivArea":1092,"BsmtFullBath":0,"BsmtHalfBath":0,"FullBath":1,"HalfBath":1,"BedroomAbvGr":3,"KitchenAbvGr":1,"KitchenQual":"TA","TotRmsAbvGrd":6,"Functional":"Typ","Fireplaces":0,"FireplaceQu":null,"GarageType":"Detchd","GarageYrBlt":1971.0,"GarageFinish":"Unf","GarageCars":1,"GarageArea":264,"GarageQual":"TA","GarageCond":"TA","PavedDrive":"Y","WoodDeckSF":0,"OpenPorchSF":0,"EnclosedPorch":0,"3SsnPorch":0,"ScreenPorch":0,"PoolArea":0,"PoolQC":null,"Fence":null,"MiscFeature":null,"MiscVal":0,"MoSold":3,"YrSold":2010,"SaleType":"WD","SaleCondition":"Family","SalePrice":88000}
```

The input data to the **metrics** job is `df_sample_scored.json`, which is a JSONS file (one-line JSON records). Here are the first three records:
```json
{"Id":564,"prediction":168353.66,"ground_truth":185000,"eOverallQual_TotalSF":17022,"OverallQual":6,"eTotalSF":2837,"GrLivArea":1674,"ExterQual":2,"KitchenQual":2,"GarageCars":2,"eTotalBathrooms":2.0,"BsmtQual":3,"GarageArea":396,"TotalBsmtSF":1163,"GarageFinish":3,"YearBuilt":1918,"eHasGarage":true,"TotRmsAbvGrd":8,"eHasRemodeling":true,"FireplaceQu":4,"MasVnrArea":0.0,"eHasFireplace":true,"eHasBsmt":true}
{"Id":507,"prediction":234576.12,"ground_truth":215000,"eOverallQual_TotalSF":21504,"OverallQual":8,"eTotalSF":2688,"GrLivArea":1911,"ExterQual":3,"KitchenQual":3,"GarageCars":2,"eTotalBathrooms":2.5,"BsmtQual":3,"GarageArea":471,"TotalBsmtSF":777,"GarageFinish":2,"YearBuilt":1993,"eHasGarage":true,"TotRmsAbvGrd":8,"eHasRemodeling":true,"FireplaceQu":3,"MasVnrArea":125.0,"eHasFireplace":true,"eHasBsmt":true}
{"Id":656,"prediction":121785.16,"ground_truth":88000,"eOverallQual_TotalSF":9702,"OverallQual":6,"eTotalSF":1617,"GrLivArea":1092,"ExterQual":2,"KitchenQual":2,"GarageCars":1,"eTotalBathrooms":1.5,"BsmtQual":2,"GarageArea":264,"TotalBsmtSF":525,"GarageFinish":1,"YearBuilt":1971,"eHasGarage":true,"TotRmsAbvGrd":6,"eHasRemodeling":false,"FireplaceQu":0,"MasVnrArea":381.0,"eHasFireplace":false,"eHasBsmt":true}
```
