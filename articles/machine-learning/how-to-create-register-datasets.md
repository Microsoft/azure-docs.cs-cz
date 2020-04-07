---
title: Vytvoření datových sad Azure Machine Learning pro přístup k datům
titleSuffix: Azure Machine Learning
description: Zjistěte, jak vytvořit datové sady Azure Machine Learning pro přístup k datům pro spuštění experimentu strojového učení.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 02/10/2020
ms.openlocfilehash: feaa0c22ec98d170a65e5c9bee119ba3904a95cf
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673726"
---
# <a name="create-azure-machine-learning-datasets"></a>Vytvoření datových sad Azure Machine Learning

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak vytvořit datové sady Azure Machine Learning pro přístup k datům pro místní nebo vzdálené experimenty.

S datovými sadami Azure Machine Learning můžete:

* Uchovávejte jednu kopii dat ve svém úložišti, na kterou odkazují datové sady.

* Bezproblémový přístup k datům během trénování modelu bez obav o připojovací řetězce nebo datové cesty.

* Sdílejte data a spolupracujte s ostatními uživateli.

## <a name="prerequisites"></a>Požadavky
' Chcete-li vytvořit datové sady a pracovat s ním, potřebujete:

* Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si bezplatný účet. Vyzkoušejte [bezplatnou nebo placenou verzi Azure Machine Learning](https://aka.ms/AMLFree).

* Pracovní [prostor Azure Machine Learning](how-to-manage-workspace.md).

* Nainstalovaná [sada Azure Machine Learning SDK pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), která obsahuje balíček azureml-datasets.

> [!NOTE]
> Některé třídy datových sad mají závislosti na balíčku [azureml-dataprep.](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) Pro uživatele Linuxu jsou tyto třídy podporovány pouze na následujících distribucích: Red Hat Enterprise Linux, Ubuntu, Fedora a CentOS.

## <a name="compute-size-guidance"></a>Pokyny pro výpočetní velikost

Při vytváření datové sady zkontrolujte výkon výpočetního zpracování a velikost dat v paměti. Velikost dat v úložišti není stejná jako velikost dat v datovém rámci. Například data v souborech CSV lze rozšířit až na 10x v datovém rámci, takže 1 GB CSV soubor může být 10 GB v datovém rámci. 

Hlavním faktorem je, jak velká je datová sada v paměti, tj. Doporučujeme, aby vaše výpočetní velikost a výpočetní výkon obsahovaly 2x velikost paměti RAM. Takže pokud je váš datový rámec 10 GB, chcete výpočetní cíl s 20 + GB paměti RAM, abyste zajistili, že datový rámec se může pohodlně vejít do paměti a být zpracován. Pokud jsou data komprimována, mohou se dále rozšiřovat. 20 GB relativně řídkých dat uložených v komprimovaném formátu parket se může rozšířit na ~ 800 GB v paměti. Vzhledem k tomu, parketové soubory ukládat data ve sloupcovém formátu, pokud potřebujete pouze polovinu sloupců, pak stačí načíst ~ 400 GB v paměti.
 
Pokud používáte Pandy, není důvod mít více než 1 virtuální procesor, protože to je vše, co bude používat. Můžete snadno paralelizovat s mnoha virtuálními procesory na jedné výpočetní instanci/uzlu Azure Machine Learning pomocí Modin a Dask/Ray a v případě potřeby horizontálním navýšení kapacity do velkého clusteru jednoduše přejdete `import pandas as pd` na `import modin.pandas as pd`. 
 
Pokud nemůžete získat dostatečně velký virtuální pro data, máte dvě možnosti: použijte rámec jako Spark nebo Dask k provedení zpracování dat na data 'nedostatek paměti', tj. Pokud je to příliš pomalé, Spark nebo Dask umožňují horizontální navýšení kapacity do clusteru, který lze stále používat interaktivně. 

## <a name="dataset-types"></a>Typy datových sad

Existují dva typy datových sad, na základě toho, jak je uživatelé spotřebovávají při školení:

* [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) představuje data v tabulkovém formátu analýzou zadaný soubor nebo seznam souborů. To vám poskytuje možnost zhmotnit data do Pandas nebo Spark DataFrame. Objekt můžete `TabularDataset` vytvořit z souborů .csv, .tsv, .parkety, .jsonl a z výsledků dotazu SQL. Úplný seznam naleznete v tématu [TabularDatasetFactory class](https://aka.ms/tabulardataset-api-reference).

* Třída [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) odkazuje na jeden nebo více souborů ve vašich úložištích dat nebo veřejných adresách URL. Touto metodou můžete stáhnout nebo připojit soubory k výpočtu jako objekt FileDataset. Soubory mohou být v libovolném formátu, což umožňuje širší škálu scénářů strojového učení, včetně hlubokého učení.

Další informace o nadcházejících změnách rozhraní API najdete v [tématu Oznámení o změně rozhraní API datové sady](https://aka.ms/tabular-dataset).

## <a name="create-datasets"></a>Vytvoření datových sad

Vytvořením datové sady vytvoříte odkaz na umístění zdroje dat spolu s kopií jeho metadat. Vzhledem k tomu, že data zůstanou ve stávajícím umístění, nevznikají vám žádné další náklady na úložiště. Můžete vytvořit `TabularDataset` obě `FileDataset` sady dat pomocí pythonu SDK nebo na . https://ml.azure.com

Aby byla data přístupná azure machine learningu, musí se vytvářet datové sady z cest v [datových úložištích Azure](how-to-access-data.md) nebo veřejných webových adresách URL. 

### <a name="use-the-sdk"></a>Použití sady SDK

Vytvoření datových sad z [úložiště dat Azure](how-to-access-data.md) pomocí sady Python SDK:

1. Ověřte, `contributor` `owner` že máte registrované úložiště dat Azure nebo k němu máte přístup.

2. Vytvořte datovou sadu odkazováním na cesty v úložišti dat.
> [!Note]
> Můžete vytvořit datovou sadu z více cest ve více úložišti dat. Počet souborů nebo velikost i dat, ze kterých můžete vytvořit datovou sadu, není nijak omezen. Pro každou cestu k datům však bude službě úložiště odesláno několik požadavků, aby se zjistilo, zda odkazuje na soubor nebo složku. Tato režie může vést ke snížení výkonu nebo selhání. Datová sada odkazující na jednu složku s 1000 soubory uvnitř je považována za odkazující na jednu datovou cestu. Doporučujeme vytvořit datovou sadu odkazující na méně než 100 cest v úložišti dat pro optimální výkon.

#### <a name="create-a-tabulardataset"></a>Vytvoření tabulkové datové sady

Pomocí [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false-) metody na `TabularDatasetFactory` třídě můžete číst soubory ve formátu .csv nebo TSV a vytvořit neregistrovanou sadu TabularDataset. Pokud čtete z více souborů, výsledky se agregují do jedné tabulkové reprezentace. 

```Python
from azureml.core import Workspace, Datastore, Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
    
# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)

# create a TabularDataset from 3 paths in datastore
datastore_paths = [(datastore, 'weather/2018/11.csv'),
                   (datastore, 'weather/2018/12.csv'),
                   (datastore, 'weather/2019/*.csv')]
weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```

Ve výchozím nastavení se při vytváření tabulární datové sady automaticky odvodí datové typy sloupců. Pokud odvozené typy neodpovídají vašim očekáváním, můžete zadat typy sloupců pomocí následujícího kódu. Parametr `infer_column_type` je použitelný pouze pro datové sady vytvořené ze souborů oddělených. Další [informace o podporovaných datových typech](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.datatype?view=azure-ml-py)naleznete také v

> [!IMPORTANT] 
> Pokud je vaše úložiště za virtuální sítí nebo bránou firewall, je podporováno pouze vytvoření datové sady prostřednictvím sady SDK. Chcete-li vytvořit datovou sadu, nezapomeňte `validate=False` `infer_column_types=False` zahrnout `from_delimited_files()` parametry a metodu. Tím se obchází počáteční kontrola ověření a zajišťuje, že můžete vytvořit datovou sadu z těchto zabezpečených souborů. 

```Python
from azureml.core import Dataset
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

| |PassengerId|Přežil|Pclass|Name (Název)|Sex|Věk|SibSp|Parch|Ticket (Lístek)|Jízdné|Kabiny|Pustil
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|False|3|Braund, pan Owen Harris|male (muž)|22.0|1|0|A/5 21171|7.2500||S
1|2|True|1|Cumings, paní John Bradley (Florence Briggs Th...|female (žena)|38.0|1|0|PC 17599|71.2833|C85|C
2|3|True|3|Heikkinen, slečno. Laina|female (žena)|26.0|0|0|STON/O2. 3101282|7.9250||S


Chcete-li vytvořit datovou sadu z datového rámce pandas v paměti, zapište data do místního souboru, jako je csv, a vytvořte datovou sadu z tohoto souboru. Následující kód ukazuje tento pracovní postup.

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

Pomocí [`from_sql_query()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-sql-query-query--validate-true--set-column-types-none--query-timeout-30-) metody na `TabularDatasetFactory` třídě číst z Azure SQL Database:

```Python

from azureml.core import Dataset, Datastore

# create tabular dataset from a SQL database in datastore
sql_datastore = Datastore.get(workspace, 'mssql')
sql_ds = Dataset.Tabular.from_sql_query((sql_datastore, 'SELECT * FROM my_table'))
```

V TabularDatasets můžete zadat časové razítko ze sloupce v datech nebo odkudkoli, kde jsou uložena data vzoru cesty, abyste povolili vlastnost časové řady. Tato specifikace umožňuje snadné a efektivní filtrování podle času.

Pomocí [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) metody na`TabularDataset` třídě můžete zadat sloupec časového razítka a povolit filtrování podle času. Další informace naleznete v [tématu Tabular time series-related API demo s daty o počasí NOAA](https://aka.ms/azureml-tsd-notebook).

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

#### <a name="create-a-filedataset"></a>Vytvoření datové sady souborů

Pomocí [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) metody na `FileDatasetFactory` třídě načíst soubory v libovolném formátu a vytvořit neregistrovaný FileDataset. Pokud je vaše úložiště za virtuální sítí nebo `validate =False` bránou firewall, nastavte parametr ve vaší `from_files()` metodě. Tím obcházíte počáteční krok ověření a zajišťujete, že můžete vytvořit datovou sadu z těchto zabezpečených souborů.

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
Následující kroky a animace ukazují, jak vytvořit datovou https://ml.azure.comsadu ve studiu Azure Machine Learning, .

![Vytvoření datové sady s ui](./media/how-to-create-register-datasets/create-dataset-ui.gif)

Vytvoření datové sady ve studiu:
1. Přihlaste https://ml.azure.comse na adrese .
1. V levé podokně vyberte **Datové sady** v části **Datové zdroje.** 
1. Vyberte **Vytvořit datovou sadu,** chcete-li vybrat zdroj datové sady. Tímto zdrojem mohou být místní soubory, úložiště dat nebo veřejné adresy URL.
1. Vyberte **Tabulkový** nebo **Soubor** pro typ datové sady.
1. Výběrem **možnosti Další** otevřete formulář **Datastore a výběr u souboru.** V tomto formuláři vyberete, kde chcete datovou sadu po vytvoření uchovávat, a také vyberete datové soubory, které chcete pro datovou sadu použít. 
1. Výběrem **možnosti Další** naplníte formuláře **Nastavení a náhledu** a **Schémata;** jsou inteligentně naplněny na základě typu souboru a můžete dále nakonfigurovat datovou sadu před vytvořením v těchto formulářích. 
1. Výběrem **možnosti Další** zkontrolujte formulář **Potvrdit podrobnosti.** Zkontrolujte výběr a vytvořte volitelný datový profil pro datovou sadu. Další informace o [profilování dat](how-to-use-automated-ml-for-ml-models.md#profile). 
1. Vyberte **Vytvořit,** chcete-li dokončit vytváření datové sady.

## <a name="register-datasets"></a>Registrace datových sad

Chcete-li dokončit proces vytváření, zaregistrujte datové sady s pracovním prostorem. Pomocí [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--create-new-version-false-) této metody můžete datasety zaregistrovat v pracovním prostoru, abyste je mohli sdílet s ostatními a znovu je používat v různých experimentech:

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

> [!Note]
> Datové sady vytvořené prostřednictvím azure machine learningového studia se automaticky zaevidují do pracovního prostoru.

## <a name="create-datasets-with-azure-open-datasets"></a>Vytváření datových sad pomocí otevřených datových sad Azure

[Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/) jsou sestavené veřejné datové sady, které můžete použít k přidání funkcí specifických pro scénář do řešení strojového učení pro přesnější modely. Datové sady obsahují data veřejné domény pro počasí, sčítání lidu, svátky, veřejnou bezpečnost a umístění, které vám pomohou trénovat modely strojového učení a obohacovat prediktivní řešení. Otevřené datové sady jsou v cloudu v Microsoft Azure a jsou součástí sady SDK i v unovém rozhraní pracovního prostoru.

### <a name="use-the-sdk"></a>Použití sady SDK

Pokud chcete vytvořit datové sady pomocí otevřených datových sad Azure ze `pip install azureml-opendatasets`sady SDK, ujistěte se, že jste balíček nainstalovali s . Každá samostatná datová sada je reprezentována vlastní třídou v sadě SDK a některé třídy jsou k dispozici jako `TabularDataset`a , `FileDataset`nebo obojí. Úplný seznam tříd naleznete v [referenční dokumentaci.](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py)

Můžete načíst určité třídy jako `TabularDataset` nebo , `FileDataset`který umožňuje manipulovat a / nebo stahovat soubory přímo. Jiné třídy můžete získat datovou `get_tabular_dataset()` sadu `get_file_dataset()` **pouze** pomocí jedné z nebo funkce. Následující ukázka kódu ukazuje několik příkladů těchto typů tříd.

```python
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()

from azureml.opendatasets import Diabetes

# Diabetes class can return ONLY TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```

Když zaregistrujete datovou sadu vytvořenou z otevřených datových sad, žádná data se okamžitě nestáhnou, ale data budou přístupná později na požádání (například během školení) z umístění centrálního úložiště.

### <a name="use-the-ui"></a>Použití ui

Můžete také vytvořit datové sady z otevřených datových sad tříd prostřednictvím ui. V pracovním prostoru vyberte kartu **Datové sady** v části **Datové zdroje**. V rozevírací nabídce **Vytvořit datovou sadu** vyberte **Z otevřít datové sady**.

![Otevření datové sady s ui](./media/how-to-create-register-datasets/open-datasets-1.png)

Vyberte datovou sadu výběrem její dlaždice. (Máte možnost filtrovat pomocí vyhledávacího panelu.) Vyberte **další**.

![Výběr datové sady](./media/how-to-create-register-datasets/open-datasets-2.png)

Zvolte název, pod kterým chcete datovou sadu zaregistrovat, a volitelně filtrujte data pomocí dostupných filtrů. V takovém případě pro datovou sadu svátků filtrujete časové období na jeden rok a kód země pouze do USA. Vyberte **Vytvořit**.

![Nastavení paramů datové sady a vytvoření datové sady](./media/how-to-create-register-datasets/open-datasets-3.png)

Datová sada je nyní k dispozici ve vašem pracovním prostoru v části **Datové sady**. Můžete ji použít stejným způsobem jako ostatní datové sady, které jste vytvořili.

## <a name="version-datasets"></a>Datové sady verzí

Novou datovou sadu pod stejným názvem můžete zaregistrovat vytvořením nové verze. Verze datové sady je způsob, jak zařadit do záložek stav dat, abyste mohli použít určitou verzi datové sady pro experimentování nebo budoucí reprodukci. Další informace o [verzích datových sad](how-to-version-track-datasets.md).
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

## <a name="access-datasets-in-your-script"></a>Přístup k datovým souborům ve skriptu

Registrované datové sady jsou přístupné místně i vzdáleně na výpočetních clusterech, jako jsou výpočetní prostředky Azure Machine Learning. Chcete-li získat přístup k registrované datové sadě v experimentech, použijte následující kód pro přístup k pracovnímu prostoru a registrované datové sadě podle názvu. Ve výchozím [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) nastavení metoda `Dataset` na třídě vrátí nejnovější verzi datové sady, která je registrována v pracovním prostoru.

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

* Přečtěte [si, jak trénovat pomocí datových sad](how-to-train-with-datasets.md).
* Pomocí automatizovaného strojového učení [můžete trénovat pomocí tabulardatasets](https://aka.ms/automl-dataset).
* Další příklady školení datové sady najdete v [ukázkových poznámkových blocích](https://aka.ms/dataset-tutorial).
