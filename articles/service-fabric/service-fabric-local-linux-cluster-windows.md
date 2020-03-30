---
title: Nastavení clusteru Azure Service Fabric Linux ve Windows
description: Tento článek popisuje, jak nastavit clustery Service Fabric Linux spuštěné na vývojových počítačích systému Windows. To je užitečné zejména pro vývoj napříč platformami.
author: suhuruli
ms.topic: conceptual
ms.date: 11/20/2017
ms.author: suhuruli
ms.openlocfilehash: 806e77a928d25e30aed24147525f74507bc32795
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75462990"
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>Nastavení clusteru Linux Service Fabric v počítači s vývojáři windows

Tento dokument popisuje, jak nastavit místní Linux Service Fabric na vývojových počítačích windows. Nastavení místního clusteru Linux je užitečné pro rychlé testování aplikací určených pro clustery Linux, ale jsou vyvíjeny na počítači se systémem Windows.

## <a name="prerequisites"></a>Požadavky
Clustery Service Fabric založené na Linuxu neběží nativně v systému Windows. Chcete-li spustit místní cluster Service Fabric, je k dispozici předem nakonfigurovaná image kontejneru Dockeru. Než začnete, budete potřebovat:

* Minimálně 4 GB RAM
* Nejnovější verzi [Dockeru](https://store.docker.com/editions/community/docker-ce-desktop-windows)
* Docker musí běžet v režimu Linuxu.

>[!TIP]
> * Můžete postupovat podle kroků uvedených v oficiální [dokumentaci Dockeru](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions) k instalaci Dockeru do windows. 
> * Po dokončení instalace ověřte, že proběhla úspěšně, podle postupu uvedeného [tady](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine).


## <a name="create-a-local-container-and-setup-service-fabric"></a>Vytvoření místního kontejneru a nastavení Service Fabric
Chcete-li nastavit místní kontejner Dockeru a mít v něm spuštěný cluster prostředků infrastruktury služeb, proveďte v Prostředí PowerShell následující kroky:


1. Následujícím způsobem aktualizujte konfiguraci démona Dockeru na hostiteli a potom démon Dockeru restartujte: 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    Doporučený způsob aktualizace je - přejděte na ikonu Dockeru > Nastavení > Daemon > Upřesnit a aktualizujte jej tam. Dále restartujte daemon Dockeru, aby se změny projevily. 

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
    > Ve výchozím nastavení se tím přetáhne image s nejnovější verzí Service Fabric. Konkrétní revize najdete na stránce [Docker Hub.](https://hub.docker.com/r/microsoft/service-fabric-onebox/)

3. Pokud ze souboru `Dockerfile` chcete sestavit opakovatelně použitelnou image, otevřete terminál, pomocí příkazu `cd` přejděte přímo do adresáře s vaším souborem `Dockerfile` a pak spusťte:

    ```powershell 
    docker build -t mysfcluster .
    ```
    
    >[!NOTE]
    >Tato operace nějakou dobu trvá, ale stačí ji provést jenom jednou.

4. Teď můžete rychle spustit místní kopii Service Fabric, kdykoli budete potřebovat, spuštěním:

    ```powershell 
    docker run --name sftestcluster -d -v //var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mysfcluster
    ```

    >[!TIP]
    >Zadejte název instance kontejneru, aby s ní šlo pracovat srozumitelněji. 
    >
    >Pokud vaše aplikace naslouchá na konkrétních portech, musí se zadat pomocí dalších značek `-p`. Pokud například aplikace naslouchá na portu 8080, přidejte následující značku `-p`:
    >
    >`docker run -itd -p 19080:19080 -p 8080:8080 --name sfonebox microsoft/service-fabric-onebox`
    >

5. Spuštění clusteru bude trvat krátkou dobu, můžete zobrazit protokoly pomocí následujícího příkazu nebo [http://localhost:19080](http://localhost:19080)přejít na řídicí panel a zobrazit stav clusterů :

    ```powershell 
    docker logs sftestcluster
    ```

6. Po úspěšném dokončení kroku 5 můžete ``http://localhost:19080`` přejít z windows a budete moci zobrazit průzkumník service fabric. V tomto okamžiku se můžete připojit k tomuto clusteru pomocí libovolných nástrojů z vašeho vývojářského počítače se systémem Windows a nasadit aplikace určené pro clustery Linux Service Fabric. 

    > [!NOTE]
    > Modul plug-in Eclipse se v systému Windows aktuálně nepodporuje. 

7. Po dokončení zastavte a vyčistěte kontejner pomocí tohoto příkazu:

    ```powershell 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>Známá omezení 
 
 Následující seznam uvádí známá omezení pro místní cluster spuštěný v kontejneru pro Mac: 
 
 * Služba DNS neběží a není podporovaná [Problém č. 132](https://github.com/Microsoft/service-fabric/issues/132)

## <a name="next-steps"></a>Další kroky
* Začínáme s [Eclipse](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-eclipse)
* Podívejte se na další [ukázky Javy](https://github.com/Azure-Samples/service-fabric-java-getting-started)


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
