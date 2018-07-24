---
title: Příklad cíle či výstupy možné s přípravou dat Azure Machine Learning | Dokumentace Microsoftu
description: Tento dokument obsahuje sadu příklady vlastních dat cíle či výstupy s přípravou dat Azure Machine Learning
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
ms.openlocfilehash: 2173ff15906940b8628aba3615f31e3f7137e3e2
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2018
ms.locfileid: "39216493"
---
# <a name="sample-of-destination-connections-python"></a>Ukázka připojení cíle (Python) 
Předtím, než se pustíte do čtení tohoto dodatku, přečtěte si [přehled rozšíření Python](data-prep-python-extensibility-overview.md).


## <a name="write-to-excel"></a>Zápis do aplikace Excel 


Zápis do aplikace Excel vyžaduje další knihovny. Přidání nové knihovny najdete v přehledu rozšiřitelnosti. `openpyxl` je knihovny, kterou je třeba přidat.

Než napíšete do aplikace Excel, může být nutné nějaké změny. Některé typy dat, které se používají v rámci přípravy dat nejsou podporovány v některé cílové formáty. Například pokud existují objekty "Chyba", nebude serializovat správně do Excelu. Proto než se pokusíte zapsat do aplikace Excel, je třeba "Nahradí chybové hodnoty" transformace, která zruší všechny sloupce chyby.

Pokud všechny předchozí práce je dokončena, zapíše následující řádek v tabulce dat do jedné tabulky v dokumentu aplikace Excel. Přidáte transformaci Transform DataFlow (Script). Zadejte následující kód do oddílu výrazu.


### <a name="on-windows"></a>V systému Windows 
```python
df.to_excel('c:\dev\data\Output\Customer.xlsx', sheet_name='Sheet1')
```

### <a name="on-macosos-x"></a>V systému macOS/OS X ###
```python
df.to_excel('c:/dev/data/Output/Customer.xlsx', sheet_name='Sheet1')
```
