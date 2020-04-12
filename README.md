# Madrid pollution data extraction and cleaning

### Goal:

This project goal is to extract public data information about pollution in Madrid, clean it and add data that makes sense if there are missing values, using Python programming language. The source is [this Madrid council webpage](https://datos.madrid.es/portal/site/egob/menuitem.c05c1f754a33a9fbe4b2e4b284f1a5a0/?vgnextoid=f3c0f7d512273410VgnVCM2000000c205a0aRCRD&vgnextchannel=374512b9ace9f310VgnVCM100000171f5a0aRCRD&vgnextfmt=default). We will download data for 2018. This data is real time hourly data. In this case we will just keep the NO2 agent.

### Steps:

In the Jupyter Notebook that you can find in this repo, the next steps are taken:

* Downloading the data ([2018 real time data url](https://datos.madrid.es/egob/catalogo/201200-10306314-calidad-aire-horario.zip)), which comes in a zip folder with a file for each month in 3 different formats (.txt, .csv and .xml). In this case we will use the .csv extension files.

* Extracting just the .csv files from the zip folder.

* Adding records for missing days.

* Transforming the monthly dataframe, as the default format is not good to work with. There is a row for each day, with a column for each hour in the day, and another column for each hour to tell if that value is validated or not. In the transformed dataframe, we will have a column for hours and another one to indicate if that record is validated or not.

* Filling  non validated hour values. We assign the average of the previous validated value and the next validated value.

* Adding columns for year-month-date and year-month-date-time.

* Finally we append all the monthly files together to have a yearly file.

* Some tests to check if the transformation has been satisfactory.

* Some data exploration.

* Some charts to see visualized data.

### Data loading:

I would store this data in a relational database, to which other users could connect to make queries, draw charts, ad-hoc analysis, etc.

This database would contain several tables:

* record: the one with all the data we cleaned. I would add an extra column with a concatenation of ('año', 'mes', 'dia', 'hora', 'punto_muestreo'), to set it as this table primary key, and finally another concatenation of 'año', 'mes', 'dia', 'hora' to set the relation with the station table.

* station: a table with the following columns: 'punto_muestreo_id' (primary key), 'estacion_id', 'municipio_id', 'provincia_id', 'punto_muestreo_id', 'estacion', 'municipio', 'provincia'. In the record table we have just the ids, but there are other tables with the names of municipio, punto_muestreo, etc. This table would include [this info](https://gist.github.com/koldLight/533038c852ca0a546da247292b5d9ab9).

* date: a table with the following columns: 'hora', 'dia', 'mes', 'año', 'dia_semana', 'fin_de_semana', 'dia/noche', a concatenation of ('año', 'mes', 'dia', 'hora'), to set it as a primary key and sort the data by time in that way.

An important decission to make, based on performance, would be whether to load the data in each loop (month by month, 12 loads) or after appending 12 months (a year of data, 1 single load)

### Next steps:

* Building the database just mentioned.

* Other ways to fill missed values. The method I used was getting the nearest previous value and the nearest next value and assign the average of these two. Well, sometimes a whole day is missing, so the actual method will fill the 24 hours in that day with the same value. Maybe we could add ways to do this, like get the average NO2 level for each hour of the day in the near spots, or apply time series algorithms.

* Automate the loads to the database.

* Automatically detect if there are new stations.