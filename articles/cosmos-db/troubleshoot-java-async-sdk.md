---
title: Diagnostika a řešení potíží s Azure Cosmos DB Async Java SDK v2
description: Použijte funkce jako protokolování na straně klienta a další nástroje třetích stran k identifikaci, diagnostice a řešení potíží s Azure Cosmos DB v asynchronní sadě Java SDK v2.
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 05/11/2020
ms.author: anfeldma
ms.devlang: java
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-java
ms.openlocfilehash: 60d73f8b3eae21ab399853e8d05b67b7b431ee5f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87321050"
---
# <a name="troubleshoot-issues-when-you-use-the-azure-cosmos-db-async-java-sdk-v2-with-sql-api-accounts"></a>Řešení potíží při použití Azure Cosmos DB Async Java SDK v2 s účty SQL API

> [!div class="op_single_selector"]
> * [Sada Java SDK v4](troubleshoot-java-sdk-v4-sql.md)
> * [Sada Async Java SDK v2](troubleshoot-java-async-sdk.md)
> * [.NET](troubleshoot-dot-net-sdk.md)
> 

> [!IMPORTANT]
> Nejedná *se o* nejnovější sadu Java SDK pro Azure Cosmos DB. Projekt byste měli upgradovat na [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md) a pak si přečtěte [Průvodce odstraňováním potíží](troubleshoot-java-sdk-v4-sql.md)Azure Cosmos DB Java SDK v4. Postupujte podle pokynů v tématu [migrace do Azure Cosmos DB příručka Java SDK v4](migrate-java-v4-sdk.md) a příručka [vs RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md) Guide to upgrade. 
>
> Tento článek popisuje řešení potíží pouze Azure Cosmos DB Async Java SDK v2. Další informace najdete v [poznámkách k verzi](sql-api-sdk-async-java.md)Azure Cosmos DB ASYNC Java SDK v2, v tématu [úložiště Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) a [tipy ke zvýšení výkonu](performance-tips-async-java.md) .
>

Tento článek se věnuje běžným problémům, alternativním řešením, diagnostickým krokům a nástrojům při použití [sady Java Async SDK](sql-api-sdk-async-java.md) s Azure Cosmos DB účty rozhraní SQL API.
Sada Java Async SDK představuje logickou reprezentaci přístupu k rozhraní SQL API služby Azure Cosmos DB na straně klienta. Tento článek popisuje nástroje a přístupy, které vám pomůžou v případě jakýchkoli problémů.

Začněte s tímto seznamem:

