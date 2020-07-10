---
ms.openlocfilehash: b4fbeb6baa4516ca4cf3ca6194195fae2c688b07
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2020
ms.locfileid: "86165650"
---
Výchozí časové pásmo použité s výrazy CRON je koordinovaný světový čas (UTC). Pokud chcete mít výraz CRON založený na jiném časovém pásmu, vytvořte nastavení aplikace pro aplikaci Function App s názvem `WEBSITE_TIME_ZONE` . 

Hodnota tohoto nastavení závisí na operačním systému a na plánu, na kterém běží aplikace Function App.

|Operační systém |Plán |Hodnota |
|-|-|-|
| **Windows** |Vše | Nastavte hodnotu na název požadovaného časového pásma, jak je znázorněno v [indexu časového pásma Microsoftu] ( https://docs.microsoft.com/previous-versions/windows/it-pro/windows-vista/cc749073(v=ws.10) . |
| **Linux** |Premium<br/>Vyhrazená |Nastavte hodnotu na název požadovaného časového pásma, jak je znázorněno v [databázi TZ](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones). |

> [!NOTE]
> `WEBSITE_TIME_ZONE`není aktuálně podporován pro plán spotřeby Linux.

Například *východní běžný čas* (Windows) nebo *Amerika/New_York* (Linux) je UTC-05:00. Pokud chcete, aby se aktivovala aktivační událost časovače v 10:00.., použijte následující výraz NCRONTAB, který obsahuje účty pro časové pásmo UTC:

```
"0 0 15 * * *"
``` 

Nebo vytvořte nastavení aplikace pro aplikaci Function App s názvem `WEBSITE_TIME_ZONE` , nastavte hodnotu na `Eastern Standard Time` (Windows) nebo `America/New_York` (Linux) a pak použijte následující výraz NCRONTAB: 

```
"0 0 10 * * *"
``` 

Když použijete `WEBSITE_TIME_ZONE` , čas se upraví pro časové změny v konkrétním časovém pásmu, jako je například letní čas. 
