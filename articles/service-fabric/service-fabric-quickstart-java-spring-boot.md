---
title: 'Úvodní příručka: Vytvoření aplikace pro jarní spuštění v Azure Service Fabric'
description: V tomto rychlém startu nasadíte aplikaci Spring Boot pro Azure Service Fabric s využitím ukázkové aplikace Spring Boot.
author: suhuruli
ms.topic: quickstart
ms.date: 01/29/2019
ms.author: suhuruli
ms.custom: mvc, devcenter, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: eb96989b4a2731e78471b848d690b48352408d1c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77121482"
---
# <a name="quickstart-deploy-a-java-spring-boot-app-on-azure-service-fabric"></a>Úvodní příručka: Nasazení aplikace Java Spring Boot na Azure Service Fabric

V tomto rychlém startu nasadíte java jarní spuštění aplikace azure service fabric pomocí známých nástrojů příkazového řádku na Linuxu nebo MacOS. Azure Service Fabric je platforma distribuovaných systémů pro nasazování a správu mikroslužeb a kontejnerů. 

## <a name="prerequisites"></a>Požadavky

#### <a name="linux"></a>[Linux](#tab/linux)

- [Java prostředí](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development) a [Pojeoman](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-yeoman-generators-for-containers-and-guest-executables)
- [Service Fabric SDK & rozhraní příkazového řádku service fabric (CLI)](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)
- [Git](https://git-scm.com/downloads)

#### <a name="macos"></a>[Macos](#tab/macos)

- [Java prostředí a Pojeoman](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-mac#create-your-application-on-your-mac-by-using-yeoman)
- [Service Fabric SDK & rozhraní příkazového řádku service fabric (CLI)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli#cli-mac)
- [Git](https://git-scm.com/downloads)

--- 

## <a name="download-the-sample"></a>Stažení ukázky

V okně terminálu spusťte následující příkaz a naklonujte ukázkovou aplikaci [Začínáme](https://github.com/spring-guides/gs-spring-boot) na jaře do místního počítače.

```bash
git clone https://github.com/spring-guides/gs-spring-boot.git
```

## <a name="build-the-spring-boot-application"></a>Sestavení aplikace Spring Boot 
Uvnitř *adresáře gs-spring-boot/complete* spusťte níže uvedený příkaz pro sestavení aplikace 

```bash
./gradlew build
``` 

## <a name="package-the-spring-boot-application"></a>Zabalení aplikace Spring Boot 
1. Uvnitř *adresáře gs-spring-boot* ve vašem `yo azuresfguest` klonu spusťte příkaz. 

1. Pro jednotlivé výzvy zadejte následující informace.

    ![Jarní Boot Yeoman položky](./media/service-fabric-quickstart-java-spring-boot/yeoman-entries-spring-boot.png)

1. Ve složce *SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/code* vytvořte soubor s názvem *entryPoint.sh*. Do souboru *entryPoint.sh* přidejte následující kód. 

    ```bash
    #!/bin/bash
    BASEDIR=$(dirname $0)
    cd $BASEDIR
    java -jar gs-spring-boot-0.1.0.jar
    ```

1. Přidání prostředku **koncových bodů** do souboru *gs-spring-boot/SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/ServiceManifest.xml*

    ```xml 
        <Resources>
          <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
          </Endpoints>
       </Resources>
    ```

    Soubor *ServiceManifest.xml* teď vypadá takto: 

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ServiceManifest Name="SpringGettingStartedPkg" Version="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" >

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

    Spuštění místního clusteru nějakou dobu trvá. Pokud chcete potvrdit, že je cluster plně funkční, přejděte do Service Fabric Exploreru na adrese `http://localhost:19080`. Pět uzlů v pořádku značí, že je místní cluster zprovozněný. 
    
    ![Průzkumník service fabric zobrazuje uzly v pořádku](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-healthy-nodes.png)

1. Otevřete složku *gs-spring-boot/SpringServiceFabric.*
1. Spusťte následující příkaz pro připojení k místnímu clusteru.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```
1. Spusťte *skript install.sh.*

    ```bash
    ./install.sh
    ```

1. Otevřete oblíbený webový prohlížeč a přejděte do aplikace na adrese `http://localhost:8080`.

    ![Ukázka struktury jarní spouštění](./media/service-fabric-quickstart-java-spring-boot/spring-boot-service-fabric-sample.png)

Teď máte přístup k aplikaci Spring Boot, která je nasazená do clusteru Service Fabric.

Další informace naleznete v [ukázce Začínáme](https://spring.io/guides/gs/spring-boot/) na jarním webu.

## <a name="scale-applications-and-services-in-a-cluster"></a>Škálování aplikací a služeb v clusteru

Služby je možné škálovat napříč clusterem a vyřešit tak změny v jejich zatížení. Služby se škálují změnou počtu instancí spuštěných v clusteru. Služby můžete škálovat mnoha způsoby – můžete použít například skripty nebo příkazy v Service Fabric CLI (sfctl). V následujících krocích se používá Service Fabric Explorer.

Service Fabric Explorer běží na všech clusterech Service Fabric a je přístupný z prohlížeče po přechodu na port HTTP pro správu clusteru (19080), například `http://localhost:19080`.

Pokud chcete škálovat webovou front-end službu, postupujte následovně:

1. Otevřete ve vašem clusteru Service Fabric Explorer – například `http://localhost:19080`.
1. Vyberte tři tečky (**...**) vedle **uzlu fabric:/SpringServiceFabric/SpringGettingStarted** v treeview a vyberte **měřítko služby**.

    ![Ukázka škálovací služby Service Fabric Explorer](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-scale-sample.png)

    Nyní můžete škálovat počet instancí služby.

1. Změňte číslo na **3** a vyberte **Škálovat službu**.

    Jako alternativu ke škálování služby pomocí příkazového řádku můžete použít následující postup.

    ```bash
    # Connect to your local cluster
    sfctl cluster select --endpoint https://<ConnectionIPOrURL>:19080 --pem <path_to_certificate> --no-verify

    # Run Bash command to scale instance count for your service
    sfctl service update --service-id 'SpringServiceFabric~SpringGettingStarted' --instance-count 3 --stateless 
    ``` 

1. Vyberte **prostředek infrastruktury:/SpringServiceFabric/SpringGettingStarted** uzlu v zobrazení stromu a rozbalte uzel oddílu (reprezentovaný identifikátorem GUID).

    ![Služba škálování Service Fabric Explorer byla dokončena](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-partition-node.png)

    Služba má tři instance a stromové zobrazení ukazuje, na kterých uzlech jsou tyto instance spuštěné.

Touto jednoduchou úlohou správy jste zdvojnásobili prostředky, které má front-end služba k dispozici pro zpracování uživatelské zátěže. Je důležité si uvědomit, že pro spolehlivý provoz služby nepotřebujete více jejích instancí. Pokud služba selže, Service Fabric zajistí v clusteru spuštění nové instance služby.

## <a name="fail-over-services-in-a-cluster"></a>Převzetí služeb při selhání v clusteru

Jako ukázku převzetí služeb při selhání simulujeme restartování uzlu pomocí Service Fabric Exploreru. Zkontrolujte, že je spuštěná jenom jedna instance vaší služby.

1. Otevřete ve vašem clusteru Service Fabric Explorer – například `http://localhost:19080`.
1. Vyberte tři tečky (**...**) vedle uzlu, ve které je spuštěna instance služby, a restartujte uzel.

    ![Uzel restartování aplikace Service Fabric Explorer](./media/service-fabric-quickstart-java-spring-boot/service=fabric-explorer-restart=node.png)
1. Instance vaší služby se přesune na jiný uzel a vaše aplikace nebude mít žádný výpadek.

    ![Aplikace Service Fabric Explorer restartovala uzel úspěšného restartování](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-service-moved.png)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili:

* Nasazení aplikace Spring Boot do služby Service Fabric
* Nasazení aplikace do místního clusteru
* Škálování aplikace na více instancí napříč několika uzly
* Převzetí služeb při selhání vaší služby bez omezení dostupnosti

Další informace o práci s aplikacemi v Javě v Service Fabric najdete v kurzu věnovaném aplikacím v Javě.

> [!div class="nextstepaction"]
> [Nasazení aplikace v Javě](./service-fabric-tutorial-create-java-app.md)
