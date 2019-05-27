---
title: Vytvoření datových sad pro přístup k datům s azureml datovými sadami
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak k vytvoření datových sad z různých zdrojů a registraci datových sad v pracovním prostoru
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/21/2019
ms.openlocfilehash: 949468dfe26b076b5c5cf5cab8bbdc2038c7bd2a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66165882"
---
# <a name="create-and-access-datasets-preview-in-azure-machine-learning"></a>Vytváření a přístup k datové sady (Preview) ve službě Azure Machine Learning

V tomto článku se dozvíte, jak vytvářet datové sady Azure Machine Learning (preview) a přístup k datům z místních a vzdálených experimentů.

Spravované datové sady vám umožní: 
* **Snadný přístup k datům během cvičení modelu** bez opětovné připojení k podkladové úložiště

* **Zajistit konzistenci dat & reprodukovatelnost** používat stejný ukazatel na experimenty: poznámkových bloků, automatizované ml, kanály, vizuální rozhraní

* **Sdílení dat a spolupráci** s jinými uživateli

* **Zkoumání dat** & Správa životního cyklu snímky dat a verze

* **Porovnání dat** do školení do produkčního prostředí


## <a name="prerequisites"></a>Požadavky

K vytváření a práci s datovými sadami, budete potřebovat:

* Předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si bezplatný účet, před zahájením. Zkuste [bezplatné nebo placené verzi aplikace služby Azure Machine Learning](https://aka.ms/AMLFree) ještě dnes.

* [Pracovního prostoru služby Azure Machine Learning službu pracovního prostoru Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace)

* [Azure Machine Learning SDK pro Python nainstalován](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), která obsahuje balíček azureml datové sady.

> [!Note]
> Některé třídy datové sady (preview) mají závislosti [azureml přípravy](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) balíčku (GA). Tyto třídy pro uživatele systému Linux jsou podporovány pouze v následujících distribucích:  Red Hat Enterprise Linux, Ubuntu, Fedora a CentOS.

## <a name="data-formats"></a>Formáty dat

Datová sada služby Azure Machine Learning můžete vytvořit z následující data:
+ [s oddělovači](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-delimited-files-path--separator------header--promoteheadersbehavior-allfileshavesameheaders--3---encoding--fileencoding-utf8--0---quoting-false--infer-column-types-true--skip-rows-0--skip-mode--skiplinesbehavior-norows--0---comment-none--include-path-false--archive-options-none-)
+ [Binární](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-binary-files-path-)
+ [json](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-json-files-path--encoding--fileencoding-utf8--0---flatten-nested-arrays-false--include-path-false-)
+ [Excel](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-excel-files-path--sheet-name-none--use-column-headers-false--skip-rows-0--include-path-false--infer-column-types-true-)
+ [Parquet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-parquet-files-path--include-path-false-)
+ [Azure SQL Database](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-sql-query-data-source--query-)
+ [Azure Data Lake Obecné. 1](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-sql-query-data-source--query-)

## <a name="create-datasets"></a>Vytvoření datových sad 

Můžete pracovat se sadami pomocí Azure ml datových sad balíčku v [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) a konkrétně [ `Dataset` třídy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py).

### <a name="create-from-local-files"></a>Vytvoření z místních souborů

Načíst soubory z místního počítače zadáním cesty souboru nebo složky se [ `auto_read_files()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#auto-read-files-path--include-path-false-) metodu z `Dataset` třídy.  Tato metoda provede následující kroky, aniž by bylo potřeba zadat typ souboru nebo Analýza argumentů:

* Odvození a nastavení oddělovač.
* Přeskakuje se prázdné záznamy v horní části souboru.
* Odvození a nastavení jeho záhlaví.
* Odvození a převádění datových typů sloupce.

```Python
from azureml.core.dataset import Dataset

dataset = Dataset.auto_read_files('./data/crime.csv')
```

Případně použijte funkce konkrétního souboru Pokud chcete explicitně kontrolovat parsování souboru. 


### <a name="create-from-azure-datastores"></a>Vytvoření z úložiště Azure

Vytvoření datové sady ze úložiště Azure:

* Ověřte, že máte `contributor` nebo `owner` přístup k registrované úložiště dat Azure.

* Import [ `Workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) a [ `Datastore` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#definition) a `Dataset` balíčky ze sady SDK.

```Python
from azureml.core.workspace import Workspace
from azureml.core.datastore import Datastore
from azureml.core.dataset import Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
```

 `get()` Metoda načte stávající úložiště dat v pracovním prostoru.

```
dstore = Datastore.get(workspace, datastore_name)
```

Použití `from_delimited_files()` metodu pro načtení souborů s oddělovači a zrušit datovou sadu vytvořit.

```Python
# create an in-memory Dataset on your local machine
datapath = dstore.path('data/src/crime.csv')
dataset = Dataset.from_delimited_files(datapath)

# returns the first 5 rows of the Dataset as a pandas Dataframe.
dataset.head(5)
```

## <a name="register-datasets"></a>Registrace datových sad

Dokončete proces vytváření zaregistrujte vaše datové sady s pracovním prostorem:

Použití [ `register()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-) metody pro registraci datových sad do pracovního prostoru, aby bylo možné sdílet s ostatními a opětovně použít napříč různými experimentů.

```Python
dataset = dataset.register(workspace = workspace,
                           name = 'dataset_crime',
                           description = 'Training data',
                           exist_ok = False
                           )
```

>[!NOTE]
> Pokud `exist_ok = False` (výchozí), a při pokusu o registraci datovou sadu se stejným názvem jako jiný, dojde k chybě. Nastavte na `True` přepsat existující.

## <a name="access-data-in-datasets"></a>Přístup k datům v datových sadách

Registrované datové sady jsou přístupná a použitelné místně, vzdáleně a na výpočetních clusterů, jako jsou výpočetní výkon Azure Machine Learning. Chcete-li znovu použít registrované datové sady přes experimenty a výpočetní prostředí, použijte následující kód k získání váš pracovní prostor a registrované datové sady podle názvu.

```Python
workspace = Workspace.from_config()

# See list of datasets registered in workspace.
Dataset.list(workspace)

# Get dataset by name
dataset = workspace.datasets['dataset_crime']
```

## <a name="next-steps"></a>Další postup

* [Prozkoumejte a příprava datových sad](how-to-explore-prepare-data.md).
* [Správa životního cyklu definicích datových sad](how-to-manage-dataset-definitions.md).
* Příklad použití datových sad, najdete v článku [ukázkové poznámkové bloky](https://aka.ms/dataset-tutorial).
