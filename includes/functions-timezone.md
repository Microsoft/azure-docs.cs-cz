---
ms.openlocfilehash: dba7a3cc7a68d360fd6e56511b71ae364f624646
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2020
ms.locfileid: "89569270"
---
Výchozí časové pásmo použité s výrazy CRON je koordinovaný světový čas (UTC). Pokud chcete mít výraz CRON založený na jiném časovém pásmu, vytvořte nastavení aplikace pro aplikaci Function App s názvem `WEBSITE_TIME_ZONE` . 

Hodnota tohoto nastavení závisí na operačním systému a na plánu, na kterém běží aplikace Function App.

|Operační systém |Plánování |Hodnota |
|-|-|-|
| **Windows** |Vše | Nastavte hodnotu na název požadovaného časového pásma, jak je znázorněno v [indexu časového pásma Microsoftu](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-vista/cc749073(v=ws.10)). |
| **Linux** |Premium<br/>Vyhrazená |Nastavte hodnotu na název požadovaného časového pásma, jak je znázorněno v [databázi TZ](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones). |

> [!NOTE]
> `WEBSITE_TIME_ZONE` není aktuálně podporován pro plán spotřeby Linux.

Například *východní běžný čas* (Windows) nebo *Amerika/New_York* (Linux) je UTC-05:00. Pokud chcete, aby se aktivovala aktivační událost časovače v 10:00.., použijte následující výraz NCRONTAB, který obsahuje účty pro časové pásmo UTC:

```
"0 0 15 * * *"
``` 

Nebo vytvořte nastavení aplikace pro aplikaci Function App s názvem `WEBSITE_TIME_ZONE` , nastavte hodnotu na `Eastern Standard Time` (Windows) nebo `America/New_York` (Linux) a pak použijte následující výraz NCRONTAB: 

```
"0 0 10 * * *"
``` 

Když použijete `WEBSITE_TIME_ZONE` , čas se upraví pro časové změny v konkrétním časovém pásmu, jako je například letní čas. 
