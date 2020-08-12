---
title: Řešení potíží s Azure Cosmos DB HTTP 408 nebo vyžádat problémy s časovým limitem sady .NET SDK
description: Jak diagnostikovat a opravit výjimku pro časový limit požadavku .NET SDK
author: j82w
ms.service: cosmos-db
ms.date: 08/06/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 09442e01fa160d3851169a51230fa4cbef7e0980
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/11/2020
ms.locfileid: "88118565"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-net-sdk-request-timeout"></a>Diagnostika a řešení potíží s Azure Cosmos DB časový limit požadavku .NET SDK
K chybě HTTP 408 dojde v případě, že sada SDK nemohla dokončit požadavek předtím, než dojde k vypršení časového limitu.

## <a name="customizing-the-timeout-on-the-azure-cosmos-net-sdk"></a>Přizpůsobení časového limitu pro sadu Azure Cosmos .NET SDK

Sada SDK obsahuje dvě odlišné alternativy k řízení časových limitů, z nichž každý má jiný obor.

### <a name="requesttimeout"></a>RequestTimeout

`CosmosClientOptions.RequestTimeout`Konfigurace (nebo `ConnectionPolicy.RequestTimeout` pro sadu SDK v2) umožňuje nastavit časový limit, který ovlivňuje jednotlivé požadavky sítě.  Operace spuštěná uživatelem může zahrnovat více síťových požadavků (například může být omezeno) a tato konfigurace by se měla použít pro každý požadavek sítě při opakovaném pokusu. Toto není časový limit požadavku na koncovou operaci.

### <a name="cancellationtoken"></a>CancellationToken

