---
title: Nastavení clusteru Azure Service Fabric Linux ve Windows
description: Tento článek popisuje, jak nastavit clustery Service Fabric Linux spuštěné ve vývojových počítačích s Windows. To je užitečné hlavně při vývoji pro různé platformy.
ms.topic: conceptual
ms.date: 11/20/2017
ms.openlocfilehash: 83d494d777a4a1e1586707c8848056ca8fe9780a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91537068"
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>Nastavení clusteru se systémem Linux Service Fabric ve vašem počítači s Windows Developer

Tento dokument popisuje, jak nastavit místní Service Fabric pro Linux ve vývojových počítačích s Windows. Nastavení místního clusteru se systémem Linux je užitečné pro rychlé testování aplikací určených pro clustery se systémem Linux, které jsou vyvíjeny na počítači s Windows.

## <a name="prerequisites"></a>Požadavky
Clustery Service Fabric se systémem Linux neběží nativně ve Windows. Pro spuštění místního clusteru Service Fabric je k dispozici předem nakonfigurovaná image kontejneru Docker. Než začnete, budete potřebovat:

* Minimálně 4 GB RAM
* Nejnovější verzi [Dockeru](https://store.docker.com/editions/community/docker-ce-desktop-windows)
* Docker musí běžet v režimu Linux.

>[!TIP]
> * Můžete postupovat podle kroků uvedených v oficiální [dokumentaci](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions) k Docker a nainstalovat Docker ve Windows. 
> * Po dokončení instalace ověřte, že proběhla úspěšně, podle postupu uvedeného [tady](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine).


## <a name="create-a-local-container-and-setup-service-fabric"></a>Vytvoření místního kontejneru a nastavení Service Fabric
Pokud chcete nastavit místní kontejner Docker a mít v něm spuštěný Cluster Service Fabric, proveďte v PowerShellu následující kroky:


1. Následujícím způsobem aktualizujte konfiguraci démona Dockeru na hostiteli a potom démon Dockeru restartujte: 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    Povýšený způsob aktualizace: Přejít na ikonu Docker > nastavení > démona > Upřesnit a aktualizovat tam. Pak restartujte démona Docker, aby se změny projevily. 

2. V novém adresáři vytvořte soubor `Dockerfile` pro sestavení vaší image Service Fabric:

    ```Dockerfile
    FROM mcr.microsoft.com/service-fabric/onebox:latest
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
    > Ve výchozím nastavení se tím přetáhne image s nejnovější verzí Service Fabric. Konkrétní revize najdete na stránce [Docker Hub](https://hub.docker.com/r/microsoft/service-fabric-onebox/) .

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
    >`docker run -itd -p 19080:19080 -p 8080:8080 --name sfonebox mcr.microsoft.com/service-fabric/onebox:latest`
    >

5. Spuštění clusteru bude chvíli trvat. Pomocí následujícího příkazu můžete zobrazit protokoly nebo můžete přejít na řídicí panel na adrese `http://localhost:19080` a zobrazit stav clusteru:

    ```powershell 
    docker logs sftestcluster
    ```

6. Po úspěšném dokončení kroku 5 můžete přejít ``http://localhost:19080`` z okna z okna a zobrazit průzkumníka Service Fabric. V tuto chvíli se můžete připojit k tomuto clusteru pomocí libovolných nástrojů ze svého počítače s Windows Developer a nasadit aplikaci cílené na Service Fabric clustery pro Linux. 

    > [!NOTE]
    > Modul plug-in Eclipse se v systému Windows aktuálně nepodporuje. 

7. Až skončíte, zastavte a vyčistěte kontejner pomocí tohoto příkazu:

    ```powershell 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>Známá omezení 
 
 Následující seznam uvádí známá omezení pro místní cluster spuštěný v kontejneru pro Mac: 
 
 * Služba DNS neběží a není podporovaná [Problém č. 132](https://github.com/Microsoft/service-fabric/issues/132)

## <a name="next-steps"></a>Další kroky
* Začínáme s [zatmění](./service-fabric-get-started-eclipse.md)
* Podívejte se na další [ukázky Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
