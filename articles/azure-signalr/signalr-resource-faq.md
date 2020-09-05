---
title: Nejčastější dotazy ke službě Azure Signal
description: Získejte odpovědi na nejčastější dotazy týkající se služby Azure Signal, včetně řešení potíží a typických scénářů použití.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.custom: devx-track-dotnet
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: d5dd765dd9b174ffbfec35b63ad5e55ce84193ad
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2020
ms.locfileid: "89489557"
---
# <a name="azure-signalr-service-faq"></a>Nejčastější dotazy ke službě Azure Signal

## <a name="is-azure-signalr-service-ready-for-production-use"></a>Je služba signálu Azure připravená na použití v produkčním prostředí?

Yes.
Informace o celkové dostupnosti najdete v tématu [Služba Azure Signal Service je teď všeobecně dostupná](https://azure.microsoft.com/blog/azure-signalr-service-now-generally-available/). 

[Signál ASP.NET Core](https://docs.microsoft.com/aspnet/core/signalr/introduction) je plně podporovaný.

Podpora ASP.NET signalizace je stále ve *verzi Public Preview*. [Zde je příklad kódu](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom).

## <a name="the-client-connection-closes-with-the-error-message-no-server-available-what-does-it-mean"></a>Připojení klienta se zavře s chybovou zprávou "není k dispozici server." Co to znamená?

K této chybě dochází pouze v případě, že klienti odesílají zprávy do služby Azure Signal.

Pokud nemáte žádný aplikační server a používáte jenom REST API služby pro signály Azure, toto chování je *záměrné*.
V architektuře bez serveru jsou připojení klientů v režimu *naslouchání* a neodesílají žádné zprávy do služby Azure Signal Service.
Přečtěte si [Další informace o REST API](./signalr-quickstart-rest-api.md).

Pokud máte aplikační servery, tato chybová zpráva znamená, že k vaší instanci služby signalizace Azure není připojený žádný aplikační server.

Možné příčiny:
- Ke službě Azure Signal Service není připojený žádný aplikační server. V protokolech aplikačního serveru vyhledejte možné chyby připojení. V takovém případě se jedná o nastavení vysoké dostupnosti, které má více než jeden aplikační server.
- Existují problémy s připojením k instancím služby Azure Signal Service. Tento problém je přechodný a instance se automaticky obnoví.
Pokud bude trvat déle než hodinu, [otevřete problém na GitHubu](https://github.com/Azure/azure-signalr/issues/new) nebo [vytvořte žádost o podporu v Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

## <a name="when-there-are-multiple-application-servers-are-client-messages-sent-to-all-servers-or-just-one-of-them"></a>Pokud je k dispozici více aplikačních serverů, jsou klientské zprávy odesílány na všechny servery nebo pouze na jednu z nich?

Mezi klientem a aplikačním serverem existuje mapování 1:1. Zprávy od jednoho klienta jsou vždy odesílány na stejný aplikační server.

Mapování se zachová, dokud se klient nebo aplikační server odpojí.

## <a name="if-one-of-my-application-servers-is-down-how-can-i-find-it-and-get-notified"></a>Jak je některý z mých aplikačních serverů mimo provoz, jak ho můžu najít a dostávat oznámení?

Služba signalizace Azure monitoruje prezenční signály z aplikačních serverů.
Pokud prezenční signály nejsou v zadaném časovém intervalu přijaty, aplikační server se považuje za offline. Všechna připojení klienta namapovaná na tento aplikační server budou odpojena.

## <a name="why-does-my-custom-iuseridprovider-throw-an-exception-when-im-switching-from-aspnet-core-signalr-sdk-to-azure-signalr-service-sdk"></a>Proč můj vlastní `IUserIdProvider` vyvolat výjimku při přepínání ze sady ASP.NET Core Signal SDK do služby Azure Signal Service SDK?

Tento parametr `HubConnectionContext context` se v případě volání sady SDK pro ASP.NET Core signalizace a sadu SDK služby Azure Signal Service liší `IUserIdProvider` .

V nástroji ASP.NET Core Signal `HubConnectionContext context` je kontextem fyzického připojení klienta s platnými hodnotami všech vlastností.

V sadě SDK služby signalizace Azure `HubConnectionContext context` je kontextem připojení k logickému klientovi. Fyzický klient je připojen ke službě Azure Signal Service, takže je k dispozici pouze omezený počet vlastností.

Pro `HubConnectionContext.GetHttpContext()` `HubConnectionContext.User` přístup je teď k dispozici jenom a.
Můžete [kontrolovat zdrojový kód](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/HubHost/ServiceHubConnectionContext.cs).

## <a name="can-i-configure-the-transports-available-in-azure-signalr-service-on-the-server-side-with-aspnet-core-signalr-for-example-can-i-disable-websocket-transport"></a>Můžu ve službě Azure Signal Service nakonfigurovat transporty, které jsou k dispozici na straně serveru pomocí služby ASP.NET Core Signal? Například je možné zakázat přenos protokolu WebSocket?

No.

Služba signalizace Azure poskytuje všechny tři přenosy, které ASP.NET Core signál podporuje ve výchozím nastavení. Nedá se nakonfigurovat. Služba signalizace Azure zpracuje připojení a přenosy pro všechna připojení klientů.

Můžete nakonfigurovat přenosy na straně klienta, jak je popsáno v [ASP.NET Core konfigurace signalizace](https://docs.microsoft.com/aspnet/core/signalr/configuration?view=aspnetcore-2.1&tabs=dotnet#configure-allowed-transports-2).

## <a name="what-is-the-meaning-of-metrics-like-message-count-or-connection-count-shown-in-the-azure-portal-which-kind-of-aggregation-type-should-i-choose"></a>Jaký je význam metrik, jako je počet zpráv nebo počet připojení zobrazený v Azure Portal? Jaký druh agregačního typu mám zvolit?

Můžete najít podrobnosti o tom, jak tyto metriky ve [zprávách a připojeních ve službě Azure Signal Service](signalr-concept-messages-and-connections.md)počítat.

V podokně Přehled prostředků služby signalizace Azure jsme už pro vás zvolili odpovídající typ agregace. Pokud přejdete do podokna metrik, můžete jako referenci použít typ agregace pro [zprávy a připojení ve službě Azure Signal](../azure-monitor/platform/metrics-supported.md#microsoftsignalrservicesignalr) .

## <a name="what-is-the-meaning-of-the-default-serverless-and-classic-service-modes-how-can-i-choose"></a>Jaký je význam `Default` `Serverless` režimů, a `Classic` služby? Jak si můžu vybrat?

Zde jsou informace o režimech:
* `Default` režim *vyžaduje* Server rozbočovače. V tomto režimu služba signalizace Azure směruje provoz klienta na jeho připojená připojení hub serveru. Služba signalizace Azure kontroluje připojený server hub. Pokud služba nemůže najít připojený hub Server, odmítne příchozí připojení klientů. *Rozhraní API pro správu* v tomto režimu můžete použít také ke správě připojených klientů přímo prostřednictvím služby Azure Signal.
* `Serverless` režim *nepovoluje žádné* připojení k serveru. To znamená, že se odmítnou všechna připojení serveru. Všichni klienti musí být v režimu bez serveru. Klienti se připojují ke službě Azure Signal Service a uživatelé obvykle používají pro zpracování logiky centra technologie bez serveru, například *Azure Functions* . [Podívejte se na jednoduchý příklad](https://docs.microsoft.com/azure/azure-signalr/signalr-quickstart-azure-functions-javascript?WT.mc_id=signalrquickstart-github-antchu) , který ve službě Azure Signal Service používá režim bez serveru.
* `Classic` režim je smíšený stav. Když má rozbočovač připojení k serveru, nový klient bude směrován do serveru rozbočovače. V takovém případě klient přejde do režimu bez serveru. 

  To může způsobit problém. Například pokud dojde ke ztrátě všech připojení k serveru, budou někteří klienti zadávat režim bez serveru namísto směrování do serveru hub.

Tady jsou některé pokyny pro výběr režimu:
- Pokud není k dispozici žádný hub Server, vyberte `Serverless` .
- Pokud má všechna centra rozbočovače servery, vyberte `Default` .
- Pokud některé rozbočovače mají servery rozbočovače, ale jiné ne, můžete si vybrat `Classic` , ale to může způsobit problém. Lepším způsobem je vytvořit dvě instance: jedna je `Serverless` a druhá `Default` .

## <a name="are-there-any-feature-differences-in-using-azure-signalr-service-with-aspnet-signalr"></a>Existují nějaké rozdíly ve funkcích používání služby signalizace Azure pomocí ASP.NET signalizace?
Pokud používáte službu Azure Signaler, některá rozhraní API a funkce signálu ASP.NET nejsou podporované:
- Možnost předat libovolný stav mezi klienty a centrem (často se označuje jako `HubState` ) není podporována.
- `PersistentConnection`Třída není podporována.
- *Přenos snímků typu navždy* není podporován.
- Služba Azure Signaler již nehraje zprávy odesílané klientovi, když je klient v režimu offline.
- Pokud používáte službu Azure Signaler, provoz pro jedno připojení klienta se vždy směruje (označuje se také jako *rychlý*) na jednu instanci aplikačního serveru po dobu trvání připojení.

Podpora ASP.NET signalizace se zaměřuje na kompatibilitu, takže nejsou podporované všechny nové funkce nástroje ASP.NET Core Signal. Například *MessagePack* a *streamování* jsou k dispozici pouze pro ASP.NET Core aplikace Signal.

Službu Azure Signal Service můžete nakonfigurovat pro různé režimy služby: `Classic` , `Default` a `Serverless` . `Serverless`Režim není podporován pro ASP.NET. REST API roviny dat se také nepodporuje.

## <a name="where-does-my-data-reside"></a>Kde se nacházejí moje data?

Služba signalizace Azure funguje jako služba data Processor. Neuloží obsah zákazníka a zařadí se data, která jsou součástí návrhu. Pokud používáte službu signalizace Azure společně s dalšími službami Azure, jako je Azure Storage pro diagnostiku, přečtěte si [Tento dokument White Paper](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/) s pokyny, jak zachovat data v oblastech Azure.
