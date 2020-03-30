---
title: Azure SignalR Service nejčastější dotazy
description: Mít rychlý přístup k nejčastějším dotazům na Azure SignalR Service, o řešení potíží a typické scénáře použití.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: dde11b6097dddb1568f5adfea811606214a9759e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "75891253"
---
# <a name="azure-signalr-service-faq"></a>Nejčastější dotazy ke službě Azure SignalR

## <a name="is-azure-signalr-service-ready-for-production-use"></a>Je služba Azure SignalR připravená k produkčnímu použití?

Ano.
Naše oznámení o obecné dostupnosti najdete [v tématu Azure SignalR Service je nyní obecně dostupné](https://azure.microsoft.com/blog/azure-signalr-service-now-generally-available/). 

[ASP.NET Core SignalR](https://docs.microsoft.com/aspnet/core/signalr/introduction) je plně podporován.

Podpora ASP.NET SignalR je stále ve *verzi Public Preview*. Zde je [příklad kódu](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom).

## <a name="the-client-connection-closes-with-the-error-message-no-server-available-what-does-it-mean"></a>Připojení Klient se zavře s chybovou zprávou "Není k dispozici žádný server". Co to znamená?

K této chybě dochází pouze v případě, že klienti odesílají zprávy do služby SignalR.

Pokud nemáte žádný aplikační server a používat pouze rozhraní REST API služby SignalR, toto chování je **záměrné**.
V architektuře bez serveru jsou připojení klientů v režimu **NASLOUCHÁNí** a neodesílají žádné zprávy službě SignalR.
Přečtěte si více o [REST API](./signalr-quickstart-rest-api.md).

Pokud máte aplikační servery, tato chybová zpráva znamená, že k instanci služby SignalR není připojen žádný aplikační server.

Možné příčiny jsou:
- Ke službě SignalR není připojen žádný aplikační server. Zkontrolujte protokoly aplikačního serveru, zda nezjistily možné chyby připojení. Tento případ je vzácné v nastavení vysoké dostupnosti s více než jeden aplikační servery.
- Existují problémy s připojením s instancemi služby SignalR. Tento problém je přechodný a automaticky se obnoví.
Pokud trvá déle než hodinu, [otevřete problém na GitHubu](https://github.com/Azure/azure-signalr/issues/new) nebo [vytvořte žádost o podporu v Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

## <a name="when-there-are-multiple-application-servers-are-client-messages-sent-to-all-servers-or-just-one-of-them"></a>Pokud existuje více aplikačních serverů, jsou klientské zprávy odesílány na všechny servery nebo jen jeden z nich?

Je to mapování 1:1 mezi klientem a aplikačním serverem. Zprávy od jednoho klienta jsou vždy odesílány na stejný aplikační server.

Mapování mezi klientem a aplikačním serverem bude zachováno, dokud se klient nebo aplikační server neodpojí.

## <a name="if-one-of-my-application-servers-is-down-how-can-i-find-it-and-get-notified"></a>Pokud je jeden z mých aplikačních serverů mimo seznam, jak ho mohu najít a nechat se upozornit?

Služba SignalR monitoruje prezenční signály z aplikačních serverů.
Pokud prezenční signály nejsou přijaty po určitou dobu, aplikační server je považován za offline. Všechna klientská připojení namapovaná na tento aplikační server budou odpojena.

## <a name="why-does-my-custom-iuseridprovider-throw-exception-when-switching-from-aspnet-core-signalr--sdk-to-azure-signalr-service-sdk"></a>Proč moje `IUserIdProvider` vlastní vyvolat výjimku při přechodu z ASP.NET Core SignalR SDK na Azure SignalR Service SDK?

Parametr `HubConnectionContext context` se liší mezi ASP.NET Core SignalR SDK a `IUserIdProvider` Azure SignalR Service SDK při volání.

V ASP.NET Core `HubConnectionContext context` SignalR je kontext z fyzického připojení klienta s platnými hodnotami pro všechny vlastnosti.

V Azure SignalR Service `HubConnectionContext context` SDK, je kontext z připojení logického klienta. Připojení fyzického klienta je připojeno k instanci služby SignalR, takže je k dispozici pouze omezený počet vlastností.

Pro tuto `HubConnectionContext.GetHttpContext()` chvíli `HubConnectionContext.User` pouze a jsou k dispozici pro přístup.
Zdrojový kód si můžete ověřit [zde](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/HubHost/ServiceHubConnectionContext.cs).

## <a name="can-i-configure-the-transports-available-in-signalr-service-as-configuring-it-on-server-side-with-aspnet-core-signalr-for-example-disable-websocket-transport"></a>Lze nakonfigurovat přenosy, které jsou k dispozici ve službě SignalR, jako konfiguraci na straně serveru pomocí ASP.NET Core SignalR? Například zakázat přenos WebSocket?

Ne.

Služba Azure SignalR poskytuje všechny tři přenosy, které ve výchozím nastavení podporuje ASP.NET core signalr. Nelze jej konfigurovat. Služba SignalR bude zpracovávat připojení a přenosy pro všechna připojení klientů.

Přenosy na straně klienta můžete nakonfigurovat tak, jak [jsou zde](https://docs.microsoft.com/aspnet/core/signalr/configuration?view=aspnetcore-2.1&tabs=dotnet#configure-allowed-transports-2)popsány .
