---
title: Přehled klientské knihovny SMS pro komunikační služby Azure
titleSuffix: An Azure Communication Services concept document
description: Poskytuje přehled klientské knihovny SMS a jejích nabídek.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: ec468f070b5dd418772aeea8760b5d0b270e093a
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102487504"
---
# <a name="sms-client-library-overview"></a>Přehled klientských knihoven pro SMS

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Klientské knihovny služby Azure Communication Services můžete použít k přidání zasílání zpráv SMS do aplikací.

## <a name="sms-client-library-capabilities"></a>Možnosti klientské knihovny SMS

Následující seznam uvádí sadu funkcí, které jsou aktuálně k dispozici v našich klientských knihovnách.

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
