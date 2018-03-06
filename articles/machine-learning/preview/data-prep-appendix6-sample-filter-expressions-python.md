---
title: "Příklady filtr výrazů možné přípravy dat Azure Machine Learning | Microsoft Docs"
description: "Tento dokument obsahuje sadu příklady výrazů filtru možné pomocí Azure Machine Learning Příprava dat"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 973c56b8b2821c8e3d63161e6a233243639c74f4
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2018
---
# <a name="sample-of-filter-expressions-python"></a>Ukázka filtr výrazů (Python) 
Před čtením této příloha, přečtěte si [Python rozšiřitelnost přehled](data-prep-python-extensibility-overview.md).

## <a name="filter-with-equivalence-test"></a>Filtr s ekvivalenční testu
Filtrování v pouze řádky, kde hodnota Col2 (číselné) je větší než 4. 

```python
    row["Col2"] > 4
```

## <a name="filter-with-multiple-columns"></a>Filtr s více sloupci 
Filtrovat pouze pro řádky kde Sloupec1 obsahuje hodnotu **dobrý** a Col2 obsahuje (číselnou) hodnotu 1. 
```python
    row["Col1"] == 'Good' and row["Col2"] == 1
```

## <a name="test-filter-against-null"></a>Filtr test s hodnotou null
Filtrovat pouze pro řádky Sloupec1, kde má hodnotu null. 
```python
    pd.isnull(row["Col1"])
```
