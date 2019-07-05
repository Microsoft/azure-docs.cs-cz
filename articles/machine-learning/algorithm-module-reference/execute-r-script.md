---
title: 'Skript R spusťte: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak spustit kód R použít modul spustit skript jazyka R ve službě Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: peterclu
ms.date: 06/01/2019
ROBOTS: NOINDEX
ms.openlocfilehash: bfddcd3db4825ea1875474aa16544aa15412bdea
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2019
ms.locfileid: "67518050"
---
# <a name="execute-r-script"></a>Spouštění skriptů R

Tento článek popisuje způsob použití **spustit skript jazyka R** modul pro spuštění kódu jazyka R v experimentu vizuální rozhraní.

S jazykem R můžete provádět úlohy, které nejsou aktuálně podporovány existující moduly, například: 
- Vytvoření vlastních dat transformace
- Vlastní metriky se používá k vyhodnocení predikcí
- Sestavujte modely pomocí algoritmů, které nejsou implementované jako nabízených samostatných modulů ve vizuální rozhraní

## <a name="r-version-support"></a>Podpora verzí R

Vizuální rozhraní služby Azure Machine Learning používá distribuce (Comprehensive R Archive Network) CRAN R. Aktuálně používané verzi je síť CRAN 3.5.1.

## <a name="supported-r-packages"></a>Podporované balíčky R

