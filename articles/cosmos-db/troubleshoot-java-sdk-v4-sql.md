---
title: Diagnostika a řešení potíží s Azure Cosmos DB Java SDK v4
description: Pomocí funkcí, jako je protokolování na straně klienta a další nástroje třetích stran, můžete identifikovat, diagnostikovat a řešit potíže s Azure Cosmos DB v sadě Java SDK v4.
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 06/11/2020
ms.author: anfeldma
ms.devlang: java
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.custom: devx-track-java
ms.openlocfilehash: cba8b97adb40ca2c277268188ff6ad541c7e9676
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100596461"
---
# <a name="troubleshoot-issues-when-you-use-azure-cosmos-db-java-sdk-v4-with-sql-api-accounts"></a>Řešení potíží při použití Azure Cosmos DB Java SDK V4 s účty SQL API
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Sada Java SDK v4](troubleshoot-java-sdk-v4-sql.md)
> * [Sada Async Java SDK v2](troubleshoot-java-async-sdk.md)
> * [.NET](troubleshoot-dot-net-sdk.md)
> 

> [!IMPORTANT]
> Tento článek popisuje řešení potíží pouze v Azure Cosmos DB Java SDK v4. Další informace najdete v tématu poznámky k [verzi](sql-api-sdk-java-v4.md)Azure Cosmos DB Java SDK v4, [úložiště Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos)a [tipy ke zvýšení výkonu](performance-tips-java-sdk-v4-sql.md) . Pokud aktuálně používáte starší verzi než v4, přečtěte si článek průvodce [migrací do Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) , který vám pomůže s upgradem na V4.
>

Tento článek popisuje běžné problémy, alternativní řešení, diagnostické kroky a nástroje při použití Azure Cosmos DB Java SDK V4 s Azure Cosmos DBmi účty rozhraní SQL API.
Azure Cosmos DB Java SDK v4 poskytuje logickou reprezentaci na straně klienta pro přístup k rozhraní SQL API Azure Cosmos DB. Tento článek popisuje nástroje a přístupy, které vám pomůžou v případě jakýchkoli problémů.

Začněte s tímto seznamem:

