---
title: 'Úvodní příručka: Vytvoření aplikace Java na Azure Service Fabric'
description: V tomto rychlém startu vytvoříte aplikaci v Javě pro Azure s využitím ukázkové aplikace Service Fabric Reliable Services.
author: suhuruli
ms.topic: quickstart
ms.date: 01/29/2019
ms.author: suhuruli
ms.custom: mvc, devcenter, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: fc615149b092aebfdde767fb3b716fb897bfd551
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77121503"
---
# <a name="quickstart--deploy-a-java-app-to-azure-service-fabric-on-linux"></a>Úvodní příručka: Nasazení aplikace Java do Azure Service Fabric na Linuxu

V tomto rychlém startu nasadíte java aplikaci do Azure Service Fabric pomocí Eclipse IDE na vývojářském počítači s Linuxem. Až budete hotovi, budete mít hlasovací aplikaci s webovým front-endem v Javě, která ukládá výsledky hlasování do stavové back-end služby v clusteru.

Azure Service Fabric je platforma distribuovaných systémů pro nasazování a správu mikroslužeb a kontejnerů.

## <a name="prerequisites"></a>Požadavky

- [Java prostředí](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development) a [Pojeoman](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-yeoman-generators-for-containers-and-guest-executables)
- [Eclipse Neon (4.6)+](https://www.eclipse.org/downloads/packages/) a [Eclipse plug-in pro service fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#install-the-eclipse-plug-in-optional)
- [Service Fabric SDK a rozhraní příkazového řádku (CLI)](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)
- [Git](https://git-scm.com/downloads)

## <a name="download-the-sample"></a>Stažení ukázky

V příkazovém okně naklonujte spuštěním následujícího příkazu úložiště ukázkové aplikace do místního počítače.

```bash
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="run-the-application-locally"></a>Místní spuštění aplikace

1. Spusťte místní cluster spuštěním následujícího příkazu:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    Spuštění místního clusteru nějakou dobu trvá. Pokud chcete potvrdit, že je cluster plně funkční, přejděte do Service Fabric Exploreru na adrese `http://localhost:19080`. Pět uzlů v pořádku značí, že je místní cluster zprovozněný.

    ![Azure Service Fabric Explorer zobrazuje uzly v pořádku](./media/service-fabric-quickstart-java/service-fabric-explorer-healthy-nodes.png)

2. Otevřete Eclipse.
3. Vyberte**Import** >  **souborů** > **Projekt** > **existujícígradle** a postupujte podle průvodce.
4. Vyberte **Adresář** a zvolte adresář **Hlasování** ze složky **service-fabric-java-quickstart,** kterou jste naklonovali z GitHubu. Vyberte **Finish** (Dokončit).

    ![Import Gradle projektu do Eclipse](./media/service-fabric-quickstart-java/eclipse-import-gradle-project.png)

5. V průzkumníku balíčků v Eclipse teď máte projekt `Voting`.
6. Klikněte pravým tlačítkem myši na projekt a v rozevíracím okně **Service Fabric** vyberte **Publikovat aplikaci.** Jako cílový profil zvolte **PublishProfiles/Local.json** a vyberte **Publikovat**.

    ![Azure Service Fabric publikovat místní JSON](./media/service-fabric-quickstart-java/service-fabric-publish-local-json.png)

7. Otevřete oblíbený webový prohlížeč a přejděte do aplikace na adrese `http://localhost:8080`.

    ![Místní ho sazí azure service fabric](./media/service-fabric-quickstart-java/service-fabric-local-host.png)

Teď můžete přidat sadu možností hlasování a začít přijímat hlasy. Aplikace je spuštěná a ukládá veškerá data v clusteru Service Fabric, aniž by potřebovala samostatnou databázi.

![Ukázka hlasování azure service fabric](./media/service-fabric-quickstart-java/service-fabric-voting-sample.png)

## <a name="scale-applications-and-services-in-a-cluster"></a>Škálování aplikací a služeb v clusteru

Služby je možné škálovat napříč clusterem a vyřešit tak změny v jejich zatížení. Služby se škálují změnou počtu instancí spuštěných v clusteru. Existuje mnoho způsobů škálování služeb. Můžete například použít skripty nebo příkazy z`sfctl`příkazového příkazu Service Fabric ( ). V následujících krocích se používá Service Fabric Explorer.

Aplikace Service Fabric Explorer je spuštěna ve všech clusterech Service Fabric a lze k němu přistupovat z prohlížeče procházením portu správy HTTP clusteru (19080). Například, `http://localhost:19080`.

Pokud chcete škálovat webovou front-end službu, postupujte následovně:

1. Otevřete v clusteru Průzkumníka prostředků service fabric. Například, `https://localhost:19080`.
2. Vyberte tři tečky (**...**) vedle **uzlu fabric:/Voting/VotingWeb** v treeview a vyberte **měřítko služby**.

    ![Škálování služby ve službě Azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-scale-service.png)

    Teď můžete škálovat počet instancí webové front-end služby.

3. Změňte číslo na **2** a vyberte **Škálovat službu**.
4. Vyberte **prostředek infrastruktury:/Voting/VotingWeb** uzel v stromovézobrazení a rozbalte uzel oddílu (reprezentovaný identifikátorem GUID).

    ![Škálované služby ve službě Azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-explorer-service-scaled.png)

    Nyní je vidět, že služba má dvě instance, a ve stromovém zobrazení vidíte, na kterých uzlech jsou tyto instance spuštěné.

Touto jednoduchou úlohou správy jste zdvojnásobili prostředky, které má front-end služba k dispozici pro zpracování uživatelské zátěže. Je důležité si uvědomit, že pro spolehlivý provoz služby nepotřebujete více jejích instancí. Pokud služba selže, Service Fabric zajistí v clusteru spuštění nové instance služby.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili:

* Používat Eclipse jako nástroj pro aplikace Service Fabric v Javě.
* Nasazovat aplikace v Javě do místního clusteru.
* Škálování aplikace na více instancí napříč několika uzly

Další informace o práci s aplikacemi v Javě v Service Fabric najdete v kurzu věnovaném aplikacím v Javě.

> [!div class="nextstepaction"]
> [Nasazení aplikace v Javě](./service-fabric-tutorial-create-java-app.md)
