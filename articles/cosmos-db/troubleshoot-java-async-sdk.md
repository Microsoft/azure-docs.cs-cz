---
title: Diagnostika a řešení potíží s Azure Cosmos DB Java asynchronní SDK | Dokumentace Microsoftu
description: Použití funkcí, jako je protokolování na straně klienta a další nástroje třetích stran k identifikaci, diagnostice a řešení potíží s Azure Cosmos DB.
services: cosmos-db
author: moderakh
ms.service: cosmos-db
ms.topic: troubleshoot
ms.date: 10/28/2018
ms.author: moderakh
ms.devlang: java
ms.component: cosmosdb-sql
ms.openlocfilehash: ef1d2d0751bf1b1a7ee88fbf37e44e6316dee8f8
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50249861"
---
# <a name="troubleshooting-issues-when-using-java-async-sdk-with-azure-cosmos-db-sql-api-accounts"></a>Řešení potíží při použití sady Java SDK pro asynchronní s účty SQL API služby Azure Cosmos DB
Tento článek popisuje běžné problémy, řešení, kroky pro diagnostiku a nástroje, při použití [ADK Async Javu](sql-api-sdk-async-java.md) s účty SQL API služby Azure Cosmos DB.
Sada SDK Java asynchronní poskytuje logickou reprezentaci na straně klienta pro přístup k rozhraní SQL API služby Azure Cosmos DB. Tento článek popisuje nástroje a přístupy k vám, pokud narazíte na případné problémy.

