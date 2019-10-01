---
title: 'Rychlý Start: Vytvoření aplikace Java v Azure Service Fabric'
description: V tomto rychlém startu vytvoříte aplikaci Java pro Azure pomocí ukázkové aplikace Service Fabric Reliable Services.
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

V tomto rychlém startu se dozvíte, jak nasadit svoji první aplikaci v jazyce Java do Azure Service Fabric pomocí integrovaného vývojového prostředí (IDE) na počítači pro vývojáře Až budete hotovi, budete mít hlasovací aplikaci s webovým front-end Java, který ukládá výsledky hlasování do stavové back-endové služby v clusteru.

Azure Service Fabric je platforma distribuovaných systémů pro nasazování a správu mikroslužeb a kontejnerů.

![Ukázka hlasování v Azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-voting-sample.png)

V tomto rychlém startu se dozvíte, jak:

* Použít zatmění jako nástroj pro aplikace Service Fabric Java
* Nasazení aplikace do místního clusteru
* Horizontální navýšení kapacity aplikace napříč více uzly

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu:

1. [Instalace sady Service Fabric SDK & Service Fabric rozhraní příkazového řádku (CLI)](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)
2. [Nainstalovat git](https://git-scm.com/)
3. [Instalace zatmění](https://www.eclipse.org/downloads/)
4. [Nastavte prostředí Java](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development)a nezapomeňte postupovat podle volitelného postupu instalace modulu plug-in zatmění.

## <a name="download-the-sample"></a>Stažení ukázky

V příkazovém okně spusťte následující příkaz, který naklonuje úložiště ukázkové aplikace do místního počítače.

```git
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="run-the-application-locally"></a>Spustit aplikaci místně

1. Spusťte místní cluster spuštěním následujícího příkazu:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    Spuštění místního clusteru trvá nějakou dobu. Pokud chcete potvrdit, že je cluster plně zapnutý, přejděte k Service Fabric Explorer v **http://localhost:19080** . Pět uzlů v pořádku značí, že místní cluster je spuštěný.

    ![Azure Service Fabric Explorer zobrazuje uzly v pořádku](./media/service-fabric-quickstart-java/service-fabric-explorer-healthy-nodes.png)

2. Otevřete zatmění.
3. Vyberte **soubor**@no__t 1**Import** > **Gradle** > **stávající projekt Gradle** a postupujte podle pokynů průvodce.
4. Vyberte **adresář** a zvolte adresář `Voting` ze složky `service-fabric-java-quickstart`, kterou jste naklonováni z GitHubu. Vyberte **Dokončit**.

    ![Importovat projekt Gradle do zatmění](./media/service-fabric-quickstart-java/eclipse-import-gradle-project.png)

5. Nyní máte projekt `Voting` v Průzkumníkovi balíčků pro zatmění.
6. Klikněte pravým tlačítkem na projekt a vyberte **publikovat aplikaci** pod rozevíracím seznamem **Service Fabric** . Jako cílový profil zvolte **PublishProfiles/Local. JSON** a vyberte **publikovat**.

    ![Azure Service Fabric publikovat místní JSON](./media/service-fabric-quickstart-java/service-fabric-publish-local-json.png)

7. Otevřete oblíbený webový prohlížeč a přejděte do aplikace pomocí přístupu `http://localhost:8080`.

    ![Místní hostitel Azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-local-host.png)

Teď můžete přidat sadu hlasovacích možností a začít přebírat hlasy. Aplikace se spustí a uloží všechna data v clusteru Service Fabric bez nutnosti samostatné databáze.

## <a name="scale-applications-and-services-in-a-cluster"></a>Škálování aplikací a služeb v clusteru

Služby je možné škálovat napříč clusterem tak, aby vyhovovaly změnám zatížení služeb. Škálujte službu změnou počtu instancí spuštěných v clusteru. Máte spoustu způsobů škálování služeb; Můžete například použít skripty nebo příkazy z Service Fabric CLI (sfctl). Následující postup použijte Service Fabric Explorer.

Service Fabric Explorer běží ve všech clusterech Service Fabric a dá se k nim dostat z prohlížeče tak, že přejdete na port pro správu HTTP clusteru (19080); například `http://localhost:19080`.

Pokud chcete škálovat webovou front-end službu, udělejte toto:

1. Otevřete Service Fabric Explorer v clusteru, například `https://localhost:19080`.
2. V ovládacím prvku TreeView vyberte tři tečky ( **...** ) vedle uzlu **Fabric:/hlasovacího/VotingWeb** a vyberte **škálovat službu**.

    ![Škálování služby v Azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-scale-service.png)

    Nyní se můžete rozhodnout pro škálování počtu instancí webové front-endové služby.

3. Změňte číslo na **2** a vyberte **škálovat službu**.
4. Ve stromovém zobrazení vyberte uzel **Fabric:/hlasovací/VotingWeb** a rozbalte uzel oddílu (REPREZENTOVANý identifikátorem GUID).

    ![Škálování služby ve službě Azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-explorer-service-scaled.png)

    Teď vidíte, že má služba dvě instance, a ve stromovém zobrazení vidíte, na kterých uzlech jsou instance spuštěné.

Prostřednictvím této jednoduché úlohy správy jste poklepali prostředky, které jsou k dispozici pro front-end službu ke zpracování uživatelského zatížení. Je důležité pochopit, že pro spolehlivou práci nepotřebujete více instancí služby. Pokud se služba nezdařila, Service Fabric zajistěte, aby se v clusteru spouštěla nová instance služby.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se dozvěděli, jak:

* Použít zatmění jako nástroj pro aplikace Service Fabric Java
* Nasazení aplikací Java do místního clusteru
* Horizontální navýšení kapacity aplikace napříč více uzly

Další informace o práci s aplikacemi Java v Service Fabric najdete v kurzu pro aplikace Java.

> [!div class="nextstepaction"]
> [Nasazení aplikace Java](./service-fabric-tutorial-create-java-app.md)
