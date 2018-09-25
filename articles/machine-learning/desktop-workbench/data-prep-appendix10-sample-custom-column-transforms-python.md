---
title: Ukázky Pythonu pro odvození nového sloupce v rámci přípravy dat Azure Machine Learning | Dokumentace Microsoftu
description: Tento dokument obsahuje příklady kódu pro Python pro vytvoření nových sloupců v rámci přípravy dat Azure Machine Learning.
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
ms.openlocfilehash: 43a66efde2623a848065bbde2be4b56fd6c7fc8b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46969304"
---
# <a name="sample-of-custom-column-transforms-python"></a>Ukázka transformací sloupců vlastní (Python) 

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


Název této transformace v nabídce je **Add Column (Script)**.

Předtím, než se pustíte do čtení tohoto dodatku, přečtěte si [přehled rozšíření Python](data-prep-python-extensibility-overview.md).

## <a name="test-equivalence-and-replace-values"></a>Srovnávací test a nahraďte hodnoty 
Pokud je hodnota v sloupci Sloupec1 méně než 4, nový sloupec by měl mít hodnotu 1. Pokud je hodnota v sloupci Sloupec1 více než 4, nový sloupec má hodnotu 2. 

```python
    1 if row["Col1"] < 4 else 2
```
## <a name="current-date-and-time"></a>Aktuální datum a čas 

```python
    datetime.datetime.now()
```
## <a name="typecasting"></a>Změna typu 
```python
    float(row["Col1"]) / float(row["Col2"] - 1)
```
## <a name="evaluate-for-nullness"></a>Vyhodnotit hodnotu Null 
Obsahuje-li Sloupec1 s hodnotou null, pak označíte nový sloupec jako **chybný**. Pokud ne, označte ji jako **dobré.** 

```python
    'Bad' if pd.isnull(row["Col1"]) else 'Good'
```
## <a name="new-computed-column"></a>Nový počítaný sloupec 
```python
    np.log(row["Col1"])
```
## <a name="epoch-computation"></a>Výpočet epocha 
Počet sekund od epochy Unix (předpokládá Sloupec1 je již datum): 
```python
    row["Col1"] - datetime.datetime.utcfromtimestamp(0)).total_seconds()
```

## <a name="hash-a-column-value-into-a-new-column"></a>Hash – hodnota sloupce do nového sloupce
```python
    import hashlib
    hash(row["MyColumnToHashCol1"])

```




