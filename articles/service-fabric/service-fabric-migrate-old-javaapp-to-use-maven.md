---
title: Migrace ze sady Java SDK na Maven
description: Aktualizace starších aplikací v Javě, které používaly Service Fabric Java SDK, k načtení závislostí Service Fabric v Javě z Mavenu. Po dokončení tohoto nastavení budou starší aplikace v Javě umožňovat sestavení.
author: rapatchi
ms.topic: conceptual
ms.date: 08/23/2017
ms.custom: devx-track-java
ms.author: rapatchi
ms.openlocfilehash: 3efa51f5632dd5cdc274ea39df5178aa0351a01f
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97652292"
---
# <a name="update-your-previous-java-service-fabric-application-to-fetch-java-libraries-from-maven"></a>Aktualizace předchozí aplikace Java Service Fabric pro načtení knihoven Javy z Mavenu
Service Fabric binární soubory Java se přesunuly z Service Fabric Java SDK na Maven hosting. Pomocí **mavencentral** můžete načíst nejnovější závislosti Service Fabric Java. Tato příručka vám pomůže aktualizovat existující aplikace Java vytvořené pro sadu Service Fabric Java SDK, aby byly kompatibilní se sestavením založeným na Maven, a to pomocí šablony Yeoman nebo zatmění.

## <a name="prerequisites"></a>Požadavky

1. Nejdřív odinstalujte stávající sadu Java SDK.

   ```bash
   sudo dpkg -r servicefabricsdkjava
   ```

2. Pomocí kroků uvedených [tady](service-fabric-cli.md) nainstalujte nejnovější rozhraní příkazového řádku Service Fabric.

3. Pokud chcete sestavovat a pracovat s Service Fabric aplikacemi Java, ujistěte se, že máte nainstalované JDK 1,8 a Gradle. Pokud ještě nejsou instalované, můžete sadu JDK 1.8.(openjdk-1.8-jdk) a Gradle nainstalovat spuštěním následujícího kódu:

   ```bash
   sudo apt-get install openjdk-8-jdk-headless
   sudo apt-get install gradle
   ```

4. Aktualizujte instalační/odinstalační skripty vaší aplikace, aby používaly nové rozhraní příkazového řádku Service Fabric, a to pomocí kroků uvedených [tady](service-fabric-application-lifecycle-sfctl.md). Pro srovnání si můžete prohlédnout naše úvodní [příklady](https://github.com/Azure-Samples/service-fabric-java-getting-started).

>[!TIP]
> Po odinstalaci Service Fabric Java SDK nebude Yeoman fungovat. Pokud chcete zprovoznit generátor šablon Service Fabric Yeoman Java, postupujte v souladu s požadavky uvedenými [tady](service-fabric-create-your-first-linux-application-with-java.md).

## <a name="service-fabric-java-libraries-on-maven"></a>Knihovny Service Fabric Java v Mavenu

Hostitelem knihoven Service Fabric Java je Maven. Můžete přidat závislosti do souborů ``pom.xml`` nebo ``build.gradle`` vašich projektů, aby se používaly knihovny Service Fabric Java z úložiště **mavenCentral**.

### <a name="actors"></a>Objekty actor

Podpora Service Fabric Reliable Actor pro vaši aplikaci.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-actors</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-actors:1.0.0'
  }
  ```

### <a name="services"></a>Služby

Podpora bezstavové služby Service Fabric pro vaši aplikaci.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-services</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-services:1.0.0'
  }
  ```

### <a name="others"></a>Ostatní

#### <a name="transport"></a>Přenos

Podpora přenosové vrstvy pro aplikace Service Fabric Java. Pokud neprogramujete na úrovni přenosové vrstvy, nemusíte tuto závislost do aplikace Reliable Actor nebo aplikace služby explicitně přidávat.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-transport</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-transport:1.0.0'
  }
  ```

#### <a name="fabric-support"></a>Podpora prostředků infrastruktury

Podpora na úrovni systému pro Service Fabric, která komunikuje s modulem runtime nativním pro Service Fabric. Tuto závislost nemusíte do aplikace Reliable Actor nebo aplikace služby explicitně přidávat. Načte se z Mavenu automaticky, jakmile zahrnete ostatní závislosti uvedené výše.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf:1.0.0'
  }
  ```

