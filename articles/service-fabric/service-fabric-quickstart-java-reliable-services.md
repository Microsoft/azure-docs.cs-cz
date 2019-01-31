---
title: Vytvoření aplikace Java na platformě Service Fabric v Azure | Microsoft Docs
description: V tomto rychlém startu vytvoříte aplikaci v Javě pro Azure s využitím ukázkové aplikace Service Fabric Reliable Services.
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
ms.openlocfilehash: ad14e552bd685c42289e7007002f5ddf039f8925
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55297952"
---
# <a name="quickstart-deploy-a-java-reliable-services-application-to-service-fabric"></a>Rychlý start: Nasazení aplikace reliable services v Javě do Service Fabric

Azure Service Fabric je platforma distribuovaných systémů pro nasazování a správu mikroslužeb a kontejnerů.

Tento rychlý start ukazuje, jak nasadit první aplikaci v Javě do Service Fabric pomocí integrovaného vývojového prostředí (IDE) Eclipse na vývojovém počítači s Linuxem. Až budete hotovi, budete mít hlasovací aplikaci s webovým front-endem v Javě, která ukládá výsledky hlasování do stavové back-end služby v clusteru.

![Snímek obrazovky aplikace](./media/service-fabric-quickstart-java/votingapp.png)

V tomto rychlém startu se naučíte:

* Používat Eclipse jako nástroj pro aplikace Service Fabric v Javě.
* Nasazení aplikace do místního clusteru
* Škálování aplikace na více instancí napříč několika uzly

## <a name="prerequisites"></a>Požadavky

K provedení kroků v tomto kurzu Rychlý start je potřeba:

1. [Nainstalovat sadu Service Fabric SDK a rozhraní příkazového řádku (CLI) Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods).
2. [Nainstalovat Git](https://git-scm.com/).
3. [Nainstalovat Eclipse](https://www.eclipse.org/downloads/).
4. [Nastavit prostředí Java](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development) a provést volitelné kroky pro instalaci modulu plug-in pro Eclipse.

## <a name="download-the-sample"></a>Stažení ukázky

V příkazovém okně naklonujte spuštěním následujícího příkazu úložiště ukázkové aplikace do místního počítače.

```git
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="run-the-application-locally"></a>Místní spuštění aplikace

1. Spusťte místní cluster spuštěním následujícího příkazu:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    Spuštění místního clusteru nějakou dobu trvá. Pokud chcete potvrdit, že je cluster plně funkční, přejděte do Service Fabric Exploreru na adrese **http://localhost:19080**. Pět uzlů v pořádku značí, že je místní cluster zprovozněný.

    ![Místní cluster v pořádku](./media/service-fabric-quickstart-java/localclusterup.png)

2. Otevřete Eclipse.
3. Klikněte na File -> Import -> Gradle -> Existing Gradle Project (Soubor -> Importovat -> Gradle -> Existující projekt Gradle) a postupujte podle pokynů v průvodci.
4. Klikněte na adresář a zvolte `Voting` z `service-fabric-java-quickstart` složky, které jste naklonovali z Githubu. Klikněte na tlačítko Dokončit.

    ![Dialogové okno importu v Eclipse](./media/service-fabric-quickstart-java/eclipseimport.png)

5. V průzkumníku balíčků v Eclipse teď máte projekt `Voting`.
6. Klikněte na projekt pravým tlačítkem a v rozevíracím seznamu **Service Fabric** vyberte **Publish Application...** (Publikovat aplikaci...). Jako Target Profile (Cílový profil) zvolte **PublishProfiles/Local.json** a klikněte na Publish (Publikovat).

    ![Dialogové okno publikování – místní prostředí](./media/service-fabric-quickstart-java/localjson.png)

7. Otevřete oblíbený webový prohlížeč a přejděte do aplikace na adrese **http://localhost:8080**.

    ![Front-end aplikace – místní prostředí](./media/service-fabric-quickstart-java/runninglocally.png)

Teď můžete přidat sadu možností hlasování a začít přijímat hlasy. Aplikace je spuštěná a ukládá veškerá data v clusteru Service Fabric, aniž by potřebovala samostatnou databázi.

## <a name="scale-applications-and-services-in-a-cluster"></a>Škálování aplikací a služeb v clusteru

Služby je možné škálovat napříč clusterem a vyřešit tak změny v jejich zatížení. Služby se škálují změnou počtu instancí spuštěných v clusteru. Služby můžete škálovat mnoha způsoby – můžete použít například skripty nebo příkazy v Service Fabric CLI (sfctl). V následujících krocích se používá Service Fabric Explorer.

Service Fabric Explorer běží na všech clusterech Service Fabric a je přístupný z prohlížeče po přechodu na port HTTP pro správu clusteru (19080), například `http://localhost:19080`.

Pokud chcete škálovat webovou front-end službu, postupujte následovně:

1. Otevřete ve vašem clusteru Service Fabric Explorer – například `https://localhost:19080`.
2. Ve stromovém zobrazení klikněte na tři tečky vedle uzlu **fabric:/Voting/VotingWeb** a zvolte **Škálovat službu**.

    ![Service Fabric Explorer – škálování služby](./media/service-fabric-quickstart-java/scaleservicejavaquickstart.png)

    Teď můžete škálovat počet instancí webové front-end služby.

3. Změňte počet na **2** a klikněte na **Škálovat službu**.
4. Ve stromovém zobrazení klikněte na uzel **fabric:/Voting/VotingWeb** a rozbalte uzel oddílu (reprezentovaný identifikátorem GUID).

    ![Service Fabric Explorer – Dokončení škálování služby](./media/service-fabric-quickstart-java/servicescaled.png)

    Nyní je vidět, že služba má dvě instance, a ve stromovém zobrazení vidíte, na kterých uzlech jsou tyto instance spuštěné.

Touto jednoduchou úlohou správy jste zdvojnásobili prostředky, které má front-end služba k dispozici pro zpracování uživatelské zátěže. Je důležité si uvědomit, že pro spolehlivý provoz služby nepotřebujete více jejích instancí. Pokud služba selže, Service Fabric zajistí v clusteru spuštění nové instance služby.

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste se naučili:

* Používat Eclipse jako nástroj pro aplikace Service Fabric v Javě.
* Nasazovat aplikace v Javě do místního clusteru.
* Škálování aplikace na více instancí napříč několika uzly

Další informace o práci s aplikacemi v Javě v Service Fabric najdete v kurzu věnovaném aplikacím v Javě.

> [!div class="nextstepaction"]
> [Nasazení aplikace v Javě](./service-fabric-tutorial-create-java-app.md)