Prostředí R je předem nainstalovaných pomocí více než 100 balíčků. Úplný seznam najdete v části [balíčky R předinstalovaný](#pre-installed-r-packages).

Můžete také přidat následující kód k libovolnému **spustit skript jazyka R** modulu a zobrazíte nainstalované balíčky.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  dataframe1 <- data.frame(installed.packages())
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="installing-r-packages"></a>Instalace balíčků R
K instalaci dalších balíčků R můžete použít `install.packages()` metody. Nezapomeňte zadat úložiště CRAN. Balíčky se nainstalují pro každého **spustit skript jazyka R** modulu a nejsou sdíleny napříč jiné **spustit skript jazyka R** moduly.

Tento příklad ukazuje, jak nainstalovat Zoo:
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

## <a name="how-to-configure-execute-r-script"></a>Postup konfigurace spustit skript jazyka R

**Spustit skript jazyka R** modul obsahuje ukázkový kód, který můžete použít jako výchozí bod. Ke konfiguraci **spustit skript jazyka R** modulu, poskytují sadu vstupů a spuštění kódu.

![Modul R](media/module/execute-r-script.png)

Datové sady uložené ve vizuální rozhraní se automaticky převedou na R datového rámce při načtení pomocí tohoto modulu.

1.  Přidat **spustit skript jazyka R** modulu do experimentu.

    > [!NOTE]
    > Všechna data předat **spustit skript jazyka R** modul je převeden na R `data.frame` formátu.

1. Připojte žádné vstupy potřeba skript. Vstupy jsou volitelné a může obsahovat data a další kód R.

    * **Dataset1**: Odkazovat prvního vstupu jako `dataframe1`. Vstupní datová sada musí být ve formátu CSV, TSV, ARFF nebo datová sada služby Azure Machine Learning se můžete připojit.

    * **Dataset2**: Odkazovat na druhého vstupu jako `dataframe2`. Tato datová sada také musí být ve formátu CSV, TSV, ARFF souboru, nebo jako datová sada služby Azure Machine Learning.

    * **Skript sady**: Třetím vstupem přijímá soubory ZIP. Komprimovaný soubor může obsahovat několik souborů a více typů souborů.

1. V **skript jazyka R** textového pole zadejte nebo vložte platný skript jazyka R.

    Abychom vám mohli začít, **skript jazyka R** textové pole je předem vyplněná ukázkový kód, který můžete upravit ani nahradit.

    * Skript může obsahovat funkci s názvem `azureml_main`, což je vstupním bodem pro tento modul.

    * Funkci vstupního bodu může obsahovat až dva vstupní argumenty: `Param<dataframe1>` a `Param<dataframe2>`
 
    > [!NOTE]
    >  Existující kód R může být nutné drobné změny pro spouštění v experimentu vizuální rozhraní. Například vstupní data, která je zadat ve formátu CSV musí explicitně převést na datové sady před použitím v kódu. Typy dat a sloupce používané v jazyce R se liší v některé z dat ve sloupci typy používané v vizuální rozhraní.

1.  **Náhodné**: Zadejte hodnotu pro použití v prostředí R jako náhodný počáteční hodnoty. Tento parametr je ekvivalentní volání `set.seed(value)` v kódu jazyka R.  

1. Spusťte experiment.  

## <a name="results"></a>Výsledky

**Spustit skript jazyka R** moduly můžete vrátit několik výstupů, ale musí být zadaný jako R datové rámce. Datové rámce se automaticky převedou na vizuální rozhraní datové sady z důvodu kompatibility s ostatními moduly.

Standardní zprávy a chyby z jazyka R se vrátí do modulu protokolu.

## <a name="sample-scripts"></a>Ukázkové skripty

Existuje mnoho způsobů, které lze rozšíření experimentů pomocí vlastních skriptů jazyka R.  Tato část obsahuje ukázkový kód pro běžné úlohy.


### <a name="add-r-script-as-an-input"></a>Přidat skript jazyka R jako vstup

**Spustit skript jazyka R** modul podporuje různé soubory skriptu jazyka R za vstupy. Uděláte to tak, musí se nahrát do pracovního prostoru jako součást souboru ZIP.

1. Nahrajte soubor ZIP obsahující kódu R do pracovního prostoru, klikněte na tlačítko **nový**, klikněte na tlačítko **datovou sadu**a pak vyberte **z místního souboru** a **soubor Zip**možnost.  

1. Ověřte, zda je k dispozici v souboru ZIP **uložení datové sady** seznamu.

1.  Připojení datovou sadu, která **skript sady** vstupním portem.

1. Všechny soubory, které jsou obsaženy v souboru ZIP sady jsou k dispozici při spuštění experimentu. 

    Pokud soubor skriptu sady obsažené adresářovou strukturu, struktura se zachovají. Ale je nutné změnit váš kód pro předřazení adresáři **. / skript sady** k cestě.

### <a name="process-data"></a>Zpracování dat

Následující příklad ukazuje, jak škálovat a normalizovat vstupních dat:

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

### <a name="read-a-zip-file-as-input"></a>Čtení souboru ZIP jako vstup

Tento příklad ukazuje, jak použít datovou sadu v souboru ZIP jako vstup **spustit skript jazyka R** modulu.

1. Vytvoření datového souboru ve formátu CSV a pojmenujte ho "mydatafile.csv".
1. Vytvořte soubor ZIP a přidání souboru CSV do archivu.
1. Nahrajte soubor ZIP do pracovního prostoru Azure Machine Learning. 
1. Připojit výslednou datovou sadu, která **ScriptBundle** vstupu z vaší **spustit skript jazyka R** modulu.
1. Pomocí následujícího kódu ze souboru ZIP číst data ve formátu CSV.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  mydataset<-read.csv("./Script Bundle/mydatafile.csv",encoding="UTF-8");  
  # Return datasets as a Named List
  return(list(dataset1=mydataset, dataset2=dataframe2))
}
```

### <a name="replicate-rows"></a>Replikovat řádků

Tento příklad ukazuje, jak replikovat kladné záznamy v datové sadě pro ukázku:

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

### <a name="pass-r-objects-between-execute-r-script-modules"></a>Předat objekty R mezi moduly spustit skript jazyka R

Objekty jazyka R můžete předat mezi instancemi **spustit skript jazyka R** modulů pomocí mechanismu interní serializace. Tento příklad předpokládá, že chcete přesunout objekt R s názvem `A` mezi dvěma **spustit skript jazyka R** moduly.

1. Přidat první **spustit skript jazyka R** modulů na experiment a zadejte následující kód na **skript jazyka R** textového pole k vytvoření serializovaný objekt `A` jako sloupec v modulu výstupní tabulka dat:  
  
    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      # some codes generated A
      
      serialized <- as.integer(serialize(A,NULL))  
      data.set <- data.frame(serialized,stringsAsFactors=FALSE)

      return(list(dataset1=data.set, dataset2=dataframe2))
    }
    ```

    Explicitní převod na celočíselný typ je provedeno, protože funkce serializace výstupů dat v R `Raw` formátu, který není podporovaný modulem vizuální rozhraní.

1. Přidání druhé instance **spustit skript jazyka R** modulu a propojte jej s výstupním portem modulu předchozí.

1. Zadejte následující kód **skript jazyka R** textového pole k extrakci objekt `A` ze vstupní tabulky dat. 

    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      A <- unserialize(as.raw(dataframe1$serialized))  
      # Return datasets as a Named List
      return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

## <a name="pre-installed-r-packages"></a>Balíčky předem nainstalovaných R

Aktuální seznam předem nainstalované balíčky R k dispozici pro použití:

