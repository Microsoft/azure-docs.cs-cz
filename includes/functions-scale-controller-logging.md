---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/15/2020
ms.author: glenga
ms.openlocfilehash: 4cbf179658ddc13aca9bf30934dc28ab0cf5fd9d
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081141"
---
| Vlastnost | Popis |
|--|--|
|**`<DESTINATION>`**| Cíl, do kterého jsou protokoly odesílány. Platné hodnoty jsou `AppInsights` a `Blob` .<br/>Když použijete `AppInsights` , ujistěte se, že [je ve vaší aplikaci Function App povolený Application Insights](../articles/azure-functions/configure-monitoring.md#enable-application-insights-integration).<br/>Když nastavíte cíl na `Blob` , vytvoří se protokoly v kontejneru objektů BLOB s názvem `azure-functions-scale-controller` ve výchozím účtu úložiště, který je nastavený v `AzureWebJobsStorage` nastavení aplikace. |
|**`<VERBOSITY>`** | Určuje úroveň protokolování. Podporované hodnoty jsou `None` , `Warning` a `Verbose` .<br/>Při nastavení na `Verbose` se kontroler škálování zaznamená důvod každé změny v počtu pracovních procesů a také informace o aktivačních událostech, které tento faktor stanoví do těchto rozhodnutí. Podrobné protokoly zahrnují upozornění triggeru a hodnoty hash používané triggery před a po spuštění kontroleru škálování. |

> [!TIP]
> Mějte na paměti, že když necháte zapnuté protokolování škálování, bude to mít vliv na [potenciální náklady na monitorování aplikace Function App](../articles/azure-functions/functions-monitoring.md#application-insights-pricing-and-limits). Zvažte povolení protokolování, dokud neshromáždíte dostatek dat, abyste pochopili, jak se kontroler škálování chová, a pak ho zakážete.
