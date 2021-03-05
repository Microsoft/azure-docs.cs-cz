---
title: Použití Apache Spark v kanálu Machine Learning (Preview)
titleSuffix: Azure Machine Learning
description: Propojte svůj pracovní prostor Azure synapse Analytics s kanálem Azure Machine Learning a použijte Apache Spark pro manipulaci s daty.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 03/04/2021
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: f52686f991e3d14a8cde82c602b182874305f27d
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102184096"
---
# <a name="how-to-use-apache-spark-powered-by-azure-synapse-analytics-in-your-machine-learning-pipeline-preview"></a>Jak používat Apache Spark (s technologií Azure synapse Analytics) v kanálu Machine Learning (Preview)

V tomto článku se dozvíte, jak používat fondy Apache Spark využívající službu Azure synapse Analytics jako cíl výpočtů pro krok přípravy dat v kanálu Azure Machine Learning. Naučíte se, jak může jeden kanál využívat výpočetní prostředky, které jsou vhodné pro konkrétní krok, jako je třeba Příprava dat nebo školení. Uvidíte, jak se připravují data pro krok Spark a jak se předává k dalšímu kroku. 

## <a name="prerequisites"></a>Požadavky

* Vytvořte [pracovní prostor Azure Machine Learning](how-to-manage-workspace.md) pro uložení všech prostředků kanálu.

* [Nakonfigurujte vývojové prostředí](how-to-configure-environment.md) pro instalaci Azure Machine Learning sady SDK nebo použijte [výpočetní instanci Azure Machine Learning](concept-compute-instance.md) s již nainstalovanou sadou SDK.

* Vytvořte pracovní prostor Azure synapse Analytics a fond Apache Spark (Další informace najdete v tématu [rychlý Start: Vytvoření fondu Apache Spark bez serveru pomocí synapse studia](../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)). 

## <a name="link-your-azure-machine-learning-workspace-and-azure-synapse-analytics-workspace"></a>Propojení pracovního prostoru Azure Machine Learning a pracovního prostoru Azure synapse Analytics 

Fondy Apache Spark můžete vytvářet a spravovat v pracovním prostoru Azure synapse Analytics. Pokud chcete fond Apache Spark integrovat s pracovním prostorem Azure Machine Learning, musíte se připojit k pracovnímu prostoru Azure synapse Analytics. 

Fond Apache Spark můžete připojit prostřednictvím uživatelského rozhraní Azure Machine Learning studia pomocí stránky **propojených služeb** . Můžete to provést také prostřednictvím **výpočetní** stránky s možností **připojit COMPUTE** .

Fond Apache Spark můžete připojit také prostřednictvím sady SDK (jak je uvedeno níže) nebo pomocí šablony ARM (viz [Příklad této šablony ARM](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json)). 

Pomocí příkazového řádku můžete dodržet šablonu ARM, přidat propojenou službu a připojit Apache Spark fond s následujícím kódem:

```bash
az deployment group create --name --resource-group <rg_name> --template-file "azuredeploy.json" --parameters @"azuredeploy.parameters.json"
```

> [!Important]
> Pokud chcete úspěšně propojit pracovní prostor analýzy Azure synapse, musíte mít roli vlastníka v prostředku pracovního prostoru Azure synapse Analytics. Ověřte přístup v Azure Portal.
> Propojená služba získá identitu přiřazenou systémem (SAI), když ji vytvoříte. Tuto službu propojení musíte přiřadit SAI roli "synapse Apache Spark Administrator" z synapse studia, aby mohla odeslat úlohu Spark (viz [jak spravovat synapse přiřazení rolí RBAC v synapse studiu](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md)). Také musíte uživateli Azure Machine Learning pracovnímu prostoru udělit roli Přispěvatel z Azure Portal prostředků Management.

## <a name="create-or-retrieve-the-link-between-your-azure-synapse-analytics-workspace-and-your-azure-machine-learning-workspace"></a>Vytvoření nebo načtení propojení mezi pracovním prostorem Azure synapse Analytics a pracovním prostorem Azure Machine Learning

Propojené služby můžete v pracovním prostoru načíst pomocí kódu, jako je:

```python
from azureml.core import Workspace, LinkedService, SynapseWorkspaceLinkedServiceConfiguration

ws = Workspace.from_config()

for service in LinkedService.list(ws) : 
    print(f"Service: {service}")

# Retrieve a known linked service
linked_service = LinkedService.get(ws, 'synapselink1')
```

Nejprve `Workspace.from_config()` přístup k pracovnímu prostoru Azure Machine Learning pomocí konfigurace v `config.json` (viz [kurz: Začínáme s Azure Machine Learning ve vašem vývojovém prostředí](tutorial-1st-experiment-sdk-setup-local.md)). Kód pak vytiskne všechny propojené služby, které jsou k dispozici v pracovním prostoru. Nakonec `LinkedService.get()` načte propojenou službu s názvem `'synapselink1'` . 

## <a name="attach-your-apache-spark-pool-as-a-compute-target-for-azure-machine-learning"></a>Připojte svůj fond Apache Spark jako výpočetní cíl pro Azure Machine Learning

Pokud chcete použít fond Apache Spark k napájení kroku v kanálu Machine Learning, musíte ho připojit jako `ComputeTarget` Krok kanálu, jak je znázorněno v následujícím kódu.

```python
from azureml.core.compute import SynapseCompute, ComputeTarget

attach_config = SynapseCompute.attach_configuration(
        linked_service = linked_service,
        type="SynapseSpark",
        pool_name="spark01") # This name comes from your Synapse workspace

synapse_compute=ComputeTarget.attach(
        workspace=ws,
        name='link1-spark01',
        attach_configuration=attach_config)

synapse_compute.wait_for_completion()
```

Prvním krokem je konfigurace `SynapseCompute` . `linked_service`Argument je `LinkedService` objekt, který jste vytvořili nebo získali v předchozím kroku. `type`Argument musí být `SynapseSpark` . `pool_name`Argument v nástroji se `SynapseCompute.attach_configuration()` musí shodovat s existujícím fondem v pracovním prostoru Azure synapse Analytics. Další informace o vytvoření fondu Apache Spark v pracovním prostoru Azure synapse Analytics najdete v tématu [rychlý Start: Vytvoření fondu Apache Spark bez serveru pomocí synapse studia](../synapse-analytics/quickstart-create-apache-spark-pool-studio.md). Typ `attach_config` je `ComputeTargetAttachConfiguration` .

Jakmile je konfigurace vytvořená, vytvoříte strojové učení `ComputeTarget` předáním do `Workspace` , `ComputeTargetAttachConfiguration` a názvem, který byste chtěli odkazovat na výpočetní prostředky v pracovním prostoru Machine Learning. Volání `ComputeTarget.attach()` je asynchronní, takže ukázka blokuje až do dokončení volání.

## <a name="create-a-synapsesparkstep-that-uses-the-linked-apache-spark-pool"></a>Vytvoření `SynapseSparkStep` , které používá propojený fond Apache Spark