## <a name="migrating-service-fabric-stateless-service"></a>Migrace bezstavové služby Service Fabric

Pokud chcete mít možnost sestavovat stávající bezstavové služby Service Fabric v Javě s využitím závislostí Service Fabric načtených z Mavenu, je potřeba v rámci příslušné služby aktualizovat soubor ``build.gradle``. Dříve vypadal takto:

```gradle
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
    compile project(':Interface')
}
.
.
.
jar {
    manifest {
    attributes(
                'Main-Class': 'statelessservice.MyStatelessServiceHost',
                "Class-Path": configurations.compile.collect { 'lib/' + it.getName() }.join(' '))
    baseName "MyStateless"
    destinationDir = file('./../MyStatelessApplication/MyStatelessPkg/Code')
}
.
.
.
task copyDeps <<{
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../MyStatelessApplication/MyStatelessPkg/Code/lib")
        include('*.jar')
    }
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../MyStatelessApplication/MyStatelessPkg/Code/lib")
        include('libj*.so')
    }
}
```

Teď by měl **aktualizovaný soubor** ``build.gradle`` pro načtení závislostí z Mavenu mít příslušné části změněné takto:

```gradle
repositories {
        mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    compile project(':Interface')
    azuresf ('com.microsoft.servicefabric:sf-services:1.0.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
.
jar {
    manifest {
        def mpath = configurations.compile.collect {'lib/'+it.getName()}.join (' ')
        mpath = mpath + ' ' + configurations.azuresf.collect {'lib/'+it.getName()}.join (' ')
        attributes(
                'Main-Class': 'statelessservice.MyStatelessServiceHost',
                "Class-Path": mpath)
    baseName "MyStateless"
    destinationDir = file('./../MyStatelessApplication/MyStatelessPkg/Code')
   }
}
.
.
.
task copyDeps <<{
    copy {
        from("lib/")
        into("./../MyStatelessApplication/MyStatelessPkg/Code/lib")
        include('*')
    }
}
```

