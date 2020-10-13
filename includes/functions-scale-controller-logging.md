---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/15/2020
ms.author: glenga
ms.openlocfilehash: 49818cf59da2d63cef4bb0bdca38d38a2feafca5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86169900"
---
| | |
|--|--|
|**`<DESTINATION>`**| Cíl, do kterého jsou protokoly odesílány. Platné hodnoty jsou `AppInsights` a `Blob` .<br/>Když použijete `AppInsights` , ujistěte se, že [je ve vaší aplikaci Function App povolený Application Insights](../articles/azure-functions/functions-monitoring.md#enable-application-insights-integration).<br/>Když nastavíte cíl na `Blob` , vytvoří se protokoly v kontejneru objektů BLOB s názvem `azure-functions-scale-controller` ve výchozím účtu úložiště, který je nastavený v `AzureWebJobsStorage` nastavení aplikace. |
|**`<VERBOSITY>`** | Určuje úroveň protokolování. Podporované hodnoty jsou `None` , `Warning` a `Verbose` .<br/>Při nastavení na `Verbose` se kontroler škálování zaznamená důvod každé změny v počtu pracovních procesů a také informace o aktivačních událostech, které tento faktor stanoví do těchto rozhodnutí. Podrobné protokoly zahrnují upozornění triggeru a hodnoty hash používané triggery před a po spuštění kontroleru škálování. |

> [!CAUTION]
> Nenechte zapnuté protokolování řadiče škálování. Povolte protokolování, dokud neshromáždíte dostatek dat, abyste pochopili, jak se kontroler škálování chová, a pak ho zakažte.