# Exporting Readings
The goal is to export readings from mongodb on RaspPi into a zipped pickle file.
# Mongo_to_Pandas
The [mongo_to_pandas script](https://github.com/BitKnitting/FitHome_mongodb/blob/master/data_results/mongo_to_pandas) takes in the database, collection names of the data in the mongo db as well as an output name for the file and:
- Exports readings in the collection into a .json file.
- Takes the .json file and pickles it using the [json_to_pickle.py](https://github.com/BitKnitting/FitHome_mongodb/blob/master/data_results/json_to_pickle.py) Python script.
- Zips the pickled file into the <filename>.pkl.zip filename.

# Additional Details
## Export Readings From MongoDb
I used `$ mongoexport --collection=aggregated --db=FitHome --out=aggregated.json`  to export the aggregated data from the mongodb into a json file. 
```
-rw-r--r-- 1 pi pi 2446337 Nov 27 12:31 aggregated.json
```
## Use Pandas to Convert to Pickled file

Ultimately, we'll be using colab to look at the data.  The pickle format is the easiest way to read in data.
Pandas makes it easy to create the pickle file.  To get Pandas to work on the Rasp Pi:
From within the venv:
```
pip install pandas
```
Then I go the error: 
```
libf77blas.so.3: cannot open shared object file: No such file or directory
```
When I tried a python script with `import pandas as pd`.
This was fixed by:
```
$ sudo apt-get install libatlas-base-dev
```
```
## Zip
I installed Zip:
```
sudo apt-get install zip unzip
```


