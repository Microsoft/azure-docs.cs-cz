---
title: Řešení potíží s Azure Cosmos DB HTTP 408 nebo vyžádat problémy s časovým limitem pro sadu .NET SDK
description: Přečtěte si, jak diagnostikovat a opravit výjimky pro časový limit požadavku .NET SDK.
author: j82w
ms.service: cosmos-db
ms.date: 08/06/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: 735c098fcf6fed76019850a1cb58d9eb6c485b7a
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93101013"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-net-sdk-request-timeout-exceptions"></a>Diagnostika a řešení potíží s Azure Cosmos DBmi výjimkami časového limitu požadavku .NET SDK
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

K chybě HTTP 408 dojde v případě, že sada SDK nemohla dokončit požadavek předtím, než došlo k vypršení časového limitu.

## <a name="customize-the-timeout-on-the-azure-cosmos-db-net-sdk"></a>Přizpůsobení časového limitu Azure Cosmos DB .NET SDK

Sada SDK obsahuje dvě odlišné alternativy k řízení časových limitů, z nichž každý má jiný obor.

### <a name="requesttimeout"></a>RequestTimeout

`CosmosClientOptions.RequestTimeout`Konfigurace (nebo `ConnectionPolicy.RequestTimeout` pro sadu SDK v2) umožňuje nastavit časový limit, který ovlivňuje jednotlivé požadavky sítě. Operace spuštěná uživatelem může zahrnovat více síťových požadavků (například může být omezeno). Tato konfigurace by se měla použít pro každý požadavek sítě při opakovaném pokusu. Tento časový limit není časovým limitem požadavku na dokončení operace.

### <a name="cancellationtoken"></a>CancellationToken

Všechny asynchronní operace v sadě SDK mají volitelný parametr CancellationToken. Tento parametr [CancellationToken](/dotnet/standard/threading/how-to-listen-for-cancellation-requests-by-polling) se používá v celé operaci v rámci všech síťových požadavků. V mezi síťovými požadavky může být zkontrolován token zrušení a operace byla zrušena, pokud vypršela platnost souvisejícího tokenu. Token zrušení by měl být použit k definování přibližné očekávaného časového limitu oboru operace.

