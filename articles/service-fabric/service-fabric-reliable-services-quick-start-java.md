---
title: Vytvoření vaší první mikroslužbu spolehlivé Azure v jazyce Java | Microsoft Docs
description: Úvod do vytváření aplikace Microsoft Azure Service Fabric s bezzstavovými i stavovými službami.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: timlt
editor: ''
ms.assetid: 7831886f-7ec4-4aef-95c5-b2469a5b7b5d
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: suhuruli
ms.openlocfilehash: 48546e84b94ad0c11a159b2f88f7e21f7eb6ae0e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="get-started-with-reliable-services"></a>Začínáme s Reliable Services
> [!div class="op_single_selector"]
> * [C# v systému Windows](service-fabric-reliable-services-quick-start.md)
> * [Java v Linuxu](service-fabric-reliable-services-quick-start-java.md)
>
>

Tento článek vysvětluje základy služby Azure Service Fabric spolehlivé a provede vás vytvořením a nasazením jednoduchou spolehlivá služba aplikaci napsanou v jazyce Java. Video tento Microsoft Virtual Academy také ukazuje postup vytvoření bezstavové spolehlivé služby: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=DOX8K86yC_206218965">  
<img src="./media/service-fabric-reliable-services-quick-start-java/ReliableServicesJavaVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="installation-and-setup"></a>Instalace a nastavení
Než začnete, ujistěte se, že máte vývojového prostředí Service Fabric na váš počítač.
Pokud potřebujete nastavit tak, přejděte na [Začínáme v systému Mac](service-fabric-get-started-mac.md) nebo [Začínáme v systému Linux](service-fabric-get-started-linux.md).

## <a name="basic-concepts"></a>Základní koncepty
Pokud chcete začít se službami Reliable Services, potřebujete jenom pochopit několik základní koncepty:

* **Typ služby**: Toto je implementace služby. Je definována v třídě napíšete, který rozšiřuje `StatelessService` a ostatní kódu nebo v něm, použít název a číslo verze závislosti.
* **S názvem instance služby**: ke spuštění služby, vytvoříte pojmenované instance typu služby mnohem jako vytvoření instance objektu typu třídy. Instance služby, které jsou ve skutečnosti instancí objektu možnosti vaší služby třídu, která můžete zapsat.
* **Hostitel služby**: pojmenované instance vytvoříte muset spustit uvnitř hostitele. Hostitel služby je právě proces, kde můžete spustit instance služby.
* **Registrace služby**: registrace soustřeďuje všechny informace dohromady. Typ služby musí být zaregistrován u modulu runtime Service Fabric v hostitele služby umožňující Service Fabric k vytvoření instance ho spustit.  

## <a name="create-a-stateless-service"></a>Vytvoření bezstavové služby
Začněte vytvořením aplikace Service Fabric. Sada Service Fabric SDK pro Linux zahrnuje Yeoman generátor zajistit generování uživatelského rozhraní pro aplikace Service Fabric pomocí bezstavové služby. Spusťte následující Yeoman spuštěním příkazu:

```bash
$ yo azuresfjava
```

Postupujte podle pokynů vytvořte **spolehlivé bezstavové služby**. V tomto kurzu, název aplikace "HelloWorldApplication" a "HelloWorld" service. Výsledek obsahuje adresářů pro `HelloWorldApplication` a `HelloWorld`.

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
Typů služeb musí být zaregistrované v modulu runtime Service Fabric. Typ služby je definována v `ServiceManifest.xml` a třídě služby, který implementuje `StatelessService`. Registrace služby se provádí v hlavní vstupního bodu procesu. V tomto příkladu je hlavní vstupního bodu procesu `HelloWorldServiceHost.java`:

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

## <a name="implement-the-service"></a>Tuto službu implementovat

Otevřete **HelloWorldApplication/HelloWorld/src/statelessservice/HelloWorldService.java**. Tato třída definuje typ služby a můžete spustit kód. Rozhraní API služby obsahuje dvě vstupní body pro váš kód:

* Metodu zprostředkovává vstupního bodu, názvem `runAsync()`, kde můžete začít provádění jakékoli úlohy, včetně dlouho běžící výpočetních úloh.

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    ...
}
```

* Ke komunikaci vstupnímu bodu kde zařaďte vaší komunikačního balíku výběru. Toto je, kde můžete spustit přijímá žádosti od uživatelů a dalších služeb.

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {
    ...
}
```

