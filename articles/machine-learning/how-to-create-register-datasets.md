---
title: Vytvoření Azure Machine Learning datových sad pro přístup k datům
titleSuffix: Azure Machine Learning
description: Naučte se vytvářet Azure Machine Learning datové sady pro přístup k datům pro spuštění experimentování ve službě Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, contperfq1
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2020
ms.openlocfilehash: 72124c7e52afcf68f33a8b8a2295448506b0719f
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2020
ms.locfileid: "92165022"
---
# <a name="create-azure-machine-learning-datasets"></a>Vytváření datových sad služby Azure Machine Learning



V tomto článku se dozvíte, jak vytvořit Azure Machine Learning datové sady pro přístup k datům pro místní nebo vzdálené experimenty pomocí sady Azure Machine Learning Python SDK. Informace o tom, kde se datové sady vejdou do celého pracovního postupu pro přístup k datům v Azure Machine Learning, najdete v článku [zabezpečený přístup k datům](concept-data.md#data-workflow) .

Vytvořením datové sady vytvoříte odkaz na umístění zdroje dat společně s kopií jeho metadat. Vzhledem k tomu, že data zůstanou ve svém stávajícím umístění, nebudete mít žádné dodatečné náklady na úložiště a nebudete mít na paměti integritu vašich zdrojů dat. Také jsou laxně vytvářená vyhodnocovány datové sady, což pomáhá zrychlit výkon pracovního postupu. Můžete vytvořit datové sady z úložišť dat, veřejných adres URL a [Azure Open DataSet](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md).

Pro prostředí s nízkým kódem můžete [pomocí Azure Machine Learning studia vytvořit Azure Machine Learning datové sady.](how-to-connect-data-ui.md#create-datasets)

S Azure Machine Learningmi datovými sadami můžete:

* Uchovávejte v úložišti jednu kopii dat, na kterou odkazují datové sady.

* Bezproblémový přístup k datům během školení modelů, aniž byste se museli starat o připojovací řetězce nebo cesty k datům. [Přečtěte si další informace o tom, jak naučit s datovými sadami](how-to-train-with-datasets.md).

* Sdílejte data a spolupracujte s ostatními uživateli.

## <a name="prerequisites"></a>Předpoklady

K vytváření a práci s datovými sadami potřebujete:

* Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si bezplatný účet. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree).

* [Pracovní prostor Azure Machine Learning](how-to-manage-workspace.md).

* [Nainstalovaná sada Azure Machine Learning SDK pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true), která zahrnuje balíček AzureML-DataSet Sets.

    * Vytvořte [instanci služby Azure Machine Learning COMPUTE](how-to-create-manage-compute-instance.md), což je plně nakonfigurované a spravované vývojové prostředí, které zahrnuje integrované poznámkové bloky a sadu SDK už je nainstalovaná.

    **OR**

    * Pracujte na vlastním poznámkovém bloku Jupyter a nainstalujte sadu SDK sami s [těmito pokyny](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true).

> [!NOTE]
> Některé třídy DataSet mají závislosti na balíčku [AzureML-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py&preserve-view=true) , který je kompatibilní pouze s 64-bitovým Pythonem. Pro uživatele se systémem Linux jsou tyto třídy podporovány pouze v následujících distribucích: Red Hat Enterprise Linux (7, 8), Ubuntu (14,04, 16,04, 18,04), Fedora (27, 28), Debian (8, 9) a CentOS (7). Pokud používáte nepodporované distribuce, použijte prosím [tuto příručku](https://docs.microsoft.com/dotnet/core/install/linux) , abyste mohli pokračovat v instalaci .net Core 2,1. 

## <a name="compute-size-guidance"></a>Doprovodné materiály k výpočetním velikostem

Při vytváření datové sady zkontrolujte výpočetní výkon a velikost vašich dat v paměti. Velikost dat v úložišti není stejná jako velikost dat v datovém rámečku. Například data v souborech CSV můžou v dataframe rozšířit až 10x, takže soubor CSV o velikosti 1 GB se může v datovém rámečku stát 10 GB. 

Pokud jsou vaše data komprimovaná, může se ještě zvětšit. 20 GB relativně zhuštěných dat uložených v komprimovaném formátu Parquet může rozšířit na ~ 800 GB v paměti. Vzhledem k tomu, že soubory Parquet ukládají data ve sloupcovém formátu, pokud potřebujete pouze polovinu sloupců, stačí pouze načíst ~ 400 GB v paměti.

[Přečtěte si další informace o optimalizaci zpracování dat v Azure Machine Learning](concept-optimize-data-processing.md).

## <a name="dataset-types"></a>Typy datových sad

Existují dva typy datových sad na základě toho, jak je uživatelé používají při školení. Datové sady a TabularDatasets. Oba typy se dají použít v Azure Machine Learning školicích pracovních postupech týkajících se, odhady, AutoML, hyperDrive a kanálů. 

### <a name="filedataset"></a>Sada dat

[Datová sada](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py&preserve-view=true) souborů odkazuje na jeden nebo více souborů v úložišti dat nebo veřejných adresách URL. Pokud jsou vaše data už vyčištěná a připravená k použití ve školicích experimentech, můžete soubory [Stáhnout nebo připojit](how-to-train-with-datasets.md#mount-vs-download) k výpočetnímu prostředí jako objekt DataSet. 

Pro pracovní postupy vašeho strojového učení doporučujeme použít datové sady, protože zdrojové soubory můžou být v libovolném formátu, což umožňuje širší škálu scénářů strojového učení, včetně hloubkového učení.

Vytvořte datovou sadu pomocí [sady Python SDK](#create-a-filedataset) nebo [Azure Machine Learning studia](how-to-connect-data-ui.md#create-datasets) .
### <a name="tabulardataset"></a>TabularDataset

[TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py&preserve-view=true) představuje data v tabulkovém formátu tak, že analyzuje zadaný soubor nebo seznam souborů. To vám umožní vyhodnotit data do PANDAS nebo Spark dataframe, abyste mohli pracovat se známými knihovnami pro přípravu a školení dat, aniž byste museli Poznámkový blok opustit. Můžete vytvořit `TabularDataset` objekt ze souborů. csv,. TSV,. Parquet,. JSON a z [výsledků dotazu SQL](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py&preserve-view=true#&preserve-view=truefrom-sql-query-query--validate-true--set-column-types-none--query-timeout-30-).

Pomocí TabularDatasets můžete zadat časové razítko ze sloupce v datech nebo z ohledu na to, kde jsou uložená data vzoru cesty, a povolit tak vlastnost časové řady. Tato specifikace umožňuje snadno a efektivně filtrovat podle času. Příklad najdete v tématu [ukázka rozhraní API pro tabulkovou časovou řadu s NOAA daty o počasí](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb).

Vytvořte TabularDataset pomocí [sady Python SDK](#create-a-tabulardataset) nebo [Azure Machine Learning studia](how-to-connect-data-ui.md#create-datasets).

>[!NOTE]
> Pracovní postupy AutoML vygenerované pomocí nástroje Azure Machine Learning Studio aktuálně podporují jenom TabularDatasets. 

## <a name="access-datasets-in-a-virtual-network"></a>Přístup k datovým sadám ve virtuální síti

Pokud je váš pracovní prostor ve virtuální síti, musíte nakonfigurovat datovou sadu, aby vynechala ověřování. Další informace o tom, jak používat úložiště dat a datové sady ve virtuální síti, najdete v tématu [zabezpečení pracovního prostoru a přidružených prostředků](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets).

<a name="datasets-sdk"></a>

## <a name="create-datasets"></a>Vytvoření datových sad

Aby data mohla být přístupná pomocí Azure Machine Learning, musí být datové sady vytvořené z cest v [Azure datastores](how-to-access-data.md) nebo adres URL veřejných webů. 

Vytvoření datových sad z [úložiště Azure DataStore](how-to-access-data.md) pomocí sady Python SDK:

1. Ověřte, že máte `contributor` nebo máte `owner` přístup k zaregistrovanému úložišti dat Azure.

2. Vytvořte datovou sadu pomocí odkazů na cesty v úložišti dat. Datovou sadu můžete vytvořit z více cest ve více úložišti dat. Neexistuje žádné omezení počtu souborů nebo velikosti dat, ze kterých lze vytvořit datovou sadu. 

> [!NOTE]
> Pro každou cestu k datům se do služby úložiště pošle pár požadavků, aby se zkontrolovalo, jestli odkazuje na soubor nebo složku. Tato režie může vést ke snížení výkonu nebo selhání. Datová sada odkazující na jednu složku s 1000 soubory uvnitř se považuje za odkazování na jednu cestu k datům. Pro zajištění optimálního výkonu doporučujeme vytvořit datovou sadu odkazující na méně než 100 cest v úložišti dat.

### <a name="create-a-filedataset"></a>Vytvoření datové sady

Použijte [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py&preserve-view=true#&preserve-view=truefrom-files-path--validate-true-) metodu `FileDatasetFactory` třídy pro načtení souborů v jakémkoli formátu a pro vytvoření neregistrované datové sady. 

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
> Nahrajte soubory z místního adresáře a v rámci jediné metody pomocí metody Public Preview Vytvořte datovou sadu typu [upload_directory ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset?view=azure-ml-py&preserve-view=true#methods). Tato metoda je funkcí [experimentální](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py&preserve-view=true#stable-vs-experimental) verze Preview a může se kdykoli změnit. 
> 
>  Tato metoda nahraje data do vašeho podkladového úložiště a výsledkem je, že se vám budou účtovat náklady na úložiště. 
### <a name="create-a-tabulardataset"></a>Vytvoření TabularDataset

Pomocí [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory) metody `TabularDatasetFactory` třídy můžete číst soubory ve formátu. csv nebo. TSV a vytvořit neregistrované TabularDataset. Pokud načítáte z více souborů, výsledky budou shrnuty do jednoho tabulkového znázornění. 

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

Ve výchozím nastavení se při vytváření TabularDataset automaticky odvozuje datové typy sloupců. Pokud odvozené typy neodpovídají vašim očekáváním, můžete určit typy sloupců pomocí následujícího kódu. Parametr `infer_column_type` je použitelný pouze pro datové sady vytvořené z oddělených souborů. [Přečtěte si další informace o podporovaných datových typech](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.datatype?view=azure-ml-py&preserve-view=true).


```Python
from azureml.core import Dataset
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|Indexovacím|PassengerId (ID pasažéra)|Zachované|Pclass|Název|Sex|Věk|SibSp|Parch|Ticket (Lístek)|Vozov|Posádk|Nastoupilo
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|Nepravda|3|Braund, Mr. Owen Harris|male (muž)|22,0|1|0|A/5 21171|7,2500||S
1|2|Ano|1|Cumings, paní Jan Bradley (Florencie Briggs th...|female (žena)|38,0|1|0|POČÍTAČ 17599|71,2833|C85|C
2|3|Ano|3|Heikkinen, chybíš. Laina|female (žena)|26,0|0|0|STON/O2. 3101282|7,9250||S

Chcete-li znovu použít a sdílet datové sady mezi experimenty v pracovním prostoru, [Zaregistrujte datovou sadu](#register-datasets).

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
> Vytvořte a zaregistrujte TabularDataset z paměti Spark nebo PANDAS dataframe s jedinou metodou s použitím metod Public Preview [`register_spark_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py&preserve-view=true#methods) a [`register_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py&preserve-view=true#methods) . Tyto metody registrace jsou [experimentální](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py&preserve-view=true#stable-vs-experimental) funkce ve verzi Preview a můžou se kdykoli změnit. 
> 
>  Tyto metody odesílají data do vašeho základního úložiště a v důsledku toho se účtují náklady na úložiště. 

## <a name="register-datasets"></a>Registrace datových sad

Pokud chcete dokončit proces vytváření, zaregistrujte své datové sady s pracovním prostorem. Použijte [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py&preserve-view=true#&preserve-view=trueregister-workspace--name--description-none--tags-none--create-new-version-false-) metodu k registraci datových sad s vaším pracovním prostorem, abyste je mohli sdílet s ostatními a znovu je použít ve vašem pracovním prostoru v rámci experimentů:

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

## <a name="create-datasets-using-azure-resource-manager"></a>Vytvoření datových sad pomocí Azure Resource Manager

[https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-dataset-create-*](https://github.com/Azure/azure-quickstart-templates/tree/master/)K vytvoření datových sad se dá použít několik šablon.

Informace o použití těchto šablon naleznete v tématu [použití šablony Azure Resource Manager k vytvoření pracovního prostoru pro Azure Machine Learning](how-to-create-workspace-template.md).


## <a name="create-datasets-with-azure-open-datasets"></a>Vytváření datových sad pomocí otevřených datových sad Azure

[Otevřené datové sady v Azure](https://azure.microsoft.com/services/open-datasets/) jsou spravované veřejné datové sady, které můžete použít k přidání funkcí specifických pro konkrétní scénář do řešení Machine Learning pro přesnější modely. Datové sady zahrnují data z veřejných domén pro počasí, sčítání, svátky, veřejné zabezpečení a umístění, které vám pomůžou naučit modely strojového učení a rozšířit prediktivní řešení. Otevřené datové sady jsou v cloudu na Microsoft Azure a jsou součástí sady SDK i sady Studio.

Naučte se vytvářet [Azure Machine Learning datové sady z Azure Open DataSets](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md). 

## <a name="train-with-datasets"></a>Trénování s datovými sadami

Využijte své datové sady ve zkušebních experimentech Machine Learning pro školení modelů ML. [Další informace o tom, jak naučit s datovými sadami](how-to-train-with-datasets.md)

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
