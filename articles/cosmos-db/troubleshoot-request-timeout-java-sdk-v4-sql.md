---
title: Řešení potíží s Azure Cosmos DB HTTP 408 nebo vyžádat problémy s vypršením časového limitu pomocí sady Java v4 SDK
description: Naučte se diagnostikovat a opravovat výjimky pro časový limit požadavku Java SDK pomocí sady Java v4 SDK.
author: kushagrathapar
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 10/28/2020
ms.author: kuthapar
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 442d6638e88462b1dc87e9321dc631fe0a4f3a10
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340066"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-java-v4-sdk-request-timeout-exceptions"></a>Diagnostika a řešení potíží s Azure Cosmos DBmi výjimkami časového limitu požadavku sady Java v4 SDK
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

K chybě HTTP 408 dojde v případě, že sada SDK nemohla dokončit požadavek předtím, než došlo k vypršení časového limitu.

## <a name="troubleshooting-steps"></a>Postup při řešení potíží
Následující seznam obsahuje známé příčiny a řešení pro výjimky časového limitu požadavku.

### <a name="high-cpu-utilization"></a>Vysoké využití procesoru
Vysoké využití procesoru je nejběžnější případ. Pro zajištění optimální latence by využití CPU mělo zhruba 40 procent. Použijte 10 sekund jako interval pro monitorování maximálního (neprůměrného) využití procesoru. Špičky procesoru jsou běžnější u dotazů mezi oddíly, kde může provádět více připojení pro jeden dotaz.

#### <a name="solution"></a>Řešení:
Klientská aplikace, která používá sadu SDK, by se měla škálovat nahoru nebo dolů.

### <a name="connection-throttling"></a>Omezování připojení
Omezení připojení může nastat kvůli limitu připojení na hostitelském počítači nebo vyčerpání portů Azure SNAT (PAT).

### <a name="connection-limit-on-a-host-machine"></a>Omezení počtu připojení na hostitelském počítači
Některé systémy Linux, jako je Red Hat, mají horní limit celkového počtu otevřených souborů. Sokety v systému Linux jsou implementovány jako soubory, takže toto číslo omezuje celkový počet připojení. Spusťte následující příkaz.

```bash
ulimit -a
```

#### <a name="solution"></a>Řešení:
Počet povolených otevřených souborů, které jsou označeny jako "soubor", musí být alespoň 10 000 nebo více. Další informace najdete v tématu [tipy k výkonu](performance-tips-java-sdk-v4-sql.md)Azure Cosmos DB Java SDK v4.

### <a name="socket-or-port-availability-might-be-low"></a>Dostupnost soketu nebo portu může být nízká.
Při spuštění v Azure můžou klienti, kteří používají Java SDK, narazit na vyčerpání portů Azure SNAT (PAT).

#### <a name="solution-1"></a>1. řešení:
Pokud pracujete na virtuálních počítačích Azure, postupujte podle pokynů v [Průvodci vyčerpáním portů SNAT](troubleshoot-java-sdk-v4-sql.md#snat).

#### <a name="solution-2"></a>2. řešení:
Pokud používáte v Azure App Service, postupujte podle pokynů pro [řešení potíží s chybami připojení](../app-service/troubleshoot-intermittent-outbound-connection-errors.md#cause) a [použijte diagnostiku App Service](https://azure.github.io/AppService/2018/03/01/Deep-Dive-into-TCP-Connections-in-App-Service-Diagnostics.html).

#### <a name="solution-3"></a>Řešení 3:
Pokud používáte na Azure Functions, ověřte, že jste se dotýkali [Azure Functions doporučení](../azure-functions/manage-connections.md#static-clients) k údržbě singleton nebo statických klientů pro všechny příslušné služby (včetně Azure Cosmos DB). Ověřte [omezení služby](../azure-functions/functions-scale.md#service-limits) na základě typu a velikosti Function App hostování.

#### <a name="solution-4"></a>Řešení 4:
Pokud používáte proxy server HTTP, ujistěte se, že může podporovat počet připojení nakonfigurovaných v sadě SDK `GatewayConnectionConfig` . Jinak čelíte problémům s připojením.

### <a name="create-multiple-client-instances"></a>Vytvoření více instancí klienta
Vytvoření více instancí klienta může vést k problémům s kolizem a vypršením časového limitu připojení.

#### <a name="solution-1"></a>1. řešení:
Postupujte podle [tipů ke zvýšení výkonu](performance-tips-java-sdk-v4-sql.md#sdk-usage)a v celé aplikaci použijte jednu instanci CosmosClient.

#### <a name="solution-2"></a>2. řešení:
Pokud v aplikaci nemůžete mít singleton CosmosClient, doporučujeme používat sdílení připojení v rámci více klientů Cosmos prostřednictvím tohoto rozhraní API `connectionSharingAcrossClientsEnabled(true)` v CosmosClient. Pokud máte více instancí klienta Cosmos ve stejném JVM, kteří pracují s více účty Cosmos, umožní to sdílení připojení v přímém režimu, pokud je to možné mezi instancemi klienta Cosmos. Upozorňujeme, že při nastavení této možnosti se pro všechny ostatní instance klientů použije konfigurace připojení (např. konfigurace časového limitu soketu, konfigurace časového limitu nečinnosti) prvního vytvořeného klienta.

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
* [Diagnostikujte a řešte](troubleshoot-java-sdk-v4-sql.md) potíže při použití Azure Cosmos DB Java v4 SDK.
* Přečtěte si o zásadách výkonu pro [jazyk Java v4](performance-tips-java-sdk-v4-sql.md).