Obecně platí, že k získání celkové představy o tom, jak bude vypadat skript sestavení pro bezstavovou službu Service Fabric v Javě, můžete využít libovolnou ukázku z našich úvodních příkladů. Tady je [build.gradle](https://github.com/Azure-Samples/service-fabric-java-getting-started/blob/master/reliable-services-actor-sample/build.gradle) pro ukázku EchoServer.

## <a name="migrating-service-fabric-actor-service"></a>Migrace Service Fabric Actor Service

Pokud chcete mít možnost sestavovat stávající aplikaci Service Fabric Actor v Javě s využitím závislostí Service Fabric načtených z Mavenu, je potřeba v rámci balíčku rozhraní a balíčku příslušné služby aktualizovat soubor ``build.gradle``. Pokud máte balíček TestClient, musíte ho také aktualizovat. Takže pro objekt actor ``Myactor``, je potřeba aktualizovat tato místa:

```
./Myactor/build.gradle
./MyactorInterface/build.gradle
./MyactorTestClient/build.gradle
```

#### <a name="updating-build-script-for-the-interface-project"></a>Aktualizace skriptu sestavení pro projekt rozhraní

Dříve vypadal takto:

```gradle
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
}
.
.
```

Teď by měl **aktualizovaný soubor** ``build.gradle`` pro načtení závislostí z Mavenu mít příslušné části změněné takto:

```gradle
repositories {
    mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    azuresf ('com.microsoft.servicefabric:sf-actors:1.0.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
```

#### <a name="updating-build-script-for-the-actor-project"></a>Aktualizace skriptu sestavení pro projekt objektu actor

Dříve vypadal takto:

```gradle
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
    compile project(':MyactorInterface')
}
.
.
.
jar {
    manifest {
    attributes(
                'Main-Class': 'reliableactor.MyactorHost',
                "Class-Path": configurations.compile.collect { 'lib/' + it.getName() }.join(' '))
      baseName "myactor"
    destinationDir = file('./../myjavaapp/MyactorPkg/Code')
    }
}
.
.
.
task copyDeps<< {
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../myjavaapp/MyactorPkg/Code/lib")
        include('*.jar')
    }
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../myjavaapp/MyactorPkg/Code/lib")
        include('libj*.so')
    }
    copy {
        from("../MyactorInterface/out/lib")
        into("./../myjavaapp/MyactorPkg/Code/lib")
        include('*.jar')
    }
}
```

Teď by měl **aktualizovaný soubor** ``build.gradle`` pro načtení závislostí z Mavenu mít příslušné části změněné takto:

```gradle
repositories {
    mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    compile project(':MyactorInterface')
    azuresf ('com.microsoft.servicefabric:sf-actors:1.0.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
.
jar {
    manifest {
        def mpath = configurations.compile.collect {'lib/'+it.getName()}.join (' ')
        mpath = mpath + ' ' + configurations.azuresf.collect {'lib/'+it.getName()}.join (' ')
        attributes(
                'Main-Class': 'reliableactor.MyactorHost',
                "Class-Path": mpath)
    baseName "myactor"
    destinationDir = file('../myjavaapp/MyactorPkg/Code')}
 }
.
.
.
task copyDeps<< {
      copy {
              from("lib/")
              into("../myjavaapp/MyactorPkg/Code/lib")
              include('*')
      }
      copy {
              from("../MyactorInterface/out/lib")
              into("../myjavaapp/MyactorPkg/Code/lib")
              include('*.jar')
      }
}
```

#### <a name="updating-build-script-for-the-test-client-project"></a>Aktualizace skriptu sestavení pro testovací klientský projekt

Změny jsou v tomto případě podobné změnám probraným v předchozí části, to znamená u projektu objektu actor. Dříve skript Gradle vypadal takto:

```gradle
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
    compile project(':MyactorInterface')
}
.
.
.
jar
{
    manifest {
    attributes(
        'Main-Class': 'reliableactor.test.MyactorTestClient',
        "Class-Path": configurations.compile.collect { 'lib/' + it.getName() }.join(' '))
    }
    baseName "myactor-test"
    destinationDir = file('out/lib')
}
.
.
.
task copyDeps<< {
        copy {
                from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
                into("./out/lib/lib")
                include('*.jar')
        }
        copy {
                from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
                into("./out/lib/lib")
                include('libj*.so')
        }
        copy {
                from("../MyactorInterface/out/lib")
                into("./out/lib/lib")
                include('*.jar')
        }
}
```

Teď by měl **aktualizovaný soubor** ``build.gradle`` pro načtení závislostí z Mavenu mít příslušné části změněné takto:

```gradle
repositories {
    mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    compile project(':MyactorInterface')
    azuresf ('com.microsoft.servicefabric:sf-actors:1.0.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
.
jar
{
    manifest {
        def mpath = configurations.compile.collect {'lib/'+it.getName()}.join (' ')
        mpath = mpath + ' ' + configurations.azuresf.collect {'lib/'+it.getName()}.join (' ')
    attributes(
                'Main-Class': 'reliableactor.test.MyactorTestClient',
                "Class-Path": mpath)
    baseName "myactor-test"
    destinationDir = file('./out/lib')
        }
}
.
.
.
task copyDeps<< {
        copy {
                from("lib/")
                into("./out/lib/lib")
                include('*')
        }
        copy {
                from("../MyactorInterface/out/lib")
                into("./out/lib/lib")
                include('*.jar')
        }
}
```

## <a name="next-steps"></a>Další kroky

* [Vytvoření a nasazení první aplikace Service Fabric v Javě v Linuxu pomocí Yeomana](service-fabric-create-your-first-linux-application-with-java.md)
* [Vytvoření a nasazení první aplikace Service Fabric v Javě v Linuxu pomocí modulu plug-in Service Fabric pro Eclipse](service-fabric-get-started-eclipse.md)
* [Komunikace s clustery Service Fabric pomocí rozhraní příkazového řádku Service Fabric](service-fabric-cli.md)
