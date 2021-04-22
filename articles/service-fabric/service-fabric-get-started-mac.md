---
title: Nastavení vývojového prostředí v macOS
description: Nainstalujte modul runtime, sadu SDK a nástroje a vytvořte místní vývojový cluster. Po dokončení tohoto nastavení budete připraveni k sestavování aplikací v macOS.
ms.topic: conceptual
ms.date: 10/16/2020
ms.custom: devx-track-js
ms.openlocfilehash: 6fe551f8371322af8d955b5233e6d9d05741f3d9
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868118"
---
# <a name="set-up-your-development-environment-on-mac-os-x"></a>Nastavení vývojového prostředí v Mac OS X
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [Mac OS X](service-fabric-get-started-mac.md)

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
    Toto nastavení můžete aktualizovat přímo v souboru daemon.json v instalační cestě Dockeru. Nastavení konfigurace démona můžete přímo upravit v Docker. Vyberte **ikonu Docker** a potom vyberte **Předvolby** > **Démon** > **Upřesnit**.
    
    >[!NOTE]
    >
    >Změna démona přímo v Docker se doporučuje, protože umístění daemon.jsv souboru se může lišit od počítače k počítači. Například ~/Library/Containers/com.docker.docker/Data/database/com.docker.driver.amd64-linux/etc/docker/daemon.json.
    >

    >[!TIP]
    >Při testování rozsáhlých aplikací doporučujeme zvýšit množství prostředků přidělených Dockeru. Můžete to provést tak, že vyberete **ikonu Dockeru**, pak vyberete **Upřesnit** a upravíte počet jader a velikost paměti.

2. Spusťte cluster.<br/>
    <b>Ubuntu 18,04 LTS:</b>
    ```bash
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u18
    ```

    <b>Ubuntu 16,04 LTS:</b>
    ```bash
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u16
    ```

    >[!TIP]
    > Ve výchozím nastavení se tím přetáhne image s nejnovější verzí Service Fabric. Konkrétní revize najdete na stránce [Service Fabric OneBox](https://hub.docker.com/_/microsoft-service-fabric-onebox) v Docker Hub.



3. Volitelné: Sestavte obrázek rozšířené Service Fabric.

    V novém adresáři vytvořte soubor `Dockerfile` s názvem pro sestavení vlastní image:

    >[!NOTE]
    >Obrázek uvedený výše můžete upravit pomocí souboru Dockerfile a přidat do svého kontejneru další programy nebo závislosti.
    >Například přidáním `RUN apt-get install nodejs -y` se povolí podpora aplikací `nodejs` jako spustitelných souborů typu Host.
    ```Dockerfile
    FROM mcr.microsoft.com/service-fabric/onebox:u18
    RUN apt-get install nodejs -y
    EXPOSE 19080 19000 80 443
    WORKDIR /home/ClusterDeployer
    CMD ["./ClusterDeployer.sh"]
    ```
    
    >[!TIP]
    > Ve výchozím nastavení se tím přetáhne image s nejnovější verzí Service Fabric. Konkrétní revize najdete na stránce [Docker Hubu](https://hub.docker.com/r/microsoft/service-fabric-onebox/).

    Pokud chcete vytvořit opakovaně použitelnou image z `Dockerfile` , otevřete terminál a přímo si `cd` podržíte své `Dockerfile` spuštění:

    ```bash 
    docker build -t mysfcluster .
    ```
    
    >[!NOTE]
    >Tato operace nějakou dobu trvá, ale stačí ji provést jenom jednou.

    Teď můžete rychle začít místní kopii Service Fabric, kdykoli ji budete potřebovat, a to spuštěním:

    ```bash 
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mysfcluster
    ```

    >[!TIP]
    >Zadejte název instance kontejneru, aby s ní šlo pracovat srozumitelněji. 
    >
    >Pokud vaše aplikace naslouchá na konkrétních portech, musí se zadat pomocí dalších značek `-p`. Pokud například aplikace naslouchá na portu 8080, přidejte následující značku `-p`:
    >
    >`docker run -itd -p 19000:19000 -p 19080:19080 -p 8080:8080 --name sfonebox mcr.microsoft.com/service-fabric/onebox:u18`
    >

4. Spuštění clusteru bude chvíli trvat. Když je spuštěný, můžete zobrazit protokoly pomocí následujícího příkazu nebo přejít na řídicí panel a zobrazit stav clusterů: `http://localhost:19080`

    ```bash 
    docker logs sftestcluster
    ```


5. Chcete-li zastavit a vyčistit kontejner, použijte následující příkaz. Tento kontejner ale použijeme v dalším kroku.

    ```bash 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>Známá omezení 
 
 Následující seznam uvádí známá omezení pro místní cluster spuštěný v kontejneru pro Mac: 
 
 * Služba DNS není spuštěna a v rámci kontejneru není aktuálně podporována. [Problém #132](https://github.com/Microsoft/service-fabric/issues/132)
 * Spouštění aplikací založených na kontejnerech vyžaduje spuštění SF na hostiteli se systémem Linux. Vnořené aplikace kontejneru se v tuto chvíli nepodporují.

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
2. Nainstalujte na svém počítači generátor šablon [Yeoman](https://yeoman.io/) z NPM:

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

    > [!IMPORTANT]
    > Aktuální verze nástroje `brew cask install java` mohou instalovat novější verzi JDK.
    > Nezapomeňte nainstalovat JDK 8.

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

## <a name="set-up-net-core-31-development"></a>Nastavení vývoje .NET Core 3,1

Pokud chcete začít [vytvářet aplikace Service Fabric C#](service-fabric-create-your-first-linux-application-with-csharp.md), nainstalujte [sadu .NET Core 3,1 SDK pro Mac](https://dotnet.microsoft.com/download?initial-os=macos) . Balíčky pro aplikace .NET Core Service Fabric se hostují na NuGet.org.

## <a name="install-the-service-fabric-plug-in-for-eclipse-on-your-mac"></a>Instalace modulu plug-in Service Fabric pro Eclipse na počítači Mac

Azure Service Fabric poskytuje modul plug-in pro Eclipse Neon (nebo novější) pro Java IDE. Tento modul plug-in zjednodušuje proces vytváření, sestavování a nasazování služeb Java. Pokud chcete nainstalovat nebo aktualizovat modul plug-in Service Fabric pro Eclipse na nejnovější verzi, postupujte podle [těchto kroků](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse). Ostatní kroky v [dokumentaci Service Fabric pro Eclipse](service-fabric-get-started-eclipse.md) lze také použít: sestavit aplikaci, přidat službu do aplikace, odinstalovat aplikaci a podobně.

Posledním krokem je vytvoření instance kontejneru s cestu, která je sdílená s hostitelem. Modul plug-in vyžaduje tento typ vytváření instancí, aby pracoval s kontejnerem Dockeru na vašem počítači Mac. Například:

```bash
docker run -itd -p 19080:19080 -v /Users/sayantan/work/workspaces/mySFWorkspace:/tmp/mySFWorkspace --name sfonebox mcr.microsoft.com/service-fabric/onebox:latest
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

## <a name="next-steps"></a>Další kroky
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
