---
title: ScaleR a SparkR pomocí Azure HDInsight
description: ScaleR a SparkR pomocí služby ML v HDInsight
services: hdinsight
author: jasonwhowell
ms.author: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/19/2017
ms.openlocfilehash: c59ac5efab8b46463f97c375d999a131667d3cea
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2018
ms.locfileid: "43699545"
---
# <a name="combine-scaler-and-sparkr-in-hdinsight"></a>Kombinovat ScaleR a SparkR v HDInsight

Tento dokument popisuje, jak k předpovědi zpoždění letů doručení pomocí **ScaleR** Logistický regresní model. V příkladu se používá počasí a zpoždění letů, připojený k použití **SparkR**.

I když oba balíčky se spouští na Hadoop Spark prováděcího modulu, jsou blokovány z dat v paměti pro sdílení obsahu jednotlivých potřebují vlastní příslušné relace Spark. Dokud tento problém je vyřešen v nadcházející verzi součástí ML Server, alternativním řešením je udržovat překrývat relace Spark a vyměňovat data prostřednictvím zprostředkující soubory. Zde uvedených pokynů ukazují, že tyto požadavky jsou jednoduché dosáhnout.

V tomto příkladu byl zpočátku sdílí v Přednáška na setkání Strata 2016 Mario Inchiosa a Roni Burd. Tato Přednáška na můžete najít [sestavování škálovatelná platforma pro datovou vědu s jazykem R](http://event.on24.com/eventRegistration/console/EventConsoleNG.jsp?uimode=nextgeneration&eventid=1160288&sessionid=1&key=8F8FB9E2EB1AEE867287CD6757D5BD40&contenttype=A&eventuserid=305999&playerwidth=1000&playerheight=650&caller=previewLobby&text_language_id=en&format=fhaudio).

Kód byl původně zapsán pro ML Server běžící na Sparku v clusteru služby HDInsight v Azure. Ale koncept kombinování použití SparkR a ScaleR v jednom skriptu je taky platná v kontextu v místních prostředích.

Kroky v tomto dokumentu předpokládají, že pokročilou úroveň znalosti jazyka R a vyhoví se jim [ScaleR](https://msdn.microsoft.com/microsoft-r/scaler-user-guide-introduction) knihoven ML serveru. Jste se seznámili s [SparkR](https://spark.apache.org/docs/2.1.0/sparkr.html) při procházení tohoto scénáře.

## <a name="the-airline-and-weather-datasets"></a>Letecké dopravy a meteorologická datové sady

Zapisovači letových údajů je k dispozici [archivy státní správy USA](http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236). Je také k dispozici jako soubor zip z [AirOnTimeCSV.zip](http://packages.revolutionanalytics.com/datasets/AirOnTime87to12/AirOnTimeCSV.zip).

Data o počasí si můžete stáhnout jako soubory zip v nezpracované podobě podle měsíců a fáze z [národním úřadem pro oceán a atmosféru správy úložiště](http://www.ncdc.noaa.gov/orders/qclcd/). V tomto příkladu stáhněte data pro květen 2007 – prosince 2012. Použít hodinové datové soubory a `YYYYMMMstation.txt` souboru v rámci všech zips. 

## <a name="setting-up-the-spark-environment"></a>Nastavení prostředí Spark

K nastavení prostředí Spark pomocí následujícího kódu:

```
workDir        <- '~'  
myNameNode     <- 'default' 
myPort         <- 0
inputDataDir   <- 'wasb://hdfs@myAzureAcccount.blob.core.windows.net'
hdfsFS         <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

# create a persistent Spark session to reduce startup times 
#   (remember to stop it later!)
 
sparkCC        <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort, persistentRun=TRUE)

# create working directories 

rxHadoopMakeDir('/user')
rxHadoopMakeDir('user/RevoShare')
rxHadoopMakeDir('user/RevoShare/remoteuser')

(dataDir <- '/share')
rxHadoopMakeDir(dataDir)
rxHadoopListFiles(dataDir) 

setwd(workDir)
getwd()

# version of rxRoc that runs in a local CC 
rxRoc <- function(...){
  rxSetComputeContext(RxLocalSeq())
  roc <- RevoScaleR::rxRoc(...)
  rxSetComputeContext(sparkCC)
  return(roc)
}

logmsg <- function(msg) { cat(format(Sys.time(), "%Y-%m-%d %H:%M:%S"),':',msg,'\n') } 
t0 <- proc.time() 

#..start 

logmsg('Start') 
(trackers <- system("mapred job -list-active-trackers", intern = TRUE))
logmsg(paste('Number of task nodes=',length(trackers)))
```

V dalším kroku přidejte `Spark_Home` do cesty pro hledání pro balíčky R. Přidávání do cesty pro hledání umožňuje použít SparkR a inicializovat SparkR relace:

```
#..setup for use of SparkR  

logmsg('Initialize SparkR') 

.libPaths(c(file.path(Sys.getenv("SPARK_HOME"), "R", "lib"), .libPaths()))
library(SparkR)

sparkEnvir <- list(spark.executor.instances = '10',
                   spark.yarn.executor.memoryOverhead = '8000')

sc <- sparkR.init(
  sparkEnvir = sparkEnvir,
  sparkPackages = "com.databricks:spark-csv_2.10:1.3.0"
)

sqlContext <- sparkRSQL.init(sc)
```

## <a name="preparing-the-weather-data"></a>Připravují se data o počasí

K přípravě dat o počasí, podmnožina sloupců potřeboval pro modelování: 

- "Viditelnost"
- "DryBulbCelsius"
- "DewPointCelsius"
- "RelativeHumidity"
- "Rychlost větru"
- "Výškoměru"

Pak přidejte kód letiště přidružené meteorologická stanice a proveďte převod měření z místního času na čas UTC.

Začněte tím, že vytvoříte soubor, který chcete namapovat na kód letiště informace meteorologická stanice (WBAN). Následující kód načte všechny hodinové datové soubory nezpracované počasí, podmnožiny sloupců, budeme potřebovat, sloučí soubor mapování meteorologická stanice, upraví data časy měření na čas UTC a pak zapíše si novou verzi souboru:

```
# Look up AirportID and Timezone for WBAN (weather station ID) and adjust time

adjustTime <- function(dataList)
{
  dataset0 <- as.data.frame(dataList)
  
  dataset1 <- base::merge(dataset0, wbanToAirIDAndTZDF1, by = "WBAN")

  if(nrow(dataset1) == 0) {
    dataset1 <- data.frame(
      Visibility = numeric(0),
      DryBulbCelsius = numeric(0),
      DewPointCelsius = numeric(0),
      RelativeHumidity = numeric(0),
      WindSpeed = numeric(0),
      Altimeter = numeric(0),
      AdjustedYear = numeric(0),
      AdjustedMonth = numeric(0),
      AdjustedDay = integer(0),
      AdjustedHour = integer(0),
      AirportID = integer(0)
    )
    
    return(dataset1)
  }
  
  Year <- as.integer(substr(dataset1$Date, 1, 4))
  Month <- as.integer(substr(dataset1$Date, 5, 6))
  Day <- as.integer(substr(dataset1$Date, 7, 8))
  
  Time <- dataset1$Time
  Hour <- ceiling(Time/100)
  
  Timezone <- as.integer(dataset1$TimeZone)
  
  adjustdate = as.POSIXlt(sprintf("%4d-%02d-%02d %02d:00:00", Year, Month, Day, Hour), tz = "UTC") + Timezone * 3600

  AdjustedYear = as.POSIXlt(adjustdate)$year + 1900
  AdjustedMonth = as.POSIXlt(adjustdate)$mon + 1
  AdjustedDay   = as.POSIXlt(adjustdate)$mday
  AdjustedHour  = as.POSIXlt(adjustdate)$hour
  
  AirportID = dataset1$AirportID
  Weather = dataset1[,c("Visibility", "DryBulbCelsius", "DewPointCelsius", "RelativeHumidity", "WindSpeed", "Altimeter")]
  
  data.set = data.frame(cbind(AdjustedYear, AdjustedMonth, AdjustedDay, AdjustedHour, AirportID, Weather))
  
  return(data.set)
}

wbanToAirIDAndTZDF <- read.csv("wban-to-airport-id-tz.csv")

colInfo <- list(
  WBAN = list(type="integer"),
  Date = list(type="character"),
  Time = list(type="integer"),
  Visibility = list(type="numeric"),
  DryBulbCelsius = list(type="numeric"),
  DewPointCelsius = list(type="numeric"),
  RelativeHumidity = list(type="numeric"),
  WindSpeed = list(type="numeric"),
  Altimeter = list(type="numeric")
)

weatherDF <- RxTextData(file.path(inputDataDir, "WeatherRaw"), colInfo = colInfo)

weatherDF1 <- RxTextData(file.path(inputDataDir, "Weather"), colInfo = colInfo,
                filesystem=hdfsFS)

rxSetComputeContext("localpar")
rxDataStep(weatherDF, outFile = weatherDF1, rowsPerRead = 50000, overwrite = T,
           transformFunc = adjustTime,
           transformObjects = list(wbanToAirIDAndTZDF1 = wbanToAirIDAndTZDF))
```

## <a name="importing-the-airline-and-weather-data-to-spark-dataframes"></a>Import letecké dopravy a meteorologická data do datových rámců Spark

Teď můžeme použít SparkR [read.df()](https://docs.databricks.com/spark/1.6/sparkr/functions/read.df.html#read-df) k importu dat o počasí a letecká společnost Spark datových rámců. Tato funkce, stejně jako mnoho dalších metod Spark jsou spouštěny laxně, to znamená, že jsou zařazené do fronty pro provedení ale nebyl proveden, dokud se vyžaduje.

```
airPath     <- file.path(inputDataDir, "AirOnTime08to12CSV")
weatherPath <- file.path(inputDataDir, "Weather") # pre-processed weather data
rxHadoopListFiles(airPath) 
rxHadoopListFiles(weatherPath) 

# create a SparkR DataFrame for the airline data

logmsg('create a SparkR DataFrame for the airline data') 
# use inferSchema = "false" for more robust parsing
airDF <- read.df(sqlContext, airPath, source = "com.databricks.spark.csv", 
                 header = "true", inferSchema = "false")

# Create a SparkR DataFrame for the weather data

logmsg('create a SparkR DataFrame for the weather data') 
weatherDF <- read.df(sqlContext, weatherPath, source = "com.databricks.spark.csv", 
                     header = "true", inferSchema = "true")
```

## <a name="data-cleansing-and-transformation"></a>Čištění dat a transformace

Dále jsme si udělat trochu pořádek jsme importu dat letecká společnost přejmenování sloupců. Jsme pouze proměnné potřeba zachovat a zaokrouhlení plánované odeslání časy až nejbližší hodinu umožňující sloučení s nejnovější data o počasí na odeslání:

```
logmsg('clean the airline data') 
airDF <- rename(airDF,
                ArrDel15 = airDF$ARR_DEL15,
                Year = airDF$YEAR,
                Month = airDF$MONTH,
                DayofMonth = airDF$DAY_OF_MONTH,
                DayOfWeek = airDF$DAY_OF_WEEK,
                Carrier = airDF$UNIQUE_CARRIER,
                OriginAirportID = airDF$ORIGIN_AIRPORT_ID,
                DestAirportID = airDF$DEST_AIRPORT_ID,
                CRSDepTime = airDF$CRS_DEP_TIME,
                CRSArrTime =  airDF$CRS_ARR_TIME
)

# Select desired columns from the flight data. 
varsToKeep <- c("ArrDel15", "Year", "Month", "DayofMonth", "DayOfWeek", "Carrier", "OriginAirportID", "DestAirportID", "CRSDepTime", "CRSArrTime")
airDF <- select(airDF, varsToKeep)

# Apply schema
coltypes(airDF) <- c("character", "integer", "integer", "integer", "integer", "character", "integer", "integer", "integer", "integer")

# Round down scheduled departure time to full hour.
airDF$CRSDepTime <- floor(airDF$CRSDepTime / 100)
```

Teď můžeme provádět podobné operací s daty o počasí:

```
# Average weather readings by hour
logmsg('clean the weather data') 
weatherDF <- agg(groupBy(weatherDF, "AdjustedYear", "AdjustedMonth", "AdjustedDay", "AdjustedHour", "AirportID"), Visibility="avg",
                  DryBulbCelsius="avg", DewPointCelsius="avg", RelativeHumidity="avg", WindSpeed="avg", Altimeter="avg"
                  )

weatherDF <- rename(weatherDF,
                    Visibility = weatherDF$'avg(Visibility)',
                    DryBulbCelsius = weatherDF$'avg(DryBulbCelsius)',
                    DewPointCelsius = weatherDF$'avg(DewPointCelsius)',
                    RelativeHumidity = weatherDF$'avg(RelativeHumidity)',
                    WindSpeed = weatherDF$'avg(WindSpeed)',
                    Altimeter = weatherDF$'avg(Altimeter)'
)
```

## <a name="joining-the-weather-and-airline-data"></a>Spojování dat o počasí a letecká společnost

Teď používáme SparkR [join()](https://docs.databricks.com/spark/1.6/sparkr/functions/join.html#join) funkce levé vnější spojení odeslání AirportID letecké dopravy a meteorologická data a data a času. Vnější spojení umožňuje zachovat všechny záznamy letecká společnost i v případě, že neexistuje žádná odpovídající data o počasí. Po spojení jsme některé sloupce redundantní odebrání a přejmenování uchované sloupce, které chcete odebrat předponu příchozí datový rámec zavedené spojení.

```
logmsg('Join airline data with weather at Origin Airport')
joinedDF <- SparkR::join(
  airDF,
  weatherDF,
  airDF$OriginAirportID == weatherDF$AirportID &
    airDF$Year == weatherDF$AdjustedYear &
    airDF$Month == weatherDF$AdjustedMonth &
    airDF$DayofMonth == weatherDF$AdjustedDay &
    airDF$CRSDepTime == weatherDF$AdjustedHour,
  joinType = "left_outer"
)

# Remove redundant columns
vars <- names(joinedDF)
varsToDrop <- c('AdjustedYear', 'AdjustedMonth', 'AdjustedDay', 'AdjustedHour', 'AirportID')
varsToKeep <- vars[!(vars %in% varsToDrop)]
joinedDF1 <- select(joinedDF, varsToKeep)

joinedDF2 <- rename(joinedDF1,
                    VisibilityOrigin = joinedDF1$Visibility,
                    DryBulbCelsiusOrigin = joinedDF1$DryBulbCelsius,
                    DewPointCelsiusOrigin = joinedDF1$DewPointCelsius,
                    RelativeHumidityOrigin = joinedDF1$RelativeHumidity,
                    WindSpeedOrigin = joinedDF1$WindSpeed,
                    AltimeterOrigin = joinedDF1$Altimeter
)
```

Podobným způsobem jsme spojujte data o počasí a letecká společnost na základě přijetí AirportID a datum a čas:

```
logmsg('Join airline data with weather at Destination Airport')
joinedDF3 <- SparkR::join(
  joinedDF2,
  weatherDF,
  airDF$DestAirportID == weatherDF$AirportID &
    airDF$Year == weatherDF$AdjustedYear &
    airDF$Month == weatherDF$AdjustedMonth &
    airDF$DayofMonth == weatherDF$AdjustedDay &
    airDF$CRSDepTime == weatherDF$AdjustedHour,
  joinType = "left_outer"
)

# Remove redundant columns
vars <- names(joinedDF3)
varsToDrop <- c('AdjustedYear', 'AdjustedMonth', 'AdjustedDay', 'AdjustedHour', 'AirportID')
varsToKeep <- vars[!(vars %in% varsToDrop)]
joinedDF4 <- select(joinedDF3, varsToKeep)

joinedDF5 <- rename(joinedDF4,
                    VisibilityDest = joinedDF4$Visibility,
                    DryBulbCelsiusDest = joinedDF4$DryBulbCelsius,
                    DewPointCelsiusDest = joinedDF4$DewPointCelsius,
                    RelativeHumidityDest = joinedDF4$RelativeHumidity,
                    WindSpeedDest = joinedDF4$WindSpeed,
                    AltimeterDest = joinedDF4$Altimeter
                    )
```

## <a name="save-results-to-csv-for-exchange-with-scaler"></a>Pro exchange s ScaleR uložit výsledky do sdíleného svazku clusteru

Tím končí spojení, které musíme udělat s SparkR. Uložení dat z poslední Spark DataFrame "joinedDF5" do souboru CSV pro vstup do ScaleR jsme pak zavřete SparkR relace. Tom explicitně SparkR uložit výsledné sdíleného svazku clusteru v samostatných oddílech 80 povolit dostatek paralelismus v ScaleR zpracování:

```
logmsg('output the joined data from Spark to CSV') 
joinedDF5 <- repartition(joinedDF5, 80) # write.df below will produce this many CSVs

# write result to directory of CSVs
write.df(joinedDF5, file.path(dataDir, "joined5Csv"), "com.databricks.spark.csv", "overwrite", header = "true")

# We can shut down the SparkR Spark context now
sparkR.stop()

# remove non-data files
rxHadoopRemove(file.path(dataDir, "joined5Csv/_SUCCESS"))
```

## <a name="import-to-xdf-for-use-by-scaler"></a>Importovat do XDF používají ScaleR

Mohli bychom použít souboru CSV se připojené k doméně letecké dopravy a meteorologická data jako-je pro modelování prostřednictvím zdroje dat ScaleR text. Ale jsme importovat ho do XDF nejprve, protože je mnohem efektivnější při spuštění více operací u datové sady:

```
logmsg('Import the CSV to compressed, binary XDF format') 

# set the Spark compute context for ML Services 
rxSetComputeContext(sparkCC)
rxGetComputeContext()

colInfo <- list(
  ArrDel15 = list(type="numeric"),
  Year = list(type="factor"),
  Month = list(type="factor"),
  DayofMonth = list(type="factor"),
  DayOfWeek = list(type="factor"),
  Carrier = list(type="factor"),
  OriginAirportID = list(type="factor"),
  DestAirportID = list(type="factor"),
  RelativeHumidityOrigin = list(type="numeric"),
  AltimeterOrigin = list(type="numeric"),
  DryBulbCelsiusOrigin = list(type="numeric"),
  WindSpeedOrigin = list(type="numeric"),
  VisibilityOrigin = list(type="numeric"),
  DewPointCelsiusOrigin = list(type="numeric"),
  RelativeHumidityDest = list(type="numeric"),
  AltimeterDest = list(type="numeric"),
  DryBulbCelsiusDest = list(type="numeric"),
  WindSpeedDest = list(type="numeric"),
  VisibilityDest = list(type="numeric"),
  DewPointCelsiusDest = list(type="numeric")
)

joinedDF5Txt <- RxTextData(file.path(dataDir, "joined5Csv"),
                           colInfo = colInfo, fileSystem = hdfsFS)
rxGetInfo(joinedDF5Txt) 

destData <- RxXdfData(file.path(dataDir, "joined5XDF"), fileSystem = hdfsFS)

rxImport(inData = joinedDF5Txt, destData, overwrite = TRUE)

rxGetInfo(destData, getVarInfo = T)

# File name: /user/RevoShare/dev/delayDataLarge/joined5XDF 
# Number of composite data files: 80 
# Number of observations: 148619655 
# Number of variables: 22 
# Number of blocks: 320 
# Compression type: zlib 
# Variable information: 
#   Var 1: ArrDel15, Type: numeric, Low/High: (0.0000, 1.0000)
# Var 2: Year
# 26 factor levels: 1987 1988 1989 1990 1991 ... 2008 2009 2010 2011 2012
# Var 3: Month
# 12 factor levels: 10 11 12 1 2 ... 5 6 7 8 9
# Var 4: DayofMonth
# 31 factor levels: 1 3 4 5 7 ... 29 30 2 18 31
# Var 5: DayOfWeek
# 7 factor levels: 4 6 7 1 3 2 5
# Var 6: Carrier
# 30 factor levels: PI UA US AA DL ... HA F9 YV 9E VX
# Var 7: OriginAirportID
# 374 factor levels: 15249 12264 11042 15412 13930 ... 13341 10559 14314 11711 10558
# Var 8: DestAirportID
# 378 factor levels: 13303 14492 10721 11057 13198 ... 14802 11711 11931 12899 10559
# Var 9: CRSDepTime, Type: integer, Low/High: (0, 24)
# Var 10: CRSArrTime, Type: integer, Low/High: (0, 2400)
# Var 11: RelativeHumidityOrigin, Type: numeric, Low/High: (0.0000, 100.0000)
# Var 12: AltimeterOrigin, Type: numeric, Low/High: (28.1700, 31.1600)
# Var 13: DryBulbCelsiusOrigin, Type: numeric, Low/High: (-46.1000, 47.8000)
# Var 14: WindSpeedOrigin, Type: numeric, Low/High: (0.0000, 81.0000)
# Var 15: VisibilityOrigin, Type: numeric, Low/High: (0.0000, 90.0000)
# Var 16: DewPointCelsiusOrigin, Type: numeric, Low/High: (-41.7000, 29.0000)
# Var 17: RelativeHumidityDest, Type: numeric, Low/High: (0.0000, 100.0000)
# Var 18: AltimeterDest, Type: numeric, Low/High: (28.1700, 31.1600)
# Var 19: DryBulbCelsiusDest, Type: numeric, Low/High: (-46.1000, 53.9000)
# Var 20: WindSpeedDest, Type: numeric, Low/High: (0.0000, 136.0000)
# Var 21: VisibilityDest, Type: numeric, Low/High: (0.0000, 88.0000)
# Var 22: DewPointCelsiusDest, Type: numeric, Low/High: (-43.0000, 29.0000)

finalData <- RxXdfData(file.path(dataDir, "joined5XDF"), fileSystem = hdfsFS)

```

## <a name="splitting-data-for-training-and-test"></a>Rozdělení dat pro trénování a testování

Můžeme použít rxDataStep oddělit data 2012 pro testování a ponechat přitom rest pro vzdělávání:

```
# split out the training data

logmsg('split out training data as all data except year 2012')
trainDS <- RxXdfData( file.path(dataDir, "finalDataTrain" ),fileSystem = hdfsFS)

rxDataStep( inData = finalData, outFile = trainDS,
            rowSelection = ( Year != 2012 ), overwrite = T )

# split out the testing data

logmsg('split out the test data for year 2012') 
testDS <- RxXdfData( file.path(dataDir, "finalDataTest" ), fileSystem = hdfsFS)

rxDataStep( inData = finalData, outFile = testDS,
            rowSelection = ( Year == 2012 ), overwrite = T )

rxGetInfo(trainDS)
rxGetInfo(testDS)
```

## <a name="train-and-test-a-logistic-regression-model"></a>Trénování a testování modelu logistické regrese

Nyní jsme připraveni k sestavení modelu. Zobrazit vliv data o počasí na zpoždění v čas doručení, použijeme ScaleR rutina logistické regrese. Můžeme použít k modelování, jestli k doručení prodlevě o délce delší než 15 minut je ovlivněno počasí na letištích odeslání a přijetí:

```
logmsg('train a logistic regression model for Arrival Delay > 15 minutes') 
formula <- as.formula(ArrDel15 ~ Year + Month + DayofMonth + DayOfWeek + Carrier +
                     OriginAirportID + DestAirportID + CRSDepTime + CRSArrTime + 
                     RelativeHumidityOrigin + AltimeterOrigin + DryBulbCelsiusOrigin +
                     WindSpeedOrigin + VisibilityOrigin + DewPointCelsiusOrigin + 
                     RelativeHumidityDest + AltimeterDest + DryBulbCelsiusDest +
                     WindSpeedDest + VisibilityDest + DewPointCelsiusDest
                   )

# Use the scalable rxLogit() function but set max iterations to 3 for the purposes of 
# this exercise 

logitModel <- rxLogit(formula, data = trainDS, maxIterations = 3)

base::summary(logitModel)
```

Nyní Podíváme se, jak to funguje v testovacích dat tím, že některé předpovědi a prohlížení roc s více TŘÍDAMI a AUC.

```
# Predict over test data (Logistic Regression).

logmsg('predict over the test data') 
logitPredict <- RxXdfData(file.path(dataDir, "logitPredict"), fileSystem = hdfsFS)

# Use the scalable rxPredict() function

rxPredict(logitModel, data = testDS, outData = logitPredict,
          extraVarsToWrite = c("ArrDel15"), 
          type = 'response', overwrite = TRUE)

# Calculate ROC and Area Under the Curve (AUC).

logmsg('calculate the roc and auc') 
logitRoc <- rxRoc("ArrDel15", "ArrDel15_Pred", logitPredict)
logitAuc <- rxAuc(logitRoc)
head(logitAuc)
logitAuc

plot(logitRoc)
```

## <a name="scoring-elsewhere"></a>Vyhodnocování jinde

Model můžeme využít také pro vyhodnocení dat na jiné platformě. Uložení do souboru vzdálené plochy a přenosu a import tohoto vzdálené plochy do cílového prostředí, například SQL Server R Services vyhodnocování. Je důležité zajistit, že úrovně dat zohlednit odpovídají těch, na kterých byla vytvořena modelu. Které odpovídají se dá dosáhnout extrahování a ukládá informace o sloupci přidružené k modelování dat prostřednictvím vaší ScaleR `rxCreateColInfo()` funkce a následným použitím tohoto sloupce informace ke zdroji vstupních dat pro předpovědi. V následujícím jsme uložit několik řádků datové sady, testovací a extrahovat a používat informace o sloupci od této ukázky ve skriptu předpovědi:

```
# save the model and a sample of the test dataset 

logmsg('save serialized version of the model and a sample of the test data')
rxSetComputeContext('localpar') 
saveRDS(logitModel, file = "logitModel.rds")
testDF <- head(testDS, 1000)  
saveRDS(testDF    , file = "testDF.rds"    )
list.files()

rxHadoopListFiles(file.path(inputDataDir,''))
rxHadoopListFiles(dataDir)

# stop the spark engine 
rxStopEngine(sparkCC) 

logmsg('Done.')
elapsed <- (proc.time() - t0)[3]
logmsg(paste('Elapsed time=',sprintf('%6.2f',elapsed),'(sec)\n\n'))
```

## <a name="summary"></a>Souhrn

V tomto článku jsme ukázali, jak je možné kombinovat použití SparkR pro manipulaci s daty s ScaleR pro vývoj pro model v hdinsight Hadoop Spark. Tento scénář vyžaduje udržovat samostatné relace Spark pouze jednu relaci spuštění najednou a vyměňovat data přes soubory CSV. I když je jasné, tento proces by měl být v příští verzi služby ML, ještě snadnější SparkR ScaleR můžete sdílet relaci Spark a tak sdílet Spark DataFrames.

## <a name="next-steps-and-more-information"></a>Další informace a další kroky

- Další informace o použití nástroje ML serveru ve Sparku, najdete v článku [Průvodce Začínáme](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started)

- Obecné informace o ML Server, najdete v článku [Začínáme s jazykem R](https://msdn.microsoft.com/microsoft-r/microsoft-r-get-started-node) článku.

- Informace týkající se služby ML na HDInsight naleznete v tématu [přehled ML služby na HDInsight](r-server/r-server-overview.md) a [Začínáme se službou ML v Azure HDInsight](r-server/r-server-get-started.md).

Další informace o použití SparkR najdete v tématu:

- [Apache SparkR dokumentu](https://spark.apache.org/docs/2.1.0/sparkr.html)

- [Přehled SparkR](https://docs.databricks.com/spark/latest/sparkr/overview.html) z Databricks
