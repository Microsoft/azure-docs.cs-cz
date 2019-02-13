---
title: Vytvoření první spolehlivé služby Azure Service Fabric v Javě | Dokumentace Microsoftu
description: Úvod do vytváření aplikace Microsoft Azure Service Fabric s bezstavové a stavové služby.
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
ms.openlocfilehash: 0b044b15b41e2d74f08c4bc989e22b6a19949445
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56170920"
---
# <a name="get-started-with-reliable-services"></a>Začínáme s Reliable Services
> [!div class="op_single_selector"]
> * [C# v systému Windows](service-fabric-reliable-services-quick-start.md)
> * [Java v Linuxu](service-fabric-reliable-services-quick-start-java.md)
>
>

Tento článek popisuje základní informace o Azure Service Fabric Reliable Services a provede vás provedou vytvořením a nasazením jednoduchou Reliable Service aplikaci napsanou v jazyce Java. 

## <a name="installation-and-setup"></a>Instalace a nastavení
Než začnete, ujistěte se, že máte na svém počítači vývojového prostředí Service Fabric.
Pokud je potřeba ho nastavit, přejděte na [Začínáme na počítači Mac](service-fabric-get-started-mac.md) nebo [Začínáme v Linuxu](service-fabric-get-started-linux.md).

## <a name="basic-concepts"></a>Základní koncepty
Chcete-li začít s Reliable Services, stačí pochopit pár základních konceptů:

* **Typ služby**: Toto je vaše implementace služby. Je definován třídou napíšete, která rozšiřuje `StatelessService` a jakékoli další kód nebo závislosti v něm používat společně s název a číslo verze.
* **Instance služby s názvem**: Ke spuštění služby, můžete vytvořit pojmenované instance typu služby podobně jako vytvoříte instance objektů typu třídy. Instance služby, které jsou ve skutečnosti objekt instancí třídy služby, který píšete.
* **Hostitel služby**: Instance s názvem služby, které vytvoříte třeba spustit v hostiteli. Hostitel služby je právě proces, ve kterém můžete spustit instance vaší služby.
* **Registrace služby**: Registrace spojuje všechno. Typ služby musí být zaregistrovaná s modulem runtime Service Fabric v hostiteli služby umožňuje Service Fabric pro vytvoření instancí jeho spuštění.  

## <a name="create-a-stateless-service"></a>Vytvoření bezstavové služby
Začněte vytvořením aplikace Service Fabric. Sada Service Fabric SDK pro Linux zahrnuje Yeoman generátor kvůli generování uživatelského rozhraní pro aplikace Service Fabric s bezstavovou službu. Spusťte následující Yeoman spuštěním příkazu:

```bash
$ yo azuresfjava
```

Postupujte podle pokynů k vytvoření **spolehlivé bezstavové služby**. Pro účely tohoto kurzu, název aplikace "HelloWorldApplication" a "HelloWorld" service. Výsledek bude obsahovat adresářů pro `HelloWorldApplication` a `HelloWorld`.

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
Typy služeb, musí zaregistrovat modul runtime Service Fabric. Typ služby je definována v `ServiceManifest.xml` a vaše služba třídu, která implementuje `StatelessService`. Registrace služby se provádí v hlavní vstupní bod procesu. V tomto příkladu je hlavní vstupní bod procesu `HelloWorldServiceHost.java`:

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

## <a name="implement-the-service"></a>Implementaci této služby

Open **HelloWorldApplication/HelloWorld/src/statelessservice/HelloWorldService.java**. Tato třída definuje typ služby a můžete spustit libovolný kód. Rozhraní API služby poskytuje dva vstupní body pro váš kód:

* Metodu neuzavřenou vstupního bodu, volá `runAsync()`, kde můžete zahájit provádění všech úloh, včetně dlouho běžící výpočetní úlohy.

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    ...
}
```

* Komunikaci vstupního bodu ve kterém můžete zařadit do zásobníku komunikace podle výběru. Je to, kde můžete začít přijímat žádosti od uživatelů a dalších služeb.

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {
    ...
}
```

Tento kurz se zaměřuje na `runAsync()` metodu vstupního bodu. To je, kde můžete okamžitě začít spouštět kód.

### <a name="runasync"></a>RunAsync
Platforma volá tuto metodu, když je umístěný a připravené ke spuštění instance služby. Pro bezstavovou službu, která znamená, že při otevření instance služby. Token zrušení slouží ke koordinaci při vaší instance služby je potřeba ho zavřít. V Service Fabric tento cyklus otevřít nebo Zavřít instance služby situace může nastat v mnoha případech během životního cyklu služby jako celek. To může dojít z různých důvodů, včetně:

* Systém přesune vaší instance služby pro vyrovnávání prostředků.
* K chybám dochází ve vašem kódu.
* Aplikace nebo systému se upgraduje.
* Základní hardware dojde k výpadku.

Tuto orchestraci se spravuje pomocí Service Fabric se vaše služba s vysokou dostupností a správně vyvážené.

`runAsync()` by neměla blokovat synchronně. Implementace runAsync by měl vrátit CompletableFuture umožňuje modulu runtime, abyste mohli pokračovat. Pokud vaše úloha musí implementovat dlouho běžící úlohu, která se má počítat uvnitř CompletableFuture.

#### <a name="cancellation"></a>Zrušení
Zrušení úlohy je kooperativní úsilí orchestrované systémem poskytnutého rušícího tokenu. Systém čeká na ukončení (podle úspěšné dokončení, zrušení nebo selhání) předtím, než se přesune vaše úlohy. Je potřeba vyhovět token zrušení, Dokončit veškerou práci a ukončit `runAsync()` nejkratší možné době, když systém požaduje zrušení. Následující příklad ukazuje, jak zpracovat událost zrušení:

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

V tomto příkladu bezstavovou službu počet uloženy v místní proměnné. Ale vzhledem k tomu, že toto je Bezstavová služba, existuje hodnota, která je uložena pouze pro aktuální životní cyklus její instance služby. Když služba přesune nebo se restartuje, hodnota je ztraceny.

## <a name="create-a-stateful-service"></a>Vytvoření stavové služby
Service Fabric představuje nový typ služby, která je stavový. Stavové služby můžete udržovat stav spolehlivě v rámci samotné služby společně umístěné s kódem, který ji používá. Stav je provedené s vysokou dostupností pomocí Service Fabric bez nutnosti k uchování stavu na externím úložišti.

Chcete-li převést hodnotu čítače bezstavové na vysoce dostupné a trvalé, i když službu přesune nebo se restartuje, musíte do stavové služby.

Ve stejném adresáři jako aplikace Hello World, můžete přidat novou službu spuštěním `yo azuresfjava:AddService` příkazu. Zvolte "Reliable Stateful službu" pro vaše rozhraní a pojmenujte službu "HelloWorldStateful". 

Vaše aplikace by měla mít teď dvě služby: bezstavovou službu Hello World a stateful service HelloWorldStateful.

Stavová služba má stejný vstupní body jako bezstavové služby. Hlavní rozdíl je dostupnost zprostředkovatele stavu, ve kterém můžete spolehlivě uložený stav. Service Fabric se dodává s implementací zprostředkovatele stavu Reliable Collections, což vám umožní vytvářet replikované datové struktury prostřednictvím Reliable State Manager volá. Ve výchozím nastavení spolehlivou stavovou službu používá tento stav poskytovatele.

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
`RunAsync()` pracuje podobně jako v stavové a bezstavové služby. Ale stavové služby platformy další práci za vás provede předtím, než se provede `RunAsync()`. Tato práce může zahrnovat zajistit, aby Reliable State Manager a Reliable Collections jsou připravené k použití.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Spolehlivé kolekce a Reliable State Manager
```java
ReliableHashMap<String,Long> map = this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap")
```

