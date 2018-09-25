---
title: Tok dat transformace příklad transformace možné s přípravou dat aplikace Azure Machine Learning | Dokumentace Microsoftu
description: Tento dokument obsahuje sadu příkladů transformací toku dat transformace s přípravou dat Azure Machine Learning
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 82295e412c70065ff8ce3a686aec790614f39f2e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46947196"
---
# <a name="sample-of-custom-data-flow-transforms-python"></a>Ukázka vlastních transformací toku dat (Python) 

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


Je název transformace v nabídce **Transform Dataflow (Script)**. Předtím, než se pustíte do čtení tohoto dodatku, přečtěte si [přehled rozšíření Python](data-prep-python-extensibility-overview.md).

## <a name="transform-frame"></a>Transformace rámce
### <a name="create-a-new-column-dynamically"></a>Dynamicky vytvoří nový sloupec 
Dynamicky vytvoří sloupec (**city2**) a sloučí více různých verzích kalifornském San Franciscu k jednomu z existující sloupec Město.
```python
    df.loc[(df['city'] == 'San Francisco') | (df['city'] == 'SF') | (df['city'] == 'S.F.') | (df['city'] == 'SAN FRANCISCO'), 'city2'] = 'San Francisco'
```

### <a name="add-new-aggregates"></a>Přidat nové agregace
Vytvoří nový rámec s prvním a posledním agregace vypočítaného sloupce skóre. Ty jsou seskupeny podle **risk_category** sloupce.
```python
    df = df.groupby(['risk_category'])['Score'].agg(['first','last'])
```
### <a name="winsorize-a-column"></a>Winsorize sloupec 
Reformulates data pro splnění vzorec pro snížení odlehlé hodnoty ve sloupci.
```python
    import scipy.stats as stats
    df['Last Order'] = stats.mstats.winsorize(df['Last Order'].values, limits=0.4)
```

## <a name="transform-data-flow"></a>Transformace toku dat
### <a name="fill-down"></a>Vyplnit dolů 

Vyplnit dolů vyžaduje dva transformací. Předpokládá data, která vypadá jako v následující tabulce:

|Stav         |Město       |
|--------------|-----------|
|Washington    |Redmond    |
|              |Bellevue   |
|              |Issaquah   |
|              |Seattle    |
|Kalifornie    |Los Angeles|
|              |Síť SAN Diegu  |
|              |Síť San Jose   |
|Texas         |Dallas     |
|              |Síť SAN Antonio|
|              |Houstonu    |

1. Vytvoření transformace "Přidat sloupec (skript)" pomocí následujícího kódu:
```python
    row['State'] if len(row['State']) > 0 else None
```

2. Vytvoření transformace "Transformace datového toku (skript)", který obsahuje následující kód:
```python
    df = df.fillna( method='pad')
```

Data teď vypadá podobně jako v následující tabulce:

|Stav         |Nový stav         |Město       |
|--------------|--------------|-----------|
|Washington    |Washington    |Redmond    |
|              |Washington    |Bellevue   |
|              |Washington    |Issaquah   |
|              |Washington    |Seattle    |
|Kalifornie    |Kalifornie    |Los Angeles|
|              |Kalifornie    |Síť SAN Diegu  |
|              |Kalifornie    |Síť San Jose   |
|Texas         |Texas         |Dallas     |
|              |Texas         |Síť SAN Antonio|
|              |Texas         |Houstonu    |


### <a name="min-max-normalization"></a>Normalizace maximálních
```python
    df["NewCol"] = (df["Col1"]-df["Col1"].mean())/df["Col1"].std()
```