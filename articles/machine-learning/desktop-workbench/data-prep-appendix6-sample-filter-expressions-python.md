---
title: Příklady výrazů filtru možné s přípravou dat Azure Machine Learning | Dokumentace Microsoftu
description: Tento dokument obsahuje sadu příklady výrazů filtru je možné s přípravou dat Azure Machine Learning
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
ms.openlocfilehash: a389007dea344de461b23b96f2942686005aa119
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46982388"
---
# <a name="sample-of-filter-expressions-python"></a>Ukázka výrazu filtru (Python) 

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


Předtím, než se pustíte do čtení tohoto dodatku, přečtěte si [přehled rozšíření Python](data-prep-python-extensibility-overview.md).

## <a name="filter-with-equivalence-test"></a>Filtr s srovnávací test
Filtrovat v pouze řádky, kde hodnota Col2 (číselné) je větší než 4. 

```python
    row["Col2"] > 4
```

## <a name="filter-with-multiple-columns"></a>Filtrovat u více sloupců 
Filtrovat pouze řádky, ve kterém Sloupec1 obsahuje hodnotu **dobré** a Col2 obsahuje (číselné) hodnotu 1. 
```python
    row["Col1"] == 'Good' and row["Col2"] == 1
```

## <a name="test-filter-against-null"></a>Filtr test s hodnotou null
Filtrovat v pouze řádky, ve kterém Sloupec1 má hodnotu null. 
```python
    pd.isnull(row["Col1"])
```
