---
title: Vytvoření datových sad pro přístup k datům pomocí sad AzureML-DataSet
titleSuffix: Azure Machine Learning service
description: Naučte se vytvářet datové sady z různých zdrojů a registrovat datové sady s vaším pracovním prostorem.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 08/22/2019
ms.openlocfilehash: e5d5d36e82914f1d6d03299db0ed1427ac5a389a
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147586"
---
# <a name="create-and-access-datasets-preview-in-azure-machine-learning"></a>Vytvoření a přístup k datovým sadám (Preview) v Azure Machine Learning

V tomto článku se dozvíte, jak vytvořit Azure Machine Learning datové sady (Preview) a jak přistupovat k datům z místních nebo vzdálených experimentů.

S Azure Machine Learningmi datovými sadami můžete: 

* V úložišti, na který odkazují datové sady, **uchovejte jednu kopii dat** . 

* **Snadný přístup k datům během školení k modelu** bez obav o připojovací řetězce nebo cesty k datům.

* **Sdílení dat & spolupráci** s ostatními uživateli.

## <a name="prerequisites"></a>Požadavky

K vytváření a práci s datovými sadami potřebujete:

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze služby Azure Machine Learning](https://aka.ms/AMLFree) dnes

* [Pracovní prostor služby Azure Machine Learning](how-to-manage-workspace.md)

* [Nainstalovaná sada Azure Machine Learning SDK pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), která zahrnuje balíček AzureML-DataSet Sets.

> [!Note]
> Některé třídy DataSet (Preview) mají závislosti na balíčku [AzureML-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) . Pro uživatele se systémem Linux jsou tyto třídy podporovány pouze v následujících distribucích:  Red Hat Enterprise Linux, Ubuntu, Fedora a CentOS.

## <a name="dataset-types"></a>Typy datových sad

Datové sady jsou rozdělené do různých typů na základě toho, jak je uživatelé využívají při školení. Seznam typů datových sad:
* [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) představuje data v tabulkovém formátu tak, že analyzuje zadaný soubor nebo seznam souborů. To vám umožní vyhodnotit data do PANDAS dataframe. `TabularDataset` Objekt se dá vytvořit ze souboru CSV, TSV, souborů Parquet, výsledků dotazu SQL atd. Úplný seznam najdete v naší [dokumentaci](https://aka.ms/tabulardataset-api-reference).
* Soubor DataSet odkazuje na jeden nebo více souborů v úložišti dat nebo veřejných adresách URL. Získáte tak možnost stahovat soubory nebo je připojit k výpočetnímu prostředí. Soubory můžou být libovolného formátu, což umožňuje rozšířit řadu scénářů strojového učení, včetně obsáhlého učení.

Další informace o nadcházejících změnách rozhraní API najdete [tady](https://aka.ms/tabular-dataset).

## <a name="create-datasets"></a>Vytvoření datových sad 

Vytvořením datové sady vytvoříte odkaz na umístění zdroje dat společně s kopií jeho metadat. Data zůstanou ve svém stávajícím umístění, takže se neúčtují žádné dodatečné náklady na úložiště.

Aby byla data přístupná službou Azure Machine Learning, musí být datové sady vytvořené z cest v [Azure](how-to-access-data.md) datastores nebo veřejných webových adresách URL.

Vytvoření datových sad z [úložiště Azure DataStore](how-to-access-data.md):

* Ověřte, jestli `contributor` máte `owner` nebo máte přístup k zaregistrovanému úložišti dat Azure.

* Vytvořte datovou sadu odkazem na cestu v úložišti dat.

```Python
from azureml.core.workspace import Workspace
from azureml.core.datastore import Datastore
from azureml.core.dataset import Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()

# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)
```
### <a name="create-tabulardatasets"></a>Vytvořit TabularDatasets

`from_delimited_files()` Použijte`TabularDatasetFactory` metodu třídy pro čtení souborů ve formátu CSV nebo TSV a vytvořte neregistrované TabularDataset. Pokud čtete z více souborů, výsledky budou shrnuty do jednoho tabulkového znázornění.

```Python
# create a TabularDataset from multiple paths in datastore
datastore_paths = [
                  (datastore, 'weather/2018/11.csv'),
                  (datastore, 'weather/2018/12.csv'),
                  (datastore, 'weather/2019/*.csv')
                 ]
weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)

# create a TabularDataset from a delimited file behind a public web url
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

| |PassengerId|Zachované|Pclass|Name|Sex|Věk|SibSp|Parch|Vel|Vozov|Posádk|Nastoupilo
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|0|3|Braund, Mr. Owen Harris|male (muž)|22,0|1|0|A/5 21171|7,2500||ne
1|2|1|1|Cumings, paní Jan Bradley (Florencie Briggs th...|female (žena)|38,0|1|0|POČÍTAČ 17599|71,2833|C85|C
2|3|1|3|Heikkinen, chybíš. Laina|female (žena)|26,0|0|0|STON/O2. 3101282|7,9250||ne

### <a name="create-filedatasets"></a>Vytvoření datových sad
`from_files()` Použijte`FileDatasetFactory` metodu třídy pro načtení souborů v jakémkoli formátu a vytvořte neregistrovanou datovou sadu souborů.

```Python
# create a FileDataset from multiple paths in datastore
datastore_paths = [
                  (datastore, 'animals/dog/1.jpg'),
                  (datastore, 'animals/dog/2.jpg'),
                  (datastore, 'animals/dog/*.jpg')
                 ]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = [
            'https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
            'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz'
           ]          
mnist_ds = Dataset.File.from_files(path=web_paths)
```
## <a name="register-datasets"></a>Registrace datových sad

Pokud chcete dokončit proces vytváření, zaregistrujte své datové sady s pracovním prostorem:

`register()` Použijte metodu k registraci datových sad do vašeho pracovního prostoru, aby je bylo možné sdílet s ostatními a opakovaně používat v různých experimentech.

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data')
```

## <a name="version-datasets"></a>Datové sady verze

Novou datovou sadu můžete zaregistrovat pod stejným názvem vytvořením nové verze. Verze datové sady je způsob, jak můžete založit stav dat, abyste mohli použít určitou verzi datové sady pro experimentování nebo budoucí rozmnožování. Typické scénáře použití správy verzí: 
* Když jsou nová data dostupná pro rekurzi.
* Při použití různých přístupů k přípravě dat nebo analýze funkcí.

```Python
# create a TabularDataset from new Titanic training data
web_paths = [
            'https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
            'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv'
           ]          
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

# create a new version of titanic_ds
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'new titanic training data',
                                 create_new_version = True)
```


## <a name="access-your-data-during-training"></a>Přístup k datům během školení

Registrované datové sady jsou přístupné místně a vzdáleně na výpočetních clusterech, jako je Azure Machine Learning Compute. Chcete-li získat přístup k zadané datové sadě mezi experimenty, použijte následující kód k získání pracovního prostoru a registrované datové sady podle názvu. [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) Metoda`Dataset` ve třídě ve výchozím nastavení vrátí nejnovější verzi datové sady registrované v pracovním prostoru.

```Python
%%writefile $script_folder/train.py

from azureml.core import Dataset, Run

run = Run.get_context()
workspace = run.experiment.workspace

dataset_name = 'titanic_ds'

# Get a dataset by name
titanic_ds = Dataset.get_by_name(workspace=workspace, name=dataset_name)

# Load a TabularDataset into pandas DataFrame
df = titanic_ds.to_pandas_dataframe()
```

## <a name="next-steps"></a>Další kroky

* Pomocí automatizovaného strojového učení [se TabularDatasets naučíte](https://aka.ms/automl-dataset).
* Další příklady školení k datovým sadám najdete v ukázkových [poznámkových blocích](https://aka.ms/dataset-tutorial).
