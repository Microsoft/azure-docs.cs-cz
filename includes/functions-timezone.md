---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/20/2020
ms.author: glenga
ms.openlocfilehash: 7d1bf8dd2d1c8feab8b051a8edad7d5e570ee11b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027810"
---
Výchozí časové pásmo použité s výrazy CRON je koordinovaný světový čas (UTC). Pokud chcete mít výraz CRON založený na jiném časovém pásmu, vytvořte nastavení aplikace pro aplikaci Function App s názvem `WEBSITE_TIME_ZONE` . 

Hodnota tohoto nastavení závisí na operačním systému a na plánu, na kterém běží aplikace Function App.

|Operační systém |Plánování |Hodnota |
|-|-|-|
| **Windows** |Vše | Nastavte hodnotu na název požadovaného časového pásma, který je dán druhým řádkem z každého páru zadaného příkazem Windows. `tzutil.exe /L` |
| **Linux** |Premium<br/>Vyhrazená |Nastavte hodnotu na název požadovaného časového pásma, jak je znázorněno v [databázi TZ](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones). |

> [!NOTE]
> `WEBSITE_TIME_ZONE` není aktuálně podporován pro plán spotřeby Linux.

Například východní čas v USA (reprezentovaný `Eastern Standard Time` systémem (Windows) nebo `America/New_York` (Linux)) aktuálně používá UTC-05:00 během standardního času a utc-04:00 během letního času. Pokud chcete, aby se aktivační událost časovače aktivovala v 10:00ovém čase každý den, vytvořte nastavení aplikace pro aplikaci Function App s názvem `WEBSITE_TIME_ZONE` , nastavte hodnotu na `Eastern Standard Time` (Windows) nebo `America/New_York` (Linux) a pak použijte následující NCRONTAB výraz: 

```
"0 0 10 * * *"
``` 

Když použijete `WEBSITE_TIME_ZONE` čas, upraví se změny času v konkrétním časovém pásmu, včetně letního času a změn ve standardním čase.
