---
title: Vytvoření aplikace Azure Service Fabric Reliable Actors v Javě v Linuxu | Dokumentace Microsoftu
description: Zjistěte, jak za pět minut vytvořit a nasadit aplikaci Service Fabric Reliable Actors v Javě.
services: service-fabric
documentationcenter: java
author: rwike77
manager: timlt
editor: ''
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/18/2018
ms.author: ryanwi
ms.openlocfilehash: 16c99c2c5524a321616ac9f0975f0c9b4255ca94
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36215850"
---
# <a name="create-your-first-java-service-fabric-reliable-actors-application-on-linux"></a>Vytvoření první aplikace Service Fabric Reliable Actors v Javě v Linuxu
> [!div class="op_single_selector"]
> * [C# – Windows](service-fabric-create-your-first-application-in-visual-studio.md)
> * [Java – Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# – Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

Tento rychlý start vám pomůže během několika minut vytvořit první aplikaci Azure Service Fabric v Javě v linuxovém vývojovém prostředí.  Až budete hotovi, budete mít jednoduchou jednoúčelovou aplikaci v Javě spuštěnou v místním vývojovém clusteru.  

## <a name="prerequisites"></a>Požadavky
Než začnete, nainstalujte sadu Service Fabric SDK, Service Fabric CLI a Yeoman a nastavte ve svém [linuxovém vývojovém prostředí](service-fabric-get-started-linux.md) vývojové prostředí Java a vývojový cluster. Pokud používáte Mac OS X, můžete k [nastavit vývojové prostředí na Macu pomocí Dockeru](service-fabric-get-started-mac.md).

Nainstalujte také [Service Fabric CLI](service-fabric-cli.md).

### <a name="install-and-set-up-the-generators-for-java"></a>Instalace a nastavení generátorů pro Javu
Service Fabric nabízí nástroje pro generování uživatelského rozhraní, které vám pomůžou vytvořit aplikaci Service Fabric Java z terminálu pomocí generátoru šablon Yeoman.  Pokud ještě nemáte nainstalovaný Yeoman, přečtěte si pokyny k jeho nastavení v článku [Service Fabric – Začínáme s Linuxem](service-fabric-get-started-linux.md#set-up-yeoman-generators-for-containers-and-guest-executables). Spuštěním následujícího příkazu nainstalujte generátor šablon Service Fabric Yeoman pro Javu.

  ```bash
  sudo npm install -g generator-azuresfjava
  ```

## <a name="basic-concepts"></a>Základní koncepty
K tomu, abyste mohli začít s Reliable Actors, vám stačí pochopit pár základních konceptů:

* **Služba objektu actor**. Služby Reliable Actors jsou zabalené ve službách Reliable Services, které je možné nasadit v infrastruktuře Service Fabric. Instance objektu actor se aktivují v pojmenované instanci služby.
* **Registrace objektu actor**. Stejně jako Reliable Services musí být i služba Reliable Actors zaregistrovaná v modulu runtime Service Fabric. Kromě toho musí být typ actor zaregistrovaný v modulu runtime Actor.
* **Rozhraní objektu actor**. Rozhraní objektu actor se používá k definování veřejného rozhraní objektu actor silného typu. V terminologii modelu Reliable Actors rozhraní objektu actor definuje typ zpráv, kterým objekt actor rozumí a může je zpracovat. Rozhraní objektu actor používají ostatní objekty actor a klientské aplikace k (asynchronnímu) odesílání zpráv do objektu actor. Reliable Actors můžou implementovat více rozhraní.
* **Třída ActorProxy**. Třídu ActorProxy používají klientské aplikace k vyvolání metod zveřejněných přes rozhraní objektu actor. Třída ActorProxy zajišťuje dvě důležité funkce:
  
  * Překlad názvů: Dokáže vyhledat objekt actor v clusteru (najít uzel clusteru, který je jeho hostitelem).
  * Zpracování selhání: Dokáže opakovat vyvolání metod a znovu přeložit umístění objektu actor například po selhání, které vyžaduje přemístění objektu actor do jiného uzlu v clusteru.

Za zmínku stojí následující pravidla týkající se rozhraní objektů actor:

* Metody rozhraní objektu actor není možné přetížit.
* Metody rozhraní objektu actor nesmí obsahovat vnější, referenční ani volitelné parametry.
* Obecná rozhraní se nepodporují.

## <a name="create-the-application"></a>Vytvoření aplikace
Aplikace Service Fabric obsahuje jednu nebo víc služeb, z nichž každá má určitou roli při poskytování funkcí aplikace. Generátor, který jste nainstalovali v poslední části, vám usnadní vytvoření první služby a případná další rozšíření později.  Vytvořit, sestavit a nasadit aplikace Service Fabric v Javě můžete také pomocí modulu plug-in pro Eclipse. Viz [Vytvoření a nasazení první aplikace v Javě pomocí Eclipse](service-fabric-get-started-eclipse.md). Pro účely tohoto rychlého startu použijte Yeoman k vytvoření aplikace s jednou službou, která ukládá a získává hodnotu čítače.

1. V terminálu zadejte ``yo azuresfjava``.
2. Pojmenujte svoji aplikaci.
3. Vyberte typ první služby a pojmenujte ji. Pro účely tohoto kurzu zvolte službu Reliable Actor. Další informace o ostatních typech služeb najdete v tématu [Přehled programovacího modelu Service Fabric](service-fabric-choose-framework.md).
   ![Generátor Service Fabric Yeoman pro Javu][sf-yeoman]

Pokud pojmenujete aplikaci HelloWorldActorApplication a objekt actor HelloWorldActor, vytvoří se následující kostra aplikace:

```bash
HelloWorldActorApplication/
├── build.gradle
├── HelloWorldActor
│   ├── build.gradle
│   ├── settings.gradle
│   └── src
│       └── reliableactor
│           ├── HelloWorldActorHost.java
│           └── HelloWorldActorImpl.java
├── HelloWorldActorApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldActorPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   ├── _readme.txt
│       │   └── Settings.xml
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── HelloWorldActorInterface
│   ├── build.gradle
│   └── src
│       └── reliableactor
│           └── HelloWorldActor.java
├── HelloWorldActorTestClient
│   ├── build.gradle
│   ├── settings.gradle
│   ├── src
│   │   └── reliableactor
│   │       └── test
│   │           └── HelloWorldActorTestClient.java
│   └── testclient.sh
├── install.sh
├── settings.gradle
└── uninstall.sh
```
## <a name="reliable-actors-basic-building-blocks"></a>Základní stavební bloky Reliable Actors
Základní koncepty popsané dříve se převedou na základní stavební bloky služby Reliable Actors.

### <a name="actor-interface"></a>Rozhraní objektu actor
Tady se nachází definice rozhraní pro objekt actor. Toto rozhraní definuje kontrakt objektu actor sdílený mezi implementací objektu actor a klienty, kteří objekt actor volají. Proto je obvykle vhodné jej definovat na odděleném místě od implementace objektu actor, které může sdílet několik dalších služeb nebo klientských aplikací.

`HelloWorldActorInterface/src/reliableactor/HelloWorldActor.java`:

```java
public interface HelloWorldActor extends Actor {
    @Readonly   
    CompletableFuture<Integer> getCountAsync();

    CompletableFuture<?> setCountAsync(int count);
}
```

### <a name="actor-service"></a>Služba objektu actor
Tady se nachází implementace objektu actor a jeho registrační kód. Třída actor implementuje rozhraní objektu actor. Tady objekt actor provádí veškerou svou činnost.

`HelloWorldActor/src/reliableactor/HelloWorldActorImpl`:

```java
@ActorServiceAttribute(name = "HelloWorldActorService")
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class HelloWorldActorImpl extends FabricActor implements HelloWorldActor {
    private Logger logger = Logger.getLogger(this.getClass().getName());

    public HelloWorldActorImpl(FabricActorService actorService, ActorId actorId){
        super(actorService, actorId);
    }

    @Override
    protected CompletableFuture<?> onActivateAsync() {
        logger.log(Level.INFO, "onActivateAsync");

        return this.stateManager().tryAddStateAsync("count", 0);
    }

    @Override
    public CompletableFuture<Integer> getCountAsync() {
        logger.log(Level.INFO, "Getting current count value");
        return this.stateManager().getStateAsync("count");
    }

    @Override
    public CompletableFuture<?> setCountAsync(int count) {
        logger.log(Level.INFO, "Setting current count value {0}", count);
        return this.stateManager().addOrUpdateStateAsync("count", count, (key, value) -> count > value ? count : value);
    }
}
```

### <a name="actor-registration"></a>Registrace objektu actor
Služba objektu actor musí být zaregistrovaná s typem služby v modulu runtime Service Fabric. Aby služba objektu actor mohla spouštět instance objektu actor, musí v ní být zaregistrovaný také typ actor. Pro objekty actor tuto práci provádí metoda registrace `ActorRuntime`.

`HelloWorldActor/src/reliableactor/HelloWorldActorHost`:

```java
public class HelloWorldActorHost {

private static final Logger logger = Logger.getLogger(HelloWorldActorHost.class.getName());
    
public static void main(String[] args) throws Exception {
        
        try {

            ActorRuntime.registerActorAsync(HelloWorldActorImpl.class, (context, actorType) -> new FabricActorService(context, actorType, (a,b)-> new HelloWorldActorImpl(a,b)), Duration.ofSeconds(10));
            Thread.sleep(Long.MAX_VALUE);
        } catch (Exception e) {
            logger.log(Level.SEVERE, "Exception occured", e);
            throw e;
        }
    }
}
```

## <a name="build-the-application"></a>Sestavení aplikace
Šablony Service Fabric Yeoman zahrnují skript sestavení pro [Gradle](https://gradle.org/), který můžete použít k sestavení aplikace z terminálu.
Závislosti Service Fabric Java se získávají z Mavenu. Chcete-li sestavovat aplikace Service Fabric Java a pracovat s nimi, musíte zajistit, že máte nainstalovanou sadu JDK a Gradle. Pokud tyto součásti nemáte nainstalované, přečtěte si pokyny k jejich nastavení v článku [Service Fabric – Začínáme s Linuxem](service-fabric-get-started-linux.md#set-up-java-development).

Pokud chcete sestavit a zabalit aplikaci, spusťte následující:

  ```bash
  cd HelloWorldActorApplication
  gradle
  ```

## <a name="deploy-the-application"></a>Nasazení aplikace
Jakmile je aplikace sestavená, můžete ji nasadit do místního clusteru.

1. Připojte se k místnímu clusteru Service Fabric (cluster musí být [nastavený a spuštěný](service-fabric-get-started-linux.md#set-up-a-local-cluster)).

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Spuštěním instalačního skriptu, který je součástí šablony, zkopírujte balíček aplikace do úložiště imagí clusteru, zaregistrujte typ aplikace a vytvořte její instanci.

    ```bash
    ./install.sh
    ```

Nasazení sestavené aplikace je stejné jako u všech ostatních aplikací Service Fabric. Podrobné pokyny najdete v dokumentaci s popisem [správy aplikace Service Fabric pomocí Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md).

Parametry těchto příkazů najdete v generovaných manifestech uvnitř balíčku aplikace.

Jakmile je aplikace nasazená, otevřete prohlížeč a přejděte k nástroji [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) na adrese [http://localhost:19080/Explorer](http://localhost:19080/Explorer).
Pak rozbalte uzel **Aplikace** a všimněte si, že už obsahuje položku pro váš typ aplikace a další položku pro první instanci tohoto typu.

## <a name="start-the-test-client-and-perform-a-failover"></a>Spuštění klienta testování a převzetí služeb při selhání
Samotné objekty actor nic nedělají – vyžadují, aby jim jiná služba nebo klient posílali zprávy. Šablona actor zahrnuje jednoduchý testovací skript, který můžete použít k interakci se službou actor.

> [!Note]
> Testovací klient používá třídu ActorProxy ke komunikaci s aktéři, které musí spustit ve stejném clusteru jako u služby objektu actor nebo sdílet stejnou adresní prostor IP adres.  Testovacího klienta můžete spustit na stejném počítači jako místní vývojový cluster.  Ke komunikaci s aktéři v clusteru s podporou vzdálený, ale musíte nasadit bránu na clusteru, který zpracovává externí komunikaci s aktéři.

1. Spusťte skript pomocí pomocného sledovacího programu a prohlédněte si výstup služby actor.  Testovací skript volá metodu `setCountAsync()` objektu actor pro zvýšení čítače a metodu `getCountAsync()` objektu actor pro získání nové hodnoty čítače, kterou zobrazí v konzole.

    ```bash
    cd HelloWorldActorTestClient
    watch -n 1 ./testclient.sh
    ```

2. V nástroji Service Fabric Explorer vyhledejte uzel, který je hostitelem primární repliky pro službu objektu actor. Na snímku níže je to uzel 3. Primární replika služby zpracovává operace čtení a zápisu.  Změny stavu služby se následně replikují do sekundárních replik, které jsou na následujícím snímku obrazovku spuštěné na uzlech 0 a 1.

    ![Vyhledání primární repliky v Service Fabric Exploreru][sfx-primary]

3. V části **Uzly** klikněte na uzel, který jste našli v předchozím kroku, a pak v nabídce Akce vyberte **Deaktivovat (restartovat)**. Tato akce restartuje uzel spuštěný v primární replice služby a vynutí převzetí služeb při selhání jednou ze sekundárních replik spuštěných na jiném uzlu.  Úroveň této sekundární repliky se zvýší na primární, v jiném uzlu se vytvoří jiná sekundární replika a primární replika začne přijímat operace čtení a zápisu. Při restartování uzlu sledujte výstup z testovacího klienta a všimněte si, že se čítač bez ohledu na převzetí služeb při selhání pořád postupně zvyšuje.

## <a name="remove-the-application"></a>Odebrání aplikace
Pomocí odinstalačního skriptu, který je součástí šablony, odstraňte instanci aplikace, zrušte registraci balíčku aplikace a odeberete balíček aplikace z úložiště imagí clusteru.

```bash
./uninstall.sh
```

V nástroji Service Fabric Explorer uvidíte, že se aplikace a typ aplikace už nezobrazují v uzlu **Aplikace**.

## <a name="service-fabric-java-libraries-on-maven"></a>Knihovny Service Fabric Java v Mavenu
Hostitelem knihoven Service Fabric Java je Maven. Můžete přidat závislosti do souborů ``pom.xml`` nebo ``build.gradle`` vašich projektů, aby se používaly knihovny Service Fabric Java z úložiště **mavenCentral**. 

### <a name="actors"></a>Objekty actor

Podpora Service Fabric Reliable Actor pro vaši aplikaci.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-actors-preview</artifactId>
      <version>0.12.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-actors-preview:0.12.0'
  }
  ```

### <a name="services"></a>Služby

Podpora Service Fabric Reliable Services pro vaši aplikaci.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-services-preview</artifactId>
      <version>0.12.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-services-preview:0.12.0'
  }
  ```

### <a name="others"></a>Ostatní
#### <a name="transport"></a>Přenos

Podpora přenosové vrstvy pro aplikace Service Fabric Java. Pokud neprogramujete na úrovni přenosové vrstvy, nemusíte tuto závislost do aplikace Reliable Actor nebo aplikace služby explicitně přidávat.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-transport-preview</artifactId>
      <version>0.12.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-transport-preview:0.12.0'
  }
  ```

#### <a name="fabric-support"></a>Podpora prostředků infrastruktury

Podpora na úrovni systému pro Service Fabric, která komunikuje s modulem runtime nativním pro Service Fabric. Tuto závislost nemusíte do aplikace Reliable Actor nebo aplikace služby explicitně přidávat. Načte se z Mavenu automaticky, jakmile zahrnete ostatní závislosti uvedené výše.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-preview</artifactId>
      <version>0.12.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-preview:0.12.0'
  }
  ```

## <a name="next-steps"></a>Další postup

* [Vytvoření první aplikace Service Fabric v Javě v Linuxu pomocí Eclipse](service-fabric-get-started-eclipse.md)
* [Další informace o Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Komunikace s clustery Service Fabric pomocí rozhraní příkazového řádku Service Fabric](service-fabric-cli.md)
* Informace o [možnostech podpory pro Service Fabric](service-fabric-support.md)
* [Začínáme se Service Fabric CLI](service-fabric-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png