Ukázková úloha Spark poznámkového bloku [v rámci fondu Apache Spark](https://github.com/azure/machinelearningnotebooks) definuje jednoduchý kanál strojového učení. Jako první Poznámkový blok definuje krok přípravy dat, který je `synapse_compute` určen definovaným v předchozím kroku. Poznámkový blok pak definuje krok školení, který využívá výpočetní cíl, který je vhodnější pro školení. Ukázkový Poznámkový blok používá záchrannou databázi Titanic k předvedení vstupu a výstupu dat; ve skutečnosti data nečistí ani nevytváří prediktivní model. Vzhledem k tomu, že v této ukázce není žádné reálné školení, krok školení využívá levné výpočetní prostředky založené na procesoru.

Data se natoků do kanálu strojového učení pomocí `DatasetConsumptionConfig` objektů, které mohou obsahovat tabulková data nebo sady souborů. Data často pocházejí ze souborů v úložišti objektů BLOB v úložišti dat v pracovním prostoru. Následující kód ukazuje typický kód pro vytvoření vstupu do kanálu strojového učení:

```python
from azureml.core import Dataset

datastore = ws.get_default_datastore()
file_name = 'Titanic.csv'

titanic_tabular_dataset = Dataset.Tabular.from_delimited_files(path=[(datastore, file_name)])
step1_input1 = titanic_tabular_dataset.as_named_input("tabular_input")

# Example only: it wouldn't make sense to duplicate input data, especially one as tabular and the other as files
titanic_file_dataset = Dataset.File.from_files(path=[(datastore, file_name)])
step1_input2 = titanic_file_dataset.as_named_input("file_input").as_hdfs()
```

Výše uvedený kód předpokládá, že se soubor `Titanic.csv` nachází v úložišti objektů BLOB. Kód ukazuje, jak načíst soubor jako `TabularDataset` a jako `FileDataset` . Tento kód je určen pouze pro demonstrační účely, protože by byl matoucí pro duplicitní vstupy nebo za účelem interpretace jednoho zdroje dat jako prostředku, který je součástí tabulky, a stejně jako soubor.

> [!Important]
> Aby bylo možné použít `FileDataset` jako vstup, `azureml-core` musí být vaše verze aspoň `1.20.0` . Postup určení pomocí `Environment` třídy je popsána níže.

Po dokončení kroku se můžete rozhodnout uložit výstupní data pomocí kódu, který bude vypadat přibližně takto:

```python
from azureml.data import HDFSOutputDatasetConfig
step1_output = HDFSOutputDatasetConfig(destination=(datastore,"test")).register_on_complete(name="registered_dataset")
```

V takovém případě by se data ukládala v `datastore` souboru do souboru s názvem `test` a v pracovním prostoru Machine Learning byla k dispozici jako `Dataset` název `registered_dataset` .

Kromě dat může krok kanálu mít závislosti v jazyce Python vázané na krok. Jednotlivé `SynapseSparkStep` objekty můžou přesně určovat svou přesnou konfiguraci Apache Spark Azure synapse. Tento kód je zobrazen v následujícím kódu, který určuje, že `azureml-core` verze balíčku musí být alespoň `1.20.0` . (Jak už bylo zmíněno dříve, tento požadavek pro `azureml-core` je nutný k použití `FileDataset` jako vstupu.)

```python
from azureml.core.environment import Environment
from azureml.pipeline.steps import SynapseSparkStep

env = Environment(name="myenv")
env.python.conda_dependencies.add_pip_package("azureml-core>=1.20.0")

step_1 = SynapseSparkStep(name = 'synapse-spark',
                          file = 'dataprep.py',
                          source_directory="./code", 
                          inputs=[step1_input1, step1_input2],
                          outputs=[step1_output],
                          arguments = ["--tabular_input", step1_input1, 
                                       "--file_input", step1_input2,
                                       "--output_dir", step1_output],
                          compute_target = 'link1-spark01',
                          driver_memory = "7g",
                          driver_cores = 4,
                          executor_memory = "7g",
                          executor_cores = 2,
                          num_executors = 1,
                          environment = env)
```

Výše uvedený kód určuje jeden krok v kanálu služby Azure Machine Learning. Tento krok `environment` Určuje konkrétní `azureml-core` verzi a v případě potřeby může přidat další závislosti conda nebo PIP.

Vytvoří se soubor `SynapseSparkStep` zip a nahraje se z místního počítače do podadresáře `./code` . Tento adresář se znovu vytvoří na výpočetním serveru a krok spustí soubor `dataprep.py` z tohoto adresáře. `inputs`A `outputs` tohoto kroku jsou `step1_input1` `step1_input2` objekty, a `step1_output` dříve popsané. Nejjednodušší způsob, jak získat přístup k těmto hodnotám v rámci `dataprep.py` skriptu, je přidružit je k pojmenovanému `arguments` .

Další sada argumentů `SynapseSparkStep` ovládacího prvku konstruktoru Apache Spark. `compute_target`Je ta `'link1-spark01'` , kterou jsme dříve připojili jako cíl výpočtů. Ostatní parametry určují paměť a jádra, které bychom chtěli použít.

Ukázkový Poznámkový blok používá následující kód pro `dataprep.py` :

```python
import os
import sys
import azureml.core
from pyspark.sql import SparkSession
from azureml.core import Run, Dataset

print(azureml.core.VERSION)
print(os.environ)

import argparse
parser = argparse.ArgumentParser()
parser.add_argument("--tabular_input")
parser.add_argument("--file_input")
parser.add_argument("--output_dir")
args = parser.parse_args()

# use dataset sdk to read tabular dataset
run_context = Run.get_context()
dataset = Dataset.get_by_id(run_context.experiment.workspace,id=args.tabular_input)
sdf = dataset.to_spark_dataframe()
sdf.show()

# use hdfs path to read file dataset
spark= SparkSession.builder.getOrCreate()
sdf = spark.read.option("header", "true").csv(args.file_input)
sdf.show()

sdf.coalesce(1).write\
.option("header", "true")\
.mode("append")\
.csv(args.output_dir)
```

Tento skript pro přípravu dat neprovádí žádnou skutečnou transformaci dat, ale znázorňuje, jak data načíst, převést na Spark dataframe a jak provádět základní Apache Spark manipulace. Výstup můžete najít v Azure Machine Learning Studio otevřením podřízeného běhu, výběrem karty **výstupy + protokoly** a otevřením `logs/azureml/driver/stdout` souboru, jak je znázorněno na následujícím obrázku.

:::image type="content" source="media/how-to-use-synapsesparkstep/synapsesparkstep-stdout.png" alt-text="Snímek obrazovky studia, na kterém se zobrazuje karta stdout u podřízeného spuštění":::

## <a name="use-the-synapsesparkstep-in-a-pipeline"></a>Použití `SynapseSparkStep` v kanálu

Další kroky v kanálu můžou mít vlastní jedinečná prostředí a můžou běžet na různých výpočetních prostředcích, které jsou vhodné pro daný úkol. Vzorový Poznámkový blok spouští "krok školení" na malém clusteru s malým PROCESORem:

```python
from azureml.core.compute import AmlCompute

cpu_cluster_name = "cpucluster"

if cpu_cluster_name in ws.compute_targets:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
else:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', max_nodes=1)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
    print('Allocating new CPU compute cluster')

cpu_cluster.wait_for_completion(show_output=True)

step2_input = step1_output.as_input("step2_input").as_download()

step_2 = PythonScriptStep(script_name="train.py",
                          arguments=[step2_input],
                          inputs=[step2_input],
                          compute_target=cpu_cluster_name,
                          source_directory="./code",
                          allow_reuse=False)
```

Pokud je to nutné, kód uvedený výše vytvoří nový výpočetní prostředek. Pak `step1_output` je výsledek převeden na vstup pro krok školení. `as_download()`Možnost znamená, že data budou přesunuta do výpočetního prostředku, což vede k rychlejšímu přístupu. Pokud byla data velká, takže by se nevešla na místní výpočetní pevný disk, použili byste `as_mount()` možnost streamování dat prostřednictvím systému souborů zapékací. `compute_target`Tento druhý krok je `'cpucluster'` , nikoli `'link1-spark01'` prostředek, který jste použili v kroku Příprava dat. Tento krok používá jednoduchý program `train.py` místo toho, `dataprep.py` co jste použili v předchozím kroku. `train.py`V ukázkovém poznámkovém bloku vidíte podrobnosti.

Po definování všech svých kroků můžete vytvořit a spustit svůj kanál. 

```
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[step_1, step_2])
pipeline_run = pipeline.submit('synapse-pipeline', regenerate_outputs=True)
```

Výše uvedený kód vytvoří kanál skládající se z kroku přípravy dat na Apache Spark fondech využívajících službu Azure synapse Analytics ( `step_1` ) a krok školení ( `step_2` ). Azure vypočítá spuštění grafu prozkoumáním závislostí dat mezi jednotlivými kroky. V takovém případě je k dispozici pouze jednoduchá závislost, která `step2_input` nutně vyžaduje `step1_output` .

Volání, které se `pipeline.submit` vytvoří, v případě potřeby experiment, který se volá `synapse-pipeline` a asynchronně zahájí běh. Jednotlivé kroky v rámci kanálu jsou spouštěny jako podřízené spuštění tohoto hlavního běhu a lze je monitorovat a kontrolovat na stránce experimenty v nástroji Studio.

## <a name="next-steps"></a>Další kroky

* [Publikování a sledování kanálů strojového učení](how-to-deploy-pipelines.md) 
* [Monitorování Azure Machine Learningu](monitor-azure-machine-learning.md)
* [Použití automatizovaného ML v kanálu Azure Machine Learning v Pythonu](how-to-use-automlstep-in-pipelines.md)
