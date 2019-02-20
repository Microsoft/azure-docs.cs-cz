---
title: Nastavení vývojového prostředí v Mac OS X pro práci s Azure Service Fabric | Dokumentace Microsoftu
description: Nainstalujte modul runtime, sadu SDK a nástroje a vytvořte místní vývojový cluster. Po dokončení této instalace a nastavení budete moci sestavovat aplikace v Mac OS X.
services: service-fabric
documentationcenter: linux
author: suhuruli
manager: timlt
editor: ''
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: linux
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/17/2017
ms.author: suhuruli
ms.openlocfilehash: 4dac40e9fad8361c0e6c8a8758028743f2506f56
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2019
ms.locfileid: "56428087"
---
# <a name="set-up-your-development-environment-on-mac-os-x"></a>Nastavení vývojového prostředí v Mac OS X
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

Pomocí Mac OS X můžete sestavit aplikace Azure Service Fabric, které poběží na clusterech s Linuxem. Tento dokument popisuje nastavení počítače Mac pro vývoj.

## <a name="prerequisites"></a>Požadavky
Azure Service Fabric nefunguje v Mac OS X nativně. Pro spuštění místního clusteru Service Fabric se poskytuje předkonfigurovaná image kontejneru Dockeru. Než začnete, budete potřebovat:

