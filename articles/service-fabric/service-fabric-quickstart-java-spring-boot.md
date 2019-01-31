---
title: Vytvoření aplikace Spring Boot na platformě Service Fabric v Azure | Microsoft Docs
description: V tomto rychlém startu nasadíte aplikaci Spring Boot pro Azure Service Fabric s využitím ukázkové aplikace Spring Boot.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/29/2019
ms.author: suhuruli
ms.custom: mvc, devcenter
ms.openlocfilehash: e4fde75aeaf86219518daf92b67434fe9fd63f86
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55297393"
---
# <a name="quickstart-deploy-a-java-spring-boot-application-to-service-fabric"></a>Rychlý start: Nasazení aplikace Java Spring Boot do Service Fabric

Azure Service Fabric je platforma distribuovaných systémů pro nasazování a správu mikroslužeb a kontejnerů.

Tento rychlý start ukazuje, jak nasadit aplikaci Spring Boot do Service Fabric. Tento rychlý start využívá ukázku [Getting Started](https://spring.io/guides/gs/spring-boot/) z webu Spring. Pomocí známých nástrojů příkazového řádku vás tento rychlý start provede nasazením ukázky Spring Boot jako aplikace Service Fabric. Po dokončení budete mít funkční aplikaci Spring Boot Getting Started v Service Fabric.

![Snímek obrazovky aplikace](./media/service-fabric-quickstart-java-spring-boot/springbootsflocalhost.png)

V tomto rychlém startu se naučíte:

* Nasazení aplikace Spring Boot do služby Service Fabric
* Nasazení aplikace do místního clusteru
* Škálování aplikace na více instancí napříč několika uzly
* Převzetí služeb při selhání vaší služby bez omezení dostupnosti

## <a name="prerequisites"></a>Požadavky

K provedení kroků v tomto kurzu Rychlý start je potřeba:

1. Nainstalovat sadu Service Fabric SDK a rozhraní příkazového řádku (CLI) Service Fabric

    a. [Mac](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli#cli-mac)
    
    b. [Linux](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)

1. [Nainstalovat Git](https://git-scm.com/).
1. Nainstalovat Yeoman

    a. [Mac](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-mac#create-your-application-on-your-mac-by-using-yeoman)

    b. [Linux](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-yeoman-generators-for-containers-and-guest-executables)
1. Nastavit prostředí Java

    a. [Mac](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-mac#create-your-application-on-your-mac-by-using-yeoman)
    
    b.  [Linux](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development)

## <a name="download-the-sample"></a>Stažení ukázky

V okně terminálu naklonujte spuštěním následujícího příkazu ukázkovou aplikaci Spring Boot Getting Started do místního počítače.

```bash
git clone https://github.com/spring-guides/gs-spring-boot.git
```

## <a name="build-the-spring-boot-application"></a>Sestavení aplikace Spring Boot 
1. V adresáři `gs-spring-boot/complete` spusťte následující příkaz, který sestaví aplikaci. 

    ```bash
    ./gradlew build
    ``` 

## <a name="package-the-spring-boot-application"></a>Zabalení aplikace Spring Boot 
1. Uvnitř adresáře `gs-spring-boot` ve vašem klonu spusťte příkaz `yo azuresfguest`. 

1. Pro jednotlivé výzvy zadejte následující informace.

    ![Položky Yeomanu](./media/service-fabric-quickstart-java-spring-boot/yeomanspringboot.png)

1. Ve složce `SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/code` vytvořte soubor nazvaný `entryPoint.sh`. Do souboru `entryPoint.sh` přidejte následující kód. 

    ```bash
    #!/bin/bash
    BASEDIR=$(dirname $0)
    cd $BASEDIR
    java -jar gs-spring-boot-0.1.0.jar
    ```

1. Do souboru `gs-spring-boot/SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/ServiceManifest.xml` přidejte prostředek **Endpoints**.

    ```xml 
        <Resources>
          <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
          </Endpoints>
       </Resources>
    ```

    Soubor **ServiceManifest.xml** teď vypadá takto: 

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ServiceManifest Name="SpringGettingStartedPkg" Version="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" >

       <ServiceTypes>
          <StatelessServiceType ServiceTypeName="SpringGettingStartedType" UseImplicitHost="true">
       </StatelessServiceType>
       </ServiceTypes>

       <CodePackage Name="code" Version="1.0.0">
          <EntryPoint>
             <ExeHost>
                <Program>entryPoint.sh</Program>
                <Arguments></Arguments>
                <WorkingFolder>CodePackage</WorkingFolder>
             </ExeHost>
          </EntryPoint>
       </CodePackage>
        <Resources>
          <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
          </Endpoints>
       </Resources>
     </ServiceManifest>
    ```

V této fázi jste vytvořili aplikaci Service Fabric pro ukázku Spring Boot Getting Started, kterou můžete nasadit do Service Fabric.

## <a name="run-the-application-locally"></a>Místní spuštění aplikace

1. Zadáním následujícího příkazu spusťte místní cluster na počítačích Ubuntu:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

    Pokud používáte počítač Mac, spusťte místní cluster z image Dockeru (za předpokladu, že jste místní cluster pro Mac nastavili podle [požadavků](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-mac#create-a-local-container-and-set-up-service-fabric)). 

    ```bash
    docker run --name sftestcluster -d -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 -p 8080:8080 mysfcluster
    ```

    Spuštění místního clusteru nějakou dobu trvá. Pokud chcete potvrdit, že je cluster plně funkční, přejděte do Service Fabric Exploreru na adrese **http://localhost:19080**. Pět uzlů v pořádku značí, že je místní cluster zprovozněný. 
    
    ![Místní cluster v pořádku](./media/service-fabric-quickstart-java-spring-boot/sfxlocalhost.png)

1. Přejděte do složky `gs-spring-boot/SpringServiceFabric`.
1. Spusťte následující příkaz pro připojení k místnímu clusteru.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```
1. Spusťte skript `install.sh`.

    ```bash
    ./install.sh
    ```

1. Otevřete oblíbený webový prohlížeč a přejděte do aplikace na adrese **http://localhost:8080**.

    ![Front-end aplikace – místní prostředí](./media/service-fabric-quickstart-java-spring-boot/springbootsflocalhost.png)

Teď máte přístup k aplikaci Spring Boot, která je nasazená do clusteru Service Fabric.

## <a name="scale-applications-and-services-in-a-cluster"></a>Škálování aplikací a služeb v clusteru

Služby je možné škálovat napříč clusterem a vyřešit tak změny v jejich zatížení. Služby se škálují změnou počtu instancí spuštěných v clusteru. Služby můžete škálovat mnoha způsoby – můžete použít například skripty nebo příkazy v Service Fabric CLI (sfctl). V následujících krocích se používá Service Fabric Explorer.

Service Fabric Explorer běží na všech clusterech Service Fabric a je přístupný z prohlížeče po přechodu na port HTTP pro správu clusteru (19080), například `http://localhost:19080`.

Pokud chcete škálovat webovou front-end službu, postupujte následovně:

1. Otevřete ve vašem clusteru Service Fabric Explorer – například `http://localhost:19080`.
1. Ve stromovém zobrazení klikněte na tři tečky vedle uzlu **fabric:/SpringServiceFabric/SpringGettingStarted** a zvolte **Škálovat službu**.

    ![Service Fabric Explorer – škálování služby](./media/service-fabric-quickstart-java-spring-boot/sfxscaleservicehowto.png)

    Nyní můžete škálovat počet instancí služby.

1. Změňte počet na **3** a klikněte na **Škálovat službu**.

    Jako alternativu ke škálování služby pomocí příkazového řádku můžete použít následující postup.

    ```bash
    # Connect to your local cluster
    sfctl cluster select --endpoint https://<ConnectionIPOrURL>:19080 --pem <path_to_certificate> --no-verify

    # Run Bash command to scale instance count for your service
    sfctl service update --service-id 'SpringServiceFabric~SpringGettingStarted' --instance-count 3 --stateless 
    ``` 

1. Ve stromovém zobrazení klikněte na uzel **fabric:/SpringServiceFabric/SpringGettingStarted** a rozbalte uzel oddílu (reprezentovaný identifikátorem GUID).

    ![Service Fabric Explorer – dokončení škálování služby](./media/service-fabric-quickstart-java-spring-boot/sfxscaledservice.png)

    Služba má tři instance a stromové zobrazení ukazuje, na kterých uzlech jsou tyto instance spuštěné.

Touto jednoduchou úlohou správy jste zdvojnásobili prostředky, které má front-end služba k dispozici pro zpracování uživatelské zátěže. Je důležité si uvědomit, že pro spolehlivý provoz služby nepotřebujete více jejích instancí. Pokud služba selže, Service Fabric zajistí v clusteru spuštění nové instance služby.

## <a name="fail-over-services-in-a-cluster"></a>Převzetí služeb při selhání v clusteru

Jako ukázku převzetí služeb při selhání simulujeme restartování uzlu pomocí Service Fabric Exploreru. Zkontrolujte, že je spuštěná jenom jedna instance vaší služby.

1. Otevřete ve vašem clusteru Service Fabric Explorer – například `http://localhost:19080`.
1. Klikněte na tři tečky vedle uzlu, na kterém je spuštěná instance vaší služby, a restartujte uzel.

    ![Service Fabric Explorer – restartování uzlu](./media/service-fabric-quickstart-java-spring-boot/sfxhowtofailover.png)
1. Instance vaší služby se přesune na jiný uzel a vaše aplikace nebude mít žádný výpadek.

    ![Service Fabric Explorer – úspěšné restartování uzlu](./media/service-fabric-quickstart-java-spring-boot/sfxfailedover.png)

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste se naučili:

* Nasazení aplikace Spring Boot do služby Service Fabric
* Nasazení aplikace do místního clusteru
* Škálování aplikace na více instancí napříč několika uzly
* Převzetí služeb při selhání vaší služby bez omezení dostupnosti

Další informace o práci s aplikacemi v Javě v Service Fabric najdete v kurzu věnovaném aplikacím v Javě.

> [!div class="nextstepaction"]
> [Nasazení aplikace v Javě](./service-fabric-tutorial-create-java-app.md)
