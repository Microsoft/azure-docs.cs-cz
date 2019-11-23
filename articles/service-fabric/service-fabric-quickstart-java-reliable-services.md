---
title: 'Rychlý Start: Vytvoření aplikace Java v Azure Service Fabric'
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
ms.custom: mvc, devcenter, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 4ef0db5f72f5849942bb043261f1166cf7c046b1
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703281"
---
# <a name="quickstart--deploy-a-java-app-to-azure-service-fabric-on-linux"></a>Rychlý Start: nasazení aplikace Java do Azure Service Fabric v systému Linux

V tomto rychlém startu se dozvíte, jak nasadit svoji první aplikaci v jazyce Java do Azure Service Fabric pomocí integrovaného vývojového prostředí (IDE) na počítači pro vývojáře Až budete hotovi, budete mít hlasovací aplikaci s webovým front-endem v Javě, která ukládá výsledky hlasování do stavové back-end služby v clusteru.

Azure Service Fabric je platforma distribuovaných systémů pro nasazování a správu mikroslužeb a kontejnerů.

![Ukázka hlasování v Azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-voting-sample.png)

V tomto rychlém startu se naučíte:

* Používat Eclipse jako nástroj pro aplikace Service Fabric v Javě.
* Nasazení aplikace do místního clusteru
* Škálovat aplikaci na více instancí napříč několika uzly.

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
    Spuštění místního clusteru nějakou dobu trvá. Pokud chcete potvrdit, že je cluster plně funkční, přejděte do Service Fabric Exploreru na adrese **http://localhost:19080** . Pět uzlů v pořádku značí, že je místní cluster zprovozněný.

    ![Azure Service Fabric Explorer zobrazuje uzly v pořádku](./media/service-fabric-quickstart-java/service-fabric-explorer-healthy-nodes.png)

2. Otevřete Eclipse.
3. Vyberte **soubor** > **importovat** > **Gradle** > **stávající projekt Gradle** a postupujte podle pokynů průvodce.
4. Vyberte **adresář** a zvolte `Voting` adresář ze složky `service-fabric-java-quickstart`, kterou jste naklonoval z GitHubu. Vyberte **Finish** (Dokončit).

    ![Importovat projekt Gradle do zatmění](./media/service-fabric-quickstart-java/eclipse-import-gradle-project.png)

5. V průzkumníku balíčků v Eclipse teď máte projekt `Voting`.
6. Klikněte pravým tlačítkem na projekt a vyberte **publikovat aplikaci** pod rozevíracím seznamem **Service Fabric** . Jako cílový profil zvolte **PublishProfiles/Local. JSON** a vyberte **publikovat**.

    ![Azure Service Fabric publikovat místní JSON](./media/service-fabric-quickstart-java/service-fabric-publish-local-json.png)

7. Otevřete oblíbený webový prohlížeč a získejte přístup k aplikaci, a to tak, že získáte přístup k `http://localhost:8080`.

    ![Místní hostitel Azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-local-host.png)

Teď můžete přidat sadu možností hlasování a začít přijímat hlasy. Aplikace je spuštěná a ukládá veškerá data v clusteru Service Fabric, aniž by potřebovala samostatnou databázi.

## <a name="scale-applications-and-services-in-a-cluster"></a>Škálování aplikací a služeb v clusteru

Služby je možné škálovat napříč clusterem a vyřešit tak změny v jejich zatížení. Služby se škálují změnou počtu instancí spuštěných v clusteru. Služby můžete škálovat mnoha způsoby – můžete použít například skripty nebo příkazy v Service Fabric CLI (sfctl). V následujících krocích se používá Service Fabric Explorer.

Service Fabric Explorer běží na všech clusterech Service Fabric a je přístupný z prohlížeče po přechodu na port HTTP pro správu clusteru (19080), například `http://localhost:19080`.

Pokud chcete škálovat webovou front-end službu, postupujte následovně:

1. Otevřete ve vašem clusteru Service Fabric Explorer – například `https://localhost:19080`.
2. V ovládacím prvku TreeView vyberte tři tečky ( **...** ) vedle uzlu **Fabric:/hlasovacího/VotingWeb** a vyberte **škálovat službu**.

    ![Škálování služby v Azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-scale-service.png)

    Nyní můžete škálovat počet instancí webové front-end služby.

3. Změňte číslo na **2** a vyberte **škálovat službu**.
4. Ve stromovém zobrazení vyberte uzel **Fabric:/hlasovací/VotingWeb** a rozbalte uzel oddílu (REPREZENTOVANý identifikátorem GUID).

    ![Škálování služby ve službě Azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-explorer-service-scaled.png)

    Nyní je vidět, že služba má dvě instance, a ve stromovém zobrazení vidíte, na kterých uzlech jsou tyto instance spuštěné.

Touto jednoduchou úlohou správy jste zdvojnásobili prostředky, které má front-end služba k dispozici pro zpracování uživatelské zátěže. Je důležité si uvědomit, že pro spolehlivý provoz služby nepotřebujete více jejích instancí. Pokud služba selže, Service Fabric zajistí v clusteru spuštění nové instance služby.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili:

* Používat Eclipse jako nástroj pro aplikace Service Fabric v Javě.
* Nasazovat aplikace v Javě do místního clusteru.
* Škálovat aplikaci na více instancí napříč několika uzly.

Další informace o práci s aplikacemi v Javě v Service Fabric najdete v kurzu věnovaném aplikacím v Javě.

> [!div class="nextstepaction"]
> [Nasazení aplikace v Javě](./service-fabric-tutorial-create-java-app.md)