* Minimálně 4 GB RAM
* Nejnovější verzi [Dockeru](https://www.docker.com/)

>[!TIP]
>
>Pokud chcete nainstalovat Docker na svém počítači Mac, postupujte podle kroků v [dokumentaci k Dockeru](https://docs.docker.com/docker-for-mac/install/#what-to-know-before-you-install). Po dokončení [instalaci ověřte](https://docs.docker.com/docker-for-mac/#check-versions-of-docker-engine-compose-and-machine).
>

## <a name="create-a-local-container-and-set-up-service-fabric"></a>Vytvoření místního kontejneru a nastavení Service Fabric
Pokud chcete nastavit místní kontejner Dockeru a mít v něm spuštěný cluster Service Fabric, proveďte následující kroky:

1. Aktualizujte konfiguraci démona Dockeru na hostiteli pomocí následujícího nastavení a potom démon Dockeru restartujte: 

    ```json
    {
        "ipv6": true,
        "fixed-cidr-v6": "fd00::/64"
    }
    ```
    Toto nastavení můžete aktualizovat přímo v souboru daemon.json v instalační cestě Dockeru. Můžete přímo upravit nastavení konfigurace démona v Dockeru. Vyberte **ikonu Docker** a potom vyberte **Předvolby** > **Démon** > **Upřesnit**.
    
    >[!NOTE]
    >
    >Úprava démona v Dockeru přímo je doporučené, protože umístění souboru daemon.json se může lišit počítač od počítače. Například ~/Library/Containers/com.docker.docker/Data/database/com.docker.driver.amd64-linux/etc/docker/daemon.json.
    >

    >[!TIP]
    >Při testování rozsáhlých aplikací doporučujeme zvýšit množství prostředků přidělených Dockeru. Můžete to provést tak, že vyberete **ikonu Dockeru**, pak vyberete **Upřesnit** a upravíte počet jader a velikost paměti.

2. V novém adresáři vytvořte soubor `Dockerfile` pro sestavení vaší image Service Fabric:

    ```Dockerfile
    FROM microsoft/service-fabric-onebox
    WORKDIR /home/ClusterDeployer
    RUN ./setup.sh
    #Generate the local
    RUN locale-gen en_US.UTF-8
    #Set environment variables
    ENV LANG=en_US.UTF-8
    ENV LANGUAGE=en_US:en
    ENV LC_ALL=en_US.UTF-8
    EXPOSE 19080 19000 80 443
    #Start SSH before running the cluster
    CMD /etc/init.d/ssh start && ./run.sh
    ```

    >[!NOTE]
    >Úpravou tohoto souboru můžete do kontejneru přidat další programy nebo závislosti.
    >Například přidáním `RUN apt-get install nodejs -y` se povolí podpora aplikací `nodejs` jako spustitelných souborů typu Host.
    
    >[!TIP]
    > Ve výchozím nastavení se tím přetáhne image s nejnovější verzí Service Fabric. Konkrétní revize najdete na stránce [Docker Hubu](https://hub.docker.com/r/microsoft/service-fabric-onebox/).

3. Pokud ze souboru `Dockerfile` chcete sestavit opakovatelně použitelnou image, otevřete terminál, pomocí příkazu `cd` přejděte přímo do adresáře s vaším souborem `Dockerfile` a pak spusťte:

    ```bash 
    docker build -t mysfcluster .
    ```
    
    >[!NOTE]
    >Tato operace nějakou dobu trvá, ale stačí ji provést jenom jednou.

4. Teď můžete rychle spustit místní kopii Service Fabric, kdykoli budete potřebovat, spuštěním:

    ```bash 
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mysfcluster
    ```

    >[!TIP]
    >Zadejte název instance kontejneru, aby s ní šlo pracovat srozumitelněji. 
    >
    >Pokud vaše aplikace naslouchá na konkrétních portech, musí se zadat pomocí dalších značek `-p`. Pokud například aplikace naslouchá na portu 8080, přidejte následující značku `-p`:
    >
    >`docker run -itd -p 19080:19080 -p 8080:8080 --name sfonebox microsoft/service-fabric-onebox`
    >

5. Clusteru bude chvíli spustit. Když je spuštěn, můžete zobrazit protokoly pomocí následujícího příkazu nebo přejít na řídicí panel, aby stav clusteru [ http://localhost:19080 ](http://localhost:19080):

    ```bash 
    docker logs sftestcluster
    ```



6. Chcete-li zastavit a vyčistit kontejneru použijte následující příkaz. Ale použijeme tento kontejner v dalším kroku.

    ```bash 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>Známá omezení 
 
 Následující seznam uvádí známá omezení pro místní cluster spuštěný v kontejneru pro Mac: 
 
 * Služba DNS neběží a není podporovaná [Problém č. 132](https://github.com/Microsoft/service-fabric/issues/132)

## <a name="set-up-the-service-fabric-cli-sfctl-on-your-mac"></a>Nastavení Service Fabric CLI (sfctl) na počítači Mac

Pokud chcete nainstalovat Service Fabric CLI (`sfctl`) na svém počítači Mac, postupujte podle pokynů v tématu [Service Fabric CLI](service-fabric-cli.md#cli-mac).
Příkazy rozhraní příkazového řádku podporují komunikaci s entitami Service Fabric, včetně clusterů, aplikací a služeb.

1. Pokud se před nasazením aplikací chcete připojit ke clusteru, spusťte následující příkaz. 

```bash
sfctl cluster select --endpoint http://localhost:19080
```

## <a name="create-your-application-on-your-mac-by-using-yeoman"></a>Vytvoření aplikace na počítači Mac pomocí Yeomana

Service Fabric nabízí nástroje pro generování uživatelského rozhraní, které vám pomůžou vytvořit aplikaci Service Fabric z terminálu pomocí generátoru šablon Yeoman. Postupujte podle následujících kroků, abyste zkontrolovali, že máte na svém počítači funkční generátor šablon Service Fabric Yeoman:

1. V počítači Mac musí být instalován Node.js a Node Package Manager (NPM). Software jde nainstalovat pomocí [HomeBrew](https://brew.sh/), a to takto:

    ```bash
    brew install node
    node -v
    npm -v
    ```
2. Nainstalujte na svém počítači generátor šablon [Yeoman](http://yeoman.io/) z NPM:

    ```bash
    npm install -g yo
    ```
3. Nainstalujte generátor Yeoman, který preferujete, podle kroků v úvodní [dokumentaci](service-fabric-get-started-linux.md#set-up-yeoman-generators-for-containers-and-guest-executables). Pokud chcete vytvářet aplikace Service Fabric pomocí Yeomana, postupujte podle těchto kroků:

    ```bash
    npm install -g generator-azuresfjava       # for Service Fabric Java Applications
    npm install -g generator-azuresfguest      # for Service Fabric Guest executables
    npm install -g generator-azuresfcontainer  # for Service Fabric Container Applications
    ```
4. Po nainstalování generátorů vytvořte spustitelné soubory hosta nebo služby kontejneru spuštěním příkazu `yo azuresfguest` nebo `yo azuresfcontainer`.

5. K sestavení aplikace Service Fabric Java na počítači Mac musí být na hostitelském počítači nainstalovaná sada JDK verze 1.8 a Gradle. Software jde nainstalovat pomocí [HomeBrew](https://brew.sh/), a to takto: 

    ```bash
    brew update
    brew cask install java
    brew install gradle
    ```

    >[!TIP]
    > Nezapomeňte si ověřit, že máte správnou verzi sady JDK nainstalovaná. 

## <a name="deploy-your-application-on-your-mac-from-the-terminal"></a>Nasazení aplikace na počítači Mac z terminálu

Po vytvoření a sestavení aplikace Service Fabric ji můžete nasadit pomocí [Service Fabric CLI](service-fabric-cli.md#cli-mac):

1. Připojte se ke clusteru Service Fabric spuštěnému uvnitř instance kontejneru na vašem počítači Mac:

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Z adresáře vašeho projektu spusťte instalační skript:

    ```bash
    cd MyProject
    bash install.sh
    ```

## <a name="set-up-net-core-20-development"></a>Nastavení pro vývoj v .NET Core 2.0

Pokud chcete začít [vytvářet aplikace Service Fabric v jazyce C#](service-fabric-create-your-first-linux-application-with-csharp.md), nainstalujte sadu [.NET Core 2.0 SDK pro Mac](https://www.microsoft.com/net/core#macos). Balíčky pro aplikace Service Fabric v .NET Core 2.0 jsou hostované na NuGet.org, který je aktuálně ve verzi Preview.

## <a name="install-the-service-fabric-plug-in-for-eclipse-on-your-mac"></a>Instalace modulu plug-in Service Fabric pro Eclipse na počítači Mac

Azure Service Fabric poskytuje modul plug-in pro Eclipse Neon (nebo novější) pro Java IDE. Tento modul plug-in zjednodušuje proces vytváření, sestavování a nasazování služeb Java. Pokud chcete nainstalovat nebo aktualizovat modul plug-in Service Fabric pro Eclipse na nejnovější verzi, postupujte podle [těchto kroků](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse). Ostatní kroky v [dokumentaci Service Fabric pro Eclipse](service-fabric-get-started-eclipse.md) lze také použít: sestavit aplikaci, přidat službu do aplikace, odinstalovat aplikaci a podobně.

Posledním krokem je vytvoření instance kontejneru s cestu, která je sdílená s hostitelem. Modul plug-in vyžaduje tento typ vytváření instancí, aby pracoval s kontejnerem Dockeru na vašem počítači Mac. Příklad:

```bash
docker run -itd -p 19080:19080 -v /Users/sayantan/work/workspaces/mySFWorkspace:/tmp/mySFWorkspace --name sfonebox microsoft/service-fabric-onebox
```

Atributy jsou definovány takto:
* `/Users/sayantan/work/workspaces/mySFWorkspace` je absolutní cestou pracovního prostoru na vašem počítači Mac.
* `/tmp/mySFWorkspace` je cestou v rámci kontejneru, kam má být namapovaný pracovní prostor.

>[!NOTE]
> 
>Pokud pro pracovní prostor používáte jiný název nebo cestu, aktualizujte tyto hodnoty pomocí příkazu `docker run`.
> 
>Pokud spouštíte kontejner s jiným názvem než `sfonebox`, aktualizujte název v souboru testclient.sh ve vaší aplikaci Service Fabric Actor v Javě.
>

## <a name="next-steps"></a>Další postup
<!-- Links -->
* [Vytvoření a nasazení první aplikace Service Fabric v Javě v Linuxu pomocí Yeomana](service-fabric-create-your-first-linux-application-with-java.md)
* [Vytvoření a nasazení první aplikace Service Fabric v Javě v Linuxu pomocí modulu plug-in Service Fabric pro Eclipse](service-fabric-get-started-eclipse.md)
* [Vytvoření clusteru služby Service Fabric na webu Azure Portal](service-fabric-cluster-creation-via-portal.md)
* [Vytvoření clusteru služby Service Fabric pomocí Azure Resource Manageru](service-fabric-cluster-creation-via-arm.md)
* [Pochopení aplikačního modelu služby Service Fabric](service-fabric-application-model.md)
* [Správa aplikací pomocí Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md)
* [Příprava linuxového vývojového prostředí ve Windows](service-fabric-local-linux-cluster-windows.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship
