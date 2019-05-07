---
title: Vytvoření a registrace datové sady s pracovním prostorem
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
ms.date: 05/02/19
ms.openlocfilehash: 65a861c647c2dc92e416fa356075821aa5060042
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65205045"
---
# <a name="create-and-register-azure-machine-learning-datasets-preview"></a>Vytvoření a registrace Azure Machine Learning, datové sady (Preview)

V tomto článku se dozvíte, Azure Machine Learning pracovními postupy k vytvoření a registrace datové sady a jak k nim přístup pro opakované použití napříč místními a vzdálenými experimentů.

Sady Azure Machine Learning dat (preview) usnadňují přístup a práce s daty. Datové sady spravovat data v různých scénářích, jako je například, model školení a vytváření kanálu. Použití [SDK služby Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py), můžete pracovat s daty v oblíbených formátů, přístup k podkladové úložiště, prozkoumejte a připravit data, spravovat životní cyklus definice různé datové sady a porovnat mezi datové sady použité v školení a v produkčním prostředí.

## <a name="prerequisites"></a>Požadavky

K vytvoření a registrace datové sady budete potřebovat:

* Předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si bezplatný účet, před zahájením. Zkuste [bezplatné nebo placené verzi aplikace služby Azure Machine Learning](https://aka.ms/AMLFree) ještě dnes.

* Pracovní prostor služby Azure Machine Learning. Zobrazit [vytvořit pracovní prostor služby Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace).

* Azure Machine Learning sady SDK pro Python. Pro instalaci nebo aktualizaci na nejnovější verzi sady SDK, naleznete v tématu [nainstalovat nebo aktualizovat sadu SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

## <a name="create-datasets-from-local-files"></a>Vytvoření datové sady z místních souborů

Načíst soubory z místního počítače zadáním cesty souboru nebo složky se [ `auto_read_files()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#auto-read-files-path--include-path-false-) metodu z `Dataset` třídy.  Tato metoda provede následující kroky, aniž by bylo potřeba zadat typ souboru nebo Analýza argumentů:

* Odvození a nastavení oddělovač.
* Přeskakuje se prázdné záznamy v horní části souboru.
* Odvození a nastavení jeho záhlaví.
* Odvození a převádění datových typů sloupce.

```Python
from azureml.core.dataset import Dataset

dataset = Dataset.auto_read_files('./data/crime.csv')
```

Případně použijte funkce konkrétního souboru Pokud chcete explicitně kontrolovat parsování souboru. Datové sady SDK v současné době podporuje [oddělených](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-delimited-files-path--separator------header--promoteheadersbehavior-allfileshavesameheaders--3---encoding--fileencoding-utf8--0---quoting-false--infer-column-types-true--skip-rows-0--skip-mode--skiplinesbehavior-norows--0---comment-none--include-path-false--archive-options-none-), [Excel](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-excel-files-path--sheet-name-none--use-column-headers-false--skip-rows-0--include-path-false--infer-column-types-true-), [Parquet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-parquet-files-path--include-path-false-), [binární](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-binary-files-path-), a [json](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-json-files-path--encoding--fileencoding-utf8--0---flatten-nested-arrays-false--include-path-false-) formáty souborů.

## <a name="create-datasets-from-azure-datastores"></a>Vytvoření datové sady z úložiště Azure

Pokud chcete vytvořit datové sady ze úložiště Azure, nezapomeňte na následující:

* Zkontrolujte, že jste Přispěvatel nebo vlastník přístup k registrované úložiště dat Azure.

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

||ID|Číslo případu|Datum|Zablokovat|IUCR|Primární typ|Popis|Popis umístění|Zadržení|Domácí|...|Dál|Oblast komunity|Úřadu FBI kódu|Souřadnici x|Souřadnice Y|Rok|Aktualizace|Zeměpisná šířka|Zeměpisná délka|Umístění|
|--|--|---|---|---|---|----|------|-------|------|-----|---|----|----|-----|-----|------|----|-----|----|----|-----
|0|10498554|HZ239907|4/4/2016 23:56|007XX E 111TH ST|1153|PODVODNÝ POSTUPEM|KRÁDEŽE FINANČNÍ IDENTITY PŘES 300 USD|OSTATNÍ|FALSE|FALSE|...|9|50|11|1183356|1831503|2016|5/11/2016 15:48|41.69283384|-87.60431945|(41.692833841, -87.60431945)|
1|10516598|HZ258664|4/15/2016 17:00|082XX LOŽIT MARSHFIELD|890|KRÁDEŽ| ZE SESTAVENÍ|MÍSTO POBYTU|FALSE|FALSE|...|21|71|6|1166776|1850053|2016|5/12/2016 15:48|41.74410697|-87.66449429|(41.744106973, -87.664494285)
2|10519196|HZ261252|4/15/2016 10:00|104XX LOŽIT SACRAMENTO|1154|PODVODNÝ POSTUPEM|KRÁDEŽE FINANČNÍ IDENTITY 300 USD A V ČÁSTI|MÍSTO POBYTU|FALSE|FALSE|...|19|74|11|||2016|5/12/2016 15:50
3|10519591|HZ261534|4/15/2016 9:00|113XX LOŽIT PSOUNŮ|1120|PODVODNÝ POSTUPEM|PADĚLÁNÍ|MÍSTO POBYTU|FALSE|FALSE|...|9|49|10|||2016|5/13/2016 15:51
4|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|KRÁDEŽ|ZE SESTAVENÍ|ŠKOLA, VEŘEJNÁ, VYTVÁŘENÍ|FALSE|FALSE|...|40|13|6|||2016|5/25/2016 15:59|

## <a name="register-your-datasets-with-workspace"></a>Registrace datových sad s pracovním prostorem

Použití [ `register()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-) metoda k registraci datových sad do pracovního prostoru pro sdílení a opětovné použití v rámci vaší organizace a napříč různými experimentů.

```Python
dataset = dataset.register(workspace = workspace,
                           name = 'dataset_crime',

                           description = 'Training data',
                           exist_ok = False
                           )
```

>[!NOTE]
> Výchozí nastavení parametrů pro `register()` je `exist_ok = False`. Pokud se pokusíte zaregistrovat datovou sadu se stejným názvem beze změny tohoto nastavení dojde k chybě.

`register()` Metoda vrátí už registrované datové sady s nastavením parametru `exist_ok = True`.

```Python
dataset = dataset.register(workspace = workspace,
                           name = 'dataset_crime',
```

Použití `list()` zobrazíte všechny registrované datové sady ve vašem pracovním prostoru.

```Python
Dataset.list(workspace_name)
```

Předchozí kód za následek následující:

```Python
[Dataset(Name: dataset_crime,
         Workspace: workspace_name)]
```

## <a name="access-datasets-in-workspace"></a>Přístup k datovým sadám v pracovním prostoru

Registrované datové sady jsou přístupná a použitelné místně, vzdáleně a na výpočetních clusterů, jako jsou výpočetní výkon Azure Machine Learning. Chcete-li znovu použít registrované datové sady přes experimenty a výpočetní prostředí, použijte následující kód k získání váš pracovní prostor a registrované datové sady podle názvu.

```Python
workspace = Workspace.from_config()

dataset = workspace.datasets['dataset_crime']
```

## <a name="next-steps"></a>Další postup

* [Prozkoumejte a příprava datových sad](how-to-explore-prepare-data.md).
* [Správa životního cyklu definicích datových sad](how-to-manage-dataset-definitions.md).
* Příklad použití datových sad, najdete v článku [ukázkové poznámkové bloky](https://aka.ms/dataset-tutorial).
