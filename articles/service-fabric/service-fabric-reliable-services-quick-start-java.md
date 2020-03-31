---
title: Vytvořte si první spolehlivou službu v Javě
description: Úvod k vytvoření aplikace Microsoft Azure Service Fabric s bezstavové a stavové služby.
author: suhuruli
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: suhuruli
ms.openlocfilehash: c3b301a7a9039f1fe8095950f0a5a4e23eb52a9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614209"
---
# <a name="get-started-with-reliable-services-in-java"></a>Začínáme se spolehlivými službami v Javě
> [!div class="op_single_selector"]
> * [C# v systému Windows](service-fabric-reliable-services-quick-start.md)
> * [Java v Linuxu](service-fabric-reliable-services-quick-start-java.md)
>
>

Tento článek vysvětluje základy spolehlivých služeb Azure Service Fabric a provede vás vytvořením a nasazením jednoduché aplikace spolehlivé služby napsané v jazyce Java. 

## <a name="installation-and-setup"></a>Instalace a nastavení
Než začnete, ujistěte se, že máte ve vašem počítači nastavené vývojové prostředí Service Fabric.
Pokud ji potřebujete nastavit, přejděte na [Začínáme na Macu](service-fabric-get-started-mac.md) nebo [začínáme na Linuxu](service-fabric-get-started-linux.md).

## <a name="basic-concepts"></a>Základní koncepty
Chcete-li začít se spolehlivými službami, stačí pochopit několik základních pojmů:

* **Typ služby**: Toto je implementace služby. Je definovántřídou, kterou napíšete, která rozšiřuje `StatelessService` a všechny další kód nebo závislosti v něm použité, spolu s názvem a číslem verze.
* **Named service instance**: Chcete-li spustit službu, vytvořte pojmenované instance typu služby, podobně jako vytváříte instance objektů typu třídy. Instance služby jsou ve skutečnosti objekt instance třídy služby, které píšete.
* **Hostitel služby**: Pojmenované instance služby, které vytvoříte, musí být spuštěny uvnitř hostitele. Hostitel služby je pouze proces, kde lze spustit instance služby.
* **Registrace služby**: Registrace spojuje vše dohromady. Typ služby musí být registrován s runtime Service Fabric v hostiteli služby, aby service fabric mohl vytvářet instance, které mají být spuštěny.  

## <a name="create-a-stateless-service"></a>Vytvoření bezstavové služby
Začněte vytvořením aplikace Service Fabric. Service Fabric SDK pro Linux obsahuje Generátor Yeoman poskytnout lešení pro aplikaci Service Fabric se službou bez stavů. Začněte spuštěním následujícího příkazu Yeoman:

```bash
$ yo azuresfjava
```

Podle pokynů vytvořte **spolehlivou bezstavovou službu**. Pro účely tohoto kurzu pojmenujte aplikaci "HelloWorldApplication" a službu "HelloWorld". Výsledek zahrnuje adresáře `HelloWorldApplication` pro `HelloWorld`a .

```bash
HelloWorldApplication/
├── build.gradle
├── HelloWorld
│   ├── build.gradle
│   └── src
│       └── statelessservice
│           ├── HelloWorldServiceHost.java
│           └── HelloWorldService.java
├── HelloWorldApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   └── _readme.txt
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── install.sh
├── settings.gradle
└── uninstall.sh
```
### <a name="service-registration"></a>Registrace služby
Typy služeb musí být registrovány pomocí zaběhu Service Fabric. Typ služby je `ServiceManifest.xml` definován v a třídy služby, která implementuje `StatelessService`. Registrace služby se provádí v hlavním vstupním bodě procesu. V tomto příkladu je `HelloWorldServiceHost.java`hlavní vstupní bod procesu :

```java
public static void main(String[] args) throws Exception {
    try {
        ServiceRuntime.registerStatelessServiceAsync("HelloWorldType", (context) -> new HelloWorldService(), Duration.ofSeconds(10));
        logger.log(Level.INFO, "Registered stateless service type HelloWorldType.");
        Thread.sleep(Long.MAX_VALUE);
    }
    catch (Exception ex) {
        logger.log(Level.SEVERE, "Exception in registration:", ex);
        throw ex;
    }
}
```

## <a name="implement-the-service"></a>Implementace služby

Otevřete **HelloWorldApplication/HelloWorld/src/statelessservice/HelloWorldService.java**. Tato třída definuje typ služby a může spustit libovolný kód. Rozhraní API služby poskytuje dva vstupní body pro váš kód:

* Metoda otevřeného vstupního bodu `runAsync()`s názvem , kde můžete začít s pouštět všechny úlohy, včetně dlouhotrvajícívýpočetní úlohy.

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    ...
}
```

* Vstupní bod komunikace, do kterého můžete připojit příchozí zásobník. Toto je místo, kde můžete začít přijímat požadavky od uživatelů a dalších služeb.

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {
    ...
}
```