* Prohlédněte si část [běžné problémy a alternativní řešení] v tomto článku.
* Podívejte se na sadu Java SDK v Azure Cosmos DB centrálním úložišti, které je k dispozici [pro open source na GitHubu](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos). Má aktivně monitorovanou [část s problémy](https://github.com/Azure/azure-sdk-for-java/issues) . Zkontrolujte, zda je již archivován případný podobný problém s alternativním řešením. Jedním z užitečných tipů je vyfiltrovat problémy pomocí značky *Cosmos: v4-Item* .
* Přečtěte si [tipy ke zvýšení výkonu](performance-tips-java-sdk-v4-sql.md) pro Azure Cosmos DB Java SDK v4 a použijte doporučené postupy.
* Pokud jste nenalezli řešení, přečtěte si zbytek tohoto článku. Pak zapište [problém GitHubu](https://github.com/Azure/azure-sdk-for-java/issues). Pokud je k dispozici možnost Přidat značky k vašemu problému GitHubu, přidejte značku *Cosmos: v4-Item* .

### <a name="retry-logic"></a>Logika opakování <a id="retry-logics"></a>
Pokud sada SDK služby Cosmos DB umožňuje opakování, při každém selhání vstupně-výstupních operací se sada SDK pokusí neúspěšnou operaci zopakovat Pokus o jakékoli selhání je dobrým zvykem, ale konkrétně při zpracování nebo opakování selhání zápisu je potřeba. Doporučuje se použít nejnovější sadu SDK, protože se nepřetržitě vylepšuje logika opakování.

1. Čtení a vstupně-výstupní chyby se budou opakovat sadou SDK, aniž by je zpřístupnění koncovým uživatelům.
2. Zápisy (Create, Upsert, Replace, DELETE) jsou "NOT" idempotentní a proto sada SDK nemůže vždy bez chybně opakovat operace zápisu. Je nutné, aby logika aplikace uživatele mohla zpracovat selhání a opakovat akci.
3. [Problémy s řešením dostupnosti sady SDK](troubleshoot-sdk-availability.md) popisují opakované pokusy pro Cosmos DB účty ve více oblastech.

## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>Běžné problémy a alternativní řešení

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>Problémy se sítí, neúspěšný časový limit čtení, nízká propustnost, vysoká latence

#### <a name="general-suggestions"></a>Obecné návrhy
Nejlepší výkon:
* Ujistěte se, že aplikace běží ve stejné oblasti jako váš Azure Cosmos DB účet. 
* Ověřte využití procesoru na hostiteli, kde je aplikace spuštěná. Pokud je využití CPU 50 nebo více, spusťte aplikaci na hostiteli s vyšší konfigurací. Nebo můžete zatížení distribuovat do více počítačů.
    * Pokud aplikaci spouštíte ve službě Azure Kubernetes, můžete [k monitorování využití procesoru použít Azure monitor](../azure-monitor/containers/container-insights-analyze.md).

#### <a name="connection-throttling"></a>Omezování připojení
Omezení připojení může nastat kvůli [limitu připojení na hostitelském počítači] nebo [vyčerpání portů Azure SNAT (Pat)].

##### <a name="connection-limit-on-a-host-machine"></a><a name="connection-limit-on-host"></a>Omezení počtu připojení na hostitelském počítači
Některé systémy Linux, jako je Red Hat, mají horní limit celkového počtu otevřených souborů. Sokety v systému Linux jsou implementovány jako soubory, takže toto číslo omezuje celkový počet připojení.
Spusťte následující příkaz.

```bash
ulimit -a
```
Počet povolených otevřených souborů, které jsou označeny jako "soubor", musí být alespoň dvojnásobnou velikostí fondu připojení. Další informace najdete v tématu [tipy k výkonu](performance-tips-java-sdk-v4-sql.md)Azure Cosmos DB Java SDK v4.

##### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Vyčerpání portů Azure SNAT (PAT)

Pokud je vaše aplikace nasazená v Azure Virtual Machines bez veřejné IP adresy, ve výchozím nastavení [porty Azure SNAT](../load-balancer/load-balancer-outbound-connections.md#preallocatedports) naváže připojení ke koncovému bodu mimo váš virtuální počítač. Počet připojení povolených z virtuálního počítače do koncového bodu Azure Cosmos DB je omezen [konfigurací Azure SNAT](../load-balancer/load-balancer-outbound-connections.md#preallocatedports).

 Porty Azure SNAT se používají jenom v případě, že váš virtuální počítač má privátní IP adresu a proces z virtuálního počítače se pokusí připojit k veřejné IP adrese. Omezení Azure SNAT se vyhnete dvěma řešením:

* Přidejte koncový bod služby Azure Cosmos DB do podsítě vaší virtuální sítě Azure Virtual Machines. Další informace najdete v tématu [koncové body služby Azure Virtual Network](../virtual-network/virtual-network-service-endpoints-overview.md). 

    Pokud je povolen koncový bod služby, žádosti již nejsou odesílány z veřejné IP adresy do Azure Cosmos DB. Místo toho se pošle identita virtuální sítě a podsítě. Tato změna může vést k poklesu brány firewall, pokud jsou povolené jenom veřejné IP adresy. Pokud používáte bránu firewall a povolíte koncový bod služby, přidejte do brány firewall podsíť pomocí [Virtual Network ACL](/previous-versions/azure/virtual-network/virtual-networks-acl).
* Přiřaďte k VIRTUÁLNÍmu počítači Azure veřejnou IP adresu.

##### <a name="cant-reach-the-service---firewall"></a><a name="cant-connect"></a>Nejde se připojit ke službě – firewall
``ConnectTimeoutException`` indikuje, že sada SDK nemůže získat přístup ke službě.
Při použití přímého režimu se může zobrazit chyba podobná této:
```
GoneException{error=null, resourceAddress='https://cdb-ms-prod-westus-fd4.documents.azure.com:14940/apps/e41242a5-2d71-5acb-2e00-5e5f744b12de/services/d8aa21a5-340b-21d4-b1a2-4a5333e7ed8a/partitions/ed028254-b613-4c2a-bf3c-14bd5eb64500/replicas/131298754052060051p//', statusCode=410, message=Message: The requested resource is no longer available at the server., getCauseInfo=[class: class io.netty.channel.ConnectTimeoutException, message: connection timed out: cdb-ms-prod-westus-fd4.documents.azure.com/101.13.12.5:14940]
```

Pokud máte na počítači aplikace spuštěnou bránu firewall, otevřete rozsah portů 10 000 až 20 000, který je používán přímým režimem.
Také dodržujte [limit připojení na hostitelském počítači](#connection-limit-on-host).

#### <a name="http-proxy"></a>Proxy server HTTP

Pokud používáte proxy server HTTP, ujistěte se, že může podporovat počet připojení nakonfigurovaných v sadě SDK `ConnectionPolicy` .
Jinak čelíte problémům s připojením.

#### <a name="invalid-coding-pattern-blocking-netty-io-thread"></a>Neplatný vzor kódování: blokuje se vstupně-výstupní podproces.

Sada SDK používá ke komunikaci s Azure Cosmos DB knihovnu [v/v](https://netty.io/) . Sada SDK obsahuje asynchronní rozhraní API a používá neblokované vstupně-výstupní rozhraní API síťoviny. Pracovní operace sady SDK se provádí v vláknech v případě vstupně-výstupních operací. Počet podprocesů v/v v konfiguraci je stejný jako počet PROCESORových jader počítače aplikace. 

Vlákna v/v v/v se mají použít jenom pro neblokovou práci v/v. Sada SDK vrátí výsledek volání rozhraní API v jednom z vstupně-výstupních vláken do kódu aplikace. Pokud aplikace provádí dlouhodobou operaci poté, co obdrží výsledky ve vláknu síťoviny, sada SDK nemusí mít k dispozici dostatek vstupně-výstupních vláken, aby bylo možné provést svou interní práci v/v. Takové kódování aplikace může vést k nízké propustnosti, vysoké latenci a `io.netty.handler.timeout.ReadTimeoutException` selhání. Alternativním řešením je přepnutí vlákna, když víte, že operace trvá déle.

Podívejte se například na následující fragment kódu, který přidá položky do kontejneru ( [tady](create-sql-api-java.md) najdete pokyny k nastavení databáze a kontejneru). Můžete provádět dlouhodobou práci, která trvá více než několik milisekund na vláknech síťoviny. V takovém případě se můžete dostat do stavu, ve kterém nejsou k dispozici žádná vstupně-výstupní vlákna pro zpracování vstupně-výstupních operací. V důsledku toho se zobrazí ReadTimeoutException selhání.

### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-readtimeout"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Async API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=TroubleshootNeedsSchedulerAsync)]

Alternativním řešením je změnit vlákno, ve kterém provádíte práci, která trvá určitou dobu. Definujte instanci typu Singleton Scheduleru pro vaši aplikaci.

### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-scheduler"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Async API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=TroubleshootCustomSchedulerAsync)]

Možná budete muset udělat práci, která trvá určitou dobu, například výpočetně těžkou práci nebo blokování v/v. V takovém případě přepněte vlákno na pracovní proces poskytnutý pomocí `customScheduler` `.publishOn(customScheduler)` rozhraní API.

### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-apply-custom-scheduler"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Async API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=TroubleshootPublishOnSchedulerAsync)]

Pomocí nástroje `publishOn(customScheduler)` uvolníte vstupně-výstupní vlákna a přepnete do vlastního vlákna poskytnutého vlastním plánovačem. Tato úprava vyřeší problém. Už nebudete mít k `io.netty.handler.timeout.ReadTimeoutException` chybu.

### <a name="request-rate-too-large"></a>Příliš velký počet požadavků
Tato chyba je selhání na straně serveru. Indikuje, že jste využili zřízenou propustnost. Zkuste to znovu později. Pokud se toto selhání často dostanou, zvažte zvýšení propustnosti kolekce.

* **Implementace omezení rychlosti v intervalech getRetryAfterInMilliseconds**

    Během testování výkonu byste měli zvýšit zatížení až do omezení malých sazeb požadavků. V případě omezení by se klientská aplikace měla omezení rychlosti pro interval opakování zadaný serverem. Respektování omezení rychlosti zajistí, že strávíte minimální dobu čekání mezi opakovanými pokusy.

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>Chyba při připojování k emulátoru Azure Cosmos DB

Certifikát HTTPS emulátoru Azure Cosmos DB je podepsaný svým držitelem. Aby sada SDK mohla spolupracovat s emulátorem, importujte certifikát emulátoru do Java TrustStore. Další informace najdete v tématu [export certifikátů emulátoru Azure Cosmos DB](local-emulator-export-ssl-certificates.md).

### <a name="dependency-conflict-issues"></a>Problémy s konfliktem závislostí

Sada Azure Cosmos DB Java SDK si vyžádá několik závislostí. Obecně řečeno, pokud strom závislosti projektu obsahuje starší verzi artefaktu, který Azure Cosmos DB Java SDK závisí na, může to vést k neočekávaným chybám, které se generují při spuštění aplikace. Pokud ladíte, proč vaše aplikace neočekávaně vyvolá výjimku, je vhodné dvakrát ověřit, zda se strom závislosti nechtěně nepoužívá ve starší verzi jedné nebo více Azure Cosmos DB závislostí Java SDK.

Alternativním řešením tohoto problému je určit, které z vašich závislostí projektu přináší starou verzi a vyloučit přenosnou závislost na této starší verzi, a Azure Cosmos DB Java SDK zajistit, aby se do novější verze připojila.

Chcete-li určit, která z vašich závislostí projektu přináší starší verzi něco, co Azure Cosmos DB Java SDK závisí na, spusťte následující příkaz pro projekt pom.xml souboru:
```bash
mvn dependency:tree
```
Další informace najdete v příručce ke [stromu závislostí Maven](https://maven.apache.org/plugins/maven-dependency-plugin/examples/resolving-conflicts-using-the-dependency-tree.html).

Jakmile zjistíte, která závislost projektu závisí na starší verzi, můžete upravit závislost na této lib v souboru pom a vyloučit přenosnou závislost, a to podle následujícího příkladu (což předpokládá, že *reaktor-Core* je zastaralá závislost):

```xml
<dependency>
  <groupId>${groupid-of-lib-which-brings-in-reactor}</groupId>
  <artifactId>${artifactId-of-lib-which-brings-in-reactor}</artifactId>
  <version>${version-of-lib-which-brings-in-reactor}</version>
  <exclusions>
    <exclusion>
      <groupId>io.projectreactor</groupId>
      <artifactId>reactor-core</artifactId>
    </exclusion>
  </exclusions>
</dependency>
```

Další informace najdete v [Průvodci vyloučením přenosné závislosti](https://maven.apache.org/guides/introduction/introduction-to-optional-and-excludes-dependencies.html).


## <a name="enable-client-sdk-logging"></a><a name="enable-client-sice-logging"></a>Povolit protokolování klientské sady SDK

Azure Cosmos DB Java SDK v4 používá SLF4j jako přihlašování, které podporuje přihlášení do oblíbených protokolovacích rozhraní, jako je log4j a logback.

Například pokud chcete jako protokolovacího rozhraní používat log4j, přidejte do cesty tříd Java následující knihovny.

```xml
<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-log4j12</artifactId>
  <version>${slf4j.version}</version>
</dependency>
<dependency>
  <groupId>log4j</groupId>
  <artifactId>log4j</artifactId>
  <version>${log4j.version}</version>
</dependency>
```

Přidejte také log4j config.
```
# this is a sample log4j configuration

# Set root logger level to INFO and its only appender to A1.
log4j.rootLogger=INFO, A1

log4j.category.com.azure.cosmos=INFO
#log4j.category.io.netty=OFF
#log4j.category.io.projectreactor=OFF
# A1 is set to be a ConsoleAppender.
log4j.appender.A1=org.apache.log4j.ConsoleAppender

# A1 uses PatternLayout.
log4j.appender.A1.layout=org.apache.log4j.PatternLayout
log4j.appender.A1.layout.ConversionPattern=%d %5X{pid} [%t] %-5p %c - %m%n
```

Další informace najdete v tématu [Ruční protokolování sfl4j](https://www.slf4j.org/manual.html).

## <a name="os-network-statistics"></a><a name="netstats"></a>Statistika sítě operačního systému
Spusťte příkaz netstat, abyste získali představu o počtu připojení ve státech, jako jsou `ESTABLISHED` a `CLOSE_WAIT` .

V systému Linux můžete spustit následující příkaz.
```bash
netstat -nap
```

Ve Windows můžete spustit stejný příkaz s různými příznaky argumentu:
```bash
netstat -abn
```

Vyfiltruje výsledek jenom na připojení ke koncovému bodu Azure Cosmos DB.

Počet připojení ke koncovému bodu Azure Cosmos DB ve `ESTABLISHED` stavu nemůže být větší než nakonfigurovaná velikost fondu připojení.

Mnoho připojení ke koncovému bodu Azure Cosmos DB může být ve `CLOSE_WAIT` stavu. Může jich být více než 1 000. Číslo, které je vysoké, znamená, že jsou připojení navázána a rychlá. Tato situace může způsobovat problémy. Další informace najdete v části [běžné problémy a alternativní řešení] .

 <!--Anchors-->
[Běžné problémy a alternativní řešení]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[Omezení počtu připojení na hostitelském počítači]: #connection-limit-on-host
[Vyčerpání portů Azure SNAT (PAT)]: #snat
