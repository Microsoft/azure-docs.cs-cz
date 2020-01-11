---
title: Nejčastější dotazy ke službě Azure Signal
description: Získejte rychlý přístup k častým dotazům na službu Azure Signal Service, o řešení problémů a typických scénářích použití.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: dde11b6097dddb1568f5adfea811606214a9759e
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75891253"
---
# <a name="azure-signalr-service-faq"></a>Nejčastější dotazy ke službě Azure Signal

## <a name="is-azure-signalr-service-ready-for-production-use"></a>Je služba signálu Azure připravená na použití v produkčním prostředí?

Ano.
Informace o obecné dostupnosti najdete v tématu [Služba Azure Signal Service je teď všeobecně dostupná](https://azure.microsoft.com/blog/azure-signalr-service-now-generally-available/). 

[Signál ASP.NET Core](https://docs.microsoft.com/aspnet/core/signalr/introduction) je plně podporovaný.

Podpora ASP.NET signalizace je stále ve *verzi Public Preview*. Zde je [příklad kódu](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom).

## <a name="the-client-connection-closes-with-the-error-message-no-server-available-what-does-it-mean"></a>Připojení klienta se zavře s chybovou zprávou "žádný server není k dispozici". Co to znamená?

K této chybě dochází pouze v případě, že klienti odesílají zprávy do služby Signal.

Pokud nemáte žádný aplikační server a používáte jenom REST API služby Signal, toto chování je **záměrné**.
V architektuře bez serveru jsou připojení klienta v režimu **naslouchání** a neodesílají žádné zprávy do služby signalizace.
Přečtěte si další informace o [REST API](./signalr-quickstart-rest-api.md).

Pokud máte aplikační servery, tato chybová zpráva znamená, že k vaší instanci služby signalizace není připojen žádný aplikační server.

Možné příčiny:
- Služba signalizace není připojená k aplikačnímu serveru. V protokolech aplikačního serveru vyhledejte možné chyby připojení. Tento případ je vzácná v nastavení vysoké dostupnosti s více než jedním aplikačním serverem.
- Existují problémy s připojením k instancím služby signalizace. Tento problém je přechodný a automaticky se obnoví.
Pokud bude trvat déle než hodinu, [otevřete problém na GitHubu](https://github.com/Azure/azure-signalr/issues/new) nebo [vytvořte žádost o podporu v Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

## <a name="when-there-are-multiple-application-servers-are-client-messages-sent-to-all-servers-or-just-one-of-them"></a>Pokud je k dispozici více aplikačních serverů, jsou klientské zprávy odesílány na všechny servery nebo pouze na jednu z nich?

Mezi klientem a aplikačním serverem je mapování 1:1. Zprávy od jednoho klienta jsou vždy odesílány na stejný aplikační server.

Mapování mezi klientem a aplikačním serverem bude zachováno až do odpojení klienta nebo aplikačního serveru.

## <a name="if-one-of-my-application-servers-is-down-how-can-i-find-it-and-get-notified"></a>Jak je některý z mých aplikačních serverů mimo provoz, jak ho můžu najít a dostávat oznámení?

Služba Signal monitoruje prezenční signály z aplikačních serverů.
Pokud prezenční signály nejsou v zadaném časovém intervalu přijaty, aplikační server se považuje za offline. Všechna připojení klienta namapovaná na tento aplikační server budou odpojena.

## <a name="why-does-my-custom-iuseridprovider-throw-exception-when-switching-from-aspnet-core-signalr--sdk-to-azure-signalr-service-sdk"></a>Proč můj vlastní `IUserIdProvider` při přepínání ze sady SDK pro ASP.NET Core Signal na sadu SDK služby Azure Signaler vyvolal výjimku?

Parametr `HubConnectionContext context` se v sadě SDK pro ASP.NET Core signalizace a v případě, že se volá `IUserIdProvider`, liší.

V ASP.NET Core Signaler `HubConnectionContext context` je kontextem z fyzického připojení klienta s platnými hodnotami pro všechny vlastnosti.

V sadě SDK služby signalizace Azure je `HubConnectionContext context` kontextem z logického připojení klienta. Fyzické připojení klienta je připojeno k instanci služby signalizace, proto je k dispozici pouze omezený počet vlastností.

Pro přístup je teď k dispozici jenom `HubConnectionContext.GetHttpContext()` a `HubConnectionContext.User`.
Zdrojový kód si můžete prohlédnout [tady](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/HubHost/ServiceHubConnectionContext.cs).

## <a name="can-i-configure-the-transports-available-in-signalr-service-as-configuring-it-on-server-side-with-aspnet-core-signalr-for-example-disable-websocket-transport"></a>Můžu ve službě Signal Service nakonfigurovat transporty, jako je konfigurace na straně serveru pomocí služby ASP.NET Core Signal? Například zakažte přenos pomocí protokolu WebSocket?

Ne.

Služba signalizace Azure poskytuje všechny tři přenosy, které ASP.NET Core signál podporuje ve výchozím nastavení. Nedá se konfigurovat. Služba signalizace zpracuje připojení a přenosy pro všechna připojení klientů.

Můžete nakonfigurovat přenosy na straně klienta, jak je popsáno [zde](https://docs.microsoft.com/aspnet/core/signalr/configuration?view=aspnetcore-2.1&tabs=dotnet#configure-allowed-transports-2).
