---
title: Nejčastější dotazy ke službě Azure Signal
description: Získejte odpovědi na nejčastější dotazy týkající se služby Azure Signal, včetně řešení potíží a typických scénářů použití.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.custom: devx-track-dotnet
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 910de9efbd132fb98a0c4bd596867800f65f5ad5
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150960"
---
# <a name="azure-signalr-service-faq"></a>Nejčastější dotazy ke službě Azure Signal

## <a name="is-azure-signalr-service-ready-for-production-use"></a>Je služba signálu Azure připravená na použití v produkčním prostředí?

Ano, všeobecně dostupná je podpora pro nástroj [ASP.NET Core signaler](https://dotnet.microsoft.com/apps/aspnet/signalr) i [signalizace ASP.NET](/aspnet/signalr/overview/getting-started/introduction-to-signalr) .

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

Ne.

Služba signalizace Azure poskytuje všechny tři přenosy, které ASP.NET Core signál podporuje ve výchozím nastavení. Nedá se nakonfigurovat. Služba signalizace Azure zpracuje připojení a přenosy pro všechna připojení klientů.

Můžete nakonfigurovat přenosy na straně klienta, jak je popsáno v [ASP.NET Core konfigurace signalizace](/aspnet/core/signalr/configuration#configure-allowed-transports-1).

## <a name="what-is-the-meaning-of-metrics-like-message-count-or-connection-count-shown-in-the-azure-portal-which-kind-of-aggregation-type-should-i-choose"></a>Jaký je význam metrik, jako je počet zpráv nebo počet připojení zobrazený v Azure Portal? Jaký druh agregačního typu mám zvolit?

Můžete najít podrobnosti o tom, jak tyto metriky ve [zprávách a připojeních ve službě Azure Signal Service](signalr-concept-messages-and-connections.md)počítat.

V podokně Přehled prostředků služby signalizace Azure jsme už pro vás zvolili odpovídající typ agregace. Pokud přejdete do podokna metrik, můžete jako referenci použít typ agregace pro [zprávy a připojení ve službě Azure Signal](../azure-monitor/platform/metrics-supported.md#microsoftsignalrservicesignalr) .

## <a name="what-is-the-meaning-of-the-default-serverless-and-classic-service-modes-how-can-i-choose"></a>Jaký je význam `Default` `Serverless` režimů, a `Classic` služby? Jak si můžu vybrat?

Pro nové aplikace by se mělo použít jenom výchozí režim bez serveru. Hlavní rozdíl je bez ohledu na to, zda máte aplikační servery, které navážou připojení serveru ke službě (tj. slouží `AddAzureSignalR()` k připojení ke službě). Pokud ano, použijte výchozí režim, jinak použít režim bez serveru.

Klasický režim je určený pro zpětnou kompatibilitu pro existující aplikace, takže by se neměl používat pro nové aplikace.

Další informace o režimu služby v [tomto dokumentu](concept-service-mode.md).

## <a name="can-i-send-message-from-client-in-serverless-mode"></a>Můžu poslat zprávu z klienta v režimu bez serveru?

Můžete odeslat zprávu z klienta, pokud nakonfigurujete v instanci signalizace možnost nadřazený. Nadřazený objekt je sada koncových bodů, které mohou přijímat zprávy a události připojení ze služby signalizace. Pokud není nakonfigurované žádné nadřazené, budou se zprávy z klienta ignorovat.

Další informace o nadřazeného zobrazení najdete v [tomto dokumentu](concept-upstream.md).

V současnosti je ve verzi Public Preview.

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