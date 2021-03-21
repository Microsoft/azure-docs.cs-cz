---
title: Definice metrik pro komunikační službu Azure
titleSuffix: An Azure Communication Services concept document
description: Tento dokument obsahuje definice metrik, které jsou k dispozici v Azure Portal.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 129a718175fdda80d4d6852e3d3b4cea609da64d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103492404"
---
# <a name="metrics-overview"></a>Přehled metrik

Komunikační služby Azure aktuálně poskytují metriky pro chat a SMS. Služba [Azure Průzkumník metrik](../../azure-monitor/essentials/metrics-getting-started.md) se dá použít k seznámení s vlastními grafy, prozkoumání neobvyklých hodnot metrik a pochopení provozu rozhraní API pomocí dat metrik, která vygenerují požadavky v konverzaci a SMS.

## <a name="where-to-find-metrics"></a>Kde najít metriky

Služby chat a SMS ve službě Azure Communication Services generují metriky pro požadavky rozhraní API. Tyto metriky najdete v okně metriky v rámci prostředku komunikačních služeb. Trvalé řídicí panely můžete vytvořit také pomocí okna sešity v rámci prostředku komunikačních služeb.

## <a name="metric-definitions"></a>Definice metriky

Existují dva typy požadavků, které jsou zastoupeny v rámci metrik komunikačních služeb: **požadavky rozhraní API chatu** a **žádosti rozhraní API SMS**.

Metriky žádostí o rozhraní API pro chat a SMS obsahují tři dimenze, pomocí kterých můžete filtrovat data metrik. Tyto dimenze se dají agregovat společně pomocí `Count` typu agregace a podporují všechny standardní agregační časové řady Azure `Sum` , včetně, `Average` , `Min` a `Max` .

Další informace o podporovaných typech agregace a agregacích časových řad najdete v [části Pokročilé funkce Azure Průzkumník metrik](../../azure-monitor/essentials/metrics-charts.md#aggregation)

- **Operace** – všechny operace nebo trasy, které lze volat v bráně programu ACS chat.
- **Stavový kód** – odpověď stavového kódu odeslaného po žádosti.
- **StatusSubClass** – série stavových kódů se odeslala po odpovědi. 


### <a name="chat-api-request-metric-operations"></a>Operace metriky požadavku rozhraní API chatu

V metrikách žádostí rozhraní API chatu jsou k dispozici následující operace:

| Operace/trasa    | Description                                                                                    |
| -------------------- | ---------------------------------------------------------------------------------------------- |
| GetChatMessage       | Získá zprávu podle ID zprávy. |
| ListChatMessages     | Načte seznam zpráv chatu z vlákna. |
| SendChatMessage      | Odešle zprávu chatu do vlákna. |
| UpdateChatMessage    | Aktualizuje zprávu chatu. |
| DeleteChatMessage    | Odstraní zprávu chatu. |
| GetChatThread        | Načte chatovací vlákno. |
| ListChatThreads      | Získá seznam podprocesů konverzace uživatele. |
| UpdateChatThread     | Aktualizuje vlastnosti konverzačního vlákna. |
| CreateChatThread     | Vytvoří chatovací vlákno. |
| DeleteChatThread     | Odstraní vlákno. |
| GetReadReceipts      | Získá potvrzení čtení pro vlákno. |
| SendReadReceipt      | Pošle událost přijetí pro čtení do vlákna jménem uživatele. |
| SendTypingIndicator           | Účtuje událost zápisu do vlákna jménem uživatele. |
| ListChatThreadParticipants    | Získá členy vlákna. |
| AddChatThreadParticipants     | Přidá členy vlákna do vlákna. Pokud již členové existují, nedojde k žádné změně. |
| RemoveChatThreadParticipant   | Odebere člena z vlákna. |

:::image type="content" source="./media/chat-metric.png" alt-text="Metrika požadavku rozhraní API chatu":::

Pokud se u operace, která není rozpoznaná, dostane požadavek, obdržíte odpověď hodnoty "špatná trasa".

### <a name="sms-api-requests"></a>Žádosti rozhraní API SMS

V metrikách žádostí na rozhraní API služby SMS jsou k dispozici následující operace:

| Operace/trasa    | Description                                                                                    |
| -------------------- | ---------------------------------------------------------------------------------------------- |
| SMSMessageSent       | Odešle zprávu SMS. |
| SMSDeliveryReportsReceived     | Načte sestavy doručení SMS. |
| SMSMessagesReceived      | Získá zprávy SMS. |


:::image type="content" source="./media/sms-metric.png" alt-text="Metrika žádosti rozhraní API SMS":::

### <a name="authentication-api-requests"></a>Žádosti rozhraní API pro ověřování

V metrikách žádosti rozhraní API pro ověřování jsou dostupné tyto operace:

| Operace/trasa    | Description                                                                                    |
| -------------------- | ---------------------------------------------------------------------------------------------- |
| CreateIdentity       | Vytvoří identitu představující jednoho uživatele. |
| DeleteIdentity       | Odstraní identitu. |
| Okta          | Vytvoří přístupový token. |
| RevokeToken          | Odvolá všechny přístupové tokeny vytvořené pro identitu před uplynutím zadaného času. |

:::image type="content" source="./media/acs-auth-metrics.png" alt-text="Metrika žádosti o ověření.":::

## <a name="next-steps"></a>Další kroky

- Další informace o [metrikách datových platforem](../../azure-monitor/essentials/data-platform-metrics.md)
