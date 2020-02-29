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
ms.date: 11/19/2019
ms.openlocfilehash: d39ac40e8e29c7ff90e2accc3a519449571c1d58
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2020
ms.locfileid: "77917403"
---
# <a name="execute-r-script"></a>Spouštění skriptů R

Tento článek popisuje, jak použít modul **skriptu Run r** ke spuštění kódu R v kanálu návrháře Azure Machine Learning (Preview).

Pomocí jazyka R můžete provádět úlohy, které aktuálně nejsou podporovány existujícími moduly, například: 
- Vytváření vlastních transformací dat
- Použití vlastních metrik k vyhodnocení předpovědi
- Modely sestavení pomocí algoritmů, které nejsou implementované jako samostatné moduly v Návrháři

## <a name="r-version-support"></a>Podpora verze R

Azure Machine Learning Designer používá distribuci R v CRAN (komplexní archivní síť v R). Aktuálně použitá verze je CRAN 3.5.1.

## <a name="supported-r-packages"></a>Podporované balíčky R

Prostředí jazyka R je předem nainstalováno s více balíčky 100. Úplný seznam najdete v části [předinstalované balíčky R](#pre-installed-r-packages).

Následující kód můžete také přidat do jakéhokoli **spouštěného modulu skriptu R** a zobrazit nainstalované balíčky.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  dataframe1 <- data.frame(installed.packages())
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="installing-r-packages"></a>Instalace balíčků R
Chcete-li nainstalovat další balíčky R, použijte metodu `install.packages()`. Nezapomeňte zadat úložiště CRAN. Balíčky se nainstalují pro každý modul **spuštění skriptu r** a nesdílí se mezi jinými moduly **spouštění skriptu jazyka r** .

V této ukázce se dozvíte, jak nainstalovat:
```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# The entry point function can contain up to two input arguments:
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
  > Zkontrolujte prosím, jestli balíček už existuje, a teprve potom ho nainstalujte, abyste se vyhnuli opakované instalaci. Podobně jako `  if(!require(zoo)) install.packages("zoo",repos = "http://cran.us.r-project.org")` výše vzorový kód. Opakování instalace může způsobit vypršení časového limitu požadavku webové služby.     

## <a name="how-to-configure-execute-r-script"></a>Jak nakonfigurovat skript spouštěný v jazyce R

Modul **spuštění skriptu jazyka R** obsahuje vzorový kód, který můžete použít jako výchozí bod. Chcete-li nakonfigurovat modul **skriptu Execute R** , poskytněte sadu vstupů a kódů, které mají být provedeny.

![R – modul](media/module/execute-r-script.png)

Datové sady uložené v návrháři se při načtení s tímto modulem automaticky převedou na datový rámec R.

1.  Přidejte do svého kanálu modul **spuštění skriptu jazyka R** .

  

1. Připojte libovolné vstupy, které skript potřebuje. Vstupy jsou volitelné a můžou obsahovat data a další kód R.

    * **DataSet1.** : odkazuje na první vstup jako `dataframe1`. Vstupní datová sada musí být naformátovaná jako CSV, TSV, ARFF nebo můžete připojit Azure Machine Learning datovou sadu.

    * **Dataset2**: odkázat na druhý vstup jako `dataframe2`. Tato datová sada musí být také naformátovaná jako soubor CSV, TSV, ARFF nebo jako datová sada Azure Machine Learning.

    * **Sada skriptů**: třetí vstup přijímá soubory zip. Soubor ZIP může obsahovat více souborů a více typů souborů.

1. Do textového pole **skript jazyka r** zadejte nebo vložte platný skript r.

    Abychom vám pomohli začít, textové pole **skriptu jazyka R** je předem vyplněno ukázkovým kódem, který můžete upravit nebo nahradit.
    
```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")

  # If a zip file is connected to the third input port, it is
  # unzipped under "./Script Bundle". This directory is added
  # to sys.path.

  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

 * Skript musí obsahovat funkci s názvem `azureml_main`, což je vstupní bod pro tento modul.

 * Funkce vstupního bodu může obsahovat až dva vstupní argumenty: `Param<dataframe1>` a `Param<dataframe2>`
 
   > [!NOTE]
    > Data předaná do modulu **spuštění skriptu jazyka R** jsou odkazována jako `dataframe1` a `dataframe2`, která se liší od Azure Machine Learning Designer (odkaz návrháře jako `dataset1`, `dataset2`). Zkontrolujte prosím, jestli se vstupní data ve skriptu referneced správně.  
 
    > [!NOTE]
    >  Existující kód R může vyžadovat drobné změny ke spuštění v kanálu návrháře. Například vstupní data, která zadáte ve formátu CSV, by měla být explicitně převedena na datovou sadu, aby ji bylo možné použít ve svém kódu. Typy dat a sloupců používané v jazyce R se také liší v různých způsobech z dat a typů sloupců použitých v návrháři.

1.  **Náhodný základ**: zadejte hodnotu, která se má použít v prostředí jazyka R, jako náhodná hodnota počáteční hodnoty. Tento parametr je ekvivalentní volání `set.seed(value)` v kódu R.  

1. Spuštění kanálu  

## <a name="results"></a>Výsledky

Moduly **spouštěné skriptem R** mohou vracet více výstupů, ale musí být zadány jako datové snímky R. Datové snímky jsou automaticky převedeny na datové sady v návrháři, aby byly kompatibilní s jinými moduly.

Do protokolu modulu se vrátí standardní zprávy a chyby z R.

Pokud potřebujete ve skriptu R Tisknout výsledky, můžete najít tištěné výsledky na kartě **70_driver_log** v části **výstupy + protokoly** na pravém panelu modulu.

## <a name="sample-scripts"></a>Ukázkové skripty

Existuje mnoho způsobů, jak kanál můžete roztáhnout pomocí vlastního skriptu jazyka R.  V této části najdete vzorový kód pro běžné úlohy.


### <a name="add-r-script-as-an-input"></a>Přidat skript jazyka R jako vstup

Modul **spuštění skriptu jazyka r** podporuje jako vstupy libovolné soubory skriptu r. V takovém případě je třeba je odeslat do vašeho pracovního prostoru jako součást souboru ZIP.

1. Pokud chcete nahrát soubor ZIP, který obsahuje kód R, do svého pracovního prostoru, klikněte na **Nový**, klikněte na **datovou sadu**a pak vyberte možnost **z místního souboru** a **souboru ZIP** .  

1. Ověřte, zda je soubor zip k dispozici v seznamu **uložených datových sad** .

1.  Připojte datovou sadu ke vstupnímu portu **sady skriptu** .

1. Všechny soubory, které jsou obsaženy v souboru ZIP, jsou k dispozici během běhu kanálu. 

    Pokud soubor sady skriptu obsahuje adresářovou strukturu, struktura se zachová. Je však nutné upravit kód pro **předvedení adresáře./Script** do cesty.

### <a name="process-data"></a>Zpracování dat

Následující příklad ukazuje, jak škálovat a normalizovat vstupní data:

```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.
# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  # If a zip file is connected to the third input port, it is
  # unzipped under "./Script Bundle". This directory is added
  # to sys.path.
  series <- dataframe1$width
  # find the maximum and minimum values of width column in dataframe1
  max_v <- max(series)
  min_v <- min(series)
  # calculate the scale and bias
  scale <- max_v - min_v
  bias <- min_v / dis
  # apply min-max normalizing
  dataframe1$width <- dataframe1$width / scale - bias
  dataframe2$width <- dataframe2$width / scale - bias
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
 ```

### <a name="read-a-zip-file-as-input"></a>Přečíst soubor ZIP jako vstup

Tato ukázka ukazuje, jak použít datovou sadu v souboru ZIP jako vstup pro modul **spouštění skriptu jazyka R** .

1. Vytvořte datový soubor ve formátu CSV a pojmenujte ho "mydatafile. csv".
1. Vytvořte soubor ZIP a přidejte soubor CSV do archivu.
1. Nahrajte soubor zip do svého pracovního prostoru Azure Machine Learning. 
1. Připojte výslednou datovou sadu k **ScriptBundle** vstupu modulu **skriptu pro spuštění R** .
1. K načtení dat sdíleného svazku clusteru ze souboru ZIP použijte následující kód.

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

Můžete předat objekty R mezi instancemi modulu **spouštění skriptu jazyka r** pomocí interního mechanizmu serializace. V tomto příkladu se předpokládá, že chcete přesunout objekt R s názvem `A` mezi dvěma moduly **provádění skriptu jazyka r** .

1. Přidejte do svého kanálu první modul **spuštění skriptu jazyka r** a do textového pole **skript jazyka r** zadejte následující kód, který vytvoří serializovaný objekt `A` jako sloupec v tabulce výstupních dat modulu:  
  
    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      # some codes generated A
      
      serialized <- as.integer(serialize(A,NULL))  
      data.set <- data.frame(serialized,stringsAsFactors=FALSE)

      return(list(dataset1=data.set, dataset2=dataframe2))
    }
    ```

    Explicitní převod na celočíselný typ je proveden, protože funkce serializace výstupuje data ve formátu R `Raw`, který není podporován návrhářem.

1. Přidejte druhou instanci modulu **spuštění skriptu jazyka R** a připojte ho k výstupnímu portu předchozího modulu.

1. Do textového pole **skript R** zadejte následující kód pro extrakci `A` objektů z tabulky vstupních dat. 

    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      A <- unserialize(as.raw(dataframe1$serialized))  
      # Return datasets as a Named List
      return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

## <a name="pre-installed-r-packages"></a>Předem nainstalované balíčky R

Aktuální seznam předem nainstalovaných balíčků R dostupných k použití:

|              |            | 
|--------------|------------| 
| Balíček      | Verze    | 
| askpass      | 1.1        | 
| assertthat   | 0.2.1      | 
| porty pro porty    | 1.1.4      | 
| base         | 3.5.1      | 
| base64enc    | 0,1 – 3      | 
| BH           | 1.69.0-1   | 
| pořadač        | 0.1.1      | 
| bindrcpp     | 0.2.2      | 
| bitops       | 1,0 – 6      | 
| startování         | 1.3-22     | 
| broom        | 0.5.2      | 
| volající        | 3.2.0      | 
| něj        | 6.0 – 84     | 
| caTools      | 1.17.1.2   | 
| cellranger   | 1.1.0      | 
| Třída        | 7.3-15     | 
| CLI          | 1.1.0      | 
| Galerie        | 0.6.0      | 
| služby      | 2.0.7-1    | 
| codetools    | 0,2 – 16     | 
| colorspace   | 1.4-1      | 
| Přepínač     | 3.5.1      | 
| pastel       | 1.3.4      | 
| Curl         | 3.3        | 
| data. tabulka   | 1.12.2     | 
| datové sady     | 3.5.1      | 
| DBI          | 1.0.0      | 
| dbplyr       | 1.4.1      | 
| digest       | 0.6.19     | 
| dplyr        | 0.7.6      | 
| e1071        | 1.7-2      | 
| Vyhodnocení     | 0.14       | 
| ventilátory        | 0.4.0      | 
| forcats      | 0.3.0      | 
| foreach      | 1.4.4      | 
| cizí      | 0,8 – 71     | 
| FS           | 1.3.1      | 
| gdata        | 2.18.0     | 
| obecné typy     | 0.0.2      | 
| ggplot2      | 3.2.0      | 
| glmnet       | 2.0 – 18     | 
| spojovací         | 1.3.1      | 
| gower        | 0.2.1      | 
| gplots       | 3.0.1.1    | 
| grafika     | 3.5.1      | 
| grDevices    | 3.5.1      | 
| mřížka         | 3.5.1      | 
| gtable       | 0.3.0      | 
| gtools       | 3.8.1      | 
| Některé        | 2.1.0      | 
| vysoká        | 0,8        | 
| hms          | 0.4.2      | 
| htmltools    | 0.3.6      | 
| httr         | 1.4.0      | 
| ipred        | 0.9 – 9      | 
| iterátory    | 1.0.10     | 
| jsonlite     | 1.6        | 
| KernSmooth   | 2.23-15    | 
| knitr        | 1,23       | 
| označování     | 0,3        | 
| mřížky      | 0,20 – 38    | 
| lava         | 1.6.5      | 
| lazyeval     | 0.2.2      | 
| lubridate    | 1.7.4      | 
| magrittr     | 1,5        | 
| Markdownu     | 1          | 
| HROMADNÉ         | 7.3-51.4   | 
| Službu       | 1.2-17     | 
| metody      | 3.5.1      | 
| mgcv         | 1.8 – 28     | 
| typů         | 0,7        | 
| ModelMetrics | 1.2.2      | 
| model       | 0.1.4      | 
| munsell      | 0.5.0      | 
| nlme         | 3.1 – 140    | 
| sušené         | 7.3-12     | 
| numDeriv     | 2016.8-1.1 | 
| OpenSSL      | 1,4        | 
| parallel     | 3.5.1      | 
| pilíř       | 1.4.1      | 
| pkgconfig    | 2.0.2      | 
| plogr        | 0.2.0      | 
| plyr         | 1.8.4      | 
| prettyunits  | 1.0.2      | 
| processx     | 3.3.1      | 
| prodlim      | 2018.04.18 | 
| průběh     | 1.2.2      | 
| PS           | 1.3.0      | 
| purrr        | 0.3.2      | 
| quadprog     | 1.5-7      | 
| quantmod     | 0.4-15     | 
| R6           | 2.4.0      | 
| randomForest | 4.6-14     | 
| RColorBrewer | 1.1-2      | 
| Rcpp         | 1.0.1      | 
| RcppRoll     | 0.3.0      | 
| čtení        | 1.3.1      | 
| readxl       | 1.3.1      | 
| složení      | 0.1.5      | 
| přerovnávat      | 1.0.1      | 
| reprex       | 0.3.0      | 
| reshape2     | 1.4.3      | 
| reticulate   | 1,12       | 
| rlang        | 0.4.0      | 
| rmarkdown    | 1,13       | 
| ROCR         | 1,0 – 7      | 
| rpart        | 4.1-15     | 
| rstudioapi   | 0.1        | 
| rvest        | 0.3.4      | 
| Uprav       | 1.0.0      | 
| výběr      | 0.4-1      | 
| klepání      | 7.3-11     | 
| křivky vyhlazení      | 3.5.1      | 
| SQUAREM      | 2017.10-1  | 
| statistické        | 3.5.1      | 
| stats4       | 3.5.1      | 
| řetězce      | 1.4.3      | 
| Stringer      | 1.3.1      | 
| záchran     | 2.44-1.1   | 
| sys          | 3,2        | 
| tcltk        | 3.5.1      | 
| tibble       | 2.1.3      | 
| tidyr        | 0.8.3      | 
| tidyselect   | 0.2.5      | 
| tidyverse    | 1.2.1      | 
| timeDate     | 3043,102   | 
| tinytex      | 0,13       | 
| nástroje        | 3.5.1      | 
| tseries      | 0.10 – 47    | 
| TTR          | 0.23-4     | 
| utf8         | 1.1.4      | 
| utils        | 3.5.1      | 
| vctrs        | 0.1.0      | 
| viridisLite  | 0.3.0      | 
| vous      | 0,3 – 2      | 
| s ním        | 2.1.2      | 
| xfun         | 0,8        | 
| xml2         | 1.2.0      | 
| XTS          | 0,11 – 2     | 
| YAML         | 2.2.0      | 
| zeallot      | 0.1.0      | 
| zoo          | 1.8 – 6      | 

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