|              |            | 
|--------------|------------| 
| Balíček      | Version    | 
| askpass      | 1.1        | 
| assertthat   | 0.2.1      | 
| zpětné    | 1.1.4      | 
| základ         | 3.5.1      | 
| base64enc    | 0.1-3      | 
| BH           | 1.69.0-1   | 
| bindr        | 0.1.1      | 
| bindrcpp     | 0.2.2      | 
| bitops       | 1.0-6      | 
| startování         | 1.3-22     | 
| zarovnání        | 0.5.2      | 
| callr        | 3.2.0      | 
| Blikající kurzor        | 6.0-84     | 
| caTools      | 1.17.1.2   | 
| cellranger   | 1.1.0      | 
| třída        | 7.3-15     | 
| rozhraní příkazového řádku          | 1.1.0      | 
| clipr        | 0.6.0      | 
| Clusteru      | 2.0.7-1    | 
| codetools    | 0.2-16     | 
| barevný prostor   | 1.4-1      | 
| Kompilátor     | 3.5.1      | 
| crayon       | 1.3.4      | 
| Curl         | 3.3        | 
| data.Table   | 1.12.2     | 
| datasets     | 3.5.1      | 
| DBI          | 1.0.0      | 
| dbplyr       | 1.4.1      | 
| digest       | 0.6.19     | 
| dplyr        | 0.7.6      | 
| e1071        | 1.7-2      | 
| Vyhodnocení     | 0.14       | 
| fansi        | 0.4.0      | 
| forcats      | 0.3.0      | 
| foreach      | 1.4.4      | 
| cizí      | 0.8-71     | 
| fs           | 1.3.1      | 
| gdata        | 2.18.0     | 
| Obecné typy     | 0.0.2      | 
| ggplot2      | 3.2.0      | 
| glmnet       | 2.0-18     | 
| spojovací         | 1.3.1      | 
| gower        | 0.2.1      | 
| gplots       | 3.0.1.1    | 
| Grafika     | 3.5.1      | 
| grDevices    | 3.5.1      | 
| Mřížka         | 3.5.1      | 
| gtable       | 0.3.0      | 
| gtools       | 3.8.1      | 
| haven        | 2.1.0      | 
| highr        | 0.8        | 
| hms          | 0.4.2      | 
| htmltools    | 0.3.6      | 
| httr         | 1.4.0      | 
| ipred        | 0.9-9      | 
| Iterátory    | 1.0.10     | 
| jsonlite     | 1.6        | 
| KernSmooth   | 2.23-15    | 
| knitr        | 1,23       | 
| Používání popisků     | 0.3        | 
| mřížky      | 0.20-38    | 
| lava         | 1.6.5      | 
| lazyeval     | 0.2.2      | 
| lubridate    | 1.7.4      | 
| magrittr     | 1.5        | 
| Markdown     | 1          | 
| MASS         | 7.3-51.4   | 
| matice       | 1.2-17     | 
| Metody      | 3.5.1      | 
| mgcv         | 1.8-28     | 
| MIME         | 0.7        | 
| ModelMetrics | 1.2.2      | 
| modelr       | 0.1.4      | 
| Munsell      | 0.5.0      | 
| nlme         | 3.1-140    | 
| nnet         | 7.3-12     | 
| numDeriv     | 2016.8-1.1 | 
| OpenSSL      | 1.4        | 
| parallel     | 3.5.1      | 
| Pilíře       | 1.4.1      | 
| pkgconfig    | 2.0.2      | 
| plogr        | 0.2.0      | 
| plyr         | 1.8.4      | 
| prettyunits  | 1.0.2      | 
| processx     | 3.3.1      | 
| prodlim      | 2018.04.18 | 
| progress     | 1.2.2      | 
| ps           | 1.3.0      | 
| purrr        | 0.3.2      | 
| quadprog     | 1.5-7      | 
| quantmod     | 0.4-15     | 
| R6           | 2.4.0      | 
| RandomForest | 4.6-14     | 
| RColorBrewer | 1.1-2      | 
| Rcpp         | 1.0.1      | 
| RcppRoll     | 0.3.0      | 
| readr        | 1.3.1      | 
| readxl       | 1.3.1      | 
| recepty      | 0.1.5      | 
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
| škálování       | 1.0.0      | 
| selectr      | 0.4-1      | 
| Prostorový      | 7.3-11     | 
| křivky      | 3.5.1      | 
| SQUAREM      | 2017.10-1  | 
| Statistiky        | 3.5.1      | 
| stats4       | 3.5.1      | 
| stringi      | 1.4.3      | 
| stringr      | 1.3.1      | 
| přežití     | 2.44-1.1   | 
| Sys          | 3.2        | 
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
| Utils        | 3.5.1      | 
| vctrs        | 0.1.0      | 
| viridisLite  | 0.3.0      | 
| krabicového grafu      | 0.3-2      | 
| withr        | 2.1.2      | 
| xfun         | 0.8        | 
| xml2         | 1.2.0      | 
| xts          | 0.11-2     | 
| yaml         | 2.2.0      | 
| zeallot      | 0.1.0      | 
| zoo          | 1.8-6      | 

## <a name="next-steps"></a>Další postup

Zobrazit [sada modulů, které jsou k dispozici](module-reference.md) do služby Azure Machine Learning. 