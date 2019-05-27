---
title: Definice datové sady a správa verzí pomocí Azure ml datové sady
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak aktualizovat definice datové sady a spravovat životní cyklus definice
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: larryfr
ms.date: 05/02/2019
ms.openlocfilehash: e58ce156deaaad259ea7b74521bcf9b79afbd183
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66146203"
---
# <a name="update-and-manage-the-lifecycle-of-dataset-definitions"></a>Aktualizace a správa životního cyklu definicích datových sad

Zjistěte, jak aktualizovat a spravovat definice datové sady pomocí Azure Machine Learning, datové sady (preview).

Definice datové sady jsou recepty, umožňuje připravit vaše datové sady, patří mezi ně i odkaz na zdroj dat a transformace postupu. Jednu datovou sadu, může mít mnoho definic, a každá definice má svůj vlastní životní cyklus.

Následující příklad je příklad použití definice datové sady:

1. Můžete vytvářet služby machine learning kanál, který používá aktuální definici datové sady.
1. Se změnami podkladových dat.; přidali jsme nové atributy.
1. Můžete vytvořit novou verzi vaší definice, která přidává další transformací pro zpracování nových atributů.

V tomto příkladu i nadále využívat původní definice verze existujícího kanálu. Nová definice verze lze použít pro nové scénáře, jako je například trénování modelu nebo vytvořením kanálu.

## <a name="prerequisites"></a>Požadavky

Musíte mít předplatné Azure a pracovních prostorů zaregistrovat datové sady za účelem správy životního cyklu definicích datových sad.

Ukázkový soubor jako v příkladech v tomto dokumentu je k dispozici na [ https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv ](https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv).

## <a name="update-dataset-definitions"></a>Aktualizovat definice datové sady

Nejprve jsme se vytvořit a zaregistrovat pracovního prostoru datovou sadu.

```python
from azureml.core import Workspace, Datastore, Dataset

# change the configuration for workspace and Datastore
subscription_id = 'your subscription id'
resource_group = 'your resource group name'
workspace_name = 'your workspace name'
datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace(subscription_id, resource_group, workspace_name)

# get a Datastore that has already been created in the workspace
dstore = Datastore.get(workspace, datastore_name)

# create an in-memory Dataset from Datastore
datapath = dstore.path('data/src/crime.csv')
dataset = Dataset.from_delimited_files(datapath)

# register the Dataset with the workspace. if a Dataset with the same name already exists, retrieve it by turning `exist_ok = True`
dataset_name = 'crime dataset'
dataset = dataset.register(workspace = workspace, 
                 name = dataset_name, 
                 description = 'crime dataset for demo', 
                 tags = {'year':'2019', 'month':'Apr'},
                 exist_ok = True)
```

První definici datové sady (`version_id` = 1) se vytvoří při vytvoření datové sady. Při každé aktualizaci definice datové sady, nové version_id bude přiřazen k definici nejnovější.

```python
# get the Dataset from the workspace by name
dataset = workspace.datasets['dataset_name']

# get the latest Dataset definition
ds_def = dataset.get_definition()

# update the Dataset definition to select only the columns I'm interested in
ds_def = ds_def.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])

# with this update, the latest definition for dataset now has `version_id = 2`
dataset = dataset.update_definition(ds_def, 'select useful column')

dataset = workspace.datasets['dataset_name']
dataset.head(5)
```
<div>
<style scoped> .dataframe tbody tr th: pouze of-type {vertical-align: uprostřed;}

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>ID</th>
      <th>Zadržení</th>
      <th>Zeměpisná šířka</th>
      <th>Zeměpisná délka</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>10498554</td>
      <td>FALSE</td>
      <td>41.69283384</td>
      <td>-87.60431945</td>
    </tr>
    <tr>
      <th>1</th>
      <td>10516598</td>
      <td>FALSE</td>
      <td>41.74410697</td>
      <td>-87.66449429</td>
    </tr>
    <tr>
      <th>2</th>
      <td>10519196</td>
      <td>FALSE</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>3</th>
      <td>10519591</td>
      <td>FALSE</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>4</th>
      <td>10534446</td>
      <td>FALSE</td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>
</div>

