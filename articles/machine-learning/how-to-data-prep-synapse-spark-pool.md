---
title: Data tahání s fondy Apache Spark (Preview)
titleSuffix: Azure Machine Learning
description: Naučte se, jak připojit a spustit fondy Apache Spark pro data tahání pomocí Azure synapse Analytics a Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 03/02/2021
ms.custom: how-to, devx-track-python, data4ml, synapse-azureml
ms.openlocfilehash: d7cc948d3631e69882eb252672e5a3eb5d5f9751
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104867436"
---
# <a name="attach-apache-spark-pools-powered-by-azure-synapse-analytics-for-data-wrangling-preview"></a>Připojení fondů Apache Spark (využívajících Azure synapse Analytics) pro tahání dat (Preview)

V tomto článku se dozvíte, jak připojit a spustit fond Apache Spark s využitím [Azure synapse Analytics](/synapse-analytics/overview-what-is.md) pro data tahání ve velkém měřítku. 

Tento článek obsahuje pokyny pro interaktivní provádění úloh tahání dat v rámci vyhrazené Synapseové relace v notebooku Jupyter. Pokud dáváte přednost použití kanálů Azure Machine Learning, přečtěte si téma [Jak používat Apache Spark (využívá Azure synapse Analytics) v kanálu Machine Learning (Preview)](how-to-use-synapsesparkstep.md).

