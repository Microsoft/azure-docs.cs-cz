---
title: Diagnostika a řešení potíží s azure cosmos DB Java Async SDK
description: Pomocí funkcí, jako je protokolování na straně klienta a další nástroje třetích stran k identifikaci, diagnostiku a řešení problémů Azure Cosmos DB.
author: moderakh
ms.service: cosmos-db
ms.date: 04/30/2019
ms.author: moderakh
ms.devlang: java
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 572139743c66546622450cef8f8a0fa264d24779
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "65519981"
---
# <a name="troubleshoot-issues-when-you-use-the-java-async-sdk-with-azure-cosmos-db-sql-api-accounts"></a>Řešení potíží při používání sady Java Async SDK s účty rozhraní SQL API služby Azure Cosmos DB
Tento článek popisuje běžné problémy, řešení, diagnostické kroky a nástroje při použití [java asynchronní sady SDK](sql-api-sdk-async-java.md) s účty Azure Cosmos DB SQL API.
Sada Java Async SDK představuje logickou reprezentaci přístupu k rozhraní SQL API služby Azure Cosmos DB na straně klienta. Tento článek popisuje nástroje a přístupy, které vám pomůžou v případě jakýchkoli problémů.

Začněte s tímto seznamem:

* Podívejte se na [běžné problémy a řešení] části v tomto článku.
* Podívejte se na SDK, který je k dispozici [open source na GitHub](https://github.com/Azure/azure-cosmosdb-java). Má [sekci problémů,](https://github.com/Azure/azure-cosmosdb-java/issues) která je aktivně sledována. Zkontrolujte, zda je podobný problém s tímto zástupným tématem již podán.
* Projděte si tipy pro [výkon](performance-tips-async-java.md)a postupujte podle doporučených postupů.
* Přečtěte si zbytek tohoto článku, pokud jste nenašli řešení. Pak soubor [GitHub problém](https://github.com/Azure/azure-cosmosdb-java/issues).

## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>Běžné problémy a alternativní řešení

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>Problémy se sítí, selhání časového času čtení Netty, nízká propustnost, vysoká latence

#### <a name="general-suggestions"></a>Obecné návrhy
* Ujistěte se, že aplikace běží ve stejné oblasti jako váš účet Azure Cosmos DB. 
* Zkontrolujte využití procesoru na hostiteli, kde je aplikace spuštěna. Pokud je využití procesoru 90 procent nebo více, spusťte aplikaci na hostiteli s vyšší konfigurací. Nebo můžete rozdělit zatížení na více strojů.

#### <a name="connection-throttling"></a>Omezení připojení
Omezení připojení může dojít z důvodu [omezení připojení na hostitelském počítači] nebo [vyčerpání portu Azure SNAT (PAT).]

##### <a name="connection-limit-on-a-host-machine"></a><a name="connection-limit-on-host"></a>Limit připojení k hostitelskému počítači
Některé linuxové systémy, například Red Hat, mají horní limit celkového počtu otevřených souborů. Sokety v Linuxu jsou implementovány jako soubory, takže toto číslo omezuje celkový počet připojení.
Spusťte následující příkaz.

```bash
ulimit -a
```
Počet maximální povolené otevřené soubory, které jsou označeny jako "nofile", musí být alespoň dvojnásobek velikosti fondu připojení. Další informace naleznete v [tématu Tipy pro výkon](performance-tips-async-java.md).

##### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Vyčerpání portu Azure SNAT (PAT)

Pokud se vaše aplikace nasadí na virtuálních počítačích Azure bez veřejné IP adresy, ve výchozím nastavení navazují [porty Azure SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports) připojení k libovolnému koncovému bodu mimo váš virtuální počítač. Počet připojení povolených z virtuálního počítače do koncového bodu Azure Cosmos DB je omezen [konfigurací Azure SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports).

 Porty Azure SNAT se používají jenom v případě, že váš virtuální počítač má privátní IP adresu a proces z virtuálního počítače se pokusí připojit k veřejné IP adrese. Existují dvě řešení, jak se vyhnout omezení Azure SNAT:

* Přidejte koncový bod služby Azure Cosmos DB do podsítě virtuální sítě Virtuálních počítačů Azure. Další informace najdete v tématu [koncové body služby Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). 

    Pokud je koncový bod služby povolen, požadavky se už neodesílají z veřejné IP adresy do Azure Cosmos DB. Místo toho jsou odesílány virtuální sítě a identity podsítě. Tato změna může způsobit, že brána firewall klesne, pokud jsou povoleny pouze veřejné IP adresy. Pokud používáte bránu firewall, při povolení koncového bodu služby přidejte podsíť do brány firewall pomocí [seznamů ACL virtuální sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl).
* Přiřaďte k virtuálnímu počítači Azure veřejnou IP adresu.

##### <a name="cant-reach-the-service---firewall"></a><a name="cant-connect"></a>Nelze dosáhnout služby - firewall
``ConnectTimeoutException``označuje, že sada SDK nemůže dosáhnout služby.
Při použití přímého režimu se může stát selhání podobné následujícímu:
```
GoneException{error=null, resourceAddress='https://cdb-ms-prod-westus-fd4.documents.azure.com:14940/apps/e41242a5-2d71-5acb-2e00-5e5f744b12de/services/d8aa21a5-340b-21d4-b1a2-4a5333e7ed8a/partitions/ed028254-b613-4c2a-bf3c-14bd5eb64500/replicas/131298754052060051p//', statusCode=410, message=Message: The requested resource is no longer available at the server., getCauseInfo=[class: class io.netty.channel.ConnectTimeoutException, message: connection timed out: cdb-ms-prod-westus-fd4.documents.azure.com/101.13.12.5:14940]
```

Pokud máte na počítači s aplikacemi spuštěný firewall, otevřete rozsah portů 10 000 až 20 000, které jsou používány v přímém režimu.
Postupujte také podle [limitu připojení na hostitelském počítači](#connection-limit-on-host).

#### <a name="http-proxy"></a>Proxy server HTTP

Pokud používáte proxy server HTTP, ujistěte se, že podporuje `ConnectionPolicy`počet připojení nakonfigurovaných v sadě SDK .
V opačném případě budete čelit problémům s připojením.

#### <a name="invalid-coding-pattern-blocking-netty-io-thread"></a>Neplatný vzor kódování: Blokování vlákna Netty IO

Sada SDK používá knihovnu [Netty](https://netty.io/) IO ke komunikaci s Azure Cosmos DB. Sada SDK má asynchronní rozhraní API a používá neblokující rozhraní API io netty. Vi práce sady SDK se provádí na vláknech IO Netty. Počet vláken IO Netty je nakonfigurován tak, aby byl stejný jako počet jader procesoru v zařízení aplikace. 

Netty IO vlákna jsou určeny k použití pouze pro neblokující Netty IO práce. Sada SDK vrátí výsledek vyvolání rozhraní API na jednom z vláken Netty IO do kódu aplikace. Pokud aplikace provádí dlouhotrvající operaci poté, co obdrží výsledky ve vlákně Netty, sada SDK nemusí mít dostatek vláken vi k provedení své interní viv práce. Takové kódování aplikace může mít za následek nízkou `io.netty.handler.timeout.ReadTimeoutException` propustnost, vysokou latenci a selhání. Řešení je přepnout vlákno, když víte, že operace nějakou dobu trvá.

Podívejte se například na následující fragment kódu. Můžete provádět dlouhotrvající práci, která trvá více než několik milisekund ve vlákně Netty. Pokud ano, nakonec se můžete dostat do stavu, kde není k dispozici žádné vlákno Netty IO pro zpracování práce vi. V důsledku toho získáte readtimeoutexception selhání.
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
   Řešení je změnit vlákno, na kterém provádíte práci, která vyžaduje čas. Definujte instanci singletonu plánovače pro vaši aplikaci.
   ```java
// Have a singleton instance of an executor and a scheduler.
ExecutorService ex  = Executors.newFixedThreadPool(30);
Scheduler customScheduler = rx.schedulers.Schedulers.from(ex);
   ```
   Možná budete muset udělat práci, která vyžaduje čas, například, výpočtově náročnou práci nebo blokování vi. V takovém případě přepněte vlákno na pracovníka poskytované ho `customScheduler` pomocí `.observeOn(customScheduler)` rozhraní API.
```java
Observable<ResourceResponse<Document>> createObservable = client
        .createDocument(getCollectionLink(), docDefinition, null, false);

createObservable
        .observeOn(customScheduler) // Switches the thread.
        .subscribe(
            // ...
        );
```
Pomocí `observeOn(customScheduler)`aplikace uvolníte vlákno Netty IO a přepnete na vlastní podproces poskytovaný vlastním plánovačem. Tato změna problém vyřešila. Už nebudeš mít `io.netty.handler.timeout.ReadTimeoutException` neúspěch.

### <a name="connection-pool-exhausted-issue"></a>Problém vyčerpání fondu připojení

`PoolExhaustedException`je selhání na straně klienta. Tato chyba označuje, že vaše úlohy aplikace je vyšší než co fond připojení sady SDK může sloužit. Zvětšete velikost fondu připojení nebo distribuujte zatížení více aplikací.

### <a name="request-rate-too-large"></a>Příliš velká míra požadavků
Tato chyba je selhání na straně serveru. Označuje, že jste spotřebovávají zřízenou propustnost. Opakujte akci později. Pokud se často dostanete toto selhání, zvažte zvýšení propustnost kolekce.

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>Selhání připojení k emulátoru Azure Cosmos DB

Certifikát HTTPS emulátoru Azure Cosmos DB je podepsaný svým držitelem. Chcete-li, aby sada SDK fungovala s emulátorem, importujte certifikát emulátoru do úložiště Java TrustStore. Další informace naleznete [v tématu Export certifikátů emulátoru Azure Cosmos DB](local-emulator-export-ssl-certificates.md).

### <a name="dependency-conflict-issues"></a>Problémy s konfliktem závislostí

```console
Exception in thread "main" java.lang.NoSuchMethodError: rx.Observable.toSingle()Lrx/Single;
```

Výše uvedená výjimka naznačuje, že máte závislost na starší verzi RxJava lib (např. 1.2.2). Naše sada SDK se spoléhá na rxJava 1.3.8, která má rozhraní API, která nejsou k dispozici v dřívější verzi RxJava. 

Řešení pro tyto issuses je určit, které další závislost přináší v RxJava-1.2.2 a vyloučit přenosnou závislost na RxJava-1.2.2, a povolit CosmosDB SDK přinést novější verzi.

Chcete-li zjistit, která knihovna přináší do RxJava-1.2.2, spusťte vedle souboru pom.xml projektu následující příkaz:
```bash
mvn dependency:tree
```
Další informace naleznete v [průvodci stromem závislostí maven](https://maven.apache.org/plugins/maven-dependency-plugin/examples/resolving-conflicts-using-the-dependency-tree.html).

Jakmile zjistíte, RxJava-1.2.2 je přenosná závislost, které další závislost vašeho projektu, můžete upravit závislost na tomto lib v souboru pom a vyloučit RxJava přenositelné závislosti to:

```xml
<dependency>
  <groupId>${groupid-of-lib-which-brings-in-rxjava1.2.2}</groupId>
  <artifactId>${artifactId-of-lib-which-brings-in-rxjava1.2.2}</artifactId>
  <version>${version-of-lib-which-brings-in-rxjava1.2.2}</version>
  <exclusions>
    <exclusion>
      <groupId>io.reactivex</groupId>
      <artifactId>rxjava</artifactId>
    </exclusion>
  </exclusions>
</dependency>
```

Další informace naleznete v [průvodci vyloučit přenositené závislosti](https://maven.apache.org/guides/introduction/introduction-to-optional-and-excludes-dependencies.html).


## <a name="enable-client-sdk-logging"></a><a name="enable-client-sice-logging"></a>Povolení protokolování sady SDK klienta

Java Async SDK používá SLF4j jako fasádu protokolování, která podporuje přihlášení do populární chod protokolování, jako je log4j a logback.

Například pokud chcete použít log4j jako rámec protokolování, přidejte následující libs v java cesta třídy.

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

Přidejte také konfiguraci log4j.
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

Další informace naleznete v [příručce k protokolování sfl4j](https://www.slf4j.org/manual.html).

## <a name="os-network-statistics"></a><a name="netstats"></a>Statistiky sítě operačního spoje
Spusťte příkaz netstat, abyste získali představu o `ESTABLISHED` `CLOSE_WAIT`tom, kolik připojení je ve stavech, jako jsou například a .

V Linuxu můžete spustit následující příkaz.
```bash
netstat -nap
```
Filtrujte výsledek pouze na připojení ke koncovému bodu Azure Cosmos DB.

Počet připojení ke koncovému bodu Azure Cosmos DB ve `ESTABLISHED` stavu nemůže být větší než nakonfigurovaná velikost fondu připojení.

Mnoho připojení ke koncovému bodu Azure Cosmos DB může být ve `CLOSE_WAIT` stavu. Může jich být víc než 1000. Číslo, které vysoké označuje, že připojení jsou navázány a rychle strženy. Tato situace potenciálně způsobuje problémy. Další informace naleznete v části [Běžné problémy a řešení.]

 <!--Anchors-->
[Běžné problémy a alternativní řešení]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[Limit připojení k hostitelskému počítači]: #connection-limit-on-host
[Vyčerpání portu Azure SNAT (PAT)]: #snat