* Prohlédněte si část [běžné problémy a alternativní řešení] v tomto článku.
* Podívejte se na sadu SDK, která je k dispozici jako [Open source na GitHubu](https://github.com/Azure/azure-cosmosdb-java). Má aktivně monitorovanou [část s problémy](https://github.com/Azure/azure-cosmosdb-java/issues) . Zkontrolujte, zda je již archivován případný podobný problém s alternativním řešením.
* Projděte si [tipy ke zvýšení výkonu](performance-tips-async-java.md)a řiďte se doporučenými postupy.
* Pokud jste nenalezli řešení, přečtěte si zbytek tohoto článku. Pak zapište [problém GitHubu](https://github.com/Azure/azure-cosmosdb-java/issues).

## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>Běžné problémy a alternativní řešení

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>Problémy se sítí, neúspěšný časový limit čtení, nízká propustnost, vysoká latence

#### <a name="general-suggestions"></a>Obecné návrhy
* Ujistěte se, že aplikace běží ve stejné oblasti jako váš Azure Cosmos DB účet. 
* Ověřte využití procesoru na hostiteli, kde je aplikace spuštěná. Pokud je využití CPU 90 nebo více, spusťte aplikaci na hostiteli s vyšší konfigurací. Nebo můžete zatížení distribuovat do více počítačů.

#### <a name="connection-throttling"></a>Omezování připojení
Omezení připojení může nastat kvůli [limitu připojení na hostitelském počítači] nebo [vyčerpání portů Azure SNAT (Pat)].

##### <a name="connection-limit-on-a-host-machine"></a><a name="connection-limit-on-host"></a>Omezení počtu připojení na hostitelském počítači
Některé systémy Linux, jako je Red Hat, mají horní limit celkového počtu otevřených souborů. Sokety v systému Linux jsou implementovány jako soubory, takže toto číslo omezuje celkový počet připojení.
Spusťte následující příkaz.

```bash
ulimit -a
```
Počet povolených otevřených souborů, které jsou označeny jako "soubor", musí být alespoň dvojnásobnou velikostí fondu připojení. Další informace najdete v tématu [tipy ke zvýšení výkonu](performance-tips-async-java.md).

##### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Vyčerpání portů Azure SNAT (PAT)

Pokud je vaše aplikace nasazená v Azure Virtual Machines bez veřejné IP adresy, ve výchozím nastavení [porty Azure SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports) naváže připojení ke koncovému bodu mimo váš virtuální počítač. Počet připojení povolených z virtuálního počítače do koncového bodu Azure Cosmos DB je omezen [konfigurací Azure SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports).

 Porty Azure SNAT se používají jenom v případě, že váš virtuální počítač má privátní IP adresu a proces z virtuálního počítače se pokusí připojit k veřejné IP adrese. Omezení Azure SNAT se vyhnete dvěma řešením:

* Přidejte koncový bod služby Azure Cosmos DB do podsítě vaší virtuální sítě Azure Virtual Machines. Další informace najdete v tématu [koncové body služby Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). 

    Pokud je povolen koncový bod služby, žádosti již nejsou odesílány z veřejné IP adresy do Azure Cosmos DB. Místo toho se pošle identita virtuální sítě a podsítě. Tato změna může vést k poklesu brány firewall, pokud jsou povolené jenom veřejné IP adresy. Pokud používáte bránu firewall a povolíte koncový bod služby, přidejte do brány firewall podsíť pomocí [Virtual Network ACL](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl).
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

Podívejte se třeba na následující fragment kódu. Můžete provádět dlouhodobou práci, která trvá více než několik milisekund na vláknech síťoviny. V takovém případě se můžete dostat do stavu, ve kterém nejsou k dispozici žádná vstupně-výstupní vlákna pro zpracování vstupně-výstupních operací. V důsledku toho se zobrazí ReadTimeoutException selhání.

### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-readtimeout"></a>Async Java SDK v2 (Maven com. Microsoft. Azure:: Azure-cosmosdb)

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
Alternativním řešením je změnit vlákno, ve kterém provádíte práci, která trvá určitou dobu. Definujte instanci typu Singleton Scheduleru pro vaši aplikaci.

### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-scheduler"></a>Async Java SDK v2 (Maven com. Microsoft. Azure:: Azure-cosmosdb)

```java
// Have a singleton instance of an executor and a scheduler.
ExecutorService ex  = Executors.newFixedThreadPool(30);
Scheduler customScheduler = rx.schedulers.Schedulers.from(ex);
```
Možná budete muset udělat práci, která trvá určitou dobu, například výpočetně těžkou práci nebo blokování v/v. V takovém případě přepněte vlákno na pracovní proces poskytnutý pomocí `customScheduler` `.observeOn(customScheduler)` rozhraní API.

### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-applycustomscheduler"></a>Async Java SDK v2 (Maven com. Microsoft. Azure:: Azure-cosmosdb)

```java
Observable<ResourceResponse<Document>> createObservable = client
        .createDocument(getCollectionLink(), docDefinition, null, false);

createObservable
        .observeOn(customScheduler) // Switches the thread.
        .subscribe(
            // ...
        );
```
Pomocí nástroje `observeOn(customScheduler)` uvolníte vstupně-výstupní vlákna a přepnete do vlastního vlákna poskytnutého vlastním plánovačem. Tato úprava vyřeší problém. Už nebudete mít k `io.netty.handler.timeout.ReadTimeoutException` chybu.

### <a name="connection-pool-exhausted-issue"></a>Problém vyčerpání fondu připojení

`PoolExhaustedException` je selhání na straně klienta. Tato chyba znamená, že zatížení vaší aplikace je vyšší než to, co může poskytovat fond připojení sady SDK. Zvyšte velikost fondu připojení nebo distribuujte zatížení více aplikací.

### <a name="request-rate-too-large"></a>Příliš velký počet požadavků
Tato chyba je selhání na straně serveru. Indikuje, že jste využili zřízenou propustnost. Zkuste to znovu později. Pokud se toto selhání často dostanou, zvažte zvýšení propustnosti kolekce.

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>Chyba při připojování k emulátoru Azure Cosmos DB

Certifikát HTTPS emulátoru Azure Cosmos DB je podepsaný svým držitelem. Aby sada SDK mohla spolupracovat s emulátorem, importujte certifikát emulátoru do Java TrustStore. Další informace najdete v tématu [export certifikátů emulátoru Azure Cosmos DB](local-emulator-export-ssl-certificates.md).

### <a name="dependency-conflict-issues"></a>Problémy s konfliktem závislostí

```console
Exception in thread "main" java.lang.NoSuchMethodError: rx.Observable.toSingle()Lrx/Single;
```

Výše uvedená výjimka navrhuje, abyste měli závislost na starší verzi RxJava lib (např. 1.2.2). Naše sada SDK spoléhá na RxJava 1.3.8 s rozhraními API, která nejsou k dispozici v dřívější verzi RxJava. 

Alternativním řešením pro tyto problémy je určit, která jiná závislost přináší RxJava-1.2.2 a vyloučit přenosnou závislost na RxJava-1.2.2 a povolení sady CosmosDB SDK připraví novější verzi.

Pokud chcete zjistit, která Knihovna přinese v RxJava-1.2.2, spusťte následující příkaz vedle souboru projektu pom.xml:
```bash
mvn dependency:tree
```
Další informace najdete v příručce ke [stromu závislostí Maven](https://maven.apache.org/plugins/maven-dependency-plugin/examples/resolving-conflicts-using-the-dependency-tree.html).

Jakmile identifikujete RxJava-1.2.2 je přenosná závislost, na které se nachází jiná závislost projektu, můžete upravit závislost na této lib v souboru pom a vyloučit RxJava přenosnou závislost:

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

Další informace najdete v [Průvodci vyloučením přenosné závislosti](https://maven.apache.org/guides/introduction/introduction-to-optional-and-excludes-dependencies.html).


## <a name="enable-client-sdk-logging"></a><a name="enable-client-sice-logging"></a>Povolit protokolování klientské sady SDK

Sada Java Async SDK používá SLF4j jako přihlašování, které podporuje přihlášení do oblíbených protokolovacích rozhraní, jako je například log4j a logback.

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

Další informace najdete v tématu [Ruční protokolování sfl4j](https://www.slf4j.org/manual.html).

## <a name="os-network-statistics"></a><a name="netstats"></a>Statistika sítě operačního systému
Spusťte příkaz netstat, abyste získali představu o počtu připojení ve státech, jako jsou `ESTABLISHED` a `CLOSE_WAIT` .

V systému Linux můžete spustit následující příkaz.
```bash
netstat -nap
```
Vyfiltruje výsledek jenom na připojení ke koncovému bodu Azure Cosmos DB.

Počet připojení ke koncovému bodu Azure Cosmos DB ve `ESTABLISHED` stavu nemůže být větší než nakonfigurovaná velikost fondu připojení.

Mnoho připojení ke koncovému bodu Azure Cosmos DB může být ve `CLOSE_WAIT` stavu. Může jich být více než 1 000. Číslo, které je vysoké, znamená, že jsou připojení navázána a rychlá. Tato situace může způsobovat problémy. Další informace najdete v části [běžné problémy a alternativní řešení] .

 <!--Anchors-->
[Běžné problémy a alternativní řešení]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[Omezení počtu připojení na hostitelském počítači]: #connection-limit-on-host
[Vyčerpání portů Azure SNAT (PAT)]: #snat