V tomto kurzu budeme soustředit se na `runAsync()` metody vstupní bod. Toto je, kde můžete okamžitě začít kód spuštěný.

### <a name="runasync"></a>RunAsync
Platforma volá tuto metodu, když je umístěný a připravené ke spuštění instance služby. Bezstavové služby, která jednoduše znamená, když je otevřen v instanci služby. Token zrušení je k dispozici pro koordinaci při instanci služby musí být uzavřen. V Service Fabric tento cyklus otevření nebo uzavření instance služby může docházet k tolikrát, kolikrát za dobu existence služby jako celek. Toto může nastat z různých důvodů, včetně:

* Systém přesune vaší instance služby pro vyrovnávání prostředků.
* K chybám dochází v kódu.
* Aplikace nebo systému byla upgradována.
* Základní hardware dojde k výpadku.

Tato orchestration je spravován pomocí Service Fabric a udržovat služby je vysoce dostupný a správně vyrovnáváním.

`runAsync()` by neměly blokovat synchronně. Implementaci runAsync by měl vrátit CompletableFuture umožňující pokračovat modulu runtime. Pokud vaše úlohy musí implementovat dlouhotrvající úlohu, která se má provést uvnitř CompletableFuture.

#### <a name="cancellation"></a>Zrušení
Zrušení úlohy je spolupráci úsilí řízená token poskytnutý zrušení. Systém čeká na ukončení (podle úspěšné dokončení, zrušení nebo selhání), než ji přesune vaše úlohy. Je důležité respektovat token zrušení, Dokončit veškerou práci a ukončete `runAsync()` provést co nejrychleji, pokud systém požadavky zrušení. Následující příklad ukazuje, jak se zpracovat událost zrušení:

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

V tomto příkladu bezstavové služby je počet uložené v místní proměnné. Ale protože je bezstavové služby, hodnotu, která je uložená existuje pouze pro aktuální životní cyklus jeho instanci služby. Pokud službu přesune nebo restartuje, hodnota je ztraceny.

## <a name="create-a-stateful-service"></a>Vytvoření stavové služby
Service Fabric zavádí nový typ služby, která je stavový. Stavové služby můžete udržovat stav spolehlivě v rámci služby samostatně, umístěn společně s kód, který se používá. Stav je vysoké dostupnosti pomocí Service Fabric bez nutnosti zachování stavu na externím obchodu.

Chcete-li převést hodnotu čítače bezstavové na vysoce dostupné a trvalé, i když služba přesune nebo restartování, je třeba stavové služby.

Ve stejném adresáři jako aplikace HelloWorld, můžete přidat novou službu spuštěním `yo azuresfjava:AddService` příkaz. Zvolte "Spolehlivé Stateful služba" pro požadované rozhraní a název služby "HelloWorldStateful". 

Aplikace musí mít teď dvě služby: bezstavové služby HelloWorld a stateful služby HelloWorldStateful.

Stavová služba má stejné vstupní body jako bezstavové služby. Hlavní rozdíl je dostupnost zprostředkovatele stavu, který může ukládat stavu spolehlivě. Service Fabric se dodává s implementace zprostředkovatele stavu se říká kolekce a spolehlivé, který umožňuje vytvářet struktury replikovaná data prostřednictvím spolehlivé správce stavu. Stavové spolehlivé služby pomocí zprostředkovatele stavu ve výchozím nastavení.

Otevřete HelloWorldStateful.java v **HelloWorldStateful -> src**, který obsahuje následující metodě RunAsync:

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
`RunAsync()` funguje podobně jako v stavová a Bezstavová služby. Ale v stavové služby platformy provede další práci vaším jménem předtím, než se provede `RunAsync()`. Tento pracovní mohou zahrnovat kontrolu, spolehlivé správce stavu a spolehlivé kolekce jsou připravené k použití.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Spolehlivé kolekce a spolehlivé správce stavu
```java
ReliableHashMap<String,Long> map = this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap")
```

