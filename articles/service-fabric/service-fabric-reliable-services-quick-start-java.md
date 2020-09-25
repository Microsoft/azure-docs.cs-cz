---
title: Vytvoření první spolehlivé služby v jazyce Java
description: Úvod k vytvoření aplikace Microsoft Azure Service Fabric se stavovou a stavovou službou v jazyce Java.
author: suhuruli
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-java
ms.author: suhuruli
ms.openlocfilehash: f74265c7b774e4b471c8621e99377a009f939ee1
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91250092"
---
# <a name="get-started-with-reliable-services-in-java"></a>Začínáme s Reliable Services v jazyce Java
> [!div class="op_single_selector"]
> * [C# v systému Windows](service-fabric-reliable-services-quick-start.md)
> * [Java v Linuxu](service-fabric-reliable-services-quick-start-java.md)
>
>

Tento článek vysvětluje základy služby Azure Service Fabric Reliable Services a provede vás vytvořením a nasazením jednoduché aplikace spolehlivé služby napsané v jazyce Java. 

## <a name="installation-and-setup"></a>Instalace a nastavení
Než začnete, ujistěte se, že máte na svém počítači nastavené vývojové prostředí Service Fabric.
Pokud ho potřebujete nastavit, přečtěte si v článku [Začínáme v počítači Mac](service-fabric-get-started-mac.md) nebo [Začínáme](service-fabric-get-started-linux.md)se systémem Linux.

## <a name="basic-concepts"></a>Základní koncepty
Abyste mohli začít s Reliable Services, stačí pochopit jenom několik základních konceptů:

* **Typ služby**: Toto je vaše implementace služby. Je definována třídou, kterou napíšete, která rozšiřuje `StatelessService` a jakýkoli jiný kód nebo závislosti, společně s názvem a číslem verze.
* **Instance pojmenované služby**: Pokud chcete službu spustit, vytvoříte pojmenované instance typu služby, podobně jako při vytváření instancí objektů typu třídy. Instance služby jsou ve skutečnosti instance objektů vaší třídy služby, kterou píšete.
* **Hostitel služby**: pojmenované instance služby, které vytvoříte, musí běžet v rámci hostitele. Hostitel služby je jenom proces, ve kterém se můžou spouštět instance služby.
* **Registrace služby**: registrace přináší všechno dohromady. Typ služby musí být zaregistrován s modulem runtime Service Fabric v hostiteli služby, aby mohl Service Fabric vytvářet instance pro spuštění.  

## <a name="create-a-stateless-service"></a>Vytvoření bezstavové služby
Začněte vytvořením aplikace Service Fabric. Sada Service Fabric SDK pro Linux zahrnuje generátor Yeoman, který poskytuje generování uživatelského rozhraní pro Service Fabric aplikace se stavovou službou. Začněte spuštěním následujícího příkazu Yeoman:

```bash
$ yo azuresfjava
```

Pokud chcete vytvořit **spolehlivou bezstavovou službu**, postupujte podle pokynů. Pro tento kurz pojmenujte aplikaci "HelloWorldApplication" a službu "HelloWorld". Výsledek zahrnuje adresáře pro `HelloWorldApplication` a `HelloWorld` .

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
Typy služeb musí být registrovány s modulem runtime Service Fabric. Typ služby je definován v `ServiceManifest.xml` třídě a vaší třídy služby, která implementuje `StatelessService` . Registrace služby se provádí v hlavním vstupním bodě procesu. V tomto příkladu je hlavním vstupním bodem procesu `HelloWorldServiceHost.java` :

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

Otevřete **HelloWorldApplication/Hello/src/statelessservice/HelloWorldService. Java**. Tato třída definuje typ služby a může spustit libovolný kód. Rozhraní API služby poskytuje dva vstupní body pro váš kód:

* Otevřená metoda vstupního bodu, která se nazývá `runAsync()` , kde můžete začít spouštět jakékoli úlohy, včetně dlouhotrvajících výpočetních úloh.

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    ...
}
```

* Vstupní bod komunikace, ve kterém můžete připojit svůj komunikační zásobník podle vlastního výběru. Tady můžete začít přijímat žádosti od uživatelů a dalších služeb.

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {
    ...
}
```