Všechny asynchronní operace v sadě SDK mají volitelný parametr CancellationToken. Tento [CancellationToken](https://docs.microsoft.com/dotnet/standard/threading/how-to-listen-for-cancellation-requests-by-polling) se používá v celé operaci v rámci všech síťových požadavků. V-mezi síťovými požadavky může být CancellationToken zkontrolováno a operace byla zrušena, pokud vypršela platnost souvisejícího tokenu. CancellationToken by měla být použita k definování přibližného očekávaného časového limitu oboru operace.

> [!NOTE]
> CancellationToken je mechanismus, ve kterém bude knihovna kontrolovat zrušení, pokud to [nezpůsobí neplatnost stavu](https://devblogs.microsoft.com/premier-developer/recommended-patterns-for-cancellationtoken/). Operace se nemusí zrušit přesně tehdy, když je čas definovaný ve zrušení, ale po uplynutí času se zruší, až to bude bezpečné.

## <a name="troubleshooting-steps"></a>Postup při řešení potíží
Následující seznam obsahuje známé příčiny a řešení pro výjimky časového limitu požadavku.

### <a name="1-high-cpu-utilization-most-common-case"></a>1. vysoké využití procesoru (Nejběžnější případ)
Pro zajištění optimální latence doporučujeme, aby využití CPU mělo přibližně 40%. Doporučuje se použít 10 sekund jako interval pro monitorování maximálního (neprůměrného) využití procesoru. Špičky procesoru jsou běžnější u různých dotazů na oddíly, kde může provádět více připojení pro jeden dotaz.

#### <a name="solution"></a>Řešení:
Klientská aplikace, která používá sadu SDK, by měla škálovat nahoru nebo dolů.

### <a name="2-socket--port-availability-might-be-low"></a>2. je možné, že dostupnost soketu/portu bude nízká.
Při spuštění v Azure můžou klienti, kteří používají .NET SDK, dosáhnout vyčerpání portů Azure SNAT (PAT).

#### <a name="solution-1"></a>1. řešení:
Pokud pracujete na virtuálních počítačích Azure, postupujte podle pokynů v [Průvodci vyčerpáním portů SNAT](troubleshoot-dot-net-sdk.md#snat).

#### <a name="solution-2"></a>2. řešení:
Pokud používáte v Azure App Service, postupujte podle pokynů pro [řešení potíží s chybami připojení](../app-service/troubleshoot-intermittent-outbound-connection-errors.md#cause) a [použijte diagnostiku App Service](https://azure.github.io/AppService/2018/03/01/Deep-Dive-into-TCP-Connections-in-App-Service-Diagnostics.html).

#### <a name="solution-3"></a>Řešení 3:
Pokud používáte na Azure Functions, ověřte, že se vám [Azure Functions doporučení](../azure-functions/manage-connections.md#static-clients) pro údržbu nejednoznačných a statických klientů pro všechny zahrnuté služby (včetně Cosmos DB), a zkontrolovat [omezení služby](../azure-functions/functions-scale.md#service-limits) na základě typu a velikosti vašeho Function App hostování.

#### <a name="solution-4"></a>Řešení 4:
Pokud používáte proxy server HTTP, ujistěte se, že může podporovat počet připojení nakonfigurovaných v sadě SDK `ConnectionPolicy` .
Jinak čelíte problémům s připojením.

### <a name="3-creating-multiple-client-instances"></a>3. vytvoření více instancí klienta
Vytvoření více instancí klienta může vést k problémům s kolizem a vypršením časového limitu připojení.

#### <a name="solution"></a>Řešení:
Postupujte podle [tipů ke zvýšení výkonu](performance-tips-dotnet-sdk-v3-sql.md#sdk-usage)a v celém procesu použijte jednu instanci CosmosClient.

### <a name="4-hot-partition-key"></a>4. klíč oddílu Hot
Azure Cosmos DB distribuuje celkovou zřízenou propustnost rovnoměrně napříč fyzickými oddíly. Pokud je k dispozici aktivní oddíl, jeden nebo více klíčů logického oddílu na fyzickém oddílu spotřebovává RU/s fyzického oddílu, zatímco RU/s na ostatních fyzických oddílech se nepoužívá. Celkový počet spotřebovaných RU/s v podobě příznaku bude nižší než celkové zřízené RU/s v databázi nebo kontejneru, ale přesto se v požadavcích na aktivním klíči logického oddílu stále zobrazuje omezení (429s). Pomocí [normalizované metriky pro spotřebu ru](monitor-normalized-request-units.md) můžete zjistit, jestli se u úlohy objevil aktivní oddíl. 

#### <a name="solution"></a>Řešení:
Vyberte dobrý klíč oddílu, který rovnoměrně distribuuje objem a úložiště žádostí. Přečtěte si, jak [změnit klíč oddílu](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/).

### <a name="5-high-degree-of-concurrency"></a>5. vysoká úroveň souběžnosti
Aplikace provádí vysokou úroveň souběžnosti, což může vést k kolizí na kanálu.

#### <a name="solution"></a>Řešení:
Klientská aplikace, která používá sadu SDK, by měla škálovat nahoru nebo dolů.

### <a name="6-large-requests-andor-responses"></a>6. velkých požadavků a odpovědí
Velké žádosti nebo odpovědi mohou vést k blokování na úrovni řádků a exacerbate, a to i s poměrně nízkou mírou souběžnosti.

#### <a name="solution"></a>Řešení:
Klientská aplikace, která používá sadu SDK, by měla škálovat nahoru nebo dolů.

### <a name="7-failure-rate-is-within-cosmos-db-sla"></a>7. frekvence selhání je v rámci Cosmos DB SLA.
Aplikace by měla být schopna zpracovávat přechodná selhání a v případě potřeby opakovat akci. výjimky 408 se neopakují, protože při vytváření cest není možné zjistit, zda služba vytvořila položku, nebo pokud nebyla. Opětovné odeslání stejné položky pro vytvoření způsobí výjimku z důvodu konfliktu. Uživatelské aplikace obchodní logika mohou mít vlastní logiku pro zpracování konfliktů, která by byla přerušena z nejednoznačnosti stávající položky vs. konflikt z operace vytvořit opakování.

### <a name="8-failure-rate-is-violating-the-cosmos-db-sla"></a>8. počet neúspěšných porušování Cosmos DB smlouvy SLA
Obraťte se prosím na podporu Azure.

## <a name="next-steps"></a>Další kroky
* [Diagnostika a řešení potíží](troubleshoot-dot-net-sdk.md) při použití Azure Cosmos DB .NET SDK
* Informace o zásadách výkonu pro [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) a [.NET v2](performance-tips.md)
