---
title: 'Rychlý Start: Vytvoření aplikace Pružiné spuštění v Azure Service Fabric'
description: V tomto rychlém startu nasadíte aplikaci pro spouštění pružiny pro Azure Service Fabric s využitím ukázkové aplikace pro jarní spuštění.
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
ms.custom: mvc, devcenter, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 4ec6addb348b8c13f124ec225e056d2003a93c38
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703520"
---
# <a name="quickstart-deploy-a-java-spring-boot-app-on-azure-service-fabric"></a>Rychlý Start: nasazení aplikace v jazyce Java pružiny do Azure Service Fabric

V tomto rychlém startu se dozvíte, jak nasadit aplikaci v jazyce Java pružiny do Azure Service Fabric. Azure Service Fabric je platforma distribuovaných systémů pro nasazování a správu mikroslužeb a kontejnerů. 

V tomto rychlém startu se používá ukázka [Začínáme](https://spring.io/guides/gs/spring-boot/) z webu pružiny. Pomocí známých nástrojů příkazového řádku vás tento rychlý Start provede nasazením ukázky pružinového spuštění jako aplikace Service Fabric. Až budete hotovi, budete mít Začínáme ukázka, jak pracovat na Service Fabric.

![Ukázka jarního spuštění Service Fabric](./media/service-fabric-quickstart-java-spring-boot/spring-boot-service-fabric-sample.png)

V tomto rychlém startu se dozvíte, jak:

* Nasazení aplikace Pružiného spouštění do Service Fabric
* Nasazení aplikace do místního clusteru
* Horizontální navýšení kapacity aplikace napříč více uzly
* Provedení převzetí služeb při selhání vaší služby bez zásahu k dostupnosti

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu:

1. Instalace sady Service Fabric SDK & Service Fabric rozhraní příkazového řádku (CLI)

    a. [Mac](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli#cli-mac)
    
    b. [Linux](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)

1. [Nainstalovat git](https://git-scm.com/)
1. Nainstalovat Yeoman

    a. [Mac](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-mac#create-your-application-on-your-mac-by-using-yeoman)

    b. [Linux](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-yeoman-generators-for-containers-and-guest-executables)
1. Nastavení prostředí Java

    a. [Mac](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-mac#create-your-application-on-your-mac-by-using-yeoman)
    
    b.  [Linux](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development)

## <a name="download-the-sample"></a>Stažení ukázky

V okně terminálu pomocí následujícího příkazu naklonujte ukázkovou aplikaci pro pružinové spouštění Začínáme do místního počítače.

```bash
git clone https://github.com/spring-guides/gs-spring-boot.git
```

## <a name="build-the-spring-boot-application"></a>Sestavení aplikace pružinového spuštění 
1. V adresáři `gs-spring-boot/complete` spusťte následující příkaz, který sestaví aplikaci. 

    ```bash
    ./gradlew build
    ``` 

## <a name="package-the-spring-boot-application"></a>Zabalení aplikace pružinového spuštění 
1. V adresáři `gs-spring-boot` v rámci klonování spusťte příkaz `yo azuresfguest`. 

1. Pro každou výzvu zadejte následující podrobnosti.

    ![Yeoman položky spouštěcího spouštění](./media/service-fabric-quickstart-java-spring-boot/yeoman-entries-spring-boot.png)

1. Ve složce `SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/code` vytvořte soubor s názvem `entryPoint.sh`. Do souboru `entryPoint.sh` přidejte následující. 

    ```bash
    #!/bin/bash
    BASEDIR=$(dirname $0)
    cd $BASEDIR
    java -jar gs-spring-boot-0.1.0.jar
    ```

1. Přidat prostředek **koncových bodů** do souboru *GS-Spring-Boot/SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/ServiceManifest. XML*

    ```xml 
        <Resources>
          <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
          </Endpoints>
       </Resources>
    ```

    **ServiceManifest. XML** teď vypadá takto: 

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

V této fázi jste vytvořili Service Fabricovou aplikaci pro ukázku Začínáme pro pružinové spouštění, kterou můžete nasadit do Service Fabric.

## <a name="run-the-application-locally"></a>Spustit aplikaci místně

1. Spusťte místní cluster na počítačích s Ubuntu spuštěním následujícího příkazu:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

    Pokud používáte Mac, spusťte místní cluster z image Docker (za předpokladu, že jste postupovali podle [požadavků](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-mac#create-a-local-container-and-set-up-service-fabric) pro nastavení místního clusteru pro Mac). 

    ```bash
    docker run --name sftestcluster -d -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 -p 8080:8080 mysfcluster
    ```

    Spuštění místního clusteru trvá nějakou dobu. Pokud chcete potvrdit, že je cluster plně zapnutý, přejděte k Service Fabric Explorer v **http://localhost:19080** . Pět uzlů v pořádku značí, že místní cluster je spuštěný. 
    
    ![Service Fabric Explorer zobrazuje uzly v pořádku](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-healthy-nodes.png)

1. Otevřete složku `gs-spring-boot/SpringServiceFabric`.
1. Spusťte následující příkaz pro připojení k místnímu clusteru.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```
1. Spusťte skript `install.sh`.

    ```bash
    ./install.sh
    ```

1. Otevřete oblíbený webový prohlížeč a přejděte do aplikace pomocí přístupu `http://localhost:8080`.

    ![Ukázka jarního spuštění Service Fabric](./media/service-fabric-quickstart-java-spring-boot/spring-boot-service-fabric-sample.png)

Nyní máte přístup k aplikaci pro spouštění pružiny, která byla nasazena do clusteru Service Fabric.

## <a name="scale-applications-and-services-in-a-cluster"></a>Škálování aplikací a služeb v clusteru

Služby je možné škálovat napříč clusterem tak, aby vyhovovaly změnám zatížení služeb. Škálujte službu změnou počtu instancí spuštěných v clusteru. Vaše služby můžete škálovat mnoha způsoby, například můžete použít skripty nebo příkazy z Service Fabric CLI (sfctl). Následující postup použijte Service Fabric Explorer.

Service Fabric Explorer běží ve všech clusterech Service Fabric a dá se k nim dostat z prohlížeče tak, že přejdete na port pro správu HTTP clusteru (19080); například `http://localhost:19080`.

Pokud chcete škálovat webovou front-end službu, udělejte toto:

1. Otevřete Service Fabric Explorer v clusteru, například `http://localhost:19080`.
1. V ovládacím prvku TreeView vyberte tři tečky ( **...** ) vedle uzlu **Fabric:/SpringServiceFabric/SpringGettingStarted** a vyberte **škálovat službu**.

    ![Ukázka služby Service Fabric Explorer Scale](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-scale-sample.png)

    Nyní se můžete rozhodnout pro škálování počtu instancí služby.

1. Změňte číslo na **3** a vyberte **škálovat službu**.

    Alternativním způsobem, jak službu škálovat pomocí příkazového řádku, je následující.

    ```bash
    # Connect to your local cluster
    sfctl cluster select --endpoint https://<ConnectionIPOrURL>:19080 --pem <path_to_certificate> --no-verify

    # Run Bash command to scale instance count for your service
    sfctl service update --service-id 'SpringServiceFabric~SpringGettingStarted' --instance-count 3 --stateless 
    ``` 

1. Ve stromovém zobrazení vyberte uzel **Fabric:/SpringServiceFabric/SpringGettingStarted** a rozbalte uzel oddílu (REPREZENTOVANý identifikátorem GUID).

    ![Služba škálování Service Fabric Explorer dokončena](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-partition-node.png)

    Služba má tři instance a stromové zobrazení ukazuje, na kterých uzlech jsou instance spuštěné.

Prostřednictvím této jednoduché úlohy správy jste poklepali prostředky, které jsou k dispozici pro front-end službu ke zpracování uživatelského zatížení. Je důležité pochopit, že pro spolehlivou práci nepotřebujete více instancí služby. Pokud se služba nezdařila, Service Fabric zajistěte, aby se v clusteru spouštěla nová instance služby.

## <a name="fail-over-services-in-a-cluster"></a>Převzetí služeb při selhání v clusteru

Aby bylo možné předvést převzetí služeb při selhání, je při restartování uzlu simulována pomocí Service Fabric Explorer. Zajistěte, aby byla spuštěná jenom jedna instance služby.

1. Otevřete Service Fabric Explorer v clusteru, například `http://localhost:19080`.
1. Vyberte tři tečky ( **...** ) vedle uzlu, na kterém je spuštěná instance služby, a restartujte uzel.

    ![Service Fabric Explorer restartovat uzel](./media/service-fabric-quickstart-java-spring-boot/service=fabric-explorer-restart=node.png)
1. Instance vaší služby se přesune na jiný uzel a vaše aplikace nebude mít žádný výpadek.

    ![Service Fabric Explorer restartování uzlu bylo úspěšné.](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-service-moved.png)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se dozvěděli, jak:

* Nasazení aplikace Pružiného spouštění do Service Fabric
* Nasazení aplikace do místního clusteru
* Horizontální navýšení kapacity aplikace napříč více uzly
* Provedení převzetí služeb při selhání vaší služby bez zásahu k dostupnosti

Další informace o práci s aplikacemi Java v Service Fabric najdete v kurzu pro aplikace Java.

> [!div class="nextstepaction"]
> [Nasazení aplikace Java](./service-fabric-tutorial-create-java-app.md)
