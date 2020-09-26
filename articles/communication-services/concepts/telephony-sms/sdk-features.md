---
title: Přehled klientské knihovny SMS pro komunikační služby Azure
titleSuffix: An Azure Communication Services concept document
description: Poskytuje přehled klientské knihovny SMS a jejích nabídek.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 4077bbe0a98f6b7788af9b6c44f73abbc936c6aa
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91332234"
---
# <a name="sms-client-library-overview"></a>Přehled klientských knihoven pro SMS

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

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
| Telefonní číslo      | Bezplatné telefonní číslo                                                                     | ✔️   | ✔️    | ✔️    | ✔️      |
| Řídící        | Zpracování výslovných výjimek                                                                      | ✔️   | ✔️    | ✔️    | ✔️      |
| Monitorování        | Monitorování využití odesílaných a přijatých zpráv                                          | ✔️   | ✔️    | ✔️    | ✔️      |
| Volání do veřejné telefonní sítě      | Přidání schopností volání přes telefonní číslo do bezplatného čísla s povoleným serverem SMS (privátní verze Preview)                    | ✔️   | ✔️    | ✔️    | ✔️      |

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Začínáme s odesíláním SMS](../../quickstarts/telephony-sms/send.md)

Následující dokumenty můžou být zajímavé:

- Seznamte se s obecnými [Koncepty SMS](../telephony-sms/concepts.md)
- Získání [telefonního čísla](../../quickstarts/telephony-sms/get-phone-number.md) s možností SMS
- [Plánování řešení SMS](../telephony-sms/plan-solution.md)
