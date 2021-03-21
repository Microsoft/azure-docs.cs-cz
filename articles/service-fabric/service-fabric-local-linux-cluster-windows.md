---
title: Nastavení clusteru Azure Service Fabric Linux ve Windows
description: Tento článek popisuje, jak nastavit clustery Service Fabric Linux spuštěné ve vývojových počítačích s Windows. Tento přístup je vhodný pro vývoj pro různé platformy.
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: 7b25a84e76773baea9f17430df1b7ba13aa661aa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93087073"
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>Nastavení clusteru se systémem Linux Service Fabric ve vašem počítači s Windows Developer

Tento dokument popisuje, jak nastavit místní Cluster Service Fabric pro Linux na vývojovém počítači s Windows. Nastavení místního clusteru se systémem Linux je užitečné pro rychlé testování aplikací určených pro clustery se systémem Linux, které jsou vyvíjeny na počítači s Windows.

## <a name="prerequisites"></a>Předpoklady
Clustery Service Fabric se systémem Linux neběží ve Windows, ale pokud chcete povolit vytváření prototypů pro různé platformy, poskytujeme pro Linux Service Fabric jeden Box kontejner Docker, který se dá nasadit prostřednictvím Docker for Windows.

Než začnete, budete potřebovat:

* Minimálně 4 GB RAM
* Nejnovější verze [Docker for Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows)
* Docker musí běžet v režimu kontejnerů Linux.

>[!TIP]
> Pokud chcete nainstalovat Docker na počítač s Windows, postupujte podle kroků v [dokumentaci k Docker](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions). Po dokončení [instalaci ověřte](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine).
>

## <a name="create-a-local-container-and-setup-service-fabric"></a>Vytvoření místního kontejneru a nastavení Service Fabric
Pokud chcete nastavit místní kontejner Docker a mít v něm spuštěný Cluster Service Fabric, spusťte následující kroky:


1. Následujícím způsobem aktualizujte konfiguraci démona Dockeru na hostiteli a potom démon Dockeru restartujte: 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    Povýšený způsob, jak ho aktualizovat, je přejít na: 

    * Ikona Docker > nastavení > Docker Engine
    * Přidat nová pole uvedená výše
    * Použít & restartovat – restartujte démona Docker, aby se změny projevily.

2. Spusťte cluster přes PowerShell.<br/>
    <b>Ubuntu 18,04 LTS:</b>
    ```powershell
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u18
    ```

    <b>Ubuntu 16,04 LTS:</b>
    ```powershell
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

    ```powershell 
    docker build -t mysfcluster .
    ```
    
    >[!NOTE]
    >Tato operace nějakou dobu trvá, ale stačí ji provést jenom jednou.

    Teď můžete rychle začít místní kopii Service Fabric, kdykoli ji budete potřebovat, a to spuštěním:

    ```powershell 
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mysfcluster
    ```

    >[!TIP]
    >Zadejte název instance kontejneru, aby s ní šlo pracovat srozumitelněji. 
    >
    >Pokud vaše aplikace naslouchá na konkrétních portech, musí se zadat pomocí dalších značek `-p`. Pokud například aplikace naslouchá na portu 8080, přidejte následující značku `-p`:
    >
    >`docker run -itd -p 19000:19000 -p 19080:19080 -p 8080:8080 --name sfonebox mcr.microsoft.com/service-fabric/onebox:u18`
    >


4. Spuštění clusteru bude chvíli trvat. Pomocí následujícího příkazu můžete zobrazit protokoly nebo můžete přejít na řídicí panel na adrese `http://localhost:19080` a zobrazit stav clusteru:

    ```powershell 
    docker logs sftestcluster
    ```

5. Po úspěšném nasazení clusteru, jak je zjištěno v kroku 4, můžete přejít na ``http://localhost:19080`` z počítače s Windows a najít Service Fabric Explorer řídicí panel. V tomto okamžiku se můžete připojit k tomuto clusteru pomocí nástrojů z počítače s Windows Developer a nasazovat aplikace cílené na Service Fabric clusterů pro Linux. 

    > [!NOTE]
    > Modul plug-in Eclipse se v systému Windows aktuálně nepodporuje. 

6. Až skončíte, zastavte a vyčistěte kontejner pomocí tohoto příkazu:

    ```powershell 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>Známá omezení 
 
 Následující seznam uvádí známá omezení pro místní cluster spuštěný v kontejneru pro Mac: 
 
 * Služba DNS není spuštěna a v rámci kontejneru není aktuálně podporována. [Problém #132](https://github.com/Microsoft/service-fabric/issues/132)
 * Spouštění aplikací založených na kontejnerech vyžaduje spuštění SF na hostiteli se systémem Linux. Vnořené aplikace kontejneru se v tuto chvíli nepodporují.

## <a name="next-steps"></a>Další kroky
* [Vytvoření a nasazení první aplikace Service Fabric v Javě v Linuxu pomocí Yeomana](service-fabric-create-your-first-linux-application-with-java.md)
* Začínáme s [zatmění](./service-fabric-get-started-eclipse.md)
* Podívejte se na další [ukázky Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)
* Informace o [možnostech podpory pro Service Fabric](service-fabric-support.md)


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