Tento kurz se `runAsync()` zaměřuje na metodu vstupního bodu. Toto je místo, kde můžete okamžitě spustit kód.

### <a name="runasync"></a>Synchronizace runasync
Platforma volá tuto metodu, když je umístěna instance služby a připravena ke spuštění. Pro službu bez stavů, to znamená, když je otevřena instance služby. Token zrušení je k dispozici pro koordinaci, když je třeba zavřít instanci služby. V Service Fabric tento otevřený/zavřít cyklus instance služby může dojít mnohokrát po dobu životnosti služby jako celku. K tomu může dojít z různých důvodů, včetně:

* Systém přesune instance služby pro vyrovnávání prostředků.
* V kódu dochází k chybám.
* Aplikace nebo systém je inovován.
* Základní hardware dochází k výpadku.

Tato orchestrace je spravována Service Fabric, aby vaše služba byla vysoce dostupná a správně vyvážená.

`runAsync()`by neměla blokovat synchronně. Implementace runAsync by měla vrátit CompletableFuture povolit modul runtime pokračovat. Pokud vaše úloha potřebuje implementovat dlouho běžící úlohu, která by měla být provedena uvnitř CompletableFuture.

#### <a name="cancellation"></a>Zrušení
Zrušení úlohy je kooperativní úsilí řízené zadaným tokenem zrušení. Systém čeká na ukončení úlohy (úspěšným dokončením, zrušením nebo chybou), než se přesune dál. Je důležité ctít zrušení token, dokončit jakoukoli `runAsync()` práci a ukončit co nejrychleji, když systém požaduje zrušení. Následující příklad ukazuje, jak zpracovat událost zrušení:

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {

    // TODO: Replace the following sample code with your own logic
    // or remove this runAsync override if it's not needed in your service.

    return CompletableFuture.runAsync(() -> {
        long iterations = 0;
        while(true)
        {
        cancellationToken.throwIfCancellationRequested();
        logger.log(Level.INFO, "Working-{0}", ++iterations);

        try {
            Thread.sleep(1000);
        } catch (InterruptedException ex){}
        }
    });
}
```

V tomto příkladu bezstavové služby je počet uložen v místní proměnné. Ale protože se jedná o bezstavovou službu, hodnota, která je uložena existuje pouze pro aktuální životní cyklus jeho instance služby. Při přesunu nebo restartování služby dojde ke ztrátě hodnoty.

## <a name="create-a-stateful-service"></a>Vytvoření stavové služby
Service Fabric zavádí nový druh služby, která je stavová. Stavová služba může spolehlivě udržovat stav v rámci samotné služby, která je umístěna společně s kódem, který ji používá. Stav je vysoce k dispozici Service Fabric bez nutnosti zachovat stav do externího úložiště.

Chcete-li převést hodnotu čítače z bezstavové na vysoce dostupné a trvalé, i když se služba přesune nebo restartuje, potřebujete stavovou službu.

Ve stejném adresáři jako aplikace HelloWorld můžete přidat novou `yo azuresfjava:AddService` službu spuštěním příkazu. Zvolte "Spolehlivé stavové služby" pro váš rámec a název služby "HelloWorldStateful". 

Vaše aplikace by nyní měla mít dvě služby: bezstavovou službu HelloWorld a stavovou službu HelloWorldStateful.

Stavová služba má stejné vstupní body jako bezstavová služba. Hlavním rozdílem je dostupnost zprostředkovatele stavu, který může spolehlivě ukládat stav. Service Fabric je dodáván s implementací zprostředkovatele stavu s názvem Spolehlivé kolekce, která umožňuje vytvářet replikované datové struktury prostřednictvím správce spolehlivého stavu. Stavová spolehlivá služba používá tohoto poskytovatele stavu ve výchozím nastavení.

Otevřete HelloWorldStateful.java v **HelloWorldStateful -> src**, který obsahuje následující metodu RunAsync:

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    Transaction tx = stateManager.createTransaction();
    return this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap").thenCompose((map) -> {
        return map.computeAsync(tx, "counter", (k, v) -> {
            if (v == null)
                return 1L;
            else
                return ++v;
            }, Duration.ofSeconds(4), cancellationToken)
                .thenCompose((r) -> tx.commitAsync())
                .whenComplete((r, e) -> {
            try {
                tx.close();
            } catch (Exception e) {
                logger.log(Level.SEVERE, e.getMessage());
            }
        });
    });
}
```

