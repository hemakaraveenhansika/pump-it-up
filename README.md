# pump-it-up

## 01. Git hub link
https://github.com/hemakaraveenhansika/pump-it-up

## 02. Explotary data analysis (EDA)

    1.	Most of the dry quantity pumps are non-functional. Functional and functional need repair are more similarly distributed across the other quantity values.
    2.	Most of the pumps with an enough quality_group are functional.  Most of the pumps with an unknow quality_group are non-functional.
    3.	Most of the old pumps are non-functional.  Most of the new pumps are functional.  
    4.	Most of the pumps with central government and district council installed are non-functional. Most of the pumps with DWE  installed are functional. 
    5.	communal standpipe waterpoint type has higher possibility to have functional. Other waterpoint_type has higher possibility for non-functionality.
    6.	A lots of non-functional water points have soft, salty and unknown water quality.
    7.	There are lots of non-functional water points as never paid.
    8.	Most of other and mono extraction types are non-functional than functional pumps.
    9.	Most of the government  funded water points are non-functional.
    10.	Iringa ,Klimanjaro, Arusha have higher portions for functional pumps.

    * numeric_columns = ['amount_tsh', 'gps_height', 'longitude', 'latitude', 'district_code', 'population', 'construction_year', 'public_meeting', 'permit']
    * category_columns = ['installer','quantity', 'payment', 'funder', 'basin','region',  'extraction_type_group', 'management', 'water_quality', 'source', 'waterpoint_type']

## 03 . Pre-processing approaches

    1. One-hot encoding for categorical columns
    2. Label encoding for 'status_group' columns
    3. Handling missing values
        construction_year -> apply non zero median : 2000 for zero values
        funder -> replace 'other' for NaN values
        installer -> replace 'other' for NaN & zero values, correct some spelling mistakes and syntax in some category
        longitude -> replace mean of region's longitude for 0.0000 values
        public_meeting -> replace true Nan values
        permit -> replace true Nan values

## 04. Feature engineering approaches

    1. Target Encoding for categorical columns
    2. Other - Used RobustScaler to scale all the numerical columns


## 05. Feature selection
    Check feature ranking for both Random Forest Classifier and xgboost Classifier
    According to the xgboost Classifier feature ranking,
        Highest rank - [no 122 feature] quantity_dry feature (0.397269)
        Lowest rank -  [no 0 feature] id feature (0.001335)
                       [no 119 feature] water_quality_fluoride abandoned feature (0.001158)  
                       [no 141 feature] waterpoint_type_dam feature (0.000000)
    Remove lowest 3 features(id, water_quality_fluoride abandoned, waterpoint_type_dam) and model again.

## 06. Other feature pre-processing/ feature engineering approaches 

    drop unwanted columns
        1.  management_group : similar to 'management', 'management' has more detailed values
        2.  scheme_management : similar to 'management', 'scheme_management' has 3877 null values
        3.  quantity_group : similar to 'quantity'
        4.  source_class : similar to 'source'
        5.  source_type : similar to 'source'
        6.  quality_group : similar to 'water_quality'
        7.  payment_type : similar to 'payment'
        8.  extraction_type : similar to 'extraction_type_group', 'extraction_type_group' has more compact values
        9.  extraction_type_class : similar to 'extraction_type_group', 'extraction_type_group' has more detailed values
        10. waterpoint_type_group : similar to 'waterpoint_type', 'waterpoint_type' has more detailed values
        11. recorded_by : has only one value, doesn't have any information
        12. wpt_name : does not have any information about functionality
        13. scheme_name : does not have any information about functionality
        14. num_private : most of the values are zero
        15. date_recorded : does not have necessary information about functionality
        16. subvillage : region and region_code are representing the same thing

    
    'installer' and 'funder' have a lot of categories. Select most 20 frequent categories from each records and set ‘other’ for rest of categories.

    According to scores values for both One-hot encoder and Target encoder, One-hot encoder gave better results. I removed Target encoder and used One-hot encoder for categorical records.

            Model	        Encoder	        score
        0	Random Forest	Target Encoder	0.807239
        1	Random Forest	Onehot Encoder	0.807323
        2	XGBClassifier	Target Encoder	0.807912
        3	XGBClassifier	Onehot Encoder	0.809428 -> best result

## 07. Modelling approaches
    1. Random Forest
    2. XGB Classifier

    According to scores values for Random Forest Classifier and XGB Classifier, XGB Classifier gave better results. I used XGB Classifier to predict outputs.

## 08. Post-processing (insight extraction) approaches

    1. Feature Importance
        Random Forest, highest ranks - longitude, latitude
        Random Forest, lowest ranks - water_quality_fluoride abandoned, waterpoint_type_dam

        XGBClassifier, highest ranks - quantity_dry, waterpoint_type_other
        XGBClassifier, lowest ranks - id, water_quality_fluoride abandoned, waterpoint_type_dam

        
    2. Partial dependence plots
        Most of the new pumps are functional pumps. Most of the old pumps are non-functional
        For pumps with highest amount_tsh and gps_height are functional. Lowest amount_tsh and gps_height are non-functional