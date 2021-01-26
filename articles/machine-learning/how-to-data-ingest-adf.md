---
title: Příjem dat s využitím Azure Data Factory
titleSuffix: Azure Machine Learning
description: Seznamte se s dostupnými možnostmi pro vytvoření kanálu pro příjem dat pomocí Azure Data Factory a výhod každého z nich.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 01/26/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: b842934ea6bb458a59a53ea7068aa9ece98aacf1
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98796148"
---
# <a name="data-ingestion-with-azure-data-factory"></a>Příjem dat s využitím Azure Data Factory

V tomto článku se seznámíte s dostupnými možnostmi vytvoření kanálu pro příjem dat pomocí [Azure Data Factory](../data-factory/introduction.md). Tento kanál Azure Data Factory slouží k ingestování dat pro použití s [Azure Machine Learning](overview-what-is-azure-ml.md). Data Factory umožňuje snadno extrahovat, transformovat a načítat data (ETL). Jakmile se data transformují a načtou do úložiště, dá se použít k výuce modelů strojového učení v Azure Machine Learning.

Jednoduchou transformaci dat lze zpracovat pomocí nativních aktivit Data Factory a nástrojů, jako je [tok dat](../data-factory/control-flow-execute-data-flow-activity.md). Pokud se to týče složitějších scénářů, můžete data zpracovat pomocí nějakého vlastního kódu. Například kód Python nebo R.

## <a name="compare-azure-data-factory-data-ingestion-pipelines"></a>Porovnání Azure Data Factorych kanálů pro přijímání dat 
Existuje několik běžných postupů použití Data Factory k transformaci dat během přijímání. Každá z těchto technik má výhody a nevýhody, které vám pomůžou zjistit, jestli je vhodná pro konkrétní případ použití:

| Technika | Výhody | Nevýhody |
| ----- | ----- | ----- |
| Data Factory + Azure Functions | <li> Nízká latence, COMPUTE bez serveru<li>Stavové funkce<li>Opakovaně použitelné funkce | Pouze dobrý pro krátkodobé zpracování |
| Data Factory a vlastní komponenta | <li>Rozsáhlé paralelní výpočty<li>Vhodné pro těžké algoritmy | <li>Vyžaduje zalamování kódu do spustitelného souboru.<li>Složitost zpracování závislostí a vstupně-výstupních operací |
| Poznámkový blok Data Factory + Azure Databricks |<li> Apache Spark<li>Nativní prostředí Pythonu |<li>Může být drahý<li>Vytváření clusterů zpočátku trvá čas a přidává latenci.

## <a name="azure-data-factory-with-azure-functions"></a>Azure Data Factory s využitím Azure Functions

Azure Functions umožňuje spouštění malých částí kódu (funkcí), aniž byste se museli starat o infrastrukturu aplikace. V této možnosti se data zpracovávají pomocí vlastního kódu Pythonu zabaleného do funkce Azure Functions. 

Funkce je vyvolána s [aktivitou služby Azure Data Factory Azure Functions](../data-factory/control-flow-azure-function-activity.md). Tento přístup je dobrou volbou pro zjednodušené transformace dat. 

![Diagram zobrazuje kanál Azure Data Factory, s funkcí Azure functions a Run ML a kanál Azure Machine Learning, s modelem výukového modelu a způsob, jakým pracují s nezpracovanými daty a připravenými daty.](media/how-to-data-ingest-adf/adf-function.png)



* Výhody:
    * Data se zpracovávají na výpočetní úrovni bez serveru s poměrně nízkou latencí.
    * Kanál Data Factory může vyvolat [trvalou funkci Azure](../azure-functions/durable/durable-functions-overview.md) , která by mohla implementovat složitý tok transformace dat. 
    * Podrobné informace o transformaci dat jsou vyvolány funkcí Azure Functions, které je možné znovu použít a vyvolat z jiných míst.
* Nevýhody
    * Před používáním ADF musí být vytvořený Azure Functions.
    * Azure Functions je vhodné jenom pro krátkodobé běžící zpracování dat.

## <a name="azure-data-factory-with-custom-component-activity"></a>Azure Data Factory s aktivitou vlastní součásti

V této možnosti se data zpracovávají pomocí vlastního kódu Pythonu zabaleného do spustitelného souboru. Je vyvolána s [ aktivitou Azure Data Factory vlastní součásti](../data-factory/transform-data-using-dotnet-custom-activity.md). Tento přístup je lépe vhodný pro velké objemy dat, než je předchozí technika.

