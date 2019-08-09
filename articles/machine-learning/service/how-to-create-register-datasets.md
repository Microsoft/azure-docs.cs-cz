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
ms.date: 05/21/2019
ms.openlocfilehash: c5b423fca3e0ec116fceefb6867189f4f8413b96
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68856091"
---
# <a name="create-and-access-datasets-preview-in-azure-machine-learning"></a>Vytvoření a přístup k datovým sadám (Preview) v Azure Machine Learning

V tomto článku se dozvíte, jak vytvořit Azure Machine Learning datové sady (Preview) a jak přistupovat k datům z místních nebo vzdálených experimentů.

S Azure Machine Learningmi datovými sadami můžete: 

* **Uchování jedné kopie dat ve vašem úložišti, na** které odkazují datové sady

* **Analyzovat data** prostřednictvím analýzy dat průzkumného testování 

* **Snadný přístup k datům během školení k modelu** bez obav o připojovací řetězec nebo cestu k datům

* **Sdílení dat & spolupráci** s ostatními uživateli

## <a name="prerequisites"></a>Požadavky

K vytváření a práci s datovými sadami potřebujete:

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze služby Azure Machine Learning](https://aka.ms/AMLFree) dnes

* [Pracovní prostor služby Azure Machine Learning](how-to-manage-workspace.md)

* [Nainstalovaná sada Azure Machine Learning SDK pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), která zahrnuje balíček AzureML-DataSet Sets.

> [!Note]
> Některé třídy DataSet (Preview) mají závislosti na balíčku [AzureML-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) (GA). Pro uživatele se systémem Linux jsou tyto třídy podporovány pouze v následujících distribucích:  Red Hat Enterprise Linux, Ubuntu, Fedora a CentOS.

## <a name="data-formats"></a>Formáty dat

Datovou sadu Azure Machine Learning můžete vytvořit z následujících formátů:
+ [oddělených](/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-delimited-files-path--separator------header--promoteheadersbehavior-all-files-have-same-headers--3---encoding--fileencoding-utf8--0---quoting-false--infer-column-types-true--skip-rows-0--skip-mode--skiplinesbehavior-no-rows--0---comment-none--include-path-false--archive-options-none--partition-format-none-)
+ [json](/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-json-files-path--encoding--fileencoding-utf8--0---flatten-nested-arrays-false--include-path-false--partition-format-none-)
+ [Excel](/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-excel-files-path--sheet-name-none--use-column-headers-false--skip-rows-0--include-path-false--infer-column-types-true--partition-format-none-)
+ [Parquet](/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-parquet-files-path--include-path-false--partition-format-none-)
+ [PANDAS – datový rámec](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-pandas-dataframe-dataframe--path-none--in-memory-false-)
+ [Dotaz SQL](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-sql-query-data-source--query-)
+ [tvaru](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-binary-files-path-)

## <a name="create-datasets"></a>Vytvoření datových sad 

Vytvořením datové sady vytvoříte odkaz na umístění zdroje dat společně s kopií jeho metadat. Data zůstanou ve svém stávajícím umístění, takže se neúčtují žádné dodatečné náklady na úložiště.

### <a name="create-from-local-files"></a>Vytvořit z místních souborů

Načtěte soubory z místního počítače zadáním cesty k souboru nebo složce pomocí [`auto_read_files()`](/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#auto-read-files-path--include-path-false--partition-format-none-) metody `Dataset` z třídy.  Tato metoda provádí následující kroky bez nutnosti zadat typ souboru nebo argumenty analýzy:

* Odvození a nastavení oddělovače.
* Vynechávání prázdných záznamů v horní části souboru.
* Odvození a nastavení řádku záhlaví.
* Odvození a převod datových typů sloupce.

```Python
from azureml.core.dataset import Dataset

dataset = Dataset.auto_read_files('./data/crime.csv')
```

Případně použijte funkce specifické pro soubor k explicitnímu řízení analýzy souboru. 


### <a name="create-from-azure-datastores"></a>Vytvoření z úložiště Azure DataStore

Vytvoření datových sad z [úložiště Azure DataStore](how-to-access-data.md):

* Ověřte, jestli `contributor` máte `owner` nebo máte přístup k zaregistrovanému úložišti dat Azure.

* Vytvořit datovou sadu odkazem na cestu v úložišti dat 

```Python
from azureml.core.workspace import Workspace
from azureml.core.datastore import Datastore
from azureml.core.dataset import Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()

# retrieve an existing datastore in the workspace by name
dstore = Datastore.get(workspace, datastore_name)
```

Použijte metodu pro čtení souborů s oddělovači z DataReference a vytvořte neregistrovanou datovou sadu. [](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py) `from_delimited_files()`

```Python
# create an in-memory Dataset on your local machine
dataset = Dataset.from_delimited_files(dstore.path('data/src/crime.csv'))

# returns the first 5 rows of the Dataset as a pandas Dataframe.
dataset.head(5)
```

## <a name="register-datasets"></a>Registrace datových sad

Pokud chcete dokončit proces vytváření, zaregistrujte své datové sady s pracovním prostorem:

[`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-) Použijte metodu k registraci datových sad do vašeho pracovního prostoru, aby je bylo možné sdílet s ostatními a opakovaně používat v různých experimentech.

```Python
dataset = dataset.register(workspace = workspace,
                           name = 'dataset_crime',
                           description = 'Training data',
                           exist_ok = False
                           )
```

>[!NOTE]
> Pokud `exist_ok = False` (výchozí) a pokusíte se zaregistrovat datovou sadu se stejným názvem jako jiný, dojde k chybě. Nastavte na `True` přepsat existující.

## <a name="access-data-in-datasets"></a>Přístup k datům v datových sadách

Registrované datové sady jsou přístupné místně a vzdáleně na výpočetních clusterech, jako je Azure Machine Learning Compute. Chcete-li získat přístup k zadané datové sadě mezi experimenty, použijte následující kód k získání pracovního prostoru a registrované datové sady podle názvu.

```Python
workspace = Workspace.from_config()

# See list of datasets registered in workspace.
print(Dataset.list(workspace))

# Get dataset by name
dataset = Dataset.get(workspace, 'dataset_crime')

# Load data into pandas DataFrame
dataset.to_pandas_dataframe()
```

## <a name="next-steps"></a>Další postup

* [Prozkoumejte a připravte datové sady](how-to-explore-prepare-data.md).
* Příklad použití datových sad najdete v ukázkových poznámkových [blocích](https://aka.ms/dataset-tutorial).