Tato aktualizace definice nebude vymazat předchozí definice datové sady. Stále můžete používat a využívat předchozí definice.

```python
# specify `version_id` to get a previous definition 
ds_def_old = dataset.get_definition(version_id = 1)
ds_def_old.head(5)
```
<div>
<style scoped> .dataframe tbody tr th: pouze of-type {vertical-align: uprostřed;}

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>ID</th>
      <th>Číslo případu</th>
      <th>Datum</th>
      <th>Zablokovat</th>
      <th>IUCR</th>
      <th>Primární typ</th>
      <th>Popis</th>
      <th>Popis umístění</th>
      <th>Zadržení</th>
      <th>Domácí</th>
      <th>...</th>
      <th>Dál</th>
      <th>Oblast komunity</th>
      <th>Úřadu FBI kódu</th>
      <th>Souřadnici x</th>
      <th>Souřadnice Y</th>
      <th>Rok</th>
      <th>Aktualizace</th>
      <th>Zeměpisná šířka</th>
      <th>Zeměpisná délka</th>
      <th>Umístění</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>10498554</td>
      <td>HZ239907</td>
      <td>4/4/2016 23:56</td>
      <td>007XX E 111TH ST</td>
      <td>1153</td>
      <td>PODVODNÝ POSTUPEM</td>
      <td>KRÁDEŽE FINANČNÍ IDENTITY PŘES 300 USD</td>
      <td>OSTATNÍ</td>
      <td>FALSE</td>
      <td>FALSE</td>
      <td>...</td>
      <td>9</td>
      <td>50</td>
      <td>11</td>
      <td>1183356</td>
      <td>1831503</td>
      <td>2016</td>
      <td>5/11/2016 15:48</td>
      <td>41.69283384</td>
      <td>-87.60431945</td>
      <td>(41.692833841, -87.60431945)</td>
    </tr>
    <tr>
      <th>1</th>
      <td>10516598</td>
      <td>HZ258664</td>
      <td>4/15/2016 17:00</td>
      <td>082XX LOŽIT MARSHFIELD</td>
      <td>890</td>
      <td>KRÁDEŽ</td>
      <td>ZE SESTAVENÍ</td>
      <td>MÍSTO POBYTU</td>
      <td>FALSE</td>
      <td>FALSE</td>
      <td>...</td>
      <td>21</td>
      <td>71</td>
      <td>6</td>
      <td>1166776</td>
      <td>1850053</td>
      <td>2016</td>
      <td>5/12/2016 15:48</td>
      <td>41.74410697</td>
      <td>-87.66449429</td>
      <td>(41.744106973, -87.664494285)</td>
    </tr>
    <tr>
      <th>2</th>
      <td>10519196</td>
      <td>HZ261252</td>
      <td>4/15/2016 10:00</td>
      <td>104XX LOŽIT SACRAMENTO</td>
      <td>1154</td>
      <td>PODVODNÝ POSTUPEM</td>
      <td>KRÁDEŽE FINANČNÍ IDENTITY 300 USD A V ČÁSTI</td>
      <td>MÍSTO POBYTU</td>
      <td>FALSE</td>
      <td>FALSE</td>
      <td>...</td>
      <td>19</td>
      <td>74</td>
      <td>11</td>
      <td></td>
      <td></td>
      <td>2016</td>
      <td>5/12/2016 15:50</td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>3</th>
      <td>10519591</td>
      <td>HZ261534</td>
      <td>4/15/2016 9:00</td>
      <td>113XX LOŽIT PSOUNŮ</td>
      <td>1120</td>
      <td>PODVODNÝ POSTUPEM</td>
      <td>PADĚLÁNÍ</td>
      <td>MÍSTO POBYTU</td>
      <td>FALSE</td>
      <td>FALSE</td>
      <td>...</td>
      <td>9</td>
      <td>49</td>
      <td>10</td>
      <td></td>
      <td></td>
      <td>2016</td>
      <td>5/13/2016 15:51</td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>4</th>
      <td>10534446</td>
      <td>HZ277630</td>
      <td>4/15/2016 10:00</td>
      <td>055XX N KEDZIE AVE</td>
      <td>890</td>
      <td>KRÁDEŽ</td>
      <td>ZE SESTAVENÍ</td>
      <td>ŠKOLA, VEŘEJNÁ, VYTVÁŘENÍ</td>
      <td>FALSE</td>
      <td>FALSE</td>
      <td>...</td>
      <td>40</td>
      <td>13</td>
      <td>6</td>
      <td></td>
      <td></td>
      <td>2016</td>
      <td>5/25/2016 15:59</td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>
