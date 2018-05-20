---
title: Nastavení clusteru Azure Service Fabric Linux v systému Windows | Microsoft Docs
description: Tento článek popisuje postup nastavení služby Fabric Linux clusterů se systémem na vývoj pro počítače s Windows. To je obzvláště užitečné pro různé platformy vývoj.
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
ms.openlocfilehash: ad0d383888c173ece5a7fbd3b0de690ed13074f7
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>Nastavení clusteru Linux Service Fabric na počítači pro vývojáře systému Windows

Tento dokument popisuje, jak nastavit místní Service Fabric Linux na vývoj pro počítače s Windows. Nastavení místní cluster Linux je užitečné rychle otestovat aplikace cílené pro Linux clustery, ale jsou vyvinuté na počítači s Windows.

## <a name="prerequisites"></a>Požadavky
Clustery se systémem Linux Service Fabric nativně nespouštějte v systému Windows. Pokud chcete spustit místní cluster Service Fabric, je k dispozici bitovou kopii předem nakonfigurovaná kontejner Docker. Než začnete, budete potřebovat:

* Minimálně 4 GB RAM
* Nejnovější verzi [Dockeru](https://store.docker.com/editions/community/docker-ce-desktop-windows)

>[!TIP]
> * Můžete provést kroky uvedené v oficiální Docker [dokumentace](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions) instalace Docker v systému Windows. 
> * Po dokončení instalace ověřte, že proběhla úspěšně, podle postupu uvedeného [tady](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine).


## <a name="create-a-local-container-and-setup-service-fabric"></a>Vytvoření místního kontejneru a nastavení Service Fabric
Nastavit místní kontejner Docker a mít cluster service fabric, v rámci něj spuštěna, proveďte následující kroky v prostředí PowerShell:

1. Stáhněte si image z úložiště Docker Hub:

    ```powershell
    docker pull microsoft/service-fabric-onebox
    ```

2. Následujícím způsobem aktualizujte konfiguraci démona Dockeru na hostiteli a potom démon Dockeru restartujte: 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    Je doporučené způsob, jak můžete aktualizovat - přejít na ikonu Docker > Nastavení > démon > Upřesnit a aktualizovat ji existuje. Poté restartujte démon Docker změny se projeví. 

3. Spusťte instanci kontejneru Service Fabric One-box s touto imagí:

    ```powershell
    docker run -itd -p 19080:19080 --name sfonebox microsoft/service-fabric-onebox
    ```
    >[!TIP]
    > * Pokud zadáte název instance kontejneru, můžete s ní pracovat srozumitelněji. 
    > * Pokud vaše aplikace naslouchá na konkrétních portech, musí se zadat pomocí dalších značek -p. Například pokud aplikace naslouchá na portu 8080, spusťte docker spustit 8080:8080 -p - itd -p 19080:19080 – název sfonebox microsoft/service-prostředků infrastruktury – onebox

4. Přihlaste se ke kontejneru Dockeru v interaktivním režimu SSH:

    ```powershell
    docker exec -it sfonebox bash
    ```

5. Spusťte instalační skript, který načte požadované závislosti a pak v kontejneru spustí cluster.

    ```bash
    ./setup.sh     # Fetches and installs the dependencies required for Service Fabric to run
    ./run.sh       # Starts the local cluster
    ```

6. Po úspěšném dokončení kroku 5, můžete přejít na ``http://localhost:19080`` z vašeho systému Windows a bude moci zobrazit v Service Fabric Exploreru. V tomto okamžiku můžete připojit k tomuto clusteru pomocí všech nástrojů od vývojáře počítač se systémem Windows a nasadit aplikaci do cílového pro Linux Service Fabric clustery. 

    > [!NOTE]
    > Modul plug-in Eclipse se v systému Windows aktuálně nepodporuje. 

## <a name="next-steps"></a>Další postup
* Začínáme s [Eclipse](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-eclipse)
* Podívejte se na jiné [ukázky Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png