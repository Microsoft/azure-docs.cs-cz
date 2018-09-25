---
title: Použití funkce shromažďování dat modelu v aplikaci Azure Machine Learning Workbench | Dokumentace Microsoftu
description: Tento článek pojednává o tom, jak používat funkce shromažďování dat modelu v aplikaci Azure Machine Learning Workbench
services: machine-learning
author: aashishb
ms.author: aashishb
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/12/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 435ef29192682f1bf19a9a53923dd043676256ab
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46954938"
---
# <a name="collect-model-data-by-using-data-collection"></a>Shromažďování dat modelu díky použití shromažďování dat

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]

Funkce shromažďování dat modelu ve službě Azure Machine Learning slouží k archivaci vstupy modelu a předpovědi z webové služby.

## <a name="install-the-data-collection-package"></a>Nainstalujte balíček shromažďování dat
Knihovna shromažďování dat můžete nainstalovat nativně na Linuxu a Windows.

### <a name="windows"></a>Windows
Na Windows nainstalujte modul kolektoru dat pomocí následujícího příkazu:

    pip install azureml.datacollector

### <a name="linux"></a>Linux
V systému Linux je třeba nejprve nainstalujte knihovny libxml ++. Spusťte následující příkaz, který musí být vydaný v rámci sudo:

    sudo apt-get install libxml++2.6-2v5

Potom spusťte následující příkaz:

    pip install azureml.datacollector

## <a name="set-environment-variables"></a>Nastavení proměnných prostředí

Shromažďování dat modelu závisí na dvou proměnných prostředí. AML_MODEL_DC_STORAGE_ENABLED musí být nastaveno na **true** (malými písmeny) a AML_MODEL_DC_STORAGE musí být nastavena na připojovací řetězec pro účet služby Azure Storage ve které chcete uložit data.

Tyto proměnné prostředí jsou již nastavení za vás, když webová služba je spuštěná na clusteru v Azure. Při místním spuštění, budete muset nastavit sami. Pokud používáte Docker, použijte parametr -e dockeru, spusťte příkaz k předání proměnných prostředí.

## <a name="collect-data"></a>Shromažďování dat

Použití shromažďování dat modelu, proveďte následující změny do souboru bodování:

1. Na začátek souboru přidejte následující kód:
   
    ```python
    from azureml.datacollector import ModelDataCollector
    ```

1. Přidejte následující řádky kódu, který `init()` funkce:
    
    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
    prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")
    ```

1. Přidejte následující řádky kódu, který `run(input_df)` funkce:
    
    ```python
    global inputs_dc, prediction_dc
    inputs_dc.collect(input_df)
    prediction_dc.collect(pred)
    ```

    Ujistěte se, že proměnné `input_df` a `pred` (předpověď hodnotu z `model.predict()`) jsou inicializovány před voláním `collect()` funkce na ně.

1. Použití `az ml service create realtime` příkazů `--collect-model-data true` přepínače k vytvoření webu v reálném čase služby. Tento krok zajistí, že při spuštění služby se shromažďují data modelu.

     ```batch
    c:\temp\myIris> az ml service create realtime -f iris_score.py --model-file model.pkl -s service_schema.json -n irisapp -r python --collect-model-data true 
    ```
    
1. Chcete-li otestovat shromažďování dat, spusťte `az ml service run realtime` příkaz:

    ```
    C:\Temp\myIris> az ml service run realtime -i irisapp -d "ADD YOUR INPUT DATA HERE!!" 
    ``` 
    
## <a name="view-the-collected-data"></a>Zobrazení shromážděných dat
Chcete-li zobrazit si shromážděná data v úložišti objektů blob:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyberte **všechny služby**.
1. Do vyhledávacího pole zadejte **účty úložiště** a stisknutím klávesy Enter.
1. Z **účty úložiště** okno hledání, vyberte **účtu úložiště** prostředků. Pokud chcete určit váš účet úložiště, postupujte následovně:

    a. Přejděte do aplikace Azure Machine Learning Workbench, vyberte projekt, pracujete a otevřete příkazový řádek **souboru** nabídky.
    
    b. Zadejte `az ml env show -v` a zkontrolujte, *storage_account* hodnotu. Toto je název vašeho účtu úložiště.

1. Vyberte **kontejnery** v prostředku nabídky okna a pak kontejner volá **modeldata**. Pokud chcete zobrazit data začala šířit do účtu úložiště, můžete potřebovat počkat až 10 minut od prvního požadavku na webovou službu. Data budou téci do objektů blob s následující cestou kontejneru:

    `/modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv`

Data můžete využívat z objektů BLOB systému Azure v různých způsobů, prostřednictvím software Microsoftu i open source nástroje. Zde je několik příkladů:
- Aplikace Azure Machine Learning Workbench: Otevřete soubor CSV v aplikaci Azure Machine Learning Workbench tak, že přidáte soubor CSV jako zdroj dat.
- Excel: Otevřete denní soubory CSV jako tabulku.
- [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/): vytvářejte grafy na základě dat získaných z dat CSV v objektech BLOB.
- [Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-overview): Vytvořte datový rámec s velkou částí dat CSV.
    ```python
    var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
    ```
- [Hive](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started): tabulky CSV načtení dat do Hive a spouštějte dotazy SQL přímo na objekt blob.

