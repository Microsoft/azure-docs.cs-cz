---
title: 'Spustit skript jazyka R: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Naučte se používat modul R skriptu Execute ve službě Azure Machine Learning ke spuštění kódu R.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: peterlu
ms.date: 06/01/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 710d64b445953ae3124830931c8cbb9315d32b83
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875715"
---
# <a name="execute-r-script"></a>Spouštění skriptů R

Tento článek popisuje, jak použít modul **skriptu Run r** ke spuštění kódu R ve experimentu vizuálního rozhraní.

Pomocí jazyka R můžete provádět úlohy, které aktuálně nejsou podporovány existujícími moduly, například: 
- Vytváření vlastních transformací dat
- Použití vlastních metrik k vyhodnocení předpovědi
- Vytváření modelů pomocí algoritmů, které nejsou implementované jako samostatné moduly v vizuálním rozhraní

## <a name="r-version-support"></a>Podpora verze R

Vizuální rozhraní Azure Machine Learning služby používá distribuci R v CRAN (komplexní archivní síť v R). Aktuálně použitá verze je CRAN 3.5.1.

## <a name="supported-r-packages"></a>Podporované balíčky R

Prostředí jazyka R je předem nainstalováno s více balíčky 100. Úplný seznam najdete v části [předinstalované balíčky R](#pre-installed-r-packages).

Následující kód můžete také přidat do jakéhokoli spouštěného modulu **skriptu R** a zobrazit nainstalované balíčky.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  dataframe1 <- data.frame(installed.packages())
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="installing-r-packages"></a>Instalace balíčků R
K instalaci dalších balíčků R použijte `install.packages()` metodu. Nezapomeňte zadat úložiště CRAN. Balíčky se nainstalují pro každý modul **spuštění skriptu r** a nesdílí se mezi jinými moduly **spouštění skriptu jazyka r** .

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

## <a name="how-to-configure-execute-r-script"></a>Jak nakonfigurovat skript spouštěný v jazyce R

Modul **spuštění skriptu jazyka R** obsahuje vzorový kód, který můžete použít jako výchozí bod. Chcete-li nakonfigurovat modul **skriptu Execute R** , poskytněte sadu vstupů a kódů, které mají být provedeny.

![R – modul](media/module/execute-r-script.png)

Datové sady uložené ve vizuálním rozhraní se při načtení s tímto modulem automaticky převedou na datový rámec R.

1.  Přidejte do experimentu modul **spuštění skriptu jazyka R** .

    > [!NOTE]
    > Všechna data předaná do modulu **spouštěného skriptu jazyka r** jsou převedena `data.frame` do formátu R.

1. Připojte libovolné vstupy, které skript potřebuje. Vstupy jsou volitelné a můžou obsahovat data a další kód R.

    * **DataSet1.** : Odkázat na první vstup `dataframe1`jako. Vstupní datová sada musí být naformátovaná jako CSV, TSV, ARFF nebo můžete připojit Azure Machine Learning datovou sadu.

    * **Dataset2**: Odkázat na druhý vstup `dataframe2`jako. Tato datová sada musí být také naformátovaná jako soubor CSV, TSV, ARFF nebo jako datová sada Azure Machine Learning.

    * **Sada skriptů**: Třetí vstup přijímá soubory ZIP. Soubor ZIP může obsahovat více souborů a více typů souborů.

1. Do textového pole **skript jazyka r** zadejte nebo vložte platný skript r.

    Abychom vám pomohli začít, textové pole **skriptu jazyka R** je předem vyplněno ukázkovým kódem, který můžete upravit nebo nahradit.

    * Skript musí obsahovat funkci s názvem `azureml_main`, která je vstupním bodem pro tento modul.

    * Funkce vstupního bodu může obsahovat až dva vstupní argumenty: `Param<dataframe1>` a`Param<dataframe2>`
 
    > [!NOTE]
    >  Existující kód R může vyžadovat drobné změny ke spuštění v experimentu vizuálního rozhraní. Například vstupní data, která zadáte ve formátu CSV, by měla být explicitně převedena na datovou sadu, aby ji bylo možné použít ve svém kódu. Typy dat a sloupců používané v jazyce R se také liší v různých způsobech z dat a typů sloupců používaných ve vizuálním rozhraní.

1.  **Náhodné osazení**: Zadejte hodnotu, která se má použít v prostředí jazyka R jako hodnota náhodného počátečního základu. Tento parametr je ekvivalentní volání `set.seed(value)` v kódu R.  

1. Spusťte experiment.  

## <a name="results"></a>Výsledky

Moduly **spouštěné skriptem R** mohou vracet více výstupů, ale musí být zadány jako datové snímky R. Datové snímky se automaticky převedou na datové sady vizuálního rozhraní, aby byly kompatibilní s jinými moduly.

Do protokolu modulu se vrátí standardní zprávy a chyby z R.

## <a name="sample-scripts"></a>Ukázkové skripty

Existuje mnoho způsobů, jak můžete experimentovat pomocí vlastního skriptu jazyka R.  V této části najdete vzorový kód pro běžné úlohy.


### <a name="add-r-script-as-an-input"></a>Přidat skript jazyka R jako vstup

Modul **spuštění skriptu jazyka r** podporuje jako vstupy libovolné soubory skriptu r. V takovém případě je třeba je odeslat do vašeho pracovního prostoru jako součást souboru ZIP.

1. Pokud chcete nahrát soubor ZIP, který obsahuje kód R, do svého pracovního prostoru, klikněte na **Nový**, klikněte na **datovou sadu**a pak vyberte možnost **z místního souboru** a **souboru ZIP** .  

1. Ověřte, zda je soubor zip k dispozici v seznamu **uložených datových sad** .

1.  Připojte datovou sadu ke vstupnímu portu **sady skriptu** .

1. Všechny soubory, které jsou obsaženy v souboru ZIP, jsou k dispozici během experimentu v době spuštění. 

    Pokud soubor sady skriptu obsahuje adresářovou strukturu, struktura se zachová. Je však nutné upravit kód pro předvedení adresáře **./Script** do cesty.

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

Můžete předat objekty R mezi instancemi modulu **spouštění skriptu jazyka r** pomocí interního mechanizmu serializace. V tomto příkladu se předpokládá, že chcete přesunout objekt r pojmenovaný `A` mezi dvěma moduly **spuštění skriptu jazyka r** .

1. Přidejte do experimentu první modul **spuštění skriptu jazyka r** a zadejte do textového pole **skript jazyka r** následující kód pro vytvoření serializovaného objektu `A` jako sloupce v tabulce výstupních dat modulu:  
  
    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      # some codes generated A
      
      serialized <- as.integer(serialize(A,NULL))  
      data.set <- data.frame(serialized,stringsAsFactors=FALSE)

      return(list(dataset1=data.set, dataset2=dataframe2))
    }
    ```

    Explicitní převod na celočíselný typ je proveden, protože funkce serializace výstupuje data ve `Raw` formátu R, což není podporováno vizuálním rozhraním.

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

## <a name="pre-installed-r-packages"></a>Předem nainstalované balíčky R

Aktuální seznam předem nainstalovaných balíčků R dostupných k použití:

|              |            | 
|--------------|------------| 
| Balíček      | Version    | 
| askpass      | 1.1        | 
| assertthat   | 0.2.1      | 
| porty pro porty    | 1.1.4      | 
| základ         | 3.5.1      | 
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
| třída        | 7.3-15     | 
| CLI          | 1.1.0      | 
| Galerie        | 0.6.0      | 
| Služby      | 2.0.7-1    | 
| codetools    | 0,2 – 16     | 
| colorspace   | 1.4-1      | 
| Přepínač     | 3.5.1      | 
| pastel       | 1.3.4      | 
| Curl         | 3.3        | 
| data. tabulka   | 1.12.2     | 
| datasets     | 3.5.1      | 
| DBI          | 1.0.0      | 
| dbplyr       | 1.4.1      | 
| digest       | 0.6.19     | 
| dplyr        | 0.7.6      | 
| e1071        | 1.7-2      | 
| Vyhodnocení     | 0.14       | 
| ventilátory        | 0.4.0      | 
| forcats      | 0.3.0      | 
| foreach      | 1.4.4      | 
| měnov      | 0,8 – 71     | 
| FS           | 1.3.1      | 
| gdata        | 2.18.0     | 
| Obecné typy     | 0.0.2      | 
| ggplot2      | 3.2.0      | 
| glmnet       | 2.0 – 18     | 
| spojovací         | 1.3.1      | 
| gower        | 0.2.1      | 
| gplots       | 3.0.1.1    | 
| prvky     | 3.5.1      | 
| grDevices    | 3.5.1      | 
| mřížky         | 3.5.1      | 
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
| magrittr     | 1.5        | 
| Markdownu     | 1          | 
| HROMADNÉ         | 7.3-51.4   | 
| Službu       | 1.2-17     | 
| Způsobů      | 3.5.1      | 
| mgcv         | 1.8 – 28     | 
| typů         | 0,7        | 
| ModelMetrics | 1.2.2      | 
| model       | 0.1.4      | 
| munsell      | 0.5.0      | 
| nlme         | 3.1 – 140    | 
| sušené         | 7.3-12     | 
| numDeriv     | 2016.8-1.1 | 
| OpenSSL      | 1.4        | 
| parallel     | 3.5.1      | 
| pilíř       | 1.4.1      | 
| pkgconfig    | 2.0.2      | 
| plogr        | 0.2.0      | 
| plyr         | 1.8.4      | 
| prettyunits  | 1.0.2      | 
| processx     | 3.3.1      | 
| prodlim      | 2018.04.18 | 
| Přejde     | 1.2.2      | 
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
| recepty      | 0.1.5      | 
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
| křivky      | 3.5.1      | 
| SQUAREM      | 2017.10-1  | 
| statistické        | 3.5.1      | 
| stats4       | 3.5.1      | 
| řetězce      | 1.4.3      | 
| Stringer      | 1.3.1      | 
| záchran     | 2.44-1.1   | 
| tabulce          | 3.2        | 
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

## <a name="next-steps"></a>Další postup

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning služby. 