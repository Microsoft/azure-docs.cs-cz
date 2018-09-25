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
ROBOTS: NOINDEX
ms.openlocfilehash: 3e827c56caa787ae9fc33119810eea0f268f0120
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46958405"
---
# <a name="sample-of-destination-connections-python"></a>Ukázka připojení cíle (Python) 

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


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
