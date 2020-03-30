---
title: 'Spustit skript R: Odkaz na modul'
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí modulu Spouštět skript R v Azure Machine Learning ke spuštění kódu R.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/10/2020
ms.openlocfilehash: f038293b48956ac89314e426df3f5dc491954df3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064206"
---
# <a name="execute-r-script"></a>Spouštění skriptů R

Tento článek popisuje, jak pomocí modulu **Spouštět skript R** ke spuštění kódu R v kanálu návrháře Azure Machine Learning (preview).

Pomocí r můžete provádět úkoly, které nejsou aktuálně podporovány existujícími moduly, například: 
- Vytvoření vlastních transformací dat
- Vyhodnocujte předpovědi pomocí vlastních metrik
- Vytváření modelů pomocí algoritmů, které nejsou implementovány jako samostatné moduly v návrháři

## <a name="r-version-support"></a>Podpora verze R

Návrhář Azure Machine Learning používá distribuci R (KOMPLEXNÍ Archivní síť R) CRAN (Komplexní archivní síť R). Aktuálně používaná verze je CRAN 3.5.1.

## <a name="supported-r-packages"></a>Podporované balíčky R

Prostředí R je předinstalováno s více než 100 balíčky. Úplný seznam naleznete v části [Předinstalované balíčky R](#pre-installed-r-packages).

Můžete také přidat následující kód do libovolného **modulu Spustit R Script** a zobrazit nainstalované balíčky.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  dataframe1 <- data.frame(installed.packages())
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="installing-r-packages"></a>Instalace balíčků R
Chcete-li nainstalovat další `install.packages()` balíčky R, použijte metodu. Nezapomeňte zadat úložiště CRAN. Balíčky jsou nainstalovány pro každý modul **Spouštění jazyka R** script a nejsou sdíleny mezi ostatními moduly **skriptu Execute R.**

Tato ukázka ukazuje, jak nainstalovat Zoo:
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
  > Před instalací zkontrolujte, zda balíček již existuje, aby nedošlo k opakované instalaci. Jako `  if(!require(zoo)) install.packages("zoo",repos = "http://cran.us.r-project.org")` ve výše uvedeném ukázkovém kódu. Opakovaná instalace může způsobit časový výtok požadavku webové služby.     

## <a name="upload-files"></a>Nahrání souborů
**Spouštění skriptu R** podporuje nahrávání souborů pomocí Azure Machine Learning R SDK.

Následující příklad ukazuje, jak nahrát soubor obrázku ve **skriptu Spustit R**:
```R

# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# The entry point function can contain up to two input arguments:
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

Po dokončení spuštění potrubí můžete zobrazit náhled obrazu v pravém panelu modulu

> [!div class="mx-imgBorder"]
> ![Nahraný obrázek](media/module/upload-image-in-r-script.png)

## <a name="how-to-configure-execute-r-script"></a>Konfigurace skriptu Spustit R

Modul **Spouštění skriptu R** obsahuje ukázkový kód, který můžete použít jako výchozí bod. Chcete-li nakonfigurovat modul **Spouštění skriptu R,** zadejte sadu vstupů a kódu, které chcete spustit.

![R-modul](media/module/execute-r-script.png)

Datové sady uložené v návrháři jsou automaticky převedeny na datový rámec R při načtení s tímto modulem.

1.  Přidejte do kanálu modul **Spouštění jazyka R.**

  

1. Připojte všechny vstupy potřebné skriptem. Vstupy jsou volitelné a mohou obsahovat data a další r kód.

    * **Dataset1**: Odkaz na `dataframe1`první vstup jako . Vstupní datová sada musí být naformátována jako CSV, TSV, ARFF nebo můžete připojit datovou sadu Azure Machine Learning.

    * **Dataset2**: Odkaz na `dataframe2`druhý vstup jako . Tato datová sada musí být také formátována jako csv, tsv, soubor ARFF nebo jako datová sada Azure Machine Learning.

    * **Sada skriptů**: Třetí vstup přijímá soubory ZIP. Zip soubor může obsahovat více souborů a více typů souborů.

1. Do textového pole **skriptu R** zadejte nebo vložte platný skript R.

    Abychom vám pomohli začít, je textové pole **Skript Jazyka R** předem vyplněno ukázkovým kódem, který můžete upravit nebo nahradit.
    
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

 * Skript musí obsahovat `azureml_main`funkci s názvem , která je vstupním bodem pro tento modul.

 * Funkce vstupního bodu může obsahovat až `Param<dataframe1>` dva vstupní argumenty: a`Param<dataframe2>`
 
   > [!NOTE]
    > Data předaná modulu **Execute R** `dataframe1` Script `dataframe2`se odvolává jako a , `dataset1`která `dataset2`se liší od návrháře Azure Machine Learning (odkaz na návrháře jako , ). Zkontrolujte, zda jsou vstupní data ve skriptu správně odkazována.  
 
    > [!NOTE]
    >  Existující kód R může vyžadovat menší změny ke spuštění v kanálu návrháře. Například vstupní data, která zadáte ve formátu CSV by měly být explicitně převedeny do datové sady před použitím v kódu. Typy dat a sloupců používané v jazyce R se také v některých ohledech liší od typů dat a sloupců používaných v návrháři.

1.  **Náhodné osiva**: Zadejte hodnotu, která se má použít uvnitř prostředí R jako náhodná hodnota osiva. Tento parametr je `set.seed(value)` ekvivalentní volání v kódu R.  

1. Odešlete potrubí.  

## <a name="results"></a>Výsledky

Moduly **Spouštění skriptu R** mohou vrátit více výstupů, ale musí být poskytnuty jako datové rámce R. Datové rámce jsou automaticky převedeny do datových sad v návrháři pro kompatibilitu s jinými moduly.

Standardní zprávy a chyby z R jsou vráceny do protokolu modulu.

Pokud potřebujete vytisknout výsledky ve skriptu R, Můžete najít vytištěné výsledky v **70_driver_log** v části **Výstupy + protokoly** kartu v pravém panelu modulu.

## <a name="sample-scripts"></a>Ukázkové skripty

Existuje mnoho způsobů, jak můžete rozšířit kanál pomocí vlastního skriptu R.  Tato část obsahuje ukázkový kód pro běžné úkoly.


### <a name="add-r-script-as-an-input"></a>Přidání skriptu R jako vstupu

Modul **Execute R Script** podporuje jako vstupy libovolné soubory skriptu R. Chcete-li tak učinit, musí být odeslány do pracovního prostoru jako součást souboru ZIP.

1. Chcete-li do pracovního prostoru nahrát soubor ZIP obsahující kód R, přejděte na stránku **datových sad,** klepněte na **tlačítko Vytvořit datovou sadu**a pak vyberte Z **místního souboru** a volbu typ datové sady **soubor.**  

1. Ověřte, zda je soubor zip k dispozici v seznamu **Moje datové sady** v kategorii Datové **sady** v levém stromu modulu.

1.  Připojte datovou sadu ke vstupnímu portu **sady Skriptovací sada.**

1. Všechny soubory, které jsou obsaženy v souboru ZIP jsou k dispozici během běhu kanálu. 

    Pokud soubor sady skriptů obsahoval adresářovou strukturu, je struktura zachována. Je však nutné změnit kód předřadit adresář **./Script Bundle** na cestu.

### <a name="process-data"></a>Zpracování dat

Následující ukázka ukazuje, jak škálovat a normalizovat vstupní data:

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

### <a name="read-a-zip-file-as-input"></a>Čtení souboru ZIP jako vstupu

Tato ukázka ukazuje, jak použít datovou sadu v souboru ZIP jako vstup do modulu **Spustit skript R.**

1. Vytvořte datový soubor ve formátu CSV a pojmenujte jej "mydatafile.csv".
1. Vytvořte soubor ZIP a přidejte soubor CSV do archivu.
1. Nahrajte soubor zip do pracovního prostoru Azure Machine Learning. 
1. Připojte výslednou datovou sadu ke vstupu **ScriptBundle** modulu **Spouštění skriptu R.**
1. Pomocí následujícího kódu pro čtení dat CSV ze souboru zip.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  mydataset<-read.csv("./Script Bundle/mydatafile.csv",encoding="UTF-8");  
  # Return datasets as a Named List
  return(list(dataset1=mydataset, dataset2=dataframe2))
}
```

### <a name="replicate-rows"></a>Replikovat řádky

Tato ukázka ukazuje, jak replikovat pozitivní záznamy v datové sadě pro vyrovnání vzorku:

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

### <a name="pass-r-objects-between-execute-r-script-modules"></a>Předat objekty R mezi moduly Skript u spuštění R

Objekty R můžete předat mezi instancemi modulu **Execute R Script** pomocí vnitřního mechanismu serializace. Tento příklad předpokládá, že chcete přesunout `A` objekt R pojmenovaný mezi dvěma moduly **Spouštění skriptu R.**

1. Přidejte do kanálu první modul **Spouštění skriptu R** a do textového pole `A` Skript **R** zadejte následující kód, který vytvoří serializovaný objekt jako sloupec ve výstupní tabulce dat modulu:  
  
    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      # some codes generated A
      
      serialized <- as.integer(serialize(A,NULL))  
      data.set <- data.frame(serialized,stringsAsFactors=FALSE)

      return(list(dataset1=data.set, dataset2=dataframe2))
    }
    ```

    Explicitní převod na typ celé číslo se provádí, protože funkce `Raw` serializace výstupy dat ve formátu R, který není podporován návrhářem.

1. Přidejte druhou instanci modulu **Spustit skript R** a připojte jej k výstupnímu portu předchozího modulu.

1. Do textového pole **Skript R** zadejte `A` následující kód, který chcete extrahovat objekt ze vstupní tabulky dat. 

    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      A <- unserialize(as.raw(dataframe1$serialized))  
      # Return datasets as a Named List
      return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

## <a name="pre-installed-r-packages"></a>Předinstalované balíčky R

Aktuální seznam předinstalovaných balíčků R, které jsou k dispozici pro použití:

|              |            | 
|--------------|------------| 
| Balíček      | Version    | 
| askpass      | 1.1        | 
| assertthat   | 0.2.1      | 
| backports    | 1.1.4      | 
| base         | 3.5.1      | 
| base64enc    | 0.1-3      | 
| BH           | 1.69.0-1   | 
| bindr        | 0.1.1      | 
| bindrcpp     | 0.2.2      | 
| bitops       | 1.0-6      | 
| startování         | 1.3-22     | 
| broom        | 0.5.2      | 
| callr        | 3.2.0      | 
| caret        | 6.0-84     | 
| caTools      | 1.17.1.2   | 
| cellranger   | 1.1.0      | 
| třída        | 7.3-15     | 
| Rozhraní příkazového řádku          | 1.1.0      | 
| zastřihovač        | 0.6.0      | 
| cluster      | 2.0.7-1    | 
| codetools    | 0.2-16     | 
| colorspace   | 1.4-1      | 
|  – kompilátor     | 3.5.1      | 
| crayon       | 1.3.4      | 
| curl         | 3.3        | 
| data.table   | 1.12.2     | 
| datové sady     | 3.5.1      | 
| DBI          | 1.0.0      | 
| dbplyr       | 1.4.1      | 
| digest       | 0.6.19     | 
| dplyr        | 0.7.6      | 
| e1071        | 1.7-2      | 
| Vyhodnocení     | 0.14       | 
| fansi        | 0.4.0      | 
| forcats      | 0.3.0      | 
| foreach      | 1.4.4      | 
| foreign      | 0.8-71     | 
| Fs           | 1.3.1      | 
| gdata        | 2.18.0     | 
| Generik     | 0.0.2      | 
| ggplot2      | 3.2.0      | 
| glmnet       | 2.0-18     | 
| glue         | 1.3.1      | 
| gower        | 0.2.1      | 
| gplots       | 3.0.1.1    | 
| grafika     | 3.5.1      | 
| grDevices    | 3.5.1      | 
| grid         | 3.5.1      | 
| gtable       | 0.3.0      | 
| gtools       | 3.8.1      | 
| haven        | 2.1.0      | 
| highr        | 0.8        | 
| hms          | 0.4.2      | 
| htmltools    | 0.3.6      | 
| httr         | 1.4.0      | 
| ipred        | 0.9-9      | 
| iterators    | 1.0.10     | 
| jsonlite     | 1.6        | 
| KernSmooth   | 2.23-15    | 
| knitr        | 1.23       | 
| labeling     | 0.3        | 
| lattice      | 0.20-38    | 
| lava         | 1.6.5      | 
| lazyeval     | 0.2.2      | 
| lubridate    | 1.7.4      | 
| magrittr     | 1,5        | 
| markdown     | 1          | 
| MASS         | 7.3-51.4   | 
| Matrix       | 1.2-17     | 
| methods      | 3.5.1      | 
| mgcv         | 1.8-28     | 
| mime         | 0.7        | 
| ModelMetrics | 1.2.2      | 
| modelr       | 0.1.4      | 
| munsell      | 0.5.0      | 
| nlme         | 3.1-140    | 
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
| Ps           | 1.3.0      | 
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
| rpart        | 4.1-15     | 
| rstudioapi   | 0.1        | 
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
| sys          | 3,2        | 
| tcltk        | 3.5.1      | 
| tibble       | 2.1.3      | 
| tidyr        | 0.8.3      | 
| tidyselect   | 0.2.5      | 
| tidyverse    | 1.2.1      | 
| timeDate     | 3043.102   | 
| tinytex      | 0.13       | 
| nástroje        | 3.5.1      | 
| tseries      | 0.10-47    | 
| TTR          | 0.23-4     | 
| utf8         | 1.1.4      | 
| utils        | 3.5.1      | 
| vctrs        | 0.1.0      | 
| viridisLite  | 0.3.0      | 
| whisker      | 0.3-2      | 
| withr        | 2.1.2      | 
| xfun         | 0.8        | 
| xml2         | 1.2.0      | 
| xts          | 0.11-2     | 
| yaml         | 2.2.0      | 
| zeallot      | 0.1.0      | 
| zoo          | 1.8-6      | 

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
