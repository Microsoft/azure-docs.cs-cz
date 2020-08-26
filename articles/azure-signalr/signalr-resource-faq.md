---
title: Nejčastější dotazy ke službě Azure Signal
description: Získejte rychlý přístup k častým dotazům na službu Azure Signal Service, o řešení problémů a typických scénářích použití.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 6d104e41a0cae906c346e81a26617a9d29795fb3
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/25/2020
ms.locfileid: "88853276"
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

## <a name="why-does-my-custom-iuseridprovider-throw-exception-when-switching-from-aspnet-core-signalr--sdk-to-azure-signalr-service-sdk"></a>Proč moje vlastní `IUserIdProvider` výjimka vyvolává při přepínání ze sady ASP.NET Core Signal SDK na sadu SDK služby Azure Signal Service?

Tento parametr `HubConnectionContext context` se při volání sady SDK pro ASP.NET Core signalizace a sadu SDK služby Azure Signal Service liší `IUserIdProvider` .

V nástroji ASP.NET Core Signal `HubConnectionContext context` je kontextem fyzického připojení klienta s platnými hodnotami všech vlastností.

V sadě SDK služby signalizace Azure `HubConnectionContext context` je kontext z připojení k logickému klientovi. Fyzické připojení klienta je připojeno k instanci služby signalizace, proto je k dispozici pouze omezený počet vlastností.

Pro `HubConnectionContext.GetHttpContext()` `HubConnectionContext.User` přístup je teď k dispozici jenom a.
Zdrojový kód si můžete prohlédnout [tady](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/HubHost/ServiceHubConnectionContext.cs).

## <a name="can-i-configure-the-transports-available-in-signalr-service-as-configuring-it-on-server-side-with-aspnet-core-signalr-for-example-disable-websocket-transport"></a>Můžu ve službě Signal Service nakonfigurovat transporty, jako je konfigurace na straně serveru pomocí služby ASP.NET Core Signal? Například zakažte přenos pomocí protokolu WebSocket?

Ne.

Služba signalizace Azure poskytuje všechny tři přenosy, které ASP.NET Core signál podporuje ve výchozím nastavení. Nedá se konfigurovat. Služba signalizace zpracuje připojení a přenosy pro všechna připojení klientů.

Můžete nakonfigurovat přenosy na straně klienta, jak je popsáno [zde](https://docs.microsoft.com/aspnet/core/signalr/configuration?view=aspnetcore-2.1&tabs=dotnet#configure-allowed-transports-2).

## <a name="what-is-the-meaning-of-metrics-like-message-count-or-connection-count-showed-in-azure-portal-which-kind-of-aggregation-type-should-i-choose"></a>Jaký je význam metrik, jako je počet zpráv nebo počet připojení zobrazený v Azure Portal? Jaký druh agregačního typu mám zvolit?

Podrobnosti o tom, jak tyto metriky vypočítat, najdete [tady](signalr-concept-messages-and-connections.md).

V okně Přehled prostředků služby signalizace Azure jsme už pro vás zvolili odpovídající typ agregace. A pokud přejdete do okna metriky, můžete jako referenci [použít typ agregace](../azure-monitor/platform/metrics-supported.md#microsoftsignalrservicesignalr) .

## <a name="what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose"></a>Jaký je význam režimu služby `Default` / `Serverless` / `Classic` ? Jak si můžu vybrat?

Druzí
* `Default` režim *vyžaduje* Server hub. V tomto režimu služba Azure Signal směruje provoz klienta na jeho připojená připojení hub serveru. Azure Signal vyhledává připojený hub Server. Pokud se nenalezne připojený hub Server, nástroj Azure Signal odmítne příchozí připojení klientů. **Rozhraní API pro správu** v tomto režimu můžete použít také ke správě připojených klientů přímo prostřednictvím služby Azure Signal.
* `Serverless` režim *nepovoluje žádné* připojení k serveru, to znamená, že budou všechna připojení serveru odmítnuta. Všichni klienti musí být v režimu bez serveru. Klienti se připojují ke službě Azure Signal a uživatelé obvykle používají pro zpracování logiky centra technologie bez serveru, jako je třeba **funkce Azure Functions** . Podívejte se na [jednoduchý příklad](https://docs.microsoft.com/azure/azure-signalr/signalr-quickstart-azure-functions-javascript?WT.mc_id=signalrquickstart-github-antchu) , který využívá režim bez serveru v Azure Signal.
* `Classic` režim je smíšený stav. Pokud má rozbočovač připojení k serveru, bude nový klient směrován do serveru hub, pokud ne, klient přejde do režimu bez serveru.

  To může způsobit nějaký problém, například všechna připojení serveru budou ztracena za chvíli, někteří klienti místo směrování do serveru hub zadají režim bez serveru.

Kliknutím
1. Bez serveru rozbočovače vyberte `Serverless` .
1. Všechna centra mají servery rozbočovače, vyberte `Default` .
1. Některá centra mají servery rozbočovače, jiné ne, nepoužívají se `Classic` , ale to může způsobit nějaký problém. lepším způsobem je vytvořit dvě instance, jeden je `Serverless` Další `Default` .

## <a name="any-feature-differences-when-using-azure-signalr-for-aspnet-signalr"></a>Při použití služby Azure Signaler pro signál ASP.NET je k disdílným rozdílům funkcí?
Pokud používáte službu Azure Signaler, některá rozhraní API a funkce nástroje ASP.NET Signal už nejsou podporované:
- Možnost předat libovolný stav mezi klienty a centrem (často se označuje jako `HubState` ) není při použití nástroje Azure Signal podporována.
- `PersistentConnection` Třída se při použití služby Azure Signal ještě nepodporuje.
- Při použití služby Azure Signal není podporován **přenos snímků navždy** .
- Služba Azure Signal už nehraje zprávy odesílané klientovi, když je klient offline.
- Při použití nástroje Azure Signal je provoz pro jedno připojení klienta vždy směrován (označuje se také jako. **Sticky**) na jednu instanci aplikačního serveru po dobu trvání připojení

Podpora ASP.NET signalizace se zaměřuje na kompatibilitu, takže nejsou podporované všechny nové funkce nástroje ASP.NET Core Signal. Například **MessagePack**, **streamování**atd., jsou k dispozici pouze pro ASP.NET Core aplikace signalizace.

Služba signalizace se dá nakonfigurovat pro jiný režim služby: `Classic` / `Default` / `Serverles` s. V této podpoře ASP.NET není `Serverless` režim podporován. REST API roviny dat se také nepodporuje.

## <a name="where-do-my-data-reside"></a>Kde se nacházejí moje data?

Služba signalizace Azure funguje jako služba data Processor. Nebudete tak ukládat žádný obsah zákazníků a zasídlí dat se bude přislíbit. Pokud používáte službu signalizace Azure společně s dalšími službami Azure, jako je Azure Storage pro diagnostiku, přečtěte si prosím tento návod [, kde najdete](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/) pokyny, jak zachovat data v oblastech Azure.