### <a name="runasync"></a>Synchronizace runasync
`RunAsync()`funguje podobně ve stavových a bezstavových službách. Však ve stavu služby `RunAsync()`platformy provádí další práci vaším jménem před tím, než provede . Tato práce může zahrnovat zajištění, že spolehlivé správce stavu a spolehlivé kolekce jsou připraveny k použití.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Spolehlivé kolekce a spolehlivý správce stavu
```java
ReliableHashMap<String,Long> map = this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap")
```

[ReliableHashMap](https://docs.microsoft.com/java/api/microsoft.servicefabric.data.collections.reliablehashmap) je implementace slovníku, který můžete použít ke spolehlivému ukládání stavu ve službě. Pomocí service fabric a spolehlivé hashmaps můžete ukládat data přímo ve vaší službě bez nutnosti externího trvalého úložiště. Spolehlivé mapy HashMaps zpřístupní vaše data vysoce. Service Fabric toho dosáhne vytvořením a správou více *replik služby* za vás. Poskytuje také rozhraní API, které abstrahuje pryč složitosti správy těchto replik a jejich přechody stavu.

Spolehlivé kolekce můžete uložit libovolný typ Java, včetně vlastní typy, s několika upozornění:

* Service Fabric umožňuje váš stav vysoce dostupné *replikací* stavu mezi uzly a spolehlivé HashMap ukládá data na místní disk na každé replice. To znamená, že vše, co je uloženo ve spolehlivých HashMaps musí být *serializovatelné*. 
* Objekty jsou replikovány pro vysokou dostupnost při potvrzení transakcí na spolehlivé HashMaps. Objekty uložené ve spolehlivých mapách HashJsou uloženy v místní paměti ve vaší službě. To znamená, že máte místní odkaz na objekt.
  
   Je důležité, abyste nemutovali místní instance těchto objektů bez provedení operace aktualizace spolehlivé kolekce v transakci. Důvodem je, že změny místních instancí objektů nebudou replikovány automaticky. Je nutné znovu vložit objekt zpět do slovníku nebo použít jednu z metod *aktualizace* ve slovníku.

Správce spolehlivého stavu spravuje spolehlivé mapy HashMaps za vás. Můžete požádat správce spolehlivého stavu o spolehlivou kolekci podle jména kdykoli a na libovolném místě ve vaší službě. Správce spolehlivého stavu zajišťuje, že získáte odkaz zpět. Nedoporučujeme ukládat odkazy na spolehlivé instance kolekce v proměnných nebo vlastnostech členů třídy. Zvláštní pozornost je třeba dbát na to, aby byl odkaz nastaven na instanci po celou dobu životního cyklu služby. Správce spolehlivého stavu zpracovává tuto práci za vás a je optimalizován pro opakované návštěvy.


### <a name="transactional-and-asynchronous-operations"></a>Transakční a asynchronní operace
```java
return map.computeAsync(tx, "counter", (k, v) -> {
    if (v == null)
        return 1L;
    else
        return ++v;
    }, Duration.ofSeconds(4), cancellationToken)
        .thenCompose((r) -> tx.commitAsync())
        .whenComplete((r, e) -> {
    try {
        tx.close();
    } catch (Exception e) {
        logger.log(Level.SEVERE, e.getMessage());
    }
});
```

Operace na spolehlivé HashMaps jsou asynchronní. Důvodem je, že operace zápisu se spolehlivými kolekcemi provádějí vstupně-vanové operace k replikaci a zachování dat na disk.

Spolehlivé operace HashMap jsou *transakční*, takže můžete udržovat stav konzistentní napříč více spolehlivé HashMaps a operace. Například můžete získat pracovní položku z jednoho spolehlivého slovníku, provést operaci na něm a uložit výsledek v jiném Spolehlivé HashMap, to vše v rámci jedné transakce. To je považováno za atomické operace a zaručuje, že celá operace bude úspěšná nebo celá operace se vrátí zpět. Pokud dojde k chybě po vyřazení položky z fronty, ale před uložením výsledku, celá transakce je vrácena zpět a položka zůstane ve frontě pro zpracování.


## <a name="build-the-application"></a>Sestavení aplikace

Yeoman lešení obsahuje gradle skript pro sestavení aplikace a bash skripty pro nasazení a odebrání aplikace. Chcete-li aplikaci spustit, nejprve vytvořte aplikaci s gradle:

```bash
$ gradle
```

Tím se vytvoří balíček aplikace Service Fabric, který lze nasadit pomocí cli service fabric.

## <a name="deploy-the-application"></a>Nasazení aplikace

Jakmile je aplikace sestavená, můžete ji nasadit do místního clusteru.

1. Připojte se k místnímu clusteru služby Service Fabric.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Spuštěním instalačního skriptu, který je součástí šablony, zkopírujte balíček aplikace do úložiště imagí clusteru, zaregistrujte typ aplikace a vytvořte její instanci.

    ```bash
    ./install.sh
    ```

Nasazení sestavené aplikace je stejné jako u všech ostatních aplikací Service Fabric. Podrobné pokyny najdete v dokumentaci s popisem [správy aplikace Service Fabric pomocí Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md).

Parametry těchto příkazů najdete v generovaných manifestech uvnitř balíčku aplikace.

Jakmile je aplikace nasazená, otevřete prohlížeč a přejděte k nástroji [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) na adrese [http://localhost:19080/Explorer](http://localhost:19080/Explorer). Pak rozbalte uzel **Aplikace** a všimněte si, že už obsahuje položku pro váš typ aplikace a další položku pro první instanci tohoto typu.

> [!IMPORTANT]
> Chcete-li nasadit aplikaci do zabezpečeného clusteru Linux v Azure, musíte nakonfigurovat certifikát pro ověření aplikace pomocí runtime Service Fabric. To umožňuje služby spolehlivé služby komunikovat s podkladových service fabric runtime API. Další informace najdete [v tématu Konfigurace aplikace Spolehlivé služby pro spuštění v clusterech Linux](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters).  
>

## <a name="next-steps"></a>Další kroky

* [Začínáme se Service Fabric CLI](service-fabric-cli.md)
