---
title: Nejčastější dotazy k službě Azure SignalR
description: Nejčastější dotazy k službě Azure SignalR.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 12/23/2018
ms.author: zhshang
ms.openlocfilehash: 6b3ddf7d8069e689231b9dcb6f0f074e84052511
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55663260"
---
# <a name="azure-signalr-service-faq"></a>Nejčastější dotazy k službě Azure SignalR

## <a name="is-azure-signalr-service-ready-for-production-use"></a>Služby Azure SignalR je připravena pro použití v produkčním prostředí?

Ano.
Naše oznámení všeobecné dostupnosti najdete v části [teď všeobecně dostupné služby Azure SignalR](https://azure.microsoft.com/en-us/blog/azure-signalr-service-now-generally-available/). 

[Funkce SignalR technologie ASP.NET Core](https://docs.microsoft.com/aspnet/core/signalr/introduction) jsou plně podporovány.

Podpora pro funkce SignalR technologie ASP.NET je stále v *ve verzi public preview*. Tady je [příklad kódu](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom).

## <a name="the-client-connection-closes-with-the-error-message-no-server-available-what-does-it-mean"></a>Připojení klienta zavře s chybovou zprávou "K dispozici žádný server". Co znamená?

K této chybě dochází pouze v případě, že klienti jsou odesílání zpráv do služby SignalR.

Pokud nemáte žádné aplikační server a použijte pouze API REST pro funkci SignalR Service, toto chování je **záměrné**.
Architektura bez serveru, připojení klientů jsou v **NASLOUCHÁNÍ** režimu a bude odeslat všechny zprávy pro služby SignalR.
Další informace o [rozhraní REST API](./signalr-quickstart-rest-api.md).

Pokud máte aplikační servery, tato chybová zpráva znamená, že žádné aplikační server je připojený k vaší instanci služby SignalR.

Možné příčiny:
- Žádný server aplikace je propojená s služby SignalR. V protokolech aplikace serveru chyb připojení je to možné. Tento případ není obvyklé v nastavení vysoké dostupnosti s více než jeden aplikační servery.
- Existují problémy s připojením s instancí služby SignalR. Tento problém je přechodná a se automaticky obnoví.
Pokud bude přetrvávat pro více než jednu hodinu [otevřete problém na Githubu](https://github.com/Azure/azure-signalr/issues/new) nebo [v Azure vytvořit žádost o podporu](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request).

## <a name="when-there-are-multiple-application-servers-are-client-messages-sent-to-all-servers-or-just-one-of-them"></a>Po několika aplikačních serverů se odesílají zprávy klienta pro všechny servery, nebo jenom jeden z nich?

Je mapování 1: 1 mezi klienta a aplikačního serveru. Zprávy od jednoho klienta byly odesílány vždy na stejném aplikačním serveru.

Mapování mezi klienta a aplikačního serveru se zachová, dokud odpojení klienta nebo aplikace serveru.

## <a name="if-one-of-my-application-servers-is-down-how-can-i-find-it-and-get-notified"></a>Pokud jeden ze serverů Moje aplikace je mimo provoz, jak ji najít a získat oznámení?

Služby SignalR monitoruje prezenční signály aplikačních serverů.
Pokud nejsou pro zadaný časový úsek prezenčních signálů, aplikační server je považován za offline. Všechna připojení klientů, které jsou namapované na tento server aplikace budou odpojeny.

## <a name="why-does-my-custom-iuseridprovider-throw-exception-when-switching-from-aspnet-core-signalr--sdk-to-azure-signalr-service-sdk"></a>Proč moje vlastní `IUserIdProvider` vyvolat výjimku při přechodu ze sady SDK SignalR technologie ASP.NET Core pro sadu SDK služby Azure SignalR?

Parametr `HubConnectionContext context` se liší mezi sadou SDK SignalR technologie ASP.NET Core a sady SDK služby Azure SignalR při `IUserIdProvider` je volána.

V knihovně SignalR technologie ASP.NET Core `HubConnectionContext context` kontext z fyzické připojení platnou hodnotou pro všechny vlastnosti.

V sadě SDK služby Azure SignalR `HubConnectionContext context` kontext z připojení logické klienta. Fyzické připojení je připojen k instanci služby SignalR, proto jsou k dispozici pouze omezený počet vlastností.

Prozatím pouze `HubConnectionContext.GetHttpContext()` a `HubConnectionContext.User` jsou k dispozici pro přístup.
Můžete zkontrolovat zdrojový kód [tady](https://github.com/Azure/azure-signalr/blob/kevinzha/faq/src/Microsoft.Azure.SignalR/ServiceHubConnectionContext.cs).

## <a name="can-i-configure-the-transports-available-in-signalr-service-as-configuring-it-on-server-side-with-aspnet-core-signalr-for-example-disable-websocket-transport"></a>Můžete nakonfigurovat přenosy ve službě SignalR k dispozici jako konfigurace na straně serveru s knihovnou SignalR technologie ASP.NET Core? Například zakázat přenos pomocí protokolu WebSocket?

Ne.

Službě Azure SignalR poskytuje všechny tři přenosy, které ve výchozím nastavení podporuje funkce SignalR technologie ASP.NET Core. Se nedá konfigurovat. Připojení a přenosy pro všechna připojení klientů bude zpracovávat služby SignalR.

Můžete nakonfigurovat přenosy na straně klienta, jak je uvedeno [tady](https://docs.microsoft.com/aspnet/core/signalr/configuration?view=aspnetcore-2.1#configure-allowed-transports).