Začněte s tímto seznamem:
    * Podívejte se na [Běžné problémy a řešení] části v tomto článku.
    * Naše sada SDK je [open source na githubu](https://github.com/Azure/azure-cosmosdb-java) a máme [vydá části](https://github.com/Azure/azure-cosmosdb-java/issues) , která se aktivně sledujeme. Zkontrolujte pomocí řešení ještě nezaznamenaly žádné podobnému problému nenajdete.
    * Kontrola [tipy ke zvýšení výkonu](performance-tips-async-java.md) a dodržujte doporučené postupy zabezpečení.
    * Postupujte podle zbývajících částí tohoto článku, pokud jste nenašli řešení, souborů [problém Githubu](https://github.com/Azure/azure-cosmosdb-java/issues).

## <a name="common-issues-workarounds"></a>Běžné problémy a řešení

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>Problémů, se sítí Netty čtení vypršení časového limitu selhání, Nízká propustnost, vysoká latence

#### <a name="general-suggestions"></a>Obecná doporučení
* Ujistěte se, že aplikace běží ve stejné oblasti jako váš účet služby Cosmos DB. 
* Zkontrolujte využití procesoru na hostiteli, kde je aplikace spuštěna. Pokud využití procesoru je 90 % nebo víc, vezměte v úvahu vaše aplikace běžela na hostitele, který má vyšší konfigurace nebo distribuovat zatížení na více počítačích.

#### <a name="connection-throttling"></a>Omezení šířky pásma připojení
Omezení šířky pásma připojení může dojít, pokud buď [Limit připojení na hostitelském počítači], nebo [Vyčerpání portů Azure SNAT PAT]:

##### <a name="connection-limit-on-host"></a>Limit připojení na hostitelském počítači
Některé systémy Linux (jako například "Red Hat") nemají horní omezení celkového počtu otevřených souborů. Sokety v systému Linux jsou implementovány jako soubory, tak toto číslo příliš omezí celkový počet připojení.
Spusťte následující příkaz:

```bash
ulimit -a
```
Počet otevřených souborů ("nofile") musí být dostatečně velký (na nejméně jako double jako velikost fondu připojení). Přečtěte si další podrobnosti v [tipy ke zvýšení výkonu](performance-tips-async-java.md).

##### <a name="snat"></a>Vyčerpání portů Azure SNAT PAT

Pokud je aplikace nasazená na virtuálním počítači Azure, ve výchozím nastavení [porty Azure SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports) se používá k navázání připojení pro libovolný koncový bod mimo váš virtuální počítač. Počet připojení povolených z virtuálního počítače do koncového bodu služby Cosmos DB je omezena [konfigurace Azure SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports).

Azure SNAT porty se používají pouze v případě, že váš virtuální počítač Azure je privátní IP adresu a proces z virtuálního počítače se pokusí navázat připojení k veřejné IP adresy. Proto existují dvě alternativní řešení, aby Azure SNAT omezení:
    * Přidání vašeho koncového bodu služby Azure Cosmos DB k podsíti virtuální sítě virtuálních počítačů Azure, jak je vysvětleno v [povolení koncového bodu služby virtuální sítě](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). Když je povolený koncový bod služby, žádosti už se odesílají z veřejné IP adresy do služby cosmos DB místo toho virtuální sítě a podsítě identity se odešle. Tato změna může způsobit drops brány firewall, pokud pouze veřejné IP adresy jsou povoleny. Pokud používáte bránu firewall, při povolení koncového bodu služby, přidejte podsíť brány firewall pomocí [seznamy ACL sítě VNET](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl).
    * Přiřadíte veřejnou IP adresu svého virtuálního počítače Azure.

#### <a name="http-proxy"></a>Proxy server http

Pokud používáte HttpProxy, ujistěte se, že vaše HttpProxy může podporovat počet připojení nakonfigurovaná v sadě SDK `ConnectionPolicy`.
Jinak kterými se setkáváte problémy s připojením.

#### <a name="invalid-coding-pattern-blocking-netty-io-thread"></a>Neplatný vzor kódování: blokování vlákna Netty vstupně-výstupních operací

Sada SDK používá [Netty](https://netty.io/) knihovny vstupně-výstupních operací pro komunikaci služby Azure Cosmos DB. Máme asynchronní rozhraní API a používáme neblokující vstupně-výstupní operace rozhraní API netty. Práce v sadě SDK vstupně-výstupní operace se provádí na netty vláken vstupně-výstupních operací. Počet vstupně-výstupních operací netty vláken byl nakonfigurován jako stejný jako počet jader procesoru počítače aplikace. Netty vláken vstupně-výstupní operace jsou určeny pouze pro práci bez blokování netty vstupně-výstupních operací. Sady SDK vrátí výsledek volání rozhraní API na jednom netty vláken vstupně-výstupních operací na kód vaší aplikace. Pokud aplikace po přijetí výsledky na netty vláken provádí operaci s dlouho trvalé netty vlákna, který může mít za následek SDK nemá dostatek počet vstupně-výstupní operace vlákna pro provádění svou práci interní vstupně-výstupních operací. Takové aplikace kódování může vést k Nízká propustnost, vysoká latence a `io.netty.handler.timeout.ReadTimeoutException` selhání. Alternativním řešením je přepnout vlákno, když víte, že že operace bude chvíli trvat.

   Například následující fragment kódu ukazuje, že pokud provádíte dlouho dlouhodobé činnosti, což trvá déle než několik milisekund, k vláknu netty, nakonec můžete získat do stavu, kdy žádné vlákno netty vstupně-výstupních operací je k dispozici pro zpracování pracovní vstupně-výstupních operací a díky tomu získáte ReadTimeou tException:
```java
@Test
public void badCodeWithReadTimeoutException() throws Exception {
    int requestTimeoutInSeconds = 10;

    ConnectionPolicy policy = new ConnectionPolicy();
    policy.setRequestTimeoutInMillis(requestTimeoutInSeconds * 1000);

    AsyncDocumentClient testClient = new AsyncDocumentClient.Builder()
            .withServiceEndpoint(TestConfigurations.HOST)
            .withMasterKeyOrResourceToken(TestConfigurations.MASTER_KEY)
            .withConnectionPolicy(policy)
            .build();

    int numberOfCpuCores = Runtime.getRuntime().availableProcessors();
    int numberOfConcurrentWork = numberOfCpuCores + 1;
    CountDownLatch latch = new CountDownLatch(numberOfConcurrentWork);
    AtomicInteger failureCount = new AtomicInteger();

    for (int i = 0; i < numberOfConcurrentWork; i++) {
        Document docDefinition = getDocumentDefinition();
        Observable<ResourceResponse<Document>> createObservable = testClient
                .createDocument(getCollectionLink(), docDefinition, null, false);
        createObservable.subscribe(r -> {
                    try {
                        // time consuming work. For example:
                        // writing to a file, computationally heavy work, or just sleep
                        // basically anything which takes more than a few milliseconds
                        // doing such operation on the IO netty thread
                        // without a proper scheduler, will cause problems.
                        // The subscriber will get ReadTimeoutException failure.
                        TimeUnit.SECONDS.sleep(2 * requestTimeoutInSeconds);
                    } catch (Exception e) {
                    }
                },

                exception -> {
                    //will be io.netty.handler.timeout.ReadTimeoutException
                    exception.printStackTrace();
                    failureCount.incrementAndGet();
                    latch.countDown();
                },
                () -> {
                    latch.countDown();
                });
    }

    latch.await();
    assertThat(failureCount.get()).isGreaterThan(0);
}
```
   Alternativním řešením je změnit vlákno, na kterém provádíte pracovní dobu a současně. Definování typu singleton instance plánovače pro vaši aplikaci:
   ```java
// have a singleton instance of executor and scheduler
ExecutorService ex  = Executors.newFixedThreadPool(30);
Scheduler customScheduler = rx.schedulers.Schedulers.from(ex);
   ```
   Pokaždé, když se budete muset čas pořízení práce (například výpočetně náročné práce blokování vstupně-výstupní operace), přepnout vlákno k pracovnímu procesu poskytované vaší `customScheduler` pomocí `.observeOn(customScheduler)` rozhraní API.
```java
Observable<ResourceResponse<Document>> createObservable = client
        .createDocument(getCollectionLink(), docDefinition, null, false);

createObservable
        .observeOn(customScheduler) // switches the thread.
        .subscribe(
            // ...
        );
```
S použitím `observeOn(customScheduler)`, uvolněte netty vláken vstupně-výstupní operace a přepněte se na vlastní vlastní vlákno poskytované customScheduler. Tato změna bude vyřešen a nezískáte `io.netty.handler.timeout.ReadTimeoutException` už selhání.

### <a name="connection-pool-exhausted-issue"></a>Problém vyčerpání fondu připojení

`PoolExhaustedException` je selhání na straně klienta. Pokud se zobrazí tato chyba často, který je indikaci toho, že zatížení vaší aplikace je vyšší, než jaké SDK fondu připojení může sloužit. Může pomoct zvýšit velikost fondu připojení nebo distribuci zatížení na více aplikací.

### <a name="request-rate-too-large"></a>Frekvence požadavků, které jsou moc velká
Tato chyba je selhání na straně serveru označující spotřebované zřízenou propustnost a opakovat později. Pokud se zobrazí tato chyba často, zvažte zvýšení propustnosti kolekce.

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>Neúspěšné připojení k emulátoru služby Azure Cosmos DB

Certifikát HTTPS emulátor služby cosmos DB je podepsaný svým držitelem. Pro sadu SDK pro práci s emulátorem měli importovat certifikát emulátor pro Java TrustStore. Jak je vysvětleno [tady](local-emulator-export-ssl-certificates.md).


## <a name="enable-client-sice-logging"></a>Povolení protokolování sady SDK klienta

Sada Java SDK používá asynchronní SLF4j jako adaptační vrstva protokolování, která podporuje protokolování do oblíbených protokolovacích rozhraní, jako je log4j a logback.

Například pokud chcete použít log4j jako protokolovacího rozhraní, přidejte vaše cesta třídy Java následující knihovny:

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

Přidejte také log4j config:
```
# this is a sample log4j configuration

# Set root logger level to DEBUG and its only appender to A1.
log4j.rootLogger=INFO, A1

log4j.category.com.microsoft.azure.cosmosdb=DEBUG
#log4j.category.io.netty=INFO
#log4j.category.io.reactivex=INFO
# A1 is set to be a ConsoleAppender.
log4j.appender.A1=org.apache.log4j.ConsoleAppender

# A1 uses PatternLayout.
log4j.appender.A1.layout=org.apache.log4j.PatternLayout
log4j.appender.A1.layout.ConversionPattern=%d %5X{pid} [%t] %-5p %c - %m%n
```

Kontrola [sfl4j protokolování ruční](https://www.slf4j.org/manual.html) Další informace.

## <a name="netstats"></a>Statistika sítě operačního systému
Spusťte příkaz netstat a získali představu o tom, kolik připojení jsou v `Established` stavu, `CLOSE_WAIT` stavu atd.

V systému Linux spustíte následující příkaz:
```bash
netstat -nap
```
Filtrovat výsledek, který má jenom připojení ke koncovému bodu služby Cosmos DB.

Zdá se, počet připojení ke koncovému bodu služby Cosmos DB v `Established` stavu nesmí být větší než velikost fondu nakonfigurované připojení.

Pokud jsou počet připojení ke koncovému bodu služby Cosmos DB v `CLOSE_WAIT` stavu pro příklad více než 1 000 připojení, které je údaj o připojení se naváže a odpojí se rychle, což může potenciálně způsobit problémy. Kontrola [Běžné problémy a řešení] části Další podrobnosti.

 <!--Anchors-->
[Běžné problémy a řešení]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[Limit připojení na hostitelském počítači]: #connection-limit-on-host
[Vyčerpání portů Azure SNAT PAT]: #snat