[ReliableHashMap](https://docs.microsoft.com/java/api/microsoft.servicefabric.data.collections.reliablehashmap) je implementaci slovníku, který můžete použít ke spolehlivému uložení stavu ve službě. S platformou Service Fabric a spolehlivé HashMaps můžete ukládat data přímo do vaší služby bez nutnosti externí trvalého úložiště. Spolehlivé HashMaps vytvořit data s vysokou dostupností. Service Fabric dosahuje tak, že vytváření a správě více *repliky* služby za vás. Také poskytuje rozhraní API, který vyčleňuje složitých úkolů při správě tyto repliky a jejich přechodů mezi stavy.

Reliable Collections ukládat jakýkoli typ Javy, včetně vašich vlastních typů pomocí několika upozornění:

* Service Fabric zajišťuje svůj stav s vysokou dostupností pomocí *replikaci* stavu mezi uzly a spolehlivé HashMap ukládá data na místní disk na jednotlivé repliky. To znamená, že musí být vše, co je uložen v Reliable HashMaps *serializovatelný*. 
* Při potvrzení transakce na spolehlivé HashMaps objekty replikují pro zajištění vysoké dostupnosti. Objekty uložené v Reliable HashMaps jsou uloženy v místní paměti ve své službě. To znamená, že máte místní odkaz na objekt.
  
   Je důležité, neprovádějte místní instancí těchto objektů bez provedení operace aktualizace na spolehlivé kolekce v transakci. Je to proto, že změny v místní instance objektů, se replikovat nebudou automaticky. Musíte znovu objektu zpět do slovníku nebo použijte jednu z *aktualizovat* metody ve slovníku.

Spolehlivé HashMaps Reliable State Manager spravuje za vás. Můžete požádat o Reliable State Manager spolehlivé kolekci podle názvu kdykoli a kdekoli ve své službě. Reliable State Manager zajišťuje, získejte odkaz zpět. Nedoporučujeme uložit odkazy na instance spolehlivé kolekce v členské proměnné třídy nebo vlastnosti. Zvláštní pozornost musí provést k zajištění, že je odkaz nastavený na instanci za všech okolností v životního cyklu služeb. Reliable State Manager zpracovává tuto práci za vás a je optimalizovaný pro opakování návštěvy.


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

Operace na spolehlivé HashMaps jsou asynchronní. Je to proto, že operace zápisu s Reliable Collections provádět vstupně-výstupních operací pro replikaci a uložení dat na disk.

Spolehlivé operace HashMap jsou *transakční*, takže můžete zachovat stav konzistentní napříč několika spolehlivé HashMaps a operace. Může například získat pracovní položky z jednoho spolehlivého slovníku, provádění operací na něj a výsledek uložit jako jiný spolehlivé HashMap, vše v rámci jedné transakce. Je zpracovaná jako atomickou operaci a zaručuje, že buď celá operace proběhne úspěšně, nebo bude celá operace vrátit zpět. Pokud dojde k chybě po odstranění z fronty položky, ale před uložením výsledku, celá transakce bude vrácena zpět a položka zůstane ve frontě pro zpracování.


## <a name="build-the-application"></a>Sestavení aplikace

Yeoman zahrnuje skript gradle pro sestavení aplikace a skripty k nasazení a odeberte aplikaci bash generování uživatelského rozhraní. Ke spuštění aplikace, nejprve sestavte aplikaci s gradlem:

```bash
$ gradle
```

Tímto se vytvoří balíček aplikace Service Fabric, který je možné nasadit pomocí rozhraní příkazového řádku Service Fabric.

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
> Pokud chcete nasadit aplikaci do zabezpečeného clusteru s Linuxem v Azure, budete muset nakonfigurovat certifikát pro ověření vaší aplikace pomocí modulu runtime Service Fabric. Díky tomu služby Reliable Services ke komunikaci s základního modulu runtime Service Fabric rozhraní API. Další informace najdete v tématu [konfigurace aplikace Reliable Services ke spuštění na clusterech s Linuxem](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters).  
>

## <a name="next-steps"></a>Další postup

* [Začínáme se Service Fabric CLI](service-fabric-cli.md)
