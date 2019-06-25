---
title: Porovnat & reprodukovat dat pomocí snímků datové sady
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak porovnat data v čase a zajistit reprodukovatelnosti se snímky datové sady
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.date: 05/23/2019
ms.openlocfilehash: 525660be0f38c9458590e52cfcd575acb4cf5444
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66162060"
---
# <a name="compare-data-and-ensure-reproducibility-with-snapshots-preview"></a>Porovnání dat a zajištění reprodukovatelnost se snímky (preview)

V tomto článku zjistíte, jak vytvořit a spravovat snímky vašich [datové sady Azure Machine Learning](how-to-create-register-datasets.md) (datové sady), můžete zachytit nebo porovnání dat v čase. Datové sady usnadňují přístup a práce s daty v cloudu v různých scénářích.

**Datová sada snímky** uložení profilu (souhrnné statistiky) dat v době jeho vytvoření. Můžete také uložit kopii dat ve vašem snímku pro reprodukovatelnost.

>[!Important]
> Snímky se vám účtovat poplatky za úložiště. Ukládání kopie dat ve vašem snímku se vyžaduje ještě více úložiště. Použití [ `dataset.delete_snapshot()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#delete-snapshot-snapshot-name-) když jsou už nepotřebujete.

## <a name="when-to-use-snapshots"></a>Kdy použít snímky

Existují tři hlavní využití pro snímky:

+ **Ověření modelu**: Porovnání dat profilu různých snímků mezi tréninkových spuštění nebo proti produkční data.

+ **Model reprodukovatelnost**: Vaše výsledky reprodukujte voláním snímek, který obsahuje data během cvičení.

+ **Sledování dat v čase**: Zobrazit, jak datovou sadu se vyvinula tak [porovnání profily](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_snapshot.datasetsnapshot?view=azure-ml-py#compare-profiles-rhs-dataset-snapshot--include-columns-none--exclude-columns-none--histogram-compare-method--histogramcomparemethod-wasserstein--0--)
  
## <a name="prerequisites"></a>Požadavky

Pokud chcete vytvořit datovou sadu snímky, budete potřebovat registrované datové sady Azure Machine Learning. Pokud nemáte, přečtěte si téma [vytvořit datovou sadu a zaregistrujte](how-to-create-register-datasets.md).

## <a name="create-dataset-snapshots"></a>Vytvořit datovou sadu snímky

Chcete-li vytvořit snímek datovou sadu, použijte [ `dataset.create_snapshot()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?#create-snapshot-snapshot-name--compute-target-none--create-data-snapshot-false--target-datastore-none-) z azureml datových sad balíčku sady Azure Machine Learning SDK.

Ve výchozím nastavení, uloží snímek profilu (souhrnné statistiky) data na nejnovější [definici datové sady](how-to-manage-dataset-definitions.md) použít. Definice datové sady obsahuje záznam o jakékoli kroky transformace definované pro data. To je skvělý způsob, jak vytvořit reprodukovatelné vaše přípravy dat, pracovat.

Volitelně můžete použít také kopii dat ve vašem snímku přidáním `create_data_snapshot = True`.  Tato data můžou být užitečná pro reprodukovatelnost.

Tento příklad používá [ukázková crime data](https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv) datovou sadu s názvem `dataset_crime` vytvořené pomocí článku ["datové sady vytvořit a zaregistrovat"](how-to-create-register-datasets.md).

```Python
from azureml.core.workspace import Workspace
from azureml.core.dataset import Dataset
from azureml.data.dataset_snapshot import DatasetSnapshot
import datetime

# get existing workspace
workspace = Workspace.from_config()

# get existing, named dataset:
dataset = workspace.datasets['dataset_crime']

# assign name to snapshot
snapshot_name = 'snapshot_' + datetime.datetime.today().strftime('%Y%m%d%H%M%S')

# create snapshot to store profile from a remote environment
# and include copy of data
snapshot = dataset.create_snapshot(snapshot_name = snapshot_name,
                                   compute_target = remote_compute_target,
                                   create_data_snapshot = True)
```

Vzhledem k tomu, že asynchronní vytváření snímků, použít [ `wait_for_completion()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_snapshot.datasetsnapshot?view=azure-ml-py#wait-for-completion-show-output-true--status-update-frequency-10-) metoda monitorování procesu.

```Python
# monitor process every 10 seconds
snapshot.wait_for_completion(show_output=True, status_update_frequency=10)

# return snapshot just created
dataset.get_snapshot(snapshot_name)
```

Výstup:

```
Action status: Running
Action status: Running
Action status: Completed


DatasetSnapshot(Name: snapshot_20190411165420,
Dataset Id: 66cd0830-2f72-41e5-b677-d2d953f54821,
Workspace: "your_workspace_name",
Dataset definition version: 1,
Snapshot created date: 2019-05-11 17:24:00+00:00)
```

Použití [ `dataset.delete_snapshot()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#delete-snapshot-snapshot-name-) když jsou už nepotřebujete.

## <a name="find-snapshots"></a>Najít snímky

Pokud chcete načíst existující snímek, použijte [ `get_snapshot()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#get-snapshot-snapshot-name-).

Chcete-li získat seznam vašich uložené snímky dané datové sadě, použijte [ `get_all_snapshots()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#get-all-snapshots--).

```Python
# Get named snapshot for this dataset
dataset.get_snapshot(snapshot_name)

# Get all snapshots for this dataset
dataset.get_all_snapshots()
```

## <a name="get-data-and-profiles-from-snapshots"></a>Získat profilů a dat ze snímků

Každý snímek generuje datové sady, který obsahuje souhrnnou statistiku a dá vám možnost k uložení kopie vašich dat profilu.

### <a name="get-the-data-profile"></a>Získat profil dat

Použití [ `get_profile()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#get-profile-arguments-none--generate-if-not-exist-true--workspace-none--compute-target-none-) pro přístup k profilu vaše data.  K porovnání dat školení a produkčním prostředí, například můžete použít tento profil.

```Python
snapshot.get_profile()
```

||Type|Minimum|Maximum|Počet|Chybí počet|Nechybí počet|Procento chybějících|Počet chyb|Prázdný počet|0,1 % Quantile|1 % Quantile|5 % Quantile|25 % Quantile|50 % Quantile|75 % Quantile|95 % Quantile|99 % Quantile|99,9 % Quantile|střední hodnotu|Směrodatná odchylka|Odchylka|Zešikmení|Míra fluktuace
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
ID|FieldType.INTEGER|1.04986e + 07|1.05351e + 07|10.0|0.0|10.0|0.0|0.0|0.0|1.04986e + 07|1.04992e + 07|1.04986e + 07|1.05166e + 07|1.05209e + 07|1.05259e + 07|1.05351e + 07|1.05351e + 07|1.05351e + 07|1.05195e + 07|12302.7|1.51358e + 08|-0.495701|-1.02814
Číslo případu|FieldType.STRING|HZ239907|HZ278872|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Datum|FieldType.DATE|2016-04-04 23:56:00+00:00|2016-04-15 17:00:00+00:00|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Zablokovat|FieldType.STRING|004XX LOŽIT KILBOURN|113XX LOŽIT PSOUNŮ|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
IUCR|FieldType.INTEGER|810|1154|10.0|0.0|10.0|0.0|0.0|0.0|810|850|810|890|1136|1153|1154|1154|1154|1058.5|137.285|18847.2|-0.785501|-1.3543
Primární typ|FieldType.STRING|PODVODNÝ POSTUPEM|KRÁDEŽ|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Popis|FieldType.STRING|NEPLATNÉ KONTROLY|NAD 500 USD|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Popis umístění|FieldType.STRING||ŠKOLA, VEŘEJNÁ, VYTVÁŘENÍ|10.0|0.0|10.0|0.0|0.0|1.0||||||||||||||
Zadržení|FieldType.BOOLEAN|False|False|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Domácí|FieldType.BOOLEAN|False|False|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Signál|FieldType.INTEGER|531|2433|10.0|0.0|10.0|0.0|0.0|0.0|531|531|531|614|1318.5|1911|2433|2433|2433|1371.1|692.094|478994|0.105418|-1.60684
District|FieldType.INTEGER|5|24|10.0|0.0|10.0|0.0|0.0|0.0|5|5|5|6|13|19|24|24|24|13.5|6.94822|48.2778|0.0930109|-1.62325
Dál|FieldType.INTEGER|1|48|10.0|0.0|10.0|0.0|0.0|0.0|1|5|1|9|22.5|40|48|48|48|24.5|16.2635|264.5|0.173723|-1.51271
Oblast komunity|FieldType.INTEGER|4|77|10.0|0.0|10.0|0.0|0.0|0.0|4|8.5|4|24|37.5|71|77|77|77|41.2|26.6366|709.511|0.112157|-1.73379

### <a name="get-the-data-from-the-snapshot"></a>Získání dat ze snímku

Pokud chcete získat kopii data uložená v datové sadě snímků, generovat pandas DataFrame s [ `to_pandas_dataframe()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#to-pandas-dataframe--) metody.

Tato metoda selže, pokud nebyl požadován kopii dat během vytváření snímku.

```Python
snapshot.to_pandas_dataframe().head(3)
```

||ID|Číslo případu|Datum|Zablokovat|IUCR|Primární typ|Popis|Popis umístění|Zadržení|Domácí|...|Dál|Oblast komunity|Úřadu FBI kódu|Souřadnici x|Souřadnice Y|Rok|Aktualizace|Zeměpisná šířka|Zeměpisná délka|Umístění
-|--|-----------|----|-----|----|------------|-----------|--------------------|------|--------|---|----|--------------|--------|------------|------------|----|----------|--------|---------|--------
0|10498554|HZ239907|2016-04-04 23:56:00|007XX E 111TH ST|1153|PODVODNÝ POSTUPEM|KRÁDEŽE FINANČNÍ IDENTITY PŘES 300 USD|OSTATNÍ|False|False|...|9|50|11|1183356.0|1831503.0|2016|2016-05-11 15:48:00|41.692834|-87.604319|(41.692833841, -87.60431945)
1|10516598|HZ258664|2016-04-15 17:00:00|082XX LOŽIT MARSHFIELD|890|KRÁDEŽ|ZE SESTAVENÍ|MÍSTO POBYTU|False|False|...|21|71|6|1166776.0|1850053.0|2016|2016-05-12 15:48:00|41.744107|-87.664494|(41.744106973, -87.664494285)
2|10519196|HZ261252|2016-04-15 10:00:00|104XX LOŽIT SACRAMENTO|1154|PODVODNÝ POSTUPEM|KRÁDEŽE FINANČNÍ IDENTITY 300 USD A V ČÁSTI|MÍSTO POBYTU|False|False|...|19|74|11|NaN|NaN|2016|2016-05-12 15:50:00|NaN|NaN|

## <a name="next-steps"></a>Další postup

* Sada SDK [přehled](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) vzory návrhu a příklady použití.

* Příklad použití snímků datové sady, najdete v článku [ukázkové poznámkové bloky](https://aka.ms/dataset-tutorial).