>[!IMPORTANT]
> Integrace analýzy Azure Machine Learning a Azure synapse je ve verzi Preview. Možnosti uvedené v tomto článku používají `azureml-synapse` balíček, který obsahuje [experimentální](/python/api/overview/azure/ml/#stable-vs-experimental) funkce verze Preview, které se můžou kdykoli změnit.

## <a name="azure-machine-learning-and-azure-synapse-analytics-integration-preview"></a>Integrace s Azure Machine Learning a Azure synapse Analytics (Preview)

Integrace služby Azure synapse Analytics s Azure Machine Learning (Preview) umožňuje připojit fond Apache Spark s Azure synapse pro interaktivní zkoumání a přípravu dat. Díky této integraci můžete mít vyhrazenou výpočetní kapacitu pro data tahání ve velkém měřítku, a to vše v rámci stejného poznámkového bloku Pythonu, který používáte pro školení vašich modelů strojového učení.

## <a name="prerequisites"></a>Předpoklady

* [Vytvořte pracovní prostor Azure Machine Learning](how-to-manage-workspace.md?tabs=python).

* [Vytvořte pracovní prostor Azure synapse Analytics v Azure Portal](../synapse-analytics/quickstart-create-workspace.md).

* [Vytvoření fondu Apache Spark pomocí Azure Portal, webových nástrojů nebo synapse studia](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)

* [Nakonfigurujte vývojové prostředí](how-to-configure-environment.md) pro instalaci Azure Machine Learning sady SDK nebo použijte [výpočetní instanci Azure Machine Learning](concept-compute-instance.md#create) s již nainstalovanou sadou SDK. 

* Nainstalujte `azureml-synapse` balíček (Preview) pomocí následujícího kódu:

  ```python
  pip install azureml-synapse
  ```

* [Propojte Azure Machine Learning pracovní prostor a pracovní prostor Azure synapse Analytics](how-to-link-synapse-ml-workspaces.md).

## <a name="get-an-existing-linked-service"></a>Získat existující propojenou službu
Než budete moct připojit vyhrazené výpočetní prostředí pro data tahání, musíte mít pracovní prostor ML propojený s pracovním prostorem Azure synapse Analytics, který se označuje jako propojená služba. 

Pokud chcete načíst a použít existující propojenou službu, musíte mít oprávnění **uživatele nebo přispěvatele** k pracovnímu prostoru Azure synapse Analytics.

Zobrazení všech propojených služeb přidružených k pracovnímu prostoru Machine Learning. 

```python
LinkedService.list(ws)
```

Tento příklad načte existující propojenou službu `synapselink1` z pracovního prostoru `ws` s [`get()`](/python/api/azureml-core/azureml.core.linkedservice#get-workspace--name-) metodou.
```python
linked_service = LinkedService.get(ws, 'synapselink1')
```
 
## <a name="attach-synapse-spark-pool-as-a-compute"></a>Připojit fond synapse Spark jako výpočetní výkon

Jakmile nanačtete propojenou službu, připojte fond Apache Spark synapse jako vyhrazený výpočetní prostředek pro úlohy tahání dat. 

Fondy Apache Spark můžete připojit prostřednictvím,
* Azure Machine Learning Studio
* [Šablony Azure Resource Manageru (ARM)](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json)
* Sada Python SDK 

### <a name="attach-a-pool-via-the-studio"></a>Připojení fondu přes Studio
Postupujte takto: 

1. Přihlaste se k [Azure Machine Learning Studiu](https://ml.azure.com/).
1. V levém podokně vyberte **propojené služby** v části **Správa** .
1. Vyberte pracovní prostor synapse.
1. V levém horním rohu vyberte **připojené fondy Spark** . 
1. Vyberte **připojit**. 
1. Ze seznamu vyberte fond Apache Spark a zadejte jeho název.  
    1. Tento seznam identifikuje dostupné fondy synapse Spark, které je možné připojit k výpočtům. 
    1. Pokud chcete vytvořit nový fond synapse Spark, přečtěte si téma [Vytvoření fondu Apache Spark pomocí synapse studia](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md) .
1. Vyberte **připojit vybrané**. 

### <a name="attach-a-pool-with-the-python-sdk"></a>Připojení fondu k sadě Python SDK

Můžete také použít **sadu Python SDK** pro připojení Apache Sparkho fondu. 

Kód následného kódu, 
1. Nakonfiguruje SynapseCompute pomocí,

   1. LinkedService, `linked_service` který jste vytvořili nebo získali v předchozím kroku. 
   1. Typ cíle výpočtů, který chcete připojit, `SynapseSpark`
   1. Název fondu Apache Spark. Ta se musí shodovat s existujícím fondem Apache Spark, který je ve vašem pracovním prostoru Azure synapse Analytics.
   
1. Vytvoří ComputeTarget strojového učení tím, že ho projde. 
   1. Pracovní prostor Machine Learning, který chcete použít, `ws`
   1. Název, který chcete odkazovat na výpočetní prostředky v pracovním prostoru Azure Machine Learning. 
   1. Attach_configuration, kterou jste zadali při konfiguraci výpočetní služby synapse.
       1. Volání ComputeTarget. Attach () je asynchronní, takže ukázka blokuje až do dokončení volání.

```python
from azureml.core.compute import SynapseCompute, ComputeTarget

attach_config = SynapseCompute.attach_configuration(linked_service, #Linked synapse workspace alias
                                                    type='SynapseSpark', #Type of assets to attach
                                                    pool_name="<Synapse Spark pool name>") #Name of Synapse spark pool 

synapse_compute = ComputeTarget.attach(workspace= ws,                
                                       name="<Synapse Spark pool alias in Azure ML>", 
                                       attach_configuration=attach_config
                                      )

synapse_compute.wait_for_completion()
```

Ověřte, zda je fond Apache Spark připojen.

```python
ws.compute_targets['Synapse Spark pool alias']
```

## <a name="launch-synapse-spark-pool-for-data-preparation-tasks"></a>Spuštění synapse Spark fondu pro úlohy přípravy dat

Můžete určit [Azure Machine Learning prostředí](concept-environments.md) , které chcete použít během relace Apache Spark. Projeví se pouze závislosti conda zadané v prostředí. Image Docker není podporovaná.

>[!WARNING]
>  V fondech Apache Spark nejsou podporované závislosti Pythonu zadané v závislosti na conda prostředí. V současné době jsou podporovány pouze pevné verze Pythonu. Podívejte se do své verze Pythonu zahrnutím  `sys.version_info` do skriptu.

Následující kód vytvoří prostředí, `myenv` které nainstaluje `azureml-core` verze 1.20.0 a `numpy` verze 1.17.0 před zahájením relace. Pak můžete toto prostředí zahrnout do příkazu Apache Spark relace `start` .

```python

from azureml.core import Workspace, Environment

# creates environment with numpy and azureml-core dependencies
ws = Workspace.from_config()
env = Environment(name="myenv")
env.python.conda_dependencies.add_pip_package("azureml-core==1.20.0")
env.python.conda_dependencies.add_conda_package("numpy==1.17.0")
env.register(workspace=ws)
```

Pokud chcete zahájit přípravu dat pomocí fondu Apache Spark, zadejte název Apache Spark fondu a zadejte ID předplatného, skupinu prostředků pracovního prostoru Machine Learning, název pracovního prostoru Machine Learning a prostředí, které chcete použít během relace Apache Spark. 

> [!IMPORTANT]
> Pokud chcete pokračovat v používání Apache Sparkho fondu, musíte určit, který výpočetní prostředek se má použít v rámci svých úloh datových tahání `%synapse` , s jedním řádkem kódu a `%%synapse` více řádky. 

```python
%synapse start -c SynapseSparkPoolAlias -s AzureMLworkspaceSubscriptionID -r AzureMLworkspaceResourceGroupName -w AzureMLworkspaceName -e myenv
```

Po spuštění relace můžete ověřit metadata relace.

```python
%synapse meta
```

## <a name="load-data-from-storage"></a>Načtení dat z úložiště

Jakmile se spustí relace Apache Spark, přečtěte si data, která chcete připravit. Načítání dat je podporované pro Azure Blob Storage a Azure Data Lake Storage generace 1 a 2.

Existují dva způsoby, jak načíst data z těchto služeb úložiště: 

* Přímo načtěte data z úložiště pomocí své cesty systému Hadoop Distributed Files (HDFS).

* Načte data z existující [datové sady Azure Machine Learning](how-to-create-register-datasets.md).

Pro přístup k těmto službám úložiště potřebujete oprávnění ke **čtení dat objektů BLOB úložiště** . Pokud máte v plánu zapisovat data zpátky do těchto služeb úložiště, potřebujete oprávnění **přispěvatele dat objektů BLOB úložiště** . [Přečtěte si další informace o oprávněních a rolích úložiště](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues).

### <a name="load-data-with-hadoop-distributed-files-system-hdfs-path"></a>Načtení dat pomocí cesty systému Hadoop Distributed Files (HDFS)

Pokud chcete načíst a načíst data z úložiště s odpovídající cestou HDFS, musíte mít k dispozici přihlašovací údaje pro ověření přístupu k datům. Tyto přihlašovací údaje se liší v závislosti na typu úložiště.  

Následující kód ukazuje, jak číst data z **úložiště objektů BLOB v Azure** do datového rámce Sparku buď pomocí tokenu sdíleného přístupového podpisu (SAS), nebo přístupového klíče. 

```python
%%synapse

# setup access key or SAS token
sc._jsc.hadoopConfiguration().set("fs.azure.account.key.<storage account name>.blob.core.windows.net", "<access key>")
sc._jsc.hadoopConfiguration().set("fs.azure.sas.<container name>.<storage account name>.blob.core.windows.net", "<sas token>")

# read from blob 
df = spark.read.option("header", "true").csv("wasbs://demo@dprepdata.blob.core.windows.net/Titanic.csv")
```

Následující kód ukazuje, jak číst data v z **Azure Data Lake Storage generace 1 (adls Gen 1)** s přihlašovacími údaji instančního objektu. 

```python

# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.access.token.provider.type","ClientCredential")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.client.id", "<client id>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.credential", "<client secret>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.refresh.url",
https://login.microsoftonline.com/<tenant id>/oauth2/token)

df = spark.read.csv("adl://<storage account name>.azuredatalakestore.net/<path>")

```

Následující kód ukazuje, jak číst data z **Azure Data Lake Storage generace 2 (adls Gen 2)** s přihlašovacími údaji instančního objektu. 

```python
# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.azure.account.auth.type.<storage account name>.dfs.core.windows.net","OAuth")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth.provider.type.<storage account name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.id.<storage account name>.dfs.core.windows.net", "<client id>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.secret.<storage account name>.dfs.core.windows.net", "<client secret>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.endpoint.<storage account name>.dfs.core.windows.net",
https://login.microsoftonline.com/<tenant id>/oauth2/token)


df = spark.read.csv("abfss://<container name>@<storage account>.dfs.core.windows.net/<path>")

```

### <a name="read-in-data-from-registered-datasets"></a>Čtení dat z registrovaných datových sad

Můžete také získat existující registrovanou datovou sadu v pracovním prostoru a provést přípravu dat jejich převedením na datový rámec Spark.  

Následující příklad získá registrovanou TabularDataset, `blob_dset` , který odkazuje na soubory v úložišti objektů BLOB a převede je na datový rámec Spark. Když převedete datové sady na datový rámec Spark, můžete využívat knihovny pro `pyspark` zkoumání a přípravu dat.  

``` python

%%synapse
from azureml.core import Workspace, Dataset

dset = Dataset.get_by_name(ws, "blob_dset")
spark_df = dset.to_spark_dataframe()
```

## <a name="perform-data-wrangling-tasks"></a>Provádění úloh tahání dat

Po načtení a prozkoumání dat můžete provádět úlohy tahání dat.

Následující kód se rozšíří na příklad HDFS v předchozí části a filtruje data v datovém Sparku Spark, na `df` základě sloupce a skupin, které  mají seznam podle **věku** .

```python
%%synapse
from pyspark.sql.functions import col, desc

df.filter(col('Survived') == 1).groupBy('Age').count().orderBy(desc('count')).show(10)

df.show()

```

## <a name="save-data-to-storage-and-stop-spark-session"></a>Uložení dat do úložiště a zastavení relace Sparku

Po dokončení průzkumu a přípravy dat uložte připravená data pro pozdější použití v účtu úložiště v Azure.

V následujícím příkladu se připravené data zapisují zpátky do služby Azure Blob Storage a přepíše původní `Titanic.csv` soubor v `training_data` adresáři. Pokud chcete zapisovat zpátky do úložiště, potřebujete oprávnění **přispěvatele dat objektů BLOB úložiště** . [Přečtěte si další informace o oprávněních a rolích úložiště](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues).

```python
%% synapse

df.write.format("csv").mode("overwrite").save("wasbs://demo@dprepdata.blob.core.windows.net/training_data/Titanic.csv")
```

Až dokončíte přípravu dat a uložíte připravená data do úložiště, pomocí následujícího příkazu zastavte používání fondu Apache Spark.

```python
%synapse stop
```

## <a name="create-dataset-to-represent-prepared-data"></a>Vytvořit datovou sadu reprezentující připravená data

Až budete připraveni využít připravená data pro školení modelů, připojte se k úložišti pomocí [Azure Machine Learning úložiště dat](how-to-access-data.md)a určete, které soubory chcete použít s [datovou sadou Azure Machine Learning](how-to-create-register-datasets.md).

Následující příklad kódu:

* Předpokládá se, že už jste vytvořili úložiště dat, které se připojuje ke službě úložiště, kam jste uložili připravená data.  
* Načte existující úložiště dat `mydatastore` z pracovního prostoru `ws` pomocí metody Get ().
* Vytvoří [datovou sadu](how-to-create-register-datasets.md#filedataset), `train_ds` která odkazuje na připravené datové soubory, které se nacházejí v `training_data` adresáři v `mydatastore` .  
* Vytvoří proměnnou `input1` , která může být použita později k zpřístupnění datových souborů `train_ds` datové sady pro cíl výpočtů.

```python
from azureml.core import Datastore, Dataset

datastore = Datastore.get(ws, datastore_name='mydatastore')

datastore_paths = [(datastore, '/training_data/')]
train_ds = Dataset.File.from_files(path=datastore_paths, validate=True)
input1 = train_ds.as_mount()

```

## <a name="example-notebook"></a>Příklad poznámkového bloku

Podrobný příklad kódu, jak provést školení pro přípravu a modelování dat z jednoho poznámkového bloku s Azure synapse Analytics a Azure Machine Learning, najdete v tomto [koncovém poznámkovém bloku](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-synapse/spark_job_on_synapse_spark_pool.ipynb) .

## <a name="next-steps"></a>Další kroky

* [Výuka modelu](how-to-set-up-training-targets.md).
* [Výuka s Azure Machine Learning datovou sadou](how-to-train-with-datasets.md)
