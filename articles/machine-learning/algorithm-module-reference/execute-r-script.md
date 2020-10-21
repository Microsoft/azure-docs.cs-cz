---
title: 'Spustit skript R: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se používat modul r skriptu Execute v Azure Machine Learning ke spuštění kódu R.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/21/2020
ms.openlocfilehash: a86c0b115ef866453e457ad528dd694ed7b49b48
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92330389"
---
# <a name="execute-r-script-module"></a>Spustit modul skriptu R

Tento článek popisuje, jak použít modul skriptu Run R ke spuštění kódu R v kanálu návrháře Azure Machine Learning.

Pomocí jazyka R můžete provádět úkoly, které nejsou podporovány existujícími moduly, například: 
- Vytváření vlastních transformací dat
- Použití vlastních metrik k vyhodnocení předpovědi
- Modely sestavení pomocí algoritmů, které nejsou implementované jako samostatné moduly v Návrháři

## <a name="r-version-support"></a>Podpora verze R

Azure Machine Learning Designer používá distribuci R v CRAN (komplexní archivní síť v R). Aktuálně použitá verze je CRAN 3.5.1.

## <a name="supported-r-packages"></a>Podporované balíčky R

Prostředí jazyka R je předem nainstalováno s více než 100 balíčky. Úplný seznam najdete v části [předinstalované balíčky R](#preinstalled-r-packages).

K zobrazení nainstalovaných balíčků můžete také přidat následující kód do libovolného modulu skriptu R Execute.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  dataframe1 <- data.frame(installed.packages())
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```
> [!NOTE]
> Pokud váš kanál obsahuje více modulů spuštění skriptu R, které vyžadují balíčky, které nejsou v předinstalovaném seznamu, nainstalujte balíčky v každém modulu. 

## <a name="installing-r-packages"></a>Instalace balíčků R
K instalaci dalších balíčků R použijte `install.packages()` metodu. Balíčky se nainstalují pro každý modul spuštění skriptu jazyka R. Nesdílí se mezi jinými moduly spouštění skriptu jazyka R.

> [!NOTE]
> Při instalaci balíčků zadejte úložiště CRAN, například `install.packages("zoo",repos = "http://cran.us.r-project.org")` .

V této ukázce se dozvíte, jak nainstalovat:
```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main,
# which is the entry point for this module.

# Note that functions dependent on the X11 library,
# such as "View," are not supported because the X11 library
# is not preinstalled.

# The entry point function MUST have two input arguments.
# If the input port is not connected, the corresponding
# dataframe argument will be null.
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  
  if(!require(zoo)) install.packages("zoo",repos = "http://cran.us.r-project.org")
  library(zoo)
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```
 > [!NOTE]
 > Před instalací balíčku ověřte, jestli už existuje, takže nebudete instalaci opakovat. Opakování instalací může způsobit vypršení požadavků webové služby.     

## <a name="uploading-files"></a>Nahrání souborů
Modul spuštění skriptu jazyka R podporuje nahrávání souborů pomocí Azure Machine Learning R SDK.

Následující příklad ukazuje, jak nahrát soubor obrázku do skriptu spustit R:
```R

# R version: 3.5.1
# The script MUST contain a function named azureml_main,
# which is the entry point for this module.

# Note that functions dependent on the X11 library,
# such as "View," are not supported because the X11 library
# is not preinstalled.

# The entry point function MUST have two input arguments.
# If the input port is not connected, the corresponding
# dataframe argument will be null.
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")

  # Generate a jpeg graph
  img_file_name <- "rect.jpg"
  jpeg(file=img_file_name)
  example(rect)
  dev.off()

  upload_files_to_run(names = list(file.path("graphic", img_file_name)), paths=list(img_file_name))


  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

Po dokončení spuštění kanálu můžete zobrazit náhled obrázku v pravém panelu modulu.

> [!div class="mx-imgBorder"]
> ![Náhled nahraného obrázku](media/module/upload-image-in-r-script.png)

## <a name="access-to-registered-dataset"></a>Přístup k registrované datové sadě

Můžete se podívat na následující vzorový kód pro [přístup k registrovaným datovým sadám](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets#access-datasets-in-your-script) v pracovním prostoru:

```R
        azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  run = get_current_run()
  ws = run$experiment$workspace
  dataset = azureml$core$dataset$Dataset$get_by_name(ws, "YOUR DATASET NAME")
  dataframe2 <- dataset$to_pandas_dataframe()
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="how-to-configure-execute-r-script"></a>Jak nakonfigurovat skript spouštěný v jazyce R

Modul spuštění skriptu jazyka R obsahuje vzorový kód jako výchozí bod.

![Diagram vstupů pro modul R](media/module/execute-r-script.png)

Datové sady uložené v návrháři se při načtení s tímto modulem automaticky převedou na datový rámec R.

1.  Přidejte do svého kanálu modul **spuštění skriptu jazyka R** .  

1. Připojte všechny vstupy, které skript potřebuje. Vstupy jsou volitelné a můžou obsahovat data a další kód R.

    * **DataSet1.**: odkazuje na první vstup jako `dataframe1` . Vstupní datová sada musí být naformátovaná jako soubor CSV, TSV nebo ARFF. Můžete také připojit Azure Machine Learning datovou sadu.

    * **Dataset2**: odkázat na druhý vstup jako `dataframe2` . Tato datová sada musí být také naformátovaná jako soubor CSV, TSV nebo ARFF nebo jako datová sada Azure Machine Learning.

    * **Sada skriptů**: třetí vstup přijímá soubory. zip. Soubor ZIP může obsahovat více souborů a více typů souborů.

1. Do textového pole **skript jazyka r** zadejte nebo vložte platný skript r.

    > [!NOTE]
    > Při psaní skriptu buďte opatrní. Ujistěte se, že neexistují žádné chyby syntaxe, jako je například použití nedeklarovaných proměnných nebo neimportované moduly nebo funkce. Věnujte další pozornost seznamu předinstalovaných balíčků na konci tohoto článku. Pokud chcete používat balíčky, které nejsou uvedené, nainstalujte je do skriptu. Příklad: `install.packages("zoo",repos = "http://cran.us.r-project.org")`.
    
    Abychom vám pomohli začít, je textové pole **skriptu jazyka R** předem vyplněno ukázkovým kódem, který lze upravit nebo nahradit.
    
    ```R
    # R version: 3.5.1
    # The script MUST contain a function named azureml_main,
    # which is the entry point for this module.

    # Note that functions dependent on the X11 library,
    # such as "View," are not supported because the X11 library
    # is not preinstalled.
    
    # The entry point function MUST have two input arguments.
    # If the input port is not connected, the corresponding
    # dataframe argument will be null.
    #   Param<dataframe1>: a R DataFrame
    #   Param<dataframe2>: a R DataFrame
    azureml_main <- function(dataframe1, dataframe2){
    print("R script run.")

    # If a .zip file is connected to the third input port, it's
    # unzipped under "./Script Bundle". This directory is added
    # to sys.path.

    # Return datasets as a Named List
    return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

    Funkce vstupního bodu musí mít vstupní argumenty `Param<dataframe1>` a `Param<dataframe2>` , i když se tyto argumenty ve funkci nepoužívají.

    > [!NOTE]
    > Data předaná do modulu spuštění skriptu jazyka R jsou odkazována jako `dataframe1` a `dataframe2` , která se liší od Azure Machine Learning Designer (odkaz návrháře jako `dataset1` `dataset2` ). Ujistěte se, že se vstupní data ve skriptu správně neodkazují.  
 
    > [!NOTE]
    > Existující kód R může vyžadovat drobné změny ke spuštění v kanálu návrháře. Například vstupní data, která zadáte ve formátu CSV, by měla být explicitně převedena na datovou sadu, aby ji bylo možné použít ve svém kódu. Typy dat a sloupců používané v jazyce R se také liší v různých způsobech z dat a typů sloupců použitých v návrháři.

1. Pokud je váš skript větší než 16 KB, použijte port **sady skriptu** , aby se předešlo chybám, jako *je příkazový řádek, který překračuje limit 16597 znaků*. 
    
    1. Vytvořte balíček skriptu a dalších vlastních prostředků do souboru ZIP.
    1. Nahrajte soubor ZIP jako **datovou sadu souboru** do studia. 
    1. Přetáhněte modul DataSet ze seznamu *datových sad* v levém podokně modulu na stránce vytváření návrháře. 
    1. Připojte modul DataSet k portu **skriptu** sady **spouštěného modulu R Script** .
    
    Následuje ukázkový kód pro využití skriptu ve skriptovém svazku:

    ```R
    azureml_main <- function(dataframe1, dataframe2){
    # Source the custom R script: my_script.R
    source("./Script Bundle/my_script.R")

    # Use the function that defined in my_script.R
    dataframe1 <- my_func(dataframe1)

    sample <- readLines("./Script Bundle/my_sample.txt")
    return (list(dataset1=dataframe1, dataset2=data.frame("Sample"=sample)))
    }
    ```

1.  V případě **náhodného osazení**zadejte hodnotu, která se má použít v prostředí jazyka R, jako náhodná hodnota počáteční hodnoty. Tento parametr je ekvivalentní volání `set.seed(value)` v kódu R.  

1. Odešlete kanál.  

## <a name="results"></a>Výsledky

Spouštění modulů skriptu jazyka R může vracet více výstupů, ale musí být zadány jako datové snímky R. Návrhář automaticky převede datové snímky na datové sady kvůli kompatibilitě s jinými moduly.

Do protokolu modulu se vrátí standardní zprávy a chyby z R.

Pokud potřebujete ve skriptu R Tisknout výsledky, můžete najít tištěné výsledky v **70_driver_log** na kartě **výstupy + protokoly** v pravém panelu modulu.

## <a name="sample-scripts"></a>Ukázkové skripty

Existuje mnoho způsobů, jak svůj kanál rozšíříte pomocí vlastních skriptů R. V této části najdete vzorový kód pro běžné úlohy.


### <a name="add-an-r-script-as-an-input"></a>Přidání skriptu jazyka R jako vstupu

Modul spuštění skriptu jazyka R podporuje jako vstupy libovolné soubory skriptu R. Pokud je chcete použít, musíte je do svého pracovního prostoru nahrát jako součást souboru. zip.

1. Pokud chcete nahrát soubor. zip, který obsahuje kód R, do svého pracovního prostoru, otevřete stránku Asset **Sets** . Vyberte **vytvořit datovou sadu**a pak vyberte možnost **z místního souboru** a typ datové sady **souborů** .  

1. Ověřte, že se soubor zip objevuje ve **složce** DataSets v kategorii **datové sady** ve stromu vlevo.

1.  Připojte datovou sadu ke vstupnímu portu **sady skriptu** .

1. Všechny soubory v souboru. zip jsou k dispozici během běhu kanálu. 

    Pokud soubor sady skriptu obsahuje adresářovou strukturu, struktura se zachová. Je však nutné upravit kód pro **předvedení adresáře./Script** do cesty.

### <a name="process-data"></a>Zpracování dat

Následující příklad ukazuje, jak škálovat a normalizovat vstupní data:

```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main,
# which is the entry point for this module.

# Note that functions dependent on the X11 library,
# such as "View," are not supported because the X11 library
# is not preinstalled.

# The entry point function MUST have two input arguments.
# If the input port is not connected, the corresponding
# dataframe argument will be null.
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  # If a .zip file is connected to the third input port, it's
  # unzipped under "./Script Bundle". This directory is added
  # to sys.path.
  series <- dataframe1$width
  # Find the maximum and minimum values of the width column in dataframe1
  max_v <- max(series)
  min_v <- min(series)
  # Calculate the scale and bias
  scale <- max_v - min_v
  bias <- min_v / dis
  # Apply min-max normalizing
  dataframe1$width <- dataframe1$width / scale - bias
  dataframe2$width <- dataframe2$width / scale - bias
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
 ```

### <a name="read-a-zip-file-as-input"></a>Přečtěte si soubor. zip jako vstup.

Tato ukázka ukazuje, jak použít datovou sadu v souboru. zip jako vstup pro modul spuštění skriptu jazyka R.

1. Vytvořte datový soubor ve formátu CSV a pojmenujte ho **mydatafile.csv**.
1. Vytvořte soubor. zip a přidejte ho do archivu.
1. Nahrajte soubor zip do svého pracovního prostoru Azure Machine Learning. 
1. Připojte výslednou datovou sadu k **ScriptBundle** vstupu modulu **skriptu pro spuštění R** .
1. Pomocí následujícího kódu si přečtěte data sdíleného svazku clusteru ze souboru ZIP.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  mydataset<-read.csv("./Script Bundle/mydatafile.csv",encoding="UTF-8");  
  # Return datasets as a Named List
  return(list(dataset1=mydataset, dataset2=dataframe2))
}
```

### <a name="replicate-rows"></a>Replikovat řádky

Tento příklad ukazuje, jak replikovat kladné záznamy v datové sadě pro vyvážení ukázky:

```R
azureml_main <- function(dataframe1, dataframe2){
  data.set <- dataframe1[dataframe1[,1]==-1,]  
  # positions of the positive samples
  pos <- dataframe1[dataframe1[,1]==1,]
  # replicate the positive samples to balance the sample  
  for (i in 1:20) data.set <- rbind(data.set,pos)  
  row.names(data.set) <- NULL
  # Return datasets as a Named List
  return(list(dataset1=data.set, dataset2=dataframe2))
}
```

### <a name="pass-r-objects-between-execute-r-script-modules"></a>Předávání objektů R mezi spouštěním modulů skriptu jazyka R

Můžete předat objekty R mezi instancemi modulu spouštění skriptu jazyka R pomocí interního mechanizmu serializace. V tomto příkladu se předpokládá, že chcete přesunout objekt R pojmenovaný `A` mezi dvěma moduly spuštění skriptu jazyka r.

1. Přidejte do svého kanálu první modul pro **spuštění skriptu jazyka R** . Poté zadejte následující kód do textového pole **skript jazyka R** pro vytvoření serializovaného objektu `A` jako sloupce v tabulce výstupních dat modulu:  
  
    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      # some codes generated A
      
      serialized <- as.integer(serialize(A,NULL))  
      data.set <- data.frame(serialized,stringsAsFactors=FALSE)

      return(list(dataset1=data.set, dataset2=dataframe2))
    }
    ```

    Explicitní převod na celočíselný typ je proveden, protože funkce serializace výstupuje data ve `Raw` formátu R, který Návrhář nepodporuje.

1. Přidejte druhou instanci modulu **spuštění skriptu jazyka R** a připojte ho k výstupnímu portu předchozího modulu.

1. Do textového pole **skript jazyka R** zadejte následující kód pro extrakci objektu `A` z tabulky vstupních dat. 

    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      A <- unserialize(as.raw(dataframe1$serialized))  
      # Return datasets as a Named List
      return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

## <a name="preinstalled-r-packages"></a>Předinstalované balíčky R

V současné době jsou k dispozici následující předinstalované balíčky jazyka R:

| Balíček      | Verze    | 
|--------------|------------| 
| askpass      | 1,1        | 
| assertthat   | 0.2.1      | 
| backports    | 1.1.4      | 
| base         | 3.5.1      | 
| base64enc    | 0.1-3      | 
| BH           | 1.69.0-1   | 
| bindr        | 0.1.1      | 
| bindrcpp     | 0.2.2      | 
| bitops       | 1.0-6      | 
| boot         | 1.3 – 22     | 
| broom        | 0.5.2      | 
| callr        | 3.2.0      | 
| stříška        | 6.0 – 84     | 
| caTools      | 1.17.1.2   | 
| cellranger   | 1.1.0      | 
| třída        | 7.3 – 15     | 
| cli          | 1.1.0      | 
| Galerie        | 0.6.0      | 
| cluster      | 2.0.7-1    | 
| codetools    | 0,2 – 16     | 
| colorspace   | 1.4-1      | 
| compiler     | 3.5.1      | 
| crayon       | 1.3.4      | 
| curl         | 3.3        | 
| data.table   | 1.12.2     | 
| datové sady     | 3.5.1      | 
| DBI          | 1.0.0      | 
| dbplyr       | 1.4.1      | 
| digest       | 0.6.19     | 
| dplyr        | 0.7.6      | 
| e1071        | 1.7-2      | 
| evaluate     | 0.14       | 
| fansi        | 0.4.0      | 
| forcats      | 0.3.0      | 
| foreach      | 1.4.4      | 
| foreign      | 0,8 – 71     | 
| FS           | 1.3.1      | 
| gdata        | 2.18.0     | 
| Obecné typy     | 0.0.2      | 
| ggplot2      | 3.2.0      | 
| glmnet       | 2.0 – 18     | 
| glue         | 1.3.1      | 
| gower        | 0.2.1      | 
| gplots       | 3.0.1.1    | 
| grafika     | 3.5.1      | 
| grDevices    | 3.5.1      | 
| grid         | 3.5.1      | 
| gtable       | 0.3.0      | 
| gtools       | 3.8.1      | 
| haven        | 2.1.0      | 
| highr        | 0,8        | 
| hms          | 0.4.2      | 
| htmltools    | 0.3.6      | 
| httr         | 1.4.0      | 
| ipred        | 0.9 – 9      | 
| iterators    | 1.0.10     | 
| jsonlite     | 1.6        | 
| KernSmooth   | 2.23-15    | 
| knitr        | 1.23       | 
| labeling     | 0.3        | 
| lattice      | 0,20 – 38    | 
| lava         | 1.6.5      | 
| lazyeval     | 0.2.2      | 
| lubridate    | 1.7.4      | 
| magrittr     | 1.5        | 
| markdown     | 1          | 
| MASS         | 7.3 – 51.4   | 
| Matice       | 1,2 – 17     | 
| methods      | 3.5.1      | 
| mgcv         | 1.8 – 28     | 
| mime         | 0,7        | 
| ModelMetrics | 1.2.2      | 
| modelr       | 0.1.4      | 
| munsell      | 0.5.0      | 
| nlme         | 3.1 – 140    | 
| nnet         | 7.3-12     | 
| numDeriv     | 2016.8-1.1 | 
| openssl      | 1.4        | 
| parallel     | 3.5.1      | 
| pillar       | 1.4.1      | 
| pkgconfig    | 2.0.2      | 
| plogr        | 0.2.0      | 
| plyr         | 1.8.4      | 
| prettyunits  | 1.0.2      | 
| processx     | 3.3.1      | 
| prodlim      | 2018.04.18 | 
| progress     | 1.2.2      | 
| PS           | 1.3.0      | 
| purrr        | 0.3.2      | 
| quadprog     | 1.5-7      | 
| quantmod     | 0.4-15     | 
| R6           | 2.4.0      | 
| randomForest | 4.6-14     | 
| RColorBrewer | 1.1-2      | 
| Rcpp         | 1.0.1      | 
| RcppRoll     | 0.3.0      | 
| readr        | 1.3.1      | 
| readxl       | 1.3.1      | 
| recipes      | 0.1.5      | 
| rematch      | 1.0.1      | 
| reprex       | 0.3.0      | 
| reshape2     | 1.4.3      | 
| reticulate   | 1.12       | 
| rlang        | 0.4.0      | 
| rmarkdown    | 1.13       | 
| ROCR         | 1.0-7      | 
| rpart        | 4.1 – 15     | 
| rstudioapi   | 0,1        | 
| rvest        | 0.3.4      | 
| scales       | 1.0.0      | 
| selectr      | 0.4-1      | 
| spatial      | 7.3-11     | 
| splines      | 3.5.1      | 
| SQUAREM      | 2017.10-1  | 
| stats        | 3.5.1      | 
| stats4       | 3.5.1      | 
| stringi      | 1.4.3      | 
| stringr      | 1.3.1      | 
| survival     | 2.44-1.1   | 
| sys          | 3.2        | 
| tcltk        | 3.5.1      | 
| tibble       | 2.1.3      | 
| tidyr        | 0.8.3      | 
| tidyselect   | 0.2.5      | 
| tidyverse    | 1.2.1      | 
| timeDate     | 3043.102   | 
| tinytex      | 0.13       | 
| tools        | 3.5.1      | 
| tseries      | 0.10 – 47    | 
| TTR          | 0.23-4     | 
| utf8         | 1.1.4      | 
| utils        | 3.5.1      | 
| vctrs        | 0.1.0      | 
| viridisLite  | 0.3.0      | 
| whisker      | 0.3-2      | 
| withr        | 2.1.2      | 
| xfun         | 0,8        | 
| xml2         | 1.2.0      | 
| xts          | 0,11 – 2     | 
| yaml         | 2.2.0      | 
| zeallot      | 0.1.0      | 
| zoo          | 1.8-6      | 

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
