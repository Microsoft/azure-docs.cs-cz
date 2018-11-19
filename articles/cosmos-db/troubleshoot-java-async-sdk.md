---
title: Diagnostika a řešení potíží s Azure Cosmos DB Java asynchronní SDK | Dokumentace Microsoftu
description: Použití funkcí, jako je protokolování na straně klienta a další nástroje třetích stran k identifikaci, diagnostice a řešení potíží s Azure Cosmos DB.
services: cosmos-db
author: moderakh
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 10/28/2018
ms.author: moderakh
ms.devlang: java
ms.component: cosmosdb-sql
ms.openlocfilehash: 2c73cda19a3f8b9b7c5ab493c0dfcd6c2e7be745
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2018
ms.locfileid: "51820235"
---
# <a name="troubleshoot-issues-when-you-use-the-java-async-sdk-with-azure-cosmos-db-sql-api-accounts"></a>Řešení potíží při použití sady Java SDK asynchronní s účty SQL API služby Azure Cosmos DB
Tento článek popisuje běžné problémy, alternativní řešení, kroky pro diagnostiku a nástroje, při použití [sady Java SDK pro asynchronní](sql-api-sdk-async-java.md) s účty SQL API služby Azure Cosmos DB.
Asynchronní sady Java SDK poskytuje logickou reprezentaci na straně klienta pro přístup k SQL API služby Azure Cosmos DB. Tento článek popisuje nástroje a přístupy k vám, pokud narazíte na případné problémy.

Začněte s tímto seznamem:

