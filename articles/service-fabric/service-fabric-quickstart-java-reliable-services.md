---
title: 'Rychlý Start: Vytvoření aplikace Java v Azure Service Fabric'
description: V tomto rychlém startu vytvoříte aplikaci v Javě pro Azure s využitím ukázkové aplikace Service Fabric Reliable Services.
ms.topic: quickstart
ms.date: 01/29/2019
ms.custom: mvc, devcenter, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 2f1e5f8f73f74d4b427e574b9e6a75aaf84a4211
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91529843"
---
# <a name="quickstart--deploy-a-java-app-to-azure-service-fabric-on-linux"></a>Rychlý Start: nasazení aplikace Java do Azure Service Fabric v systému Linux

V tomto rychlém startu nasadíte aplikaci Java do Azure Service Fabric pomocí integrovaného vývojového prostředí (IDE) v počítači pro vývojáře Linux. Až budete hotovi, budete mít hlasovací aplikaci s webovým front-endem v Javě, která ukládá výsledky hlasování do stavové back-end služby v clusteru.

Azure Service Fabric je platforma distribuovaných systémů pro nasazování a správu mikroslužeb a kontejnerů.

## <a name="prerequisites"></a>Požadavky

- [Prostředí Java](./service-fabric-get-started-linux.md#set-up-java-development) a [Yeoman](./service-fabric-get-started-linux.md#set-up-yeoman-generators-for-containers-and-guest-executables)
- [Neon zatmění (4.6) +](https://www.eclipse.org/downloads/packages/) a [modul plug-in zatmění pro Service Fabric](./service-fabric-get-started-linux.md#install-the-eclipse-plug-in-optional)
- [Sada Service Fabric SDK a rozhraní příkazového řádku (CLI)](./service-fabric-get-started-linux.md#installation-methods)
- [Git](https://git-scm.com/downloads)

## <a name="download-the-sample"></a>Stažení ukázky

V příkazovém okně naklonujte spuštěním následujícího příkazu úložiště ukázkové aplikace do místního počítače.

```bash
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="run-the-application-locally"></a>Aplikaci spustíte místně.

1. Spusťte místní cluster spuštěním následujícího příkazu:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    Spuštění místního clusteru nějakou dobu trvá. Pokud chcete potvrdit, že je cluster plně funkční, přejděte do Service Fabric Exploreru na adrese `http://localhost:19080`. Pět uzlů v pořádku značí, že je místní cluster zprovozněný.

    ![Azure Service Fabric Explorer zobrazuje uzly v pořádku](./media/service-fabric-quickstart-java/service-fabric-explorer-healthy-nodes.png)

2. Otevřete Eclipse.
3. Vyberte **soubor**  >  **Import**  >  **Gradle**  >  **stávající projekt Gradle** a postupujte podle pokynů průvodce.
4. Vyberte **adresář** a zvolte **hlasovací** adresář ze složky **Service-Fabric-Java-Started** , kterou jste naklonoval z GitHubu. Vyberte **Dokončit**.

    ![Importovat projekt Gradle do zatmění](./media/service-fabric-quickstart-java/eclipse-import-gradle-project.png)

5. V průzkumníku balíčků v Eclipse teď máte projekt `Voting`.
6. Klikněte pravým tlačítkem na projekt a vyberte **publikovat aplikaci** pod rozevíracím seznamem **Service Fabric** . Jako cílový profil zvolte **PublishProfiles/Local.js** a vyberte **publikovat**.

    ![Azure Service Fabric publikovat místní JSON](./media/service-fabric-quickstart-java/service-fabric-publish-local-json.png)

7. Otevřete oblíbený webový prohlížeč a přejděte do aplikace na adrese `http://localhost:8080`.

    ![Místní hostitel Azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-local-host.png)

Teď můžete přidat sadu možností hlasování a začít přijímat hlasy. Aplikace je spuštěná a ukládá veškerá data v clusteru Service Fabric, aniž by potřebovala samostatnou databázi.

![Ukázka hlasování v Azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-voting-sample.png)

## <a name="scale-applications-and-services-in-a-cluster"></a>Škálování aplikací a služeb v clusteru

Služby je možné škálovat napříč clusterem a vyřešit tak změny v jejich zatížení. Služby se škálují změnou počtu instancí spuštěných v clusteru. Vaše služby se škálují mnoha způsoby. Můžete například použít skripty nebo příkazy z Service Fabric CLI ( `sfctl` ). V následujících krocích se používá Service Fabric Explorer.

Service Fabric Explorer běží ve všech clusterech Service Fabric a dá se k nim dostat z prohlížeče tak, že přejdete na port pro správu HTTP clusteru (19080). Například, `http://localhost:19080`.

Pokud chcete škálovat webovou front-end službu, postupujte následovně:

1. Otevřete Service Fabric Explorer v clusteru. Například, `https://localhost:19080`.
2. V ovládacím prvku TreeView vyberte tři tečky (**...**) vedle uzlu **Fabric:/hlasovacího/VotingWeb** a vyberte **škálovat službu**.

    ![Škálování služby v Azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-scale-service.png)

    Teď můžete škálovat počet instancí webové front-end služby.

3. Změňte číslo na **2** a vyberte **škálovat službu**.
4. Ve stromovém zobrazení vyberte uzel **Fabric:/hlasovací/VotingWeb** a rozbalte uzel oddílu (REPREZENTOVANý identifikátorem GUID).

    ![Škálování služby ve službě Azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-explorer-service-scaled.png)

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
