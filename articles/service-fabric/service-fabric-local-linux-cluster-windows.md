---
title: Nastavení clusteru Azure Service Fabric s Linuxem ve Windows | Dokumentace Microsoftu
description: Tento článek popisuje, jak vytvořit clustery Service Fabric s Linuxem běží na počítačích s Windows development. To je zvláště užitečná pro pro vývoj napříč platformami.
services: service-fabric
documentationcenter: .net
author: suhuruli
manager: mfussell
editor: ''
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/20/2017
ms.author: suhuruli
ms.openlocfilehash: 26acb3a2a0cefdca74d2c761ccddf89e18aa909a
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54388492"
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>Nastavení clusteru Service Fabric s Linuxem na počítači pro vývojáře Windows

Tento dokument popisuje, jak nastavit místní Service Fabric Linux na počítačích vývojářů pro Windows. Nastavení místního clusteru Linux je užitečné pro rychlé testování aplikací služba je určená pro clustery s Linuxem, ale jsou vyvíjeny v počítači s Windows.

## <a name="prerequisites"></a>Požadavky
Linuxových clusterech Service Fabric není nativně běžet na Windows. Pro spuštění místního clusteru Service Fabric se poskytuje předkonfigurovaná image kontejneru Dockeru. Než začnete, budete potřebovat:

* Minimálně 4 GB RAM
* Nejnovější verzi [Dockeru](https://store.docker.com/editions/community/docker-ce-desktop-windows)

>[!TIP]
> * Můžete postupovat podle postupu uvedeného v oficiální Dockeru [dokumentaci](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions) chcete nainstalovat Docker na vaše Windows. 
> * Po dokončení instalace ověřte, že proběhla úspěšně, podle postupu uvedeného [tady](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine).


## <a name="create-a-local-container-and-setup-service-fabric"></a>Vytvoření místního kontejneru a nastavení Service Fabric
Pokud chcete nastavit místní kontejner Dockeru a mít v něm spuštěný cluster service fabric, proveďte následující kroky v prostředí PowerShell:


1. Následujícím způsobem aktualizujte konfiguraci démona Dockeru na hostiteli a potom démon Dockeru restartujte: 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    Je doporučený způsob, jak aktualizovat – přejděte na ikonu Dockeru > Nastavení > démon > Upřesnit a aktualizaci proveďte tady. Poté restartujte démona Dockeru, aby se změny projevily. 

2. V novém adresáři vytvořte soubor `Dockerfile` pro sestavení vaší image Service Fabric:

    ```dockerfile
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

5. Spuštění clusteru bude chvíli trvat. Pomocí následujícího příkazu můžete zobrazit protokoly nebo můžete přejít na řídicí panel na adrese [http://localhost:19080](http://localhost:19080) a zobrazit stav clusteru:

    ```powershell 
    docker logs sftestcluster
    ```

6. Po úspěšném dokončení kroku 5, můžete přejít na ``http://localhost:19080`` z vaší Windows a by se měl zobrazit Service Fabric explorer. V tomto okamžiku můžete připojit k tomuto clusteru pomocí všech nástrojů z vašeho počítače pro vývojáře Windows a nasazení aplikace je určená pro clustery Service Fabric s Linuxem. 

    > [!NOTE]
    > Modul plug-in Eclipse se v systému Windows aktuálně nepodporuje. 

7. Jakmile budete hotovi, ukončete a vyčištění kontejneru pomocí tohoto příkazu:

    ```powershell 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>Známá omezení 
 
 Následující seznam uvádí známá omezení pro místní cluster spuštěný v kontejneru pro Mac: 
 
 * Služba DNS neběží a není podporovaná [Problém č. 132](https://github.com/Microsoft/service-fabric/issues/132)

## <a name="next-steps"></a>Další postup
* Začínáme s [Eclipse](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-eclipse)
* Projděte si další [ukázky v Javě](https://github.com/Azure-Samples/service-fabric-java-getting-started)


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png