> [!NOTE]
> `CancellationToken`Parametr je mechanismus, ve kterém bude knihovna kontrolovat zrušení, pokud to [nezpůsobí neplatnost stavu](https://devblogs.microsoft.com/premier-developer/recommended-patterns-for-cancellationtoken/). Operace se nemusí zrušit přesně tehdy, když je čas definovaný ve zrušení. Místo toho se po uplynutí této doby zruší, jakmile to bude bezpečné.

## <a name="troubleshooting-steps"></a>Postup při řešení potíží
Následující seznam obsahuje známé příčiny a řešení pro výjimky časového limitu požadavku.

### <a name="high-cpu-utilization"></a>Vysoké využití procesoru
Vysoké využití procesoru je nejběžnější případ. Pro zajištění optimální latence by využití CPU mělo zhruba 40 procent. Použijte 10 sekund jako interval pro monitorování maximálního (neprůměrného) využití procesoru. Špičky procesoru jsou běžnější u dotazů mezi oddíly, kde může provádět více připojení pro jeden dotaz.

#### <a name="solution"></a>Řešení:
Klientská aplikace, která používá sadu SDK, by se měla škálovat nahoru nebo dolů.

### <a name="socket-or-port-availability-might-be-low"></a>Dostupnost soketu nebo portu může být nízká.
Při spuštění v Azure můžou klienti, kteří používají .NET SDK, dosáhnout vyčerpání portů Azure SNAT (PAT).

#### <a name="solution-1"></a>1. řešení:
Pokud pracujete na virtuálních počítačích Azure, postupujte podle pokynů v [Průvodci vyčerpáním portů SNAT](troubleshoot-dot-net-sdk.md#snat).

#### <a name="solution-2"></a>2. řešení:
Pokud používáte v Azure App Service, postupujte podle pokynů pro [řešení potíží s chybami připojení](../app-service/troubleshoot-intermittent-outbound-connection-errors.md#cause) a [použijte diagnostiku App Service](https://azure.github.io/AppService/2018/03/01/Deep-Dive-into-TCP-Connections-in-App-Service-Diagnostics.html).

#### <a name="solution-3"></a>Řešení 3:
Pokud používáte na Azure Functions, ověřte, že jste se dotýkali [Azure Functions doporučení](../azure-functions/manage-connections.md#static-clients) k údržbě singleton nebo statických klientů pro všechny příslušné služby (včetně Azure Cosmos DB). Ověřte [omezení služby](../azure-functions/functions-scale.md#service-limits) na základě typu a velikosti Function App hostování.

#### <a name="solution-4"></a>Řešení 4:
Pokud používáte proxy server HTTP, ujistěte se, že může podporovat počet připojení nakonfigurovaných v sadě SDK `ConnectionPolicy` . Jinak čelíte problémům s připojením.

### <a name="create-multiple-client-instances"></a>Vytvoření více instancí klienta
Vytvoření více instancí klienta může vést k problémům s kolizem a vypršením časového limitu připojení.

#### <a name="solution"></a>Řešení:
Postupujte podle [tipů ke zvýšení výkonu](performance-tips-dotnet-sdk-v3-sql.md#sdk-usage)a v celém procesu použijte jednu instanci CosmosClient.

### <a name="hot-partition-key"></a>Klíč horkého oddílu
Azure Cosmos DB distribuuje celkovou zřízenou propustnost rovnoměrně napříč fyzickými oddíly. Pokud je k dispozici aktivní oddíl, jeden nebo více klíčů logického oddílu na fyzickém oddílu spotřebovává všechny jednotky žádostí fyzického oddílu za sekundu (RU/s). V současné době se RU/s na ostatních fyzických oddílech nepoužívá. Celkový počet spotřebovaných RU/s v podobě příznaku bude menší než celkové zřízené RU/s v databázi nebo kontejneru, ale pořád se vám v žádostech na aktivním klíči logického oddílu stále zobrazuje omezení (429s). Pomocí [normalizované metriky pro spotřebu ru](monitor-normalized-request-units.md) můžete zjistit, jestli se u úlohy objevil aktivní oddíl. 

#### <a name="solution"></a>Řešení:
Vyberte dobrý klíč oddílu, který rovnoměrně distribuuje objem a úložiště žádostí. Přečtěte si, jak [změnit klíč oddílu](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/).

### <a name="high-degree-of-concurrency"></a>Vysoký stupeň souběžnosti
Aplikace provádí vysokou úroveň souběžnosti, což může vést k kolizí na kanálu.

#### <a name="solution"></a>Řešení:
Klientská aplikace, která používá sadu SDK, by se měla škálovat nahoru nebo dolů.

### <a name="large-requests-or-responses"></a>Velké požadavky nebo odpovědi
Velké žádosti nebo odpovědi mohou vést k blokování na úrovni řádků a exacerbate, a to i s poměrně nízkou mírou souběžnosti.

#### <a name="solution"></a>Řešení:
Klientská aplikace, která používá sadu SDK, by se měla škálovat nahoru nebo dolů.

### <a name="failure-rate-is-within-the-azure-cosmos-db-sla"></a>Frekvence selhání je v rámci smlouvy SLA Azure Cosmos DB.
Aplikace by měla být schopna zpracovávat přechodná selhání a v případě potřeby opakovat akci. Jakékoli výjimky 408 se neopakují, protože při vytváření cest není možné zjistit, zda služba vytvořila položku nebo ne. Opětovné odeslání stejné položky pro vytvoření způsobí výjimku z důvodu konfliktu. Uživatelské aplikace obchodní logika mohou mít vlastní logiku pro zpracování konfliktů, která by byla přerušena z nejednoznačnosti existující položky a v konfliktu s vytvořením opakování.

### <a name="failure-rate-violates-the-azure-cosmos-db-sla"></a>Frekvence neúspěšných porušování Azure Cosmos DB smlouvy SLA
Kontaktujte [podporu Azure](https://aka.ms/azure-support).

## <a name="next-steps"></a>Další kroky
* [Diagnostikujte a řešte](troubleshoot-dot-net-sdk.md) potíže při použití sady Azure Cosmos DB .NET SDK.
* Seznamte se s pokyny k výkonu pro [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) a [.NET v2](performance-tips.md).