![Diagram znázorňuje Azure Data Factory kanál s vlastní komponentou a spuštěním kanálu M L a kanálu Azure Machine Learning, s modelem výukového modelu a způsobem, jak komunikují s nezpracovanými daty a připravenými daty.](media/how-to-data-ingest-adf/adf-customcomponent.png)

* Výhody:
    * Data se zpracovávají ve fondu [Azure Batch](../batch/batch-technical-overview.md) , který poskytuje rozsáhlé paralelní a vysoce výkonné výpočetní prostředí.
    * Dá se použít ke spouštění těžkých algoritmů a zpracování velkých objemů dat.
* Nevýhody:
    * Před použitím s Data Factory se musí vytvořit fond Azure Batch.
    * Přes strojírenství související s zabalením kódu Pythonu do spustitelného souboru. Složitost zpracování závislostí a vstupně-výstupních parametrů

## <a name="azure-data-factory-with-azure-databricks-python-notebook"></a>Azure Data Factory pomocí poznámkového bloku Python Azure Databricks

[Azure Databricks](https://azure.microsoft.com/services/databricks/) je analytická platforma založená na Apache Spark v cloudu Microsoftu.

V této technice se transformace dat provádí pomocí [poznámkového bloku Pythonu](../data-factory/transform-data-using-databricks-notebook.md), který běží v clusteru Azure Databricks. To je pravděpodobně nejběžnější přístup, který využívá plný výkon služby Azure Databricks. Je určený pro distribuované zpracování dat ve velkém měřítku.

![Diagram znázorňuje Azure Data Factory kanál, pomocí Azure Databricks Pythonu a spuštění kanálu M L a kanálu Azure Machine Learning, s modelem výukového modelu a způsobem interakce s nezpracovanými daty a připravenými daty.](media/how-to-data-ingest-adf/adf-databricks.png)

* Výhody:
    * Data se transformují na nejvýkonnější službě Azure pro zpracování dat, která je zálohovaná pomocí Apache Sparkho prostředí.
    * Nativní podpora Pythonu spolu s rozhraními a knihovnami pro datové vědy, včetně TensorFlow, PyTorch a scikit – učení
    * Nemusíte zabalit kód Pythonu do functions nebo spustitelných modulů. Kód funguje tak, jak je.
* Nevýhody:
    * Před použitím s Data Factory se musí vytvořit infrastruktura Azure Databricks.
    * Může být nákladné v závislosti na konfiguraci Azure Databricks
    * Spuštění výpočetních clusterů z "studeného" režimu trvá určitou dobu, která přináší vysokou latenci řešení. 
    

## <a name="consume-data-in-azure-machine-learning"></a>Využití dat v Azure Machine Learning 

Kanál Data Factory uloží připravená data do cloudového úložiště (například Azure Blob nebo Azure datalake). <br>
Využijte vaše přípravná data v Azure Machine Learning. 

* Vyvolali Azure Machine Learning kanál z kanálu Data Factory.<br>**OR**
* Vytvoření [úložiště dat Azure Machine Learning](how-to-access-data.md#create-and-register-datastores) a [Azure Machine Learning datovou sadu](how-to-create-register-datasets.md) pro pozdější použití.

### <a name="invoke-azure-machine-learning-pipeline-from-data-factory"></a>Vyvolat Azure Machine Learning kanál z Data Factory

Tato metoda se doporučuje pro [pracovní postupy Machine Learningch operací (MLOps)](concept-model-management-and-deployment.md#what-is-mlops). Pokud nechcete nastavit kanál Azure Machine Learning, přečtěte si téma [čtení dat přímo z úložiště](#read-data-directly-from-storage).

Pokaždé, když Data Factory spuštění kanálu, 

1. Data se ukládají do jiného umístění v úložišti. 
1. Chcete-li předat umístění do Azure Machine Learning, kanál Data Factory volá [Azure Machine Learning kanál](concept-ml-pipelines.md). Při volání kanálu ML se umístění dat a ID spuštění odesílají jako parametry. 
1. Kanál ML pak může vytvořit úložiště dat Azure Machine Learning a datovou sadu s umístěním dat. Další informace najdete v [Data Factory spuštění Azure Machine Learningch kanálů](../data-factory/transform-data-machine-learning-service.md).

![Diagram znázorňuje Azure Data Factory kanál a kanál Azure Machine Learning a jejich interakce s nezpracovanými daty a připravenými daty. Kanál Data Factory kanálu data do připravené databáze dat, která informační kanály ukládá do úložiště dat, které datové sady informačních kanálů v pracovním prostoru Machine Learning.](media/how-to-data-ingest-adf/aml-dataset.png)

> [!TIP]
> Datové sady [podporují správu verzí](./how-to-version-track-datasets.md), takže kanál ml může zaregistrovat novou verzi datové sady, která odkazuje na nejnovější data z kanálu ADF.

Jakmile jsou data přístupná prostřednictvím úložiště dat nebo datové sady, můžete je použít ke školení modelu ML. Proces školení může být součástí stejného kanálu ML, který je volán z ADF. Nebo se může jednat o samostatný proces, jako je třeba experimentování v Jupyter poznámkovém bloku.

Vzhledem k tomu, že datové sady podporují správu verzí, a každé spuštění z kanálu vytvoří novou verzi, je snadné pochopit, která verze dat byla použita pro výuku modelu.

### <a name="read-data-directly-from-storage"></a>Čtení dat přímo z úložiště

Pokud nechcete vytvořit kanál ML, můžete k datům přistupovat přímo z účtu úložiště, ve kterém jsou dopravovaná data uložena s Azure Machine Learning úložiště dat a datovou sadou. 

Následující kód Python ukazuje, jak vytvořit úložiště dat, které se připojuje k úložišti Azure datalake generace 2. [Přečtěte si další informace o úložišti dat a o tom, kde najít oprávnění instančního objektu](how-to-access-data.md#create-and-register-datastores).

```python
ws = Workspace.from_config()
adlsgen2_datastore_name = '<ADLS gen2 storage account alias>'  #set ADLS Gen2 storage account alias in AML

subscription_id=os.getenv("ADL_SUBSCRIPTION", "<ADLS account subscription ID>") # subscription id of ADLS account
resource_group=os.getenv("ADL_RESOURCE_GROUP", "<ADLS account resource group>") # resource group of ADLS account

account_name=os.getenv("ADLSGEN2_ACCOUNTNAME", "<ADLS account name>") # ADLS Gen2 account name
tenant_id=os.getenv("ADLSGEN2_TENANT", "<tenant id of service principal>") # tenant id of service principal
client_id=os.getenv("ADLSGEN2_CLIENTID", "<client id of service principal>") # client id of service principal
client_secret=os.getenv("ADLSGEN2_CLIENT_SECRET", "<secret of service principal>") # the secret of service principal

adlsgen2_datastore = Datastore.register_azure_data_lake_gen2(
    workspace=ws,
    datastore_name=adlsgen2_datastore_name,
    account_name=account_name, # ADLS Gen2 account name
    filesystem='<filesystem name>', # ADLS Gen2 filesystem
    tenant_id=tenant_id, # tenant id of service principal
    client_id=client_id, # client id of service principal
```

Dále vytvořte datovou sadu, která bude odkazovat na soubory, které chcete použít v úloze strojového učení. 

Následující kód vytvoří TabularDataset ze souboru CSV, `prepared-data.csv` . Přečtěte si další informace o [typech datových sad a přijatých formátech souborů](how-to-create-register-datasets.md#dataset-types). 

```python
from azureml.core import Workspace, Datastore, Dataset
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig

# retrieve data via AML datastore
datastore = Datastore.get(ws, adlsgen2_datastore)
datastore_path = [(datastore, '/data/prepared-data.csv')]
        
prepared_dataset = Dataset.Tabular.from_delimited_files(path=datastore_path)
```

Tady můžete použít `prepared_dataset` k odkazování na připravená data, například ve školicích skriptech. Naučte se naučit [modely s datovými sadami v Azure Machine Learning](./how-to-train-with-datasets.md).

## <a name="next-steps"></a>Další kroky

* [Spuštění poznámkového bloku datacihly v Azure Data Factory](../data-factory/transform-data-using-databricks-notebook.md)
* [Přístup k datům ve službě Azure Storage](./how-to-access-data.md#create-and-register-datastores)
* [Výukové modely s datovými sadami v Azure Machine Learning](./how-to-train-with-datasets.md).
* [DevOps pro kanál příjmu dat](./how-to-cicd-data-ingestion.md)