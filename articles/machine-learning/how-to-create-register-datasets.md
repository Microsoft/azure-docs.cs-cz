---
title: Vytváření datových sad služby Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Naučte se vytvářet Azure Machine Learning datové sady pro přístup k datům pro spuštění experimentování ve službě Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q1, data4ml
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2020
ms.openlocfilehash: f47d610a24de2cfc8f1131f61afc8c8173a34376
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786616"
---
# <a name="create-azure-machine-learning-datasets"></a>Vytváření datových sad služby Azure Machine Learning

V tomto článku se dozvíte, jak vytvořit Azure Machine Learning datové sady pro přístup k datům pro místní nebo vzdálené experimenty pomocí sady Azure Machine Learning Python SDK. Informace o tom, kde se datové sady vejdou do celého pracovního postupu pro přístup k datům v Azure Machine Learning, najdete v článku [zabezpečený přístup k datům](concept-data.md#data-workflow) .

Vytvořením datové sady vytvoříte odkaz na umístění zdroje dat společně s kopií jeho metadat. Vzhledem k tomu, že data zůstanou ve svém stávajícím umístění, nebudete mít žádné dodatečné náklady na úložiště a nebudete mít na paměti integritu vašich zdrojů dat. Také jsou laxně vytvářená vyhodnocovány datové sady, což pomáhá zrychlit výkon pracovního postupu. Můžete vytvořit datové sady z úložišť dat, veřejných adres URL a [Azure Open DataSet](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md).

Pro prostředí s nízkým kódem můžete [pomocí sady Azure Machine Learning Studio vytvořit Azure Machine Learning datové sady.](how-to-connect-data-ui.md#create-datasets)

S Azure Machine Learningmi datovými sadami můžete:

* Uchovávejte v úložišti jednu kopii dat, na kterou odkazují datové sady.

* Bezproblémový přístup k datům během školení modelů, aniž byste se museli starat o připojovací řetězce nebo cesty k datům. [Přečtěte si další informace o tom, jak naučit s datovými sadami](how-to-train-with-datasets.md).

* Sdílejte data a spolupracujte s ostatními uživateli.

## <a name="prerequisites"></a>Požadavky

K vytváření a práci s datovými sadami potřebujete:

* Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si bezplatný účet. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree).

* [Pracovní prostor Azure Machine Learning](how-to-manage-workspace.md).

* [Nainstalovaná sada Azure Machine Learning SDK pro Python](/python/api/overview/azure/ml/install), která zahrnuje balíček AzureML-DataSet Sets.

    * Vytvořte [instanci služby Azure Machine Learning COMPUTE](how-to-create-manage-compute-instance.md), což je plně nakonfigurované a spravované vývojové prostředí, které zahrnuje integrované poznámkové bloky a sadu SDK už je nainstalovaná.

    **OR**

    * Pracujte na vlastním poznámkovém bloku Jupyter a nainstalujte sadu SDK sami s [těmito pokyny](/python/api/overview/azure/ml/install).

> [!NOTE]
> Některé třídy DataSet mají závislosti na balíčku [AzureML-dataprep](https://pypi.org/project/azureml-dataprep/) , který je kompatibilní pouze s 64-bitovým Pythonem. Pro uživatele se systémem Linux jsou tyto třídy podporovány pouze v následujících distribucích: Red Hat Enterprise Linux (7, 8), Ubuntu (14,04, 16,04, 18,04), Fedora (27, 28), Debian (8, 9) a CentOS (7). Pokud používáte nepodporované distribuce, použijte prosím [tuto příručku](/dotnet/core/install/linux) , abyste mohli pokračovat v instalaci .net Core 2,1. 

## <a name="compute-size-guidance"></a>Doprovodné materiály k výpočetním velikostem

Při vytváření datové sady zkontrolujte výpočetní výkon a velikost vašich dat v paměti. Velikost dat v úložišti není stejná jako velikost dat v datovém rámečku. Například data v souborech CSV můžou v dataframe rozšířit až 10x, takže soubor CSV o velikosti 1 GB se může v datovém rámečku stát 10 GB. 

Pokud jsou vaše data komprimovaná, může se ještě zvětšit. 20 GB relativně zhuštěných dat uložených v komprimovaném formátu Parquet může rozšířit na ~ 800 GB v paměti. Vzhledem k tomu, že soubory Parquet ukládají data ve sloupcovém formátu, pokud potřebujete pouze polovinu sloupců, stačí pouze načíst ~ 400 GB v paměti.

[Přečtěte si další informace o optimalizaci zpracování dat v Azure Machine Learning](concept-optimize-data-processing.md).

## <a name="dataset-types"></a>Typy datových sad

Existují dva typy datových sad na základě toho, jak je uživatelé používají při školení. Datové sady a TabularDatasets. Oba typy se dají použít v Azure Machine Learning školicích pracovních postupech týkajících se, odhady, AutoML, hyperDrive a kanálů. 

### <a name="filedataset"></a>Sada dat

[Datová sada](/python/api/azureml-core/azureml.data.file_dataset.filedataset) souborů odkazuje na jeden nebo více souborů v úložišti dat nebo veřejných adresách URL. Pokud jsou vaše data už vyčištěná a připravená k použití ve školicích experimentech, můžete soubory [Stáhnout nebo připojit](how-to-train-with-datasets.md#mount-vs-download) k výpočetnímu prostředí jako objekt DataSet. 

Pro pracovní postupy vašeho strojového učení doporučujeme použít datové sady, protože zdrojové soubory můžou být v libovolném formátu, což umožňuje širší škálu scénářů strojového učení, včetně hloubkového učení.

Vytvořte datovou sadu pomocí [sady Python SDK](#create-a-filedataset) nebo [Azure Machine Learning studia](how-to-connect-data-ui.md#create-datasets) .
### <a name="tabulardataset"></a>TabularDataset

[TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) představuje data v tabulkovém formátu tak, že analyzuje zadaný soubor nebo seznam souborů. To vám umožní vyhodnotit data do PANDAS nebo Spark dataframe, abyste mohli pracovat se známými knihovnami pro přípravu a školení dat, aniž byste museli Poznámkový blok opustit. Můžete vytvořit `TabularDataset` objekt ze souborů. csv,. TSV, [. Parquet](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-), [. JSON](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none--invalid-lines--error---encoding--utf8--)a z [výsledků dotazu SQL](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-sql-query-query--validate-true--set-column-types-none--query-timeout-30-).

Pomocí TabularDatasets můžete zadat časové razítko ze sloupce v datech nebo z ohledu na to, kde jsou uložená data vzoru cesty, a povolit tak vlastnost časové řady. Tato specifikace umožňuje snadno a efektivně filtrovat podle času. Příklad najdete v tématu [ukázka rozhraní API pro tabulkovou časovou řadu s NOAA daty o počasí](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb).

Vytvořte TabularDataset pomocí [sady Python SDK](#create-a-tabulardataset) nebo [Azure Machine Learning studia](how-to-connect-data-ui.md#create-datasets).

>[!NOTE]
> [Automatizované](concept-automated-ml.md) WORKFLOWy ml vygenerované pomocí nástroje Azure Machine Learning Studio aktuálně podporují jenom TabularDatasets. 

## <a name="access-datasets-in-a-virtual-network"></a>Přístup k datovým sadám ve virtuální síti

Pokud je váš pracovní prostor ve virtuální síti, musíte nakonfigurovat datovou sadu, aby vynechala ověřování. Další informace o tom, jak používat úložiště dat a datové sady ve virtuální síti, najdete v tématu [zabezpečení pracovního prostoru a přidružených prostředků](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets).

<a name="datasets-sdk"></a>

## <a name="create-datasets-from-datastores"></a>Vytvoření datových sad z úložišť dat

Aby data mohla být přístupná pomocí Azure Machine Learning, musí být datové sady vytvořené z cest v [Azure Machine Learning úložiště](how-to-access-data.md) dat nebo webových adres URL. 

> [!TIP] 
> Datové sady můžete vytvořit přímo z adres URL úložiště s přístupem k datům založeným na identitě. Další informace najdete v [připojení k úložišti pomocí přístupu k datům na základě identity (Preview)](how-to-identity-based-data-access.md) .<br><br>
Tato funkce je [experimentální](/python/api/overview/azure/ml/#stable-vs-experimental) funkcí ve verzi Preview a může se kdykoli změnit. 

 
Vytvoření datových sad z úložiště dat pomocí sady Python SDK:

1. Ověřte, že máte `contributor` nebo máte `owner` přístup k základní službě úložiště registrovaného Azure Machine Learningho úložiště dat. [V Azure Portal Ověřte oprávnění účtu úložiště](../role-based-access-control/check-access.md).

1. Vytvořte datovou sadu pomocí odkazů na cesty v úložišti dat. Datovou sadu můžete vytvořit z více cest ve více úložišti dat. Neexistuje žádné omezení počtu souborů nebo velikosti dat, ze kterých lze vytvořit datovou sadu. 

> [!NOTE]
> Pro každou cestu k datům se do služby úložiště pošle pár požadavků, aby se zkontrolovalo, jestli odkazuje na soubor nebo složku. Tato režie může vést ke snížení výkonu nebo selhání. Datová sada odkazující na jednu složku s 1000 soubory uvnitř se považuje za odkazování na jednu cestu k datům. Pro zajištění optimálního výkonu doporučujeme vytvořit datovou sadu odkazující na méně než 100 cest v úložišti dat.

### <a name="create-a-filedataset"></a>Vytvoření datové sady

Použijte [`from_files()`](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory#from-files-path--validate-true-) metodu `FileDatasetFactory` třídy pro načtení souborů v jakémkoli formátu a pro vytvoření neregistrované datové sady. 

Pokud je vaše úložiště za virtuální sítí nebo bránou firewall, nastavte parametr `validate=False` v `from_files()` metodě. Tím se obchází počáteční krok ověření a vy budete moct vytvořit datovou sadu z těchto zabezpečených souborů. Přečtěte si další informace o tom, jak [používat úložiště dat a datové sady ve virtuální síti](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets).

```Python
# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [(datastore, 'animals')]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = ['https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
             'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz']
mnist_ds = Dataset.File.from_files(path=web_paths)
```
Pokud chcete znovu použít a sdílet datové sady v rámci experimentu v pracovním prostoru, [Zaregistrujte datovou sadu](#register-datasets). 

> [!TIP] 
> Nahrajte soubory z místního adresáře a v rámci jediné metody pomocí metody Public Preview Vytvořte datovou sadu typu [upload_directory ()](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory#upload-directory-src-dir--target--pattern-none--overwrite-false--show-progress-true-). Tato metoda je funkcí [experimentální](/python/api/overview/azure/ml/#stable-vs-experimental) verze Preview a může se kdykoli změnit. 
> 
>  Tato metoda nahraje data do vašeho podkladového úložiště a výsledkem je, že se vám budou účtovat náklady na úložiště. 

### <a name="create-a-tabulardataset"></a>Vytvoření TabularDataset

Pomocí [`from_delimited_files()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory) metody `TabularDatasetFactory` třídy můžete číst soubory ve formátu. csv nebo. TSV a vytvořit neregistrované TabularDataset. Chcete-li číst soubory z formátu. Parquet, použijte [`from_parquet_files()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) metodu. Pokud načítáte z více souborů, výsledky budou shrnuty do jednoho tabulkového znázornění. 

V [referenční dokumentaci k TabularDatasetFactory](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory) najdete informace o podporovaných formátech souborů a také o syntaxech a vzorech návrhu. 

Pokud je vaše úložiště za virtuální sítí nebo bránou firewall, nastavte parametr `validate=False` v `from_delimited_files()` metodě. Tím se obchází počáteční krok ověření a vy budete moct vytvořit datovou sadu z těchto zabezpečených souborů. Přečtěte si další informace o tom, jak používat [úložiště dat a datové sady ve virtuální síti](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets).

Následující kód získá existující pracovní prostor a požadované úložiště dat podle názvu. A poté předává úložiště dat a umístění souborů do `path` parametru pro vytvoření nového TabularDataset `weather_ds` .

```Python
from azureml.core import Workspace, Datastore, Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
    
# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)

# create a TabularDataset from 3 file paths in datastore
datastore_paths = [(datastore, 'weather/2018/11.csv'),
                   (datastore, 'weather/2018/12.csv'),
                   (datastore, 'weather/2019/*.csv')]

weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```
### <a name="set-data-schema"></a>Nastavení schématu dat

Ve výchozím nastavení se při vytváření TabularDataset automaticky odvozuje datové typy sloupců. Pokud odvozené typy neodpovídají vašim očekáváním, můžete schéma datové sady aktualizovat zadáním typů sloupců pomocí následujícího kódu. Parametr `infer_column_type` je použitelný pouze pro datové sady vytvořené z oddělených souborů. [Přečtěte si další informace o podporovaných datových typech](/python/api/azureml-core/azureml.data.dataset_factory.datatype).


```Python
from azureml.core import Dataset
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|Indexovacím|PassengerId (ID pasažéra)|Zachované|Pclass|Name|Sex|Věk|SibSp|Parch|Ticket (Lístek)|Vozov|Posádk|Nastoupilo
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|Ne|3|Braund, Mr. Owen Harris|male (muž)|22,0|1|0|A/5 21171|7,2500||S
1|2|Ano|1|Cumings, paní Jan Bradley (Florencie Briggs th...|female (žena)|38,0|1|0|POČÍTAČ 17599|71,2833|C85|C
2|3|Ano|3|Heikkinen, chybíš. Laina|female (žena)|26.0|0|0|STON/O2. 3101282|7,9250||S

Chcete-li znovu použít a sdílet datové sady mezi experimenty v pracovním prostoru, [Zaregistrujte datovou sadu](#register-datasets).

## <a name="wrangle-data"></a>Wrangle data
Jakmile vytvoříte a [zaregistrujete](#register-datasets) datovou sadu, můžete ji načíst do svého poznámkového bloku pro tahání a [průzkum](#explore-data) dat před školením modelu. 

Pokud nepotřebujete dělat žádné tahání nebo průzkumy dat, přečtěte si téma Postup využívání datových sad ve školicích skriptech pro odesílání experimentů ML v [vlakech s datovými sadami](how-to-train-with-datasets.md).

### <a name="filter-datasets-preview"></a>Filtrovat datové sady (Preview)

Možnosti filtrování závisí na typu datové sady, kterou máte. 
> [!IMPORTANT]
> Filtrování datových sad pomocí metody Preview [`filter()`](/python/api/azureml-core/azureml.data.tabulardataset#filter-expression-) je funkce [experimentální](/python/api/overview/azure/ml/#stable-vs-experimental) verze Preview a může se kdykoli kdykoli změnit. 
> 
**Pro TabularDatasets** můžete sloupce zachovat nebo odebrat pomocí metod [keep_columns ()](/python/api/azureml-core/azureml.data.tabulardataset#keep-columns-columns--validate-false-) a [drop_columns ()](/python/api/azureml-core/azureml.data.tabulardataset#drop-columns-columns-) .

Chcete-li vyfiltrovat řádky podle konkrétní hodnoty sloupce ve TabularDataset, použijte metodu [Filter ()](/python/api/azureml-core/azureml.data.tabulardataset#filter-expression-) (Náhled). 

Následující příklady vrátí neregistrovanou datovou sadu založenou na zadaných výrazech.

```python
# TabularDataset that only contains records where the age column value is greater than 15
tabular_dataset = tabular_dataset.filter(tabular_dataset['age'] > 15)

# TabularDataset that contains records where the name column value contains 'Bri' and the age column value is greater than 15
tabular_dataset = tabular_dataset.filter((tabular_dataset['name'].contains('Bri')) & (tabular_dataset['age'] > 15))
```

**V objektech DataSet** každý řádek odpovídá cestě k souboru, takže filtrování podle hodnoty sloupce není užitečné. Můžete ale [filtrovat ()](/python/api/azureml-core/azureml.data.filedataset#filter-expression-) řádky podle metadat, například, CreationTime, Size atd.

Následující příklady vrátí neregistrovanou datovou sadu založenou na zadaných výrazech.

```python
# FileDataset that only contains files where Size is less than 100000
file_dataset = file_dataset.filter(file_dataset.file_metadata['Size'] < 100000)

# FileDataset that only contains files that were either created prior to Jan 1, 2020 or where 
file_dataset = file_dataset.filter((file_dataset.file_metadata['CreatedTime'] < datetime(2020,1,1)) | (file_dataset.file_metadata['CanSeek'] == False))
```

**Označené datové sady** , které jsou vytvořené z [projektů označování dat](how-to-create-labeling-projects.md) , jsou zvláštním případem. Tyto datové sady jsou typem TabularDataset tvořeného soubory imagí. Pro tyto typy datových sad můžete [filtrovat ()](/python/api/azureml-core/azureml.data.tabulardataset#filter-expression-) image podle metadat a podle hodnot sloupců, jako jsou `label` a `image_details` .

```python
# Dataset that only contains records where the label column value is dog
labeled_dataset = labeled_dataset.filter(labeled_dataset['label'] == 'dog')

# Dataset that only contains records where the label and isCrowd columns are True and where the file size is larger than 100000
labeled_dataset = labeled_dataset.filter((labeled_dataset['label']['isCrowd'] == True) & (labeled_dataset.file_metadata['Size'] > 100000))
```

### <a name="partition-data-preview"></a>Data oddílu (Preview)

Můžete rozdělit datovou sadu zahrnutím `partitions_format` parametru při vytváření TabularDataset nebo DataSet. 

> [!IMPORTANT]
> Vytváření oddílů datové sady je [experimentální](/python/api/overview/azure/ml/#stable-vs-experimental) možnost verze Preview a může se kdykoli kdykoli změnit. 

Při vytváření oddílů datové sady jsou informace o oddílu každé cesty k souboru extrahovány do sloupců v závislosti na zadaném formátu. Formát by měl začít od pozice prvního klíče oddílu až po konec cesty k souboru. 

Například s ohledem na cestu, `../Accounts/2019/01/01/data.jsonl` kde je oddíl podle názvu a času oddělení; `partition_format='/{Department}/{PartitionDate:yyyy/MM/dd}/data.jsonl'` vytvoří sloupec String "Department" s hodnotou "Accounts" a sloupec typu DateTime "PartitionDate" s hodnotou `2019-01-01` .

Pokud již vaše data obsahují existující oddíly a chcete zachovat tento formát, zahrňte `partitioned_format` parametr do [`from_files()`](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory#from-files-path--validate-true--partition-format-none-) metody pro vytvoření datové sady. 

Chcete-li vytvořit TabularDataset, který zachovává stávající oddíly, zahrňte `partitioned_format` parametr do metody [from_parquet_files ()](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) nebo [from_delimited_files ()](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false--empty-as-string-false--encoding--utf8--) .

Následující příklad:
* Vytvoří datovou sadu souborů z dělených souborů.
* Získá klíče oddílu.
* Vytvoří novou indexovou datovou sadu pomocí
 
```Python

file_dataset = Dataset.File.from_files(data_paths, partition_format = '{userid}/*.wav')
ds.register(name='speech_dataset')

# access partition_keys
indexes = file_dataset.partition_keys # ['userid']

# get all partition key value pairs should return [{'userid': 'user1'}, {'userid': 'user2'}]
partitions = file_dataset.get_partition_key_values()


partitions = file_dataset.get_partition_key_values(['userid'])
# return [{'userid': 'user1'}, {'userid': 'user2'}]

# filter API, this will only download data from user1/ folder
new_file_dataset = file_dataset.filter(ds['userid'] == 'user1').download()
```

Můžete také vytvořit novou strukturu oddílů pro TabularDatasets pomocí metody [partitions_by ()](/python/api/azureml-core/azureml.data.tabulardataset#partition-by-partition-keys--target--name-none--show-progress-true--partition-as-file-dataset-false-) .

```Python

 dataset = Dataset.get_by_name('test') # indexed by country, state, partition_date

# call partition_by locally
new_dataset = ds.partition_by(name="repartitioned_ds", partition_keys=['country'], target=DataPath(datastore, "repartition"))
partition_keys = new_dataset.partition_keys # ['country']
```

>[!IMPORTANT]
> Oddíly TabularDataset se dají použít i v kanálu Azure Machine Learning jako vstup do vaší ParallelRunStep v mnoha aplikacích modelů. Podívejte se na příklad v [dokumentaci ke akcelerátoru modelů](https://github.com/microsoft/solution-accelerator-many-models/blob/master/01_Data_Preparation.ipynb).

## <a name="explore-data"></a>Zkoumání dat

Po dokončení tahání dat můžete datovou sadu [zaregistrovat](#register-datasets) a pak ji načíst do svého poznámkového bloku pro zkoumání dat před školením modelu.

V případě datových sad můžete buď **připojit** nebo **Stáhnout** datovou sadu, a použít knihovny Pythonu, které běžně používáte pro zkoumání dat. [Přečtěte si další informace o připojení a stažení](how-to-train-with-datasets.md#mount-vs-download).

```python
# download the dataset 
dataset.download(target_path='.', overwrite=False) 

# mount dataset to the temp directory at `mounted_path`

import tempfile
mounted_path = tempfile.mkdtemp()
mount_context = dataset.mount(mounted_path)

mount_context.start()
```

Pro TabularDatasets použijte [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) metodu k zobrazení dat v datovém rámečku. 

```python
# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|Indexovacím|PassengerId (ID pasažéra)|Zachované|Pclass|Name|Sex|Věk|SibSp|Parch|Ticket (Lístek)|Vozov|Posádk|Nastoupilo
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|Ne|3|Braund, Mr. Owen Harris|male (muž)|22,0|1|0|A/5 21171|7,2500||S
1|2|Ano|1|Cumings, paní Jan Bradley (Florencie Briggs th...|female (žena)|38,0|1|0|POČÍTAČ 17599|71,2833|C85|C
2|3|Ano|3|Heikkinen, chybíš. Laina|female (žena)|26.0|0|0|STON/O2. 3101282|7,9250||S

## <a name="create-a-dataset-from-pandas-dataframe"></a>Vytvoření datové sady z PANDAS dataframe

Chcete-li vytvořit TabularDataset z PANDAS dataframe v paměti, zapište data do místního souboru, třeba do sdíleného svazku clusteru, a vytvořte datovou sadu z tohoto souboru. Následující kód demonstruje tento pracovní postup.

```python
# azureml-core of version 1.0.72 or higher is required
# azureml-dataprep[pandas] of version 1.1.34 or higher is required

from azureml.core import Workspace, Dataset
local_path = 'data/prepared.csv'
dataframe.to_csv(local_path)

# upload the local file to a datastore on the cloud

subscription_id = 'xxxxxxxxxxxxxxxxxxxxx'
resource_group = 'xxxxxx'
workspace_name = 'xxxxxxxxxxxxxxxx'

workspace = Workspace(subscription_id, resource_group, workspace_name)

# get the datastore to upload prepared data
datastore = workspace.get_default_datastore()

# upload the local file from src_dir to the target_path in datastore
datastore.upload(src_dir='data', target_path='data')

# create a dataset referencing the cloud location
dataset = Dataset.Tabular.from_delimited_files(path = [(datastore, ('data/prepared.csv'))])
```

> [!TIP]
> Vytvořte a zaregistrujte TabularDataset z paměti Spark nebo PANDAS dataframe s jedinou metodou s použitím metod Public Preview [`register_spark_dataframe()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#methods) a [`register_pandas_dataframe()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#methods) . Tyto metody registrace jsou [experimentální](/python/api/overview/azure/ml/#stable-vs-experimental) funkce ve verzi Preview a můžou se kdykoli změnit. 
> 
>  Tyto metody odesílají data do vašeho základního úložiště a v důsledku toho se účtují náklady na úložiště. 

## <a name="register-datasets"></a>Registrace datových sad

Pokud chcete dokončit proces vytváření, zaregistrujte své datové sady s pracovním prostorem. Použijte [`register()`](/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset#&preserve-view=trueregister-workspace--name--description-none--tags-none--create-new-version-false-) metodu k registraci datových sad s vaším pracovním prostorem, abyste je mohli sdílet s ostatními a znovu je použít ve vašem pracovním prostoru v rámci experimentů:

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

## <a name="create-datasets-using-azure-resource-manager"></a>Vytvoření datových sad pomocí Azure Resource Manager

Existuje mnoho šablon [https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-dataset-create-*](https://github.com/Azure/azure-quickstart-templates/tree/master/) , které lze použít k vytvoření datových sad.

Informace o použití těchto šablon naleznete v tématu [použití šablony Azure Resource Manager k vytvoření pracovního prostoru pro Azure Machine Learning](how-to-create-workspace-template.md).


## <a name="create-datasets-from-azure-open-datasets"></a>Vytváření datových sad z Azure Open DataSet

[Otevřené datové sady v Azure](https://azure.microsoft.com/services/open-datasets/) jsou spravované veřejné datové sady, které můžete použít k přidání funkcí specifických pro konkrétní scénář do řešení Machine Learning pro přesnější modely. Datové sady zahrnují data z veřejných domén pro počasí, sčítání, svátky, veřejné zabezpečení a umístění, které vám pomůžou naučit modely strojového učení a rozšířit prediktivní řešení. Otevřené datové sady jsou v cloudu na Microsoft Azure a jsou součástí sady SDK i sady Studio.

Naučte se vytvářet [Azure Machine Learning datové sady z Azure Open DataSets](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md). 

## <a name="train-with-datasets"></a>Trénování s datovými sadami

Využijte své datové sady ve zkušebních experimentech Machine Learning pro školení modelů ML. [Přečtěte si další informace o tom, jak naučit s datovými sadami](how-to-train-with-datasets.md).

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

## <a name="next-steps"></a>Další kroky

* Naučte [se naučit s datovými sadami](how-to-train-with-datasets.md).
* Pomocí automatizovaného strojového učení [se TabularDatasets naučíte](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb).
* Další příklady školení pro datovou sadu najdete v [ukázkových poznámkových blocích](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/).
