---
title: Vytvoření Azure Machine Learning datových sad pro přístup k datům
titleSuffix: Azure Machine Learning
description: Naučte se vytvářet Azure Machine Learning datové sady pro přístup k datům pro spuštění experimentování ve službě Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 02/10/2020
ms.openlocfilehash: bb3a18af89b0baa532309ac76905aa5550af98e5
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78398207"
---
# <a name="create-azure-machine-learning-datasets"></a>Vytváření Azure Machine Learning datových sad

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak vytvořit Azure Machine Learning datové sady pro přístup k datům pro místní nebo vzdálené experimenty.

S Azure Machine Learningmi datovými sadami můžete:

* Uchovávejte v úložišti jednu kopii dat, na kterou odkazují datové sady.

* Bezproblémový přístup k datům během školení modelů, aniž byste se museli starat o připojovací řetězce nebo cesty k datům.

* Sdílejte data a spolupracujte s ostatními uživateli.

## <a name="prerequisites"></a>Předpoklady

K vytváření a práci s datovými sadami potřebujete:

* Předplatné Azure. Pokud ho ještě nemáte, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree).

* [Pracovní prostor Azure Machine Learning](how-to-manage-workspace.md).

* [Nainstalovaná sada Azure Machine Learning SDK pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), která zahrnuje balíček AzureML-DataSet Sets.

> [!NOTE]
> Některé třídy DataSet mají závislosti na balíčku [AzureML-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) . Pro uživatele systému Linux jsou tyto třídy podporovány pouze v následujících distribucích: Red Hat Enterprise Linux, Ubuntu, Fedora a CentOS.

## <a name="dataset-types"></a>Typy datových sad

Existují dva typy datových sad na základě toho, jak je uživatelé používají při školení:

* [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) představuje data v tabulkovém formátu tak, že analyzuje zadaný soubor nebo seznam souborů. To vám umožní vyhodnotit data do datového rámce PANDAS nebo Sparku. Objekt `TabularDataset` můžete vytvořit ze souborů. csv,. TSV,. Parquet,. JSON a z výsledků dotazu SQL. Úplný seznam naleznete v tématu [Třída TabularDatasetFactory](https://aka.ms/tabulardataset-api-reference).

* Třída [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) odkazuje na jeden nebo více souborů v úložišti dat nebo veřejných adresách URL. Tato metoda umožňuje stáhnout nebo připojit soubory do služby COMPUTE jako objekt DataSet. Soubory mohou být v libovolném formátu, což umožňuje širší škálu scénářů strojového učení, včetně obsáhlého učení.

Další informace o nadcházejících změnách rozhraní API najdete v tématu [Upozornění na změnu rozhraní API pro datovou sadu](https://aka.ms/tabular-dataset).

## <a name="create-datasets"></a>Vytvoření datových sad

Vytvořením datové sady vytvoříte odkaz na umístění zdroje dat společně s kopií jeho metadat. Vzhledem k tomu, že data zůstanou ve svém stávajícím umístění, nebudete mít žádné dodatečné náklady na úložiště. Sady dat `TabularDataset` i `FileDataset` můžete vytvořit pomocí sady Python SDK nebo https://ml.azure.com.

Aby data mohla být přístupná pomocí Azure Machine Learning, musí být datové sady vytvořené z cest v [Azure datastores](how-to-access-data.md) nebo adres URL veřejných webů.

### <a name="use-the-sdk"></a>Použití sady SDK

Vytvoření datových sad z [úložiště Azure DataStore](how-to-access-data.md) pomocí sady Python SDK:

1. Ověřte, že máte `contributor` nebo `owner` přístup k zaregistrovanému úložišti dat Azure.

2. Vytvořte datovou sadu pomocí odkazů na cesty v úložišti dat.
> [!Note]
> Datovou sadu můžete vytvořit z více cest ve více úložišti dat. Neexistuje žádné omezení počtu souborů nebo velikosti dat, ze kterých lze vytvořit datovou sadu. Pro každou cestu k datům se ale do služby úložiště pošle pár požadavků, aby se zkontrolovalo, jestli odkazuje na soubor nebo složku. Tato režie může vést ke snížení výkonu nebo selhání. Datová sada odkazující na jednu složku s 1000 soubory uvnitř se považuje za odkazování na jednu cestu k datům. Pro zajištění optimálního výkonu doporučujeme vytvořit datovou sadu odkazující na méně než 100 cest v úložišti dat.


#### <a name="create-a-tabulardataset"></a>Vytvoření TabularDataset

Pomocí metody [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none-) třídy `TabularDatasetFactory` můžete číst soubory ve formátu CSV nebo TSV a vytvořit neregistrované TabularDataset. Pokud načítáte z více souborů, výsledky budou shrnuty do jednoho tabulkového znázornění. 

```Python
from azureml.core import Workspace, Datastore, Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
    
# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)

# create a TabularDataset from 3 paths in datastore
datastore_paths = [(datastore, 'ather/2018/11.csv'),
                   (datastore, 'weather/2018/12.csv'),
                   (datastore, 'weather/2019/*.csv')]
weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```

Ve výchozím nastavení se při vytváření TabularDataset automaticky odvozuje datové typy sloupců. Pokud odvozené typy neodpovídají vašim očekáváním, můžete určit typy sloupců pomocí následujícího kódu. Parametr `infer_column_type` lze použít pouze pro datové sady vytvořené z oddělených souborů. Můžete si taky [přečíst další informace o podporovaných datových typech](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.datatype?view=azure-ml-py).

> [!IMPORTANT] 
> Pokud je vaše úložiště za virtuální sítí nebo bránou firewall, podporuje se jenom vytváření datových sad prostřednictvím sady SDK. Chcete-li vytvořit datovou sadu, nezapomeňte zahrnout parametry `validate=False` a `infer_column_types=False` v metodě `from_delimited_files()`. Tím se obchází prvotní kontrola ověření a je zajištěno, že z těchto zabezpečených souborů můžete vytvořit datovou sadu. 

```Python
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

| |PassengerId|Zachované|Pclass|Název|Sex|Věk|SibSp|Parch|Vel|Vozov|Posádk|Nastoupilo
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|False|3|Braund, Mr. Owen Harris|male (muž)|22,0|1|0|A/5 21171|7,2500||S
1|2|True|1|Cumings, paní Jan Bradley (Florencie Briggs th...|female (žena)|38,0|1|0|POČÍTAČ 17599|71,2833|C85|C
2|3|True|3|Heikkinen, chybíš. Laina|female (žena)|26,0|0|0|STON/O2. 3101282|7,9250||S


Chcete-li vytvořit datovou sadu z datového rámce v PANDAS paměti, zapište data do místního souboru, třeba do sdíleného svazku clusteru, a vytvořte datovou sadu z tohoto souboru. Následující kód demonstruje tento pracovní postup.

```python
local_path = 'data/prepared.csv'
dataframe.to_csv(local_path)
upload the local file to a datastore on the cloud
# azureml-core of version 1.0.72 or higher is required
# azureml-dataprep[pandas] of version 1.1.34 or higher is required
from azureml.core import Workspace, Dataset

subscription_id = 'xxxxxxxxxxxxxxxxxxxxx'
resource_group = 'xxxxxx'
workspace_name = 'xxxxxxxxxxxxxxxx'

workspace = Workspace(subscription_id, resource_group, workspace_name)

# get the datastore to upload prepared data
datastore = workspace.get_default_datastore()

# upload the local file from src_dir to the target_path in datastore
datastore.upload(src_dir='data', target_path='data')
# create a dataset referencing the cloud location
dataset = Dataset.Tabular.from_delimited_files(datastore.path('data/prepared.csv'))
```

Pro čtení z Azure SQL Database použijte metodu [`from_sql_query()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-sql-query-query--validate-true--set-column-types-none-) třídy `TabularDatasetFactory`.

```Python

from azureml.core import Dataset, Datastore

# create tabular dataset from a SQL database in datastore
sql_datastore = Datastore.get(workspace, 'mssql')
sql_ds = Dataset.Tabular.from_sql_query((sql_datastore, 'SELECT * FROM my_table'))
```

V TabularDatasets můžete zadat časové razítko ze sloupce v datech nebo z místa, kde jsou uložená data vzorů cesty, a povolit tak vlastnost časové řady. Tato specifikace umožňuje snadno a efektivně filtrovat podle času.

Pomocí metody [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-) třídy`TabularDataset` Určete sloupec časového razítka a povolte filtrování podle času. Další informace najdete v tématu [ukázka rozhraní API pro tabulkovou časovou řadu s daty o počasí v NOAA](https://aka.ms/azureml-tsd-notebook).

```Python
# create a TabularDataset with time series trait
datastore_paths = [(datastore, 'weather/*/*/*/data.parquet')]

# get a coarse timestamp column from the path pattern
dataset = Dataset.Tabular.from_parquet_files(path=datastore_path, partition_format='weather/{coarse_time:yyy/MM/dd}/data.parquet')

# set coarse timestamp to the virtual column created, and fine grain timestamp from a column in the data
dataset = dataset.with_timestamp_columns(fine_grain_timestamp='datetime', coarse_grain_timestamp='coarse_time')

# filter with time-series-trait-specific methods
data_slice = dataset.time_before(datetime(2019, 1, 1))
data_slice = dataset.time_after(datetime(2019, 1, 1))
data_slice = dataset.time_between(datetime(2019, 1, 1), datetime(2019, 2, 1))
data_slice = dataset.time_recent(timedelta(weeks=1, days=1))
```

#### <a name="create-a-filedataset"></a>Vytvoření datové sady

Použijte metodu [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) třídy `FileDatasetFactory` pro načtení souborů v jakémkoli formátu a pro vytvoření neregistrované datové sady. Pokud je vaše úložiště za virtuální sítí nebo bránou firewall, nastavte parametr `validate =False` v metodě `from_files()`. Tím se obchází počáteční krok ověření a vy budete moct vytvořit datovou sadu z těchto zabezpečených souborů.

```Python
# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [(datastore, 'animals')]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = ['https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
             'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz']
mnist_ds = Dataset.File.from_files(path=web_paths)
```

#### <a name="on-the-web"></a>Na webu 
Následující kroky a animace ukazují, jak vytvořit datovou sadu v Azure Machine Learning Studiu https://ml.azure.com.

![Vytvoření datové sady pomocí uživatelského rozhraní](./media/how-to-create-register-datasets/create-dataset-ui.gif)

Vytvoření datové sady v studiu:
1. Přihlaste se na https://ml.azure.com.
1. V části **assets (prostředky** ) v levém podokně vyberte datové **sady** . 
1. Vyberte **vytvořit datovou sadu** a zvolte zdroj datové sady. Tento zdroj může být místní soubory, úložiště dat nebo veřejné adresy URL.
1. Vyberte **tabulkové** nebo **soubor** pro typ datové sady.
1. Výběrem **Další** otevřete formulář **úložiště dat a výběr souboru** . V tomto formuláři můžete vybrat, kde má být datová sada po vytvoření, a také vybrat datové soubory, které se mají použít pro datovou sadu. 
1. Výběrem možnosti **Další** naplníte formuláře **nastavení a náhled** a **schéma** . jsou inteligentně vyplněné na základě typu souboru a můžete ještě před vytvořením těchto formulářů nakonfigurovat datovou sadu. 
1. Kliknutím na tlačítko **Další** zkontrolujte formulář **potvrdit podrobnosti** . Projděte si výběr a vytvořte pro datovou sadu volitelný datový profil. Přečtěte si další informace o [profilování dat](how-to-create-portal-experiments.md#profile). 
1. Vytvoření datové sady dokončíte výběrem **vytvořit** .

## <a name="register-datasets"></a>Registrace datových sad

Pokud chcete dokončit proces vytváření, zaregistrujte své datové sady s pracovním prostorem. Pomocí metody [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--create-new-version-false-) můžete zaregistrovat datové sady s vaším pracovním prostorem, aby je bylo možné sdílet s ostatními a znovu je používat v různých experimentech:

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

> [!Note]
> Datové sady vytvořené prostřednictvím Azure Machine Learning studia se automaticky zaregistrují do pracovního prostoru.

## <a name="create-datasets-with-azure-open-datasets"></a>Vytváření datových sad pomocí otevřených datových sad Azure

[Otevřené datové sady v Azure](https://azure.microsoft.com/services/open-datasets/) jsou spravované veřejné datové sady, které můžete použít k přidání funkcí specifických pro konkrétní scénář do řešení Machine Learning pro přesnější modely. Datové sady zahrnují data z veřejných domén pro počasí, sčítání, svátky, veřejné zabezpečení a umístění, které vám pomůžou naučit modely strojového učení a rozšířit prediktivní řešení. Otevřené datové sady jsou v cloudu na Microsoft Azure a jsou zahrnuté v sadě SDK i v uživatelském rozhraní pracovního prostoru.

### <a name="use-the-sdk"></a>Použití sady SDK

Pokud chcete vytvořit datové sady s otevřenými datovými sadami Azure ze sady SDK, ujistěte se, že jste balíček nainstalovali pomocí `pip install azureml-opendatasets`. Jednotlivé diskrétní datové sady jsou reprezentovány vlastní třídou v sadě SDK a některé třídy jsou k dispozici jako `TabularDataset`, `FileDataset`nebo obojí. Úplný seznam tříd naleznete v [referenční dokumentaci](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py) .

Můžete načíst určité třídy jako `TabularDataset` nebo `FileDataset`, což vám umožní manipulovat přímo nebo stahovat soubory. Jiné třídy mohou získat datovou sadu **pouze** pomocí jedné z `get_tabular_dataset()` nebo `get_file_dataset()` funkcí. Následující ukázka kódu ukazuje několik příkladů těchto typů tříd.

```python
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()

from azureml.opendatasets import Diabetes

# Diabetes class can return ONLY return TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```

Když zaregistrujete datovou sadu vytvořenou z otevřených datových sad, nestahují se okamžitě žádná data, ale data budou k dispozici později, až se vyžádají (například během školení) z centrálního umístění úložiště.

### <a name="use-the-ui"></a>Použití uživatelského rozhraní

Můžete také vytvořit datové sady z tříd Open DataSets prostřednictvím uživatelského rozhraní. V pracovním prostoru vyberte kartu datové **sady** v části **prostředky**. V rozevírací nabídce **vytvořit datovou sadu** vyberte možnost **z otevřených datových sad**.

![Otevřít datovou sadu s uživatelským rozhraním](./media/how-to-create-register-datasets/open-datasets-1.png)

Vyberte datovou sadu tím, že vyberete její dlaždici. (Máte možnost filtrovat pomocí panelu hledání.) Vyberte **Další**.

![Zvolit datovou sadu](./media/how-to-create-register-datasets/open-datasets-2.png)

Vyberte název, pod kterým chcete datovou sadu zaregistrovat, a volitelně data filtrujte pomocí dostupných filtrů. V tomto případě můžete pro datovou sadu veřejných svátků filtrovat časové období na jeden rok a směrové číslo země pouze na nás. Vyberte **Vytvořit**.

![Nastavení parametrů datové sady a vytvoření datové sady](./media/how-to-create-register-datasets/open-datasets-3.png)

Datová sada je teď v pracovním prostoru v části datové **sady**dostupná. Můžete ji použít stejným způsobem jako jiné datové sady, které jste vytvořili.

## <a name="version-datasets"></a>Datové sady verze

Novou datovou sadu můžete zaregistrovat pod stejným názvem vytvořením nové verze. Verze datové sady je způsob, jak můžete založit stav vašich dat, abyste mohli použít určitou verzi datové sady pro experimentování nebo budoucí reprodukci. Přečtěte si další informace o [verzích datových sad](how-to-version-track-datasets.md).
```Python
# create a TabularDataset from Titanic training data
web_paths = ['https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
             'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv']
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

# create a new version of titanic_ds
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'new titanic training data',
                                 create_new_version = True)
```

## <a name="access-datasets-in-your-script"></a>Přístup k datovým sadám ve skriptu

Registrované datové sady jsou přístupné místně i vzdáleně na výpočetních clusterech, jako je Azure Machine Learning Compute. Chcete-li získat přístup k zadané datové sadě mezi experimenty, použijte následující kód pro přístup k pracovnímu prostoru a registrované datové sadě podle názvu. Ve výchozím nastavení metoda [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) třídy `Dataset` vrátí nejnovější verzi datové sady, která je zaregistrována v pracovním prostoru.

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

* Naučte [se naučit s datovými sadami](how-to-train-with-datasets.md).
* Pomocí automatizovaného strojového učení [se TabularDatasets naučíte](https://aka.ms/automl-dataset).
* Další příklady školení pro datovou sadu najdete v [ukázkových poznámkových blocích](https://aka.ms/dataset-tutorial).