Tento kurz se zaměřuje na `runAsync()` metodu vstupního bodu. Tady můžete hned začít s kódem.

### <a name="runasync"></a>RunAsync
Platforma volá tuto metodu, když je umístěna instance služby a je připravena k provedení. U bezstavových služeb to znamená, že když je instance služby otevřená. Token zrušení se poskytuje ke koordinaci, když je potřeba uzavřít instanci služby. V Service Fabric může tento cyklus otevření nebo ukončení instance služby probíhat mnohokrát po celou dobu životnosti služby. K tomu může dojít z různých důvodů, včetně:

* Systém přesune vaše instance služby pro vyrovnávání prostředků.
* Ve vašem kódu dojde k chybám.
* Aplikace nebo systém se upgraduje.
* Dojde k výpadku základního hardwaru.

Tato orchestrace je spravovaná nástrojem Service Fabric, aby byla vaše služba vysoce dostupná a správně vyvážená.

`runAsync()` nemělo by se blokovat synchronně. Vaše implementace runAsync by měla vrátit CompletableFuture, aby bylo možné pokračovat v běhu. Pokud vaše úlohy potřebují implementovat dlouhodobou spuštěnou úlohu, která by se měla provést v rámci CompletableFuture.

#### <a name="cancellation"></a>Zrušení
Zrušení úloh je úsilí v družstvu, které provádí poskytnutý token zrušení. Systém čeká na ukončení úlohy (po úspěšném dokončení, zrušení nebo chybě) před tím, než se přesune. Je důležité přijmout token zrušení, dokončit práci a skončit `runAsync()` co nejrychleji, když systém požaduje zrušení. Následující příklad ukazuje, jak zpracovat událost zrušení:

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

V tomto příkladu služby bez stavu je počet uložený v místní proměnné. Vzhledem k tomu, že se jedná o bezstavovou službu, hodnota, která je uložena, je určena pouze pro aktuální životní cyklus své instance služby. Při přesunu nebo restartu služby dojde ke ztrátě hodnoty.

## <a name="create-a-stateful-service"></a>Vytvoření stavové služby
Service Fabric zavádí nový druh služby, která je stavová. Stavová služba může udržovat stav spolehlivě v rámci samotné služby, společně umístěná pomocí kódu, který je používá. Stav je vysoce dostupný pomocí Service Fabric bez nutnosti zachovat stav do externího úložiště.

Chcete-li převést hodnotu čítače ze stavu bez stavů na vysokou dostupnost a trvalé, i když se služba přesune nebo restartuje, budete potřebovat stavovou službu.

Ve stejném adresáři jako aplikace HelloWorld můžete přidat novou službu spuštěním `yo azuresfjava:AddService` příkazu. Vyberte možnost Reliable stavová služba pro vaši architekturu a pojmenujte službu "HelloWorldStateful". 

Vaše aplikace by teď měla mít dvě služby: Hello a stavovou službu HelloWorldStateful.

Stavová služba má stejné vstupní body jako Bezstavová služba. Hlavním rozdílem je dostupnost zprostředkovatele stavu, který může spolehlivě ukládat stav. Service Fabric obsahuje implementaci poskytovatele stavu nazvanou Reliable Collections, která umožňuje vytvářet replikované datové struktury prostřednictvím Správce spolehlivého stavu. Stavová služba Reliable využívá tohoto poskytovatele stavu ve výchozím nastavení.

Otevřete HelloWorldStateful. Java v **HelloWorldStateful-> src**, který obsahuje následující RunAsync metodu:

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

### <a name="runasync"></a>RunAsync
`RunAsync()` funguje podobně jako stavová a Bezstavová služba. Ve stavové službě ale platforma před spuštěním provede další práci vaším jménem `RunAsync()` . Tato práce může zahrnovat jistotu, že je správce spolehlivých stavů a spolehlivé kolekce připravený k použití.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Spolehlivé kolekce a správce spolehlivého stavu
```java
ReliableHashMap<String,Long> map = this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap")
```