</div>

Jak je vidět z výsledku, první verzi definici datové sady stále udržuje všechny sloupce z crime dat bez ohledu na novější aktualizace. Například pokud máte machine learning model v současné době využívání první verzi datovou sadu a pomocí `Date` sloupce z datové sady jako jedna z funkcí, nebude narazíte na jakékoli chyby s nejnovější aktualizací definic, zůstanou pouze `ID`, `Arrest`, `Latitude`, `Longitude` sloupce z dat podvody.

## <a name="how-to-access-dataset-definitions"></a>Jak získat přístup k definicích datových sad

Chcete-li získat seznam všech definic, použijte `get_definitions()`. Chcete-li získat konkrétní verzi definice, použijte `get_definition()`. Následující příklad ukazuje načítá se seznam všech definic a potom načtením verze 1:

```python
# list all definitions for the dataset
dataset.get_definitions()
# get version ID 1
dataset.get_definition(version_id=1)
```

Výstup `get_definitions()` je podobné jako v následujícím příkladu:

```text
{'2': VersionID: 2, State: active, Created: 2019-04-19 16:43:52.439890+00:00, Modified: 2019-04-19 16:43:52.439890+00:00, Notes: select useful column,
 '1': VersionID: 1, State: active, Created: 2019-04-19 16:39:14.112046+00:00, Modified: 2019-04-19 16:44:12.912663+00:00, Notes: None}
```

Po načtení definicí, můžete ho pomocí vašich modelů machine learning nebo v kanálu strojového učení.

## <a name="manage-lifecycle-of-dataset-definitions"></a>Správa životního cyklu definicích datových sad

Životní cyklus každá definice datové sady můžete spravovat nezávisle na sobě. Existují tři fáze životního cyklu: aktivní, zastaralé nebo archivovat.

### <a name="active"></a>Aktivní

Když se vytvoří nová definice datové sady, je ve výchozím nastavení aktivní. 

### <a name="deprecate"></a>Vyřazení

Definice datové sady můžete přestanou při použití už nedoporučuje, a můžou nahradit aktuální soubor je k dispozici. Při použití zastaralé definice datové sady v machine learning kanály získá vrátil zprávu s upozorněním, ale nebude blokovat spuštění.

Při ukončení podpory pro definici datové sady, zadejte ID datové sady a ID verze definice datové sady pro nahrazení definice. Tyto informace jsou uloženy a použita v upozornění při pokusu o využívání zastaralé definice datové sady.

```python
# get the definition that you want to deprecate
ds_def = dataset.get_definition(version_id = 1)

# deprecate it by providing the information for the replacement definition, which is recommended to be used in machine learning scenarios
ds_def.deprecate(deprecate_by_dataset_id=dataset.id, deprecated_by_definition_version=2)
```

### <a name="archive"></a>Archiv

Definice datové sady je možné archivovat, pokud definice neměl používat z jakéhokoli důvodu (například podkladových dat již není k dispozici). Při definici Archivovaná datová sada se používá v machine learning kanály, provádění se zablokuje s chybou.

```python
# archive the definition with `version_id = 1`
ds_def = dataset.get_definition(version_id = 1)
ds_def.archive()
```

### <a name="reactivate"></a>Opětovná aktivace

Snadno můžete znovu aktivovat všechny nepoužívané nebo archivované definici datové sady.

```python
# reactivate Dataset definition with `version_id =1` which was archived in the previous step
ds_def = dataset.get_definition(version_id = 1)
ds_def.reactivate()
```

## <a name="next-steps"></a>Další postup

Další informace o práci s datovými sadami, naleznete v tématu [vytvořit a zaregistrovat datové sady Azure Machine Learning](how-to-create-register-datasets.md).

Příklad použití datových sad, najdete v článku [ukázkové poznámkové bloky](https://aka.ms/dataset-tutorial).