* Podívejte se na [Běžné problémy a řešení] části v tomto článku.
* Podívejte se na sadu SDK, která je k dispozici [open source na Githubu](https://github.com/Azure/azure-cosmosdb-java). Má [vydá části](https://github.com/Azure/azure-cosmosdb-java/issues) , která je aktivně sledována. Zaškrtněte, pokud chcete zobrazit, pokud všechny podobné potíže s řešením je již zařazen.
* Zkontrolujte [tipy ke zvýšení výkonu](performance-tips-async-java.md)a dodržujte doporučené postupy zabezpečení.
* Nenašli jste řešení pro čtení zbývajících částí tohoto článku. K souboru [problém Githubu](https://github.com/Azure/azure-cosmosdb-java/issues).

## <a name="common-issues-workarounds"></a>Běžné problémy a řešení

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>Problémů, se sítí Netty čtení vypršení časového limitu selhání, Nízká propustnost, vysoká latence

#### <a name="general-suggestions"></a>Obecná doporučení
* Ujistěte se, že aplikace běží ve stejné oblasti jako váš účet Azure Cosmos DB. 
* Zkontrolujte využití procesoru na hostiteli, kde je aplikace spuštěna. Pokud je využití procesoru 90 % nebo víc, spusťte aplikaci na hostitele s vyšší konfigurace. Nebo můžete distribuovat zatížení na více počítačích.

#### <a name="connection-throttling"></a>Omezení šířky pásma připojení
Omezení šířky pásma připojení může dojít, protože buď [Limit připojení na hostitelském počítači] nebo [Vyčerpání portů Azure SNAT PAT].

##### <a name="connection-limit-on-host"></a>Limit připojení na hostitelském počítači
Některé systémy Linux, jako je Red Hat, nemají horní omezení celkového počtu otevřených souborů. Sokety v systému Linux jsou implementovány jako soubory, tak toto číslo omezí celkový počet připojení, příliš.
Spusťte následující příkaz.

```bash
ulimit -a
```
Počet maximální počet povolených otevřených souborů, které jsou označeny jako "nofile", musí být aspoň double velikost fondu připojení. Další informace najdete v tématu [tipy ke zvýšení výkonu](performance-tips-async-java.md).

##### <a name="snat"></a>Vyčerpání portů Azure SNAT PAT

Pokud vaše aplikace je nasazená ve službě Azure Virtual Machines bez veřejné IP adresy, ve výchozím nastavení [porty Azure SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports) k navázání spojení s libovolný koncový bod mimo váš virtuální počítač. Počet připojení povolených z virtuálního počítače do koncového bodu služby Azure Cosmos DB je omezena [konfigurace Azure SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports).

 Azure SNAT porty se používají pouze v případě, že váš virtuální počítač má privátní IP adresu a proces z virtuálního počítače se pokusí připojit k veřejné IP adresy. Existují dvě alternativní řešení, aby se zabránilo Azure SNAT omezení:

* Přidáte koncový bod služby Azure Cosmos DB k podsíti virtuální sítě Azure Virtual Machines. Další informace najdete v tématu [koncové body služeb virtuální sítě Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). 

    Když je povolený koncový bod služby, žádosti už odesílají z veřejnou IP adresu do služby Azure Cosmos DB. Místo toho jsou odeslány virtuální síť a podsíť identity. Tato změna může vést drops brány firewall, pokud pouze veřejné IP adresy jsou povoleny. Pokud používáte bránu firewall, při povolení koncového bodu služby, přidejte podsíť brány firewall pomocí [virtuální sítě ACL](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl).
* Přiřadíte veřejnou IP adresu svého virtuálního počítače Azure.

#### <a name="http-proxy"></a>Proxy server HTTP

Pokud používáte proxy server HTTP, ujistěte se, že počet připojení nakonfigurovaná v sadě SDK, které může podporovat `ConnectionPolicy`.
Jinak kterými se setkáváte problémy s připojením.

#### <a name="invalid-coding-pattern-blocking-netty-io-thread"></a>Neplatný vzor kódování: blokování vlákna Netty vstupně-výstupních operací

Sada SDK používá [Netty](https://netty.io/) vstupně-výstupních operací knihovny ke komunikaci s Azure Cosmos DB. Sada SDK má asynchronní rozhraní API a používá neblokující vstupně-výstupní operace rozhraní API Netty. Vstupně-výstupních operací pracovního sadě SDK se provádí na vstupně-výstupních operací Netty vlákna. Počet vláken vstupně-výstupních operací Netty byl nakonfigurován jako stejný jako počet jader procesoru počítače aplikace. 

Vlákna Netty vstupně-výstupní operace jsou určeny pro použití pouze pro práci bez blokování Netty vstupně-výstupních operací. Sady SDK do kódu aplikace vrátí výsledek volání rozhraní API na jedno z vláken Netty vstupně-výstupních operací. Pokud aplikace provádí operace dlouhodobých až dostane od výsledků na Netty vlákna, sady SDK nemusí mít dostatek vláken vstupně-výstupní operace k provedení své práce interní vstupně-výstupních operací. Takové aplikace kódování může vést k Nízká propustnost, vysoká latence a `io.netty.handler.timeout.ReadTimeoutException` selhání. Alternativním řešením je přepnout vlákno, když víte, že operace trvá určitou dobu.

Příklad může posloužit podívejte se na následující fragment kódu. Můžete provést dlouhodobých práce, která přijímá více než několik milisekund, než na Netty vlákna. Pokud ano, případně můžete získat do stavu, kde je k dispozici pro zpracování prací vstupně-výstupní operace žádné vlákno Netty vstupně-výstupních operací. Díky tomu získáte ReadTimeoutException selhání.
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
                        // Time-consuming work is, for example,
                        // writing to a file, computationally heavy work, or just sleep.
                        // Basically, it's anything that takes more than a few milliseconds.
                        // Doing such operations on the IO Netty thread
                        // without a proper scheduler will cause problems.
                        // The subscriber will get a ReadTimeoutException failure.
                        TimeUnit.SECONDS.sleep(2 * requestTimeoutInSeconds);
                    } catch (Exception e) {
                    }
                },

                exception -> {
                    //It will be io.netty.handler.timeout.ReadTimeoutException.
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
   Alternativním řešením je změnit vlákno, na kterém provádíte práci, kterou trvá určitou dobu. Definování typu singleton instance plánovače pro vaši aplikaci.
   ```java
// Have a singleton instance of an executor and a scheduler.
ExecutorService ex  = Executors.newFixedThreadPool(30);
Scheduler customScheduler = rx.schedulers.Schedulers.from(ex);
   ```
   Můžete potřebovat pro práci, že trvá čas, například blokující v/v nebo výpočetně náročné práce. V takovém případě přepnout vlákno k pracovnímu procesu poskytované vaší `customScheduler` pomocí `.observeOn(customScheduler)` rozhraní API.
```java
Observable<ResourceResponse<Document>> createObservable = client
        .createDocument(getCollectionLink(), docDefinition, null, false);

createObservable
        .observeOn(customScheduler) // Switches the thread.
        .subscribe(
            // ...
        );
```
S použitím `observeOn(customScheduler)`, uvolnit vlákno Netty vstupně-výstupní operace a přepněte se na vlastní vlastní vlákno, které poskytuje vlastní plánovač. Tato změna řeší problém. Nezískáte `io.netty.handler.timeout.ReadTimeoutException` už selhání.

### <a name="connection-pool-exhausted-issue"></a>Problém vyčerpání fondu připojení

`PoolExhaustedException` je selhání na straně klienta. Tato chyba znamená, že vašich úloh aplikace vyšší, než co může sloužit fondu připojení SDK. Zvětšit velikost fondu připojení nebo distribuovat zatížení na více aplikací.

### <a name="request-rate-too-large"></a>Frekvence požadavků, které jsou moc velká
Tato chyba je selhání na straně serveru. Znamená to, že spotřebované zřízené propustnosti. Zkuste to znovu později. Pokud se zobrazí tato chyba často, zvažte zvýšení propustnosti kolekce.

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>Neúspěšné připojení k emulátoru služby Azure Cosmos DB

Certifikát HTTPS emulátor služby Azure Cosmos DB je podepsaný svým držitelem. Sady SDK pracovat s emulátorem importujte certifikát emulátor do Java TrustStore. Další informace najdete v tématu [certifikátů emulátoru Export služby Azure Cosmos DB](local-emulator-export-ssl-certificates.md).


## <a name="enable-client-sice-logging"></a>Povolení protokolování sady SDK klienta

Použití sady Java SDK pro asynchronní SLF4j jako adaptační vrstva protokolování, která podporuje protokolování do oblíbených protokolovacích rozhraní, jako je log4j a logback.

Například pokud chcete použít log4j jako protokolovacího rozhraní, přidejte následující knihovny v vaše cesta třídy Java.

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

Další informace najdete v tématu [sfl4j protokolování ruční](https://www.slf4j.org/manual.html).

## <a name="netstats"></a>Statistika sítě operačního systému
Spusťte příkaz netstat získat představu o tom, kolik připojení jsou ve stavu, jako `ESTABLISHED` a `CLOSE_WAIT`.

V systému Linux můžete spustit následující příkaz.
```bash
netstat -nap
```
Filtrovat výsledek, který má jenom připojení ke koncovému bodu služby Azure Cosmos DB.

Počet připojení ke koncovému bodu služby Azure Cosmos DB v `ESTABLISHED` stavu nemůže být větší než velikost fondu nakonfigurované připojení.

Počet připojení ke koncovému bodu služby Azure Cosmos DB je možné `CLOSE_WAIT` stavu. Může existovat více než 1 000. To vysoké číslo označuje, že připojení jsou vytvářena a rychle protržen. Tato situace potenciálně způsobuje problémy. Další informace najdete v tématu [Běžné problémy a řešení] oddílu.

 <!--Anchors-->
[Běžné problémy a řešení]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[Limit připojení na hostitelském počítači]: #connection-limit-on-host
[Vyčerpání portů Azure SNAT PAT]: #snat


