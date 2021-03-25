---
title: Přehled sady SMS SDK pro komunikační služby Azure
titleSuffix: An Azure Communication Services concept document
description: Poskytuje přehled sady SMS SDK a jejích nabídek.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 25a5befab6ef141b059caaac7b1784d47f2f93cf
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2021
ms.locfileid: "105108107"
---
# <a name="sms-sdk-overview"></a>Přehled sady SMS SDK

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Sady SDK pro službu Azure Communication Services je možné použít k přidávání zpráv SMS do aplikací.

## <a name="sms-sdk-capabilities"></a>Možnosti sady SMS SDK

Následující seznam uvádí sadu funkcí, které jsou aktuálně dostupné v našich sadách SDK.

| Skupina funkcí | Schopnost                                                                            | JS  | Java | .NET | Python |
| ----------------- | ------------------------------------------------------------------------------------- | --- | ---- | ---- | ------ |
| Základní funkce | Odesílání a příjem zpráv SMS </br> *Podporovaná sada Unicode Emoji*                        | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Příjem zpráv o doručení pro odeslané zprávy                                            | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Všechny znakové sady (podpora jazyků a kódování Unicode)                                         | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Podpora dlouhých zpráv (až 2048 znaků)                                           | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Automatické zřetězení dlouhých zpráv                                                   | ✔️   | ✔️    | ✔️    | ✔️      |
| Události            | Použití Event Grid ke konfiguraci webhooků pro příjem příchozích zpráv a zpráv o doručení | ✔️   | ✔️    | ✔️    | ✔️      |
| Telefonní číslo      | Toll-Free čísla                                                                     | ✔️   | ✔️    | ✔️    | ✔️      |
| Řídící        | Zpracování Opt-Out                                                                      | ✔️   | ✔️    | ✔️    | ✔️      |
| Monitorování        | Monitorování využití odesílaných a přijatých zpráv                                          | ✔️   | ✔️    | ✔️    | ✔️      |
| Volání do veřejné telefonní sítě      | Přidání schopností volání přes telefonní číslo do bezplatného čísla s povoleným serverem SMS                    | ✔️   | ✔️    | ✔️    | ✔️      |

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Začínáme s odesíláním SMS](../../quickstarts/telephony-sms/send.md)

Následující dokumenty můžou být zajímavé:

- Seznamte se s obecnými [Koncepty SMS](../telephony-sms/concepts.md)
- Získání [telefonního čísla](../../quickstarts/telephony-sms/get-phone-number.md) s možností SMS
- [Typy telefonních čísel v komunikačních službách Azure](../telephony-sms/plan-solution.md)