[ReliableHashMap](https://docs.microsoft.com/java/api/microsoft.servicefabric.data.collections._reliable_hash_map) je implementace slovník, který můžete použít k spolehlivě uložení stavu ve službě. Service Fabric a spolehlivé Hashmaps můžete uložit data přímo ve službě bez nutnosti externí trvalého úložiště. Spolehlivé Hashmaps dají vašim datům vysoce dostupný. Service Fabric dosahuje tak, že vytváření a správu více *repliky* služby za vás. Také poskytuje rozhraní API, které abstrahuje rychle složitosti správy tyto repliky a jejich přechodů mezi stavy.

Spolehlivé kolekce můžete ukládat jakýkoli typ Java, včetně vlastních typů, pomocí několika upozornění:

* Service Fabric umožňuje vašemu stavu vysoce dostupné podle *replikace* stav napříč uzly a spolehlivé Hashmap ukládá data na místní disk na jednotlivé repliky. To znamená, že musí být vše, co je uložen v spolehlivé Hashmaps *serializovatelný*. 
* Pokud provedete transakce na spolehlivé Hashmaps, replikují se objekty pro zajištění vysoké dostupnosti. Objekty uložené v spolehlivé Hashmaps udržovaly v místní paměti ve službě. To znamená, že máte místní odkaz na objekt.
  
   Je důležité, neprovádějte místní instancí těchto objektů bez provádění operace aktualizace na kolekci spolehlivé v transakci. To je proto nebude automaticky replikovat změny místní instance objektů. Musíte znovu vložit objekt zpět do slovníku nebo použijte jednu z *aktualizace* metody ve slovníku.

Správce spolehlivé stavu spravuje spolehlivé Hashmaps za vás. Můžete jednoduše pokládat spolehlivé správce stavu pro kolekci spolehlivé podle názvu kdykoli a kdekoli v službě. Spolehlivé správce stavu zajišťuje, získejte odkaz na zpět. Doporučujeme si uložit odkazy na spolehlivé kolekci instancí v člen třídy, proměnné nebo vlastnosti. Musí dát zvláštní pozor zajistit, že je odkaz nastavený na instanci za všech okolností v průběhu životního cyklu služby. Spolehlivé správce stavu zpracuje tato práce pro uživatele a je optimalizovaný pro opakování návštěvách.


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

Operace na spolehlivé Hashmaps jsou asynchronní. Je to proto, že operace zápisu ke kolekcím, spolehlivé provádění vstupně-výstupních operací se budou replikovat a zachovat data na disk.

Spolehlivé Hashmap operace jsou *transakcí*, takže můžete zachovat stav konzistentní napříč více spolehlivé Hashmaps a operace. Může například získat pracovní položky ze slovníku jeden spolehlivé, proveďte operaci na něm a uložit výsledek anoter spolehlivé Hashmap, vše v rámci jedné transakce. To je považován za atomická operace, a zaručuje, že buď celé operace proběhne úspěšně, nebo celou operaci vrátíte zpět. Pokud dojde k chybě po dequeue položku, ale před uložením výsledek, celá transakce bude vrácena zpět a položka zůstane ve frontě pro zpracování.


## <a name="run-the-application"></a>Spuštění aplikace

Yeoman generování uživatelského rozhraní obsahuje skript gradle sestavení aplikace a skripty pro nasazení a odeberte aplikaci bash. Ke spuštění aplikace, nejprve sestavte aplikaci s gradlem:

```bash
$ gradle
```

Tímto se vytvoří balíček aplikace Service Fabric, které se dá nasadit pomocí Service Fabric rozhraní příkazového řádku.

### <a name="deploy-with-service-fabric-cli"></a>Nasazení pomocí Service Fabric rozhraní příkazového řádku

Install.sh skript obsahuje potřebné příkazy Service Fabric rozhraní příkazového řádku pro nasazení balíčku aplikace. Spusťte skript install.sh k nasazení aplikace.

```bash
$ ./install.sh
```

## <a name="next-steps"></a>Další postup

* [Začínáme se Service Fabric CLI](service-fabric-cli.md)