[ReliableHashMap](/java/api/microsoft.servicefabric.data.collections.reliablehashmap) je slovníková implementace, kterou můžete použít k spolehlivému ukládání stavu ve službě. Pomocí Service Fabric a spolehlivých HashMaps můžete ukládat data přímo do služby bez nutnosti externího trvalého úložiště. Spolehlivé HashMaps zajistí vysokou dostupnost vašich dat. Service Fabric toho dosahuje vytvořením a správou více *replik* vaší služby za vás. Poskytuje také rozhraní API, které abstrakce zjednodušuje správu těchto replik a jejich přechodů na stav.

Spolehlivé kolekce můžou ukládat jakýkoli typ Java, včetně vašich vlastních typů, s několika upozorněními:

* Service Fabric zajistí, aby byl stav vysoce dostupný při *replikaci* do všech uzlů, a spolehlivý HashMap ukládá vaše data na místní disk v každé replice. To znamená, že všechno, co je uloženo v Reliable HashMaps, musí být *serializovatelný*. 
* Objekty jsou replikovány pro zajištění vysoké dostupnosti při potvrzení transakcí na spolehlivých HashMaps. Objekty uložené v Reliable HashMaps jsou v rámci služby uchovávány v místní paměti. To znamená, že máte místní odkaz na objekt.
  
   Je důležité, abyste nemuseli provádět místní instance těchto objektů bez provedení operace aktualizace pro spolehlivou kolekci v transakci. Důvodem je to, že změny místních instancí objektů nebudou replikovány automaticky. Objekt je nutné znovu vložit zpět do slovníku nebo použít jednu z metod *aktualizace* ve slovníku.

Správce Reliable State spravuje spolehlivé HashMaps za vás. Správce spolehlivého stavu můžete požádat o spolehlivou kolekci podle názvu kdykoli a na jakémkoli místě ve vaší službě. Správce Reliable State zajišťuje, že získáte odkaz zpátky. Nedoporučujeme ukládat odkazy na spolehlivé instance kolekcí v proměnných členů třídy nebo vlastnostech. Aby se zajistilo, že odkaz bude v životním cyklu služby neustále nastavený na instanci, musí být podniknuta zvláštní péče. Reliable State Manager zpracovává tuto práci za vás a je optimalizovaná pro opakované návštěvy.


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

Operace s Reliable HashMaps jsou asynchronní. Důvodem je to, že operace zápisu s spolehlivými kolekcemi provádějí vstupně-výstupní operace pro replikaci a uchovávání dat na disk.

Spolehlivé operace HashMap jsou *transakční*, takže je možné udržet stav konzistentně napříč několika spolehlivými HashMaps a operacemi. Například můžete získat pracovní položku z jednoho spolehlivého slovníku, provést na ní operaci a výsledek Uložit do jiného spolehlivého HashMapu, a to vše v rámci jedné transakce. Tato možnost se považuje za atomickou operaci a zaručuje, že celá operace bude úspěšná nebo se vrátí celá operace. Pokud dojde k chybě po vyřazení položky z fronty, ale před uložením výsledku, je celá transakce vrácena zpět a položka zůstane ve frontě ke zpracování.


## <a name="build-the-application"></a>Sestavení aplikace

Generování uživatelského rozhraní Yeoman zahrnuje skript Gradle pro sestavení aplikace a bash skripty pro nasazení a odebrání aplikace. Chcete-li spustit aplikaci, nejprve sestavte aplikaci pomocí Gradle:

```bash
$ gradle
```

Tím se vytvoří balíček aplikace Service Fabric, který se dá nasadit pomocí Service Fabric CLI.

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

Jakmile je aplikace nasazená, otevřete prohlížeč a přejděte k nástroji [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) na adrese `http://localhost:19080/Explorer`. Pak rozbalte uzel **Aplikace** a všimněte si, že už obsahuje položku pro váš typ aplikace a další položku pro první instanci tohoto typu.

> [!IMPORTANT]
> Pokud chcete nasadit aplikaci do clusteru zabezpečeného Linux v Azure, musíte nakonfigurovat certifikát pro ověření aplikace pomocí modulu runtime Service Fabric. To umožňuje, aby vaše služby Reliable Services komunikovaly se základními rozhraními API Service Fabric runtime. Další informace najdete v tématu [Konfigurace aplikace Reliable Services pro spouštění v clusterech se systémem Linux](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters).  
>

## <a name="next-steps"></a>Další kroky

* [Začínáme se Service Fabric CLI](service-fabric-cli.md)
