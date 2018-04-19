---
title: "Použít funkci modelu dat kolekce v nástroji Azure Machine Learning Workbench | Microsoft Docs"
description: "Tento článek pojednává o tom, jak používat funkci modelu dat kolekce v nástroji Azure Machine Learning Workbench"
services: machine-learning
author: aashishb
ms.author: aashishb
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/12/2017
ms.openlocfilehash: 2251f8d241f3ec47d46c04160caf997bcd025124
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2018
---
# <a name="collect-model-data-by-using-data-collection"></a>Shromažďování dat modelu pomocí shromažďování dat

Můžete použít funkci modelu dat kolekce v Azure Machine Learning k archivaci vstupy modelu a předpovědi z webové služby.

## <a name="install-the-data-collection-package"></a>Nainstalujte balíček shromažďování dat
Knihovna shromažďování dat můžete nainstalovat nativně Linux a Windows.

### <a name="windows"></a>Windows
V systému Windows nainstalujte modul kolekce dat pomocí následujícího příkazu:

    pip install azureml.datacollector

### <a name="linux"></a>Linux
V systému Linux je třeba nejprve nainstalujte knihovně libxml ++. Spusťte následující příkaz, který musí být vydaný v části sudo:

    sudo apt-get install libxml++2.6-2v5

Spusťte následující příkaz:

    pip install azureml.datacollector

## <a name="set-environment-variables"></a>Proměnné prostředí sady

Shromažďování dat modelu závisí na dvou proměnných prostředí. AML_MODEL_DC_STORAGE_ENABLED musí být nastavena na **true** (malá písmena.) a AML_MODEL_DC_STORAGE musí být nastaven připojovací řetězec pro účet úložiště Azure ve které chcete data uložit.

Tyto proměnné prostředí jsou již nastavení za vás, pokud webová služba běží na clusteru s podporou v Azure. Při místním spuštění, budete muset nastavit sami. Pokud používáte Docker, pomocí parametru -e docker, spusťte příkaz předat proměnné prostředí.

## <a name="collect-data"></a>Shromažďování dat

Pokud chcete použít model shromažďování dat, proveďte následující změny k vyhodnocování souboru:

1. V horní části souboru přidejte následující kód:
   
    ```python
    from azureml.datacollector import ModelDataCollector
    ```

2. Přidejte následující řádky kódu `init()` funkce:
    
    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
    prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")
    ```

3. Přidejte následující řádky kódu `run(input_df)` funkce:
    
    ```python
    global inputs_dc, prediction_dc
    inputs_dc.collect(input_df)
    prediction_dc.collect(pred)
    ```

    Ujistěte se, že proměnné `input_df` a `pred` (hodnota předpovědi z `model.predict()`) se inicializují před voláním `collect()` funkce na ně.

4. Použití `az ml service create realtime` s `--collect-model-data true` přepínače k vytvoření služby webu v reálném čase. Tento krok zajistí, že model data jsou shromažďována při spuštění služby.

     ```batch
    c:\temp\myIris> az ml service create realtime -f iris_score.py --model-file model.pkl -s service_schema.json -n irisapp -r python --collect-model-data true 
    ```
    
5. Chcete-li otestovat shromažďování dat, spusťte `az ml service run realtime` příkaz:

    ```
    C:\Temp\myIris> az ml service run realtime -i irisapp -d "ADD YOUR INPUT DATA HERE!!" 
    ``` 
    
## <a name="view-the-collected-data"></a>Zobrazení shromážděných dat
Chcete-li zobrazit shromážděná data v úložišti objektů blob:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny služby**.
3. Do vyhledávacího pole zadejte **účty úložiště** a vyberte klávesu Enter.
4. Z **účty úložiště** okno vyhledávání, vyberte **účet úložiště** prostředků. K určení účtu úložiště, použijte následující kroky:

    a. Přejděte na Azure Machine Learning Workbench, vyberte projekt na práci a otevřete příkazový řádek z **souboru** nabídky.
    
    b. Zadejte `az ml env show -v` a zkontrolujte *storage_account* hodnotu. Toto je název vašeho účtu úložiště.

5. Vyberte **kontejnery** na prostředku okno nabídky a kontejner s názvem **modeldata**. Pokud chcete zobrazit data spustit šíření k účtu úložiště, může musíte počkat až 10 minut po první žádosti webové služby. Data budou téci do objektů blob s následující cestou kontejneru:

    `/modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv`

Data mohou být využívány z Azure BLOB v mnoha různými způsoby, prostřednictvím software společnosti Microsoft a open source nástroje. Zde je několik příkladů:
- Azure Machine Learning Workbench: Otevřete soubor CSV v nástroji Azure Machine Learning Workbench tak, že přidáte jako zdroj dat souboru CSV.
- V aplikaci Excel: Otevřete denní soubory CSV jako tabulku.
- [Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-azure-and-power-bi/): vytvářet grafy s daty ze .csv data do objektů BLOB.
- [Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-overview): vytvořte snímek dat s velká část dat CSV.
    ```python
    var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
    ```
- [Hive](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started): načítání dat CSV do podregistru tabulky a provádět dotazy SQL na přímo pro objekt blob.

