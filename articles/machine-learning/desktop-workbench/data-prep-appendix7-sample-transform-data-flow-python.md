---
title: Příklad transformace dat toku transformace možné pomocí Azure Machine Learning Data přípravy | Microsoft Docs
description: Tento dokument obsahuje sadu příklady transformace datového toku transformací možné pomocí Azure Machine Learning Příprava dat
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: aa213a3b1a8949f0fca5e4bbb7ec5a6a775ae6ec
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="sample-of-custom-data-flow-transforms-python"></a>Ukázka vlastních dat toku transformací (Python) 
Je název transformace v nabídce **transformace toku dat (skript)**. Před čtením této příloha, přečtěte si [Python rozšiřitelnost přehled](data-prep-python-extensibility-overview.md).

## <a name="transform-frame"></a>Transformace rámce
### <a name="create-a-new-column-dynamically"></a>Vytvoří nový sloupec, dynamicky 
Dynamicky vytvoří sloupec (**city2**) a sloučí více různých verzích Brno na jednu z existující sloupec města.
```python
    df.loc[(df['city'] == 'San Francisco') | (df['city'] == 'SF') | (df['city'] == 'S.F.') | (df['city'] == 'SAN FRANCISCO'), 'city2'] = 'San Francisco'
```

### <a name="add-new-aggregates"></a>Přidat nové agregace
Vytvoří nový snímek se první a poslední agregace, počítaného sloupce skóre. Tyto jsou seskupené podle **risk_category** sloupce.
```python
    df = df.groupby(['risk_category'])['Score'].agg(['first','last'])
```
### <a name="winsorize-a-column"></a>Winsorize sloupec 
Reformulates data podle vzorce pro snížení zatížení odlehlé hodnoty ve sloupci.
```python
    import scipy.stats as stats
    df['Last Order'] = stats.mstats.winsorize(df['Last Order'].values, limits=0.4)
```

## <a name="transform-data-flow"></a>Transformace toku dat
### <a name="fill-down"></a>Vyplnit dolů 

Výplně dolů vyžaduje dva transformací. Předpokládá data, která vypadá jako v následující tabulce:

|Stav         |Město       |
|--------------|-----------|
|Washington    |Redmond    |
|              |Bellevue   |
|              |Issaquah   |
|              |Seattle    |
|Kalifornie    |Los Angeles|
|              |Síť San Diego  |
|              |San Jose   |
|Texas         |Dallas     |
|              |Síť SAN Antonio|
|              |Houston    |

1. Vytvořte transformaci "Přidat sloupec (skript)" pomocí následujícího kódu:
```python
    row['State'] if len(row['State']) > 0 else None
```

2. Vytvořte transformace "Transformace datový tok (skript)", který obsahuje následující kód:
```python
    df = df.fillna( method='pad')
```

Data se nyní vypadá jako v následující tabulce:

|Stav         |Nový stav         |Město       |
|--------------|--------------|-----------|
|Washington    |Washington    |Redmond    |
|              |Washington    |Bellevue   |
|              |Washington    |Issaquah   |
|              |Washington    |Seattle    |
|Kalifornie    |Kalifornie    |Los Angeles|
|              |Kalifornie    |Síť San Diego  |
|              |Kalifornie    |San Jose   |
|Texas         |Texas         |Dallas     |
|              |Texas         |Síť SAN Antonio|
|              |Texas         |Houston    |


### <a name="min-max-normalization"></a>Min-max normalizaci
```python
    df["NewCol"] = (df["Col1"]-df["Col1"].mean())/df["Col1"].std()
```