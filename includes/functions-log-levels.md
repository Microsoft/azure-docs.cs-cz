---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/04/2020
ms.author: glenga
ms.openlocfilehash: 72588ccfd9ad00305cbdeaf8132999731d86b9b1
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2020
ms.locfileid: "92164900"
---
*Úroveň protokolu* je přiřazena ke každému protokolu. Hodnota je celé číslo, které označuje relativní důležitost:

|LogLevel    |Kód| Popis |
|------------|---|--------------|
|Trasování       | 0 |Protokoly, které obsahují nejpodrobnější zprávy. Tyto zprávy mohou obsahovat citlivá aplikační data. Tyto zprávy jsou ve výchozím nastavení zakázané a v produkčním prostředí by se nikdy neměly povolit.|
|Ladění       | 1 | Protokoly, které se používají pro interaktivní šetření během vývoje. Tyto protokoly by měly primárně obsahovat informace užitečné pro ladění a nemají žádnou dlouhodobou hodnotu. |
|Informace | 2 | Protokoly, které sledují obecný tok aplikace. Tyto protokoly musí mít dlouhodobou hodnotu. |
|Upozornění     | 3 | Protokoly, které zvýrazňují neobvyklou nebo neočekávanou událost v toku aplikace, ale ne jinak způsobí zastavení spuštění aplikace. |
|Chyba       | 4 | Protokoly, které se zvýrazňují, když je aktuální tok spuštění zastaven z důvodu chyby. Tyto chyby by měly poukazovat na selhání aktuální aktivity, nikoli při selhání celé aplikace. |
|Kritické    | 5 | Protokoly, které popisují neodstranitelnou chybu aplikace nebo systému, nebo závažnou chybu, která vyžaduje okamžitou pozornost. |
|Žádné        | 6 | Zakáže protokolování pro určenou kategorii. |

[ *host.jsv* konfiguraci souborů](../articles/azure-functions/functions-host-json.md) určuje, kolik protokolování aplikace functions odesílá do Application Insights.  
