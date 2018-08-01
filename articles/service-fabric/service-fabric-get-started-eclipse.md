---
title: Modul plug-in Azure Service Fabric pro Eclipse | Microsoft Docs
description: Začínáme s modulem plug-in Service Fabric pro Eclipse.
services: service-fabric
documentationcenter: java
author: rapatchi
manager: timlt
editor: ''
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/06/2018
ms.author: rapatchi
ms.openlocfilehash: a9888a23088949b5373aa0eef7d4df3b3064466f
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2018
ms.locfileid: "39358581"
---
# <a name="service-fabric-plug-in-for-eclipse-java-application-development"></a>Modul plug-in Service Fabric pro vývoj aplikací v Eclipse Javě
Eclipse je jedním z nejčastěji používaných integrovaných vývojových prostředí (IDE) pro vývojáře v jazyce Java. V tomto článku probereme možnosti nastavení vývojového prostředí Eclipse pro práci s Azure Service Fabric. Zjistíte, jak v Eclipse nainstalovat modul plug-in Service Fabric, vytvořit aplikaci Service Fabric a nasadit ji do místního nebo vzdáleného clusteru Service Fabric. 

> [!NOTE]
> Modul plug-in Eclipse se v systému Windows aktuálně nepodporuje. 

## <a name="install-or-update-the-service-fabric-plug-in-in-eclipse"></a>Instalace a aktualizace modulu plug-in Service Fabric v Eclipse
Modul plug-in Service Fabric můžete nainstalovat do Eclipse. Tento modul plug-in může zjednodušit proces vytváření a nasazování služeb v Javě.

> [!IMPORTANT]
> Modul plug-in Service Fabric vyžaduje verzi Eclipse Neon nebo novější. Návod k ověření verze Eclipse najdete v pokynech pod touto poznámkou. Pokud máte nainstalovanou starší verzi Eclipse, můžete si stáhnout novější verzi z [webu Eclipse](https://www.eclipse.org). Instalace přes stávající instalaci Eclipse (její přepsání) se nedoporučuje. Před spuštěním instalačního programu ji můžete odebrat nebo můžete novou verzi nainstalovat do jiného adresáře. 
> 
> V Ubuntu doporučujeme provést instalaci přímo z webu Eclipse, a nepoužívat instalační program balíčků (`apt` nebo `apt-get`). Tím zajistíte, že budete mít nejnovější verzi Eclipse. 

Nainstalujte Eclipse Neon nebo novější verzi z [webu Eclipse](https://www.eclipse.org).  Nainstalujte také Buildship verze 2.2.1 nebo novější (modul plug-in Service Fabric není kompatibilní se staršími verzemi Buildshipu):
-   Verze nainstalovaných komponent můžete v Eclipse zkontrolovat v části **Help** > **About Eclipse** > **Installation Details** (Nápověda > O Eclipse > Podrobnosti o instalaci).
-   Pokud chcete aktualizovat Buildship, přečtěte si téma [Eclipse Buildship: Moduly plug-in Eclipse pro Gradle][buildship-update].
-   Pokud chcete zkontrolovat a nainstalovat aktualizace pro Eclipse, přejděte do části **Help** > **Check for Updates** (Nápověda > Vyhledat aktualizace).

Pokud chcete nainstalovat modul plug-in Service Fabric v Eclipse, přejděte do části **Help** > **Install New Software** (Nápověda > Instalace nového softwaru).
1. Do pole **Work with** (Pracovat s) zadejte **http://dl.microsoft.com/eclipse**.
2. Klikněte na tlačítko **Add** (Přidat).

   ![Modul plug-in Service Fabric pro Eclipse][sf-eclipse-plugin-install]
3. Vyberte modul plug-in Service Fabric a potom klikněte na **Další**.
4. Dokončete instalaci a přijměte licenční podmínky pro software společnosti Microsoft.
  
Pokud už máte modul plug-in Service Fabric nainstalovaný, nainstalujte nejnovější verzi. 
1. Pokud chcete vyhledat dostupné aktualizace, přejděte do části **Help** > **About Eclipse** > **Installation Details** (Nápověda > O Eclipse > Podrobnosti o instalaci). 
2. V seznamu nainstalovaných modulů plug-in vyberte Service Fabric a potom klikněte na **Aktualizovat**. Nainstalují se dostupné aktualizace.
3. Jakmile aktualizujete modul plug-in Service Fabric, aktualizujte také projekt Gradle.  Klikněte pravým tlačítkem na **build.gradle** a vyberte **Aktualizovat**.

> [!NOTE]
> Pokud je instalace nebo aktualizace modulu plug-in Service Fabric pomalá, může být důvodem nastavení Eclipse. Eclipse shromažďuje metadata o všech změnách, aby aktualizoval weby, které jsou registrované pro vaši instanci Eclipse. Pokud chcete proces vyhledávání a instalace aktualizace modulu plug-in Service Fabric urychlit, přejděte k části **Available Software Sites** (Dostupné softwarové servery). Zrušte zaškrtnutí políček pro všechny weby kromě políčka odkazujícího na umístění modulu plug-in Service Fabric (http://dl.microsoft.com/eclipse/azure/servicefabric)).

> [!NOTE]
>Pokud Eclipse nefunguje na vašem počítači Mac podle očekávání nebo vyžaduje spuštění od superuživatele, přepněte se do složky **ECLIPSE_INSTALLATION_PATH** a přejděte do podsložky **Eclipse.app/Contents/MacOS**. Spusťte Eclipse spuštěním příkazu `./eclipse`.


## <a name="create-a-service-fabric-application-in-eclipse"></a>Vytvoření aplikace Service Fabric pomocí Eclipse

1.  V Eclipse přejděte do části **File** > **New** > **Other** (Soubor > Nový > Ostatní). Vyberte **Service Fabric Project** (Projekt Service Fabric) a potom klikněte na **Next** (Další).

    ![Nový projekt Service Fabric – stránka 1][create-application/p1]

2.  Zadejte název pro svůj projekt a potom klikněte na **Next** (Další).

    ![Nový projekt Service Fabric – stránka 2][create-application/p2]

3.  V seznamu šablon vyberte **Service Template** (Šablona služby). Vyberte typ šablony služby (Actor, Stateless, Container nebo Guest Binary) a potom klikněte na **Next** (Další).

    ![Nový projekt Service Fabric – stránka 3][create-application/p3]

4.  Zadejte název a podrobnosti služby a potom klikněte na **Finish** (Dokončit).

    ![Nový projekt Service Fabric – stránka 4][create-application/p4]

5. Když vytvoříte svůj první projekt Service Fabric, v dialogovém okně **Open Associated Perspective** Otevřít přidruženou perspektivu) klikněte na **Yes** (Ano).

    ![Nový projekt Service Fabric – stránka 5][create-application/p5]

6.  Nový projekt vypadá takto:

    ![Nový projekt Service Fabric – stránka 6][create-application/p6]

## <a name="build-a-service-fabric-application-in-eclipse"></a>Sestavení aplikace Service Fabric v Eclipse

1.  Klikněte na novou aplikaci Service Fabric pravým tlačítkem a potom vyberte **Service Fabric**.

    ![Nabídka Service Fabric zobrazená po kliknutí pravým tlačítkem][publish/RightClick]

2. V místní nabídce vyberte jednu z následujících možností:
    -   **Build Application** (Sestavit aplikaci) sestaví aplikaci bez vyčištění.
    -   **Rebuild Application** (Znovu sestavit aplikaci) provede vyčištění a nové sestavení aplikace.
    -   **Clean Application** (Vyčistit aplikaci) vyčistí aplikaci od artefaktů sestavení.
     
## <a name="deploy-a-service-fabric-application-to-the-local-cluster-with-eclipse"></a>Nasazení aplikace Service Fabric do místního clusteru pomocí Eclipse

Po vytvoření aplikace Service Fabric, postupujte podle těchto kroků a nasaďte ho do místního clusteru.

1. Pokud jste ještě nespustili místního clusteru, postupujte podle pokynů v [nastavení místního clusteru](./service-fabric-get-started-linux.md#set-up-a-local-cluster) spuštění místního clusteru a ujistěte se, že je spuštěná.
2. Klikněte pravým tlačítkem na aplikaci Service Fabric a potom vyberte **Service Fabric**.

    ![Nabídka Service Fabric zobrazená po kliknutí pravým tlačítkem][publish/RightClick]

3.  V místní nabídce klikněte na tlačítko **nasazení aplikace**.
4.  Můžete sledovat průběh operace nasazení v okně konzoly.
5.  Chcete-li ověřit, že je aplikace spuštěná, otevřete Service Fabric Explorer v místním clusteru v okně prohlížeče [ http://localhost:19080/Explorer ](http://localhost:19080/Explorer). Rozbalte **aplikací** uzlu a ujistěte se, že je aplikace spuštěná. 

Zjistěte, jak ladit aplikaci v prostředí Eclipse pomocí místního clusteru, najdete v článku [ladění služby v Javě v prostředí Eclipse](./service-fabric-debugging-your-application-java.md).

Můžete také nasadit aplikaci do místního clusteru wuth **publikovat aplikaci** příkaz:

1. Klikněte pravým tlačítkem na aplikaci Service Fabric a potom vyberte **Service Fabric**.
2. V místní nabídce klikněte na tlačítko **publikovat aplikaci...** .
3. V **publikovat aplikaci** okně zvolte **PublishProfiles/Local.json** cílový profil a klikněte na **publikovat**.

    ![Dialogové okno publikování – místní prostředí](./media/service-fabric-get-started-eclipse/localjson.png)

    Ve výchozím nastavení Local.json publikování profilu nastavení publikování do místního clusteru. Další informace o parametrech připojení a koncového bodu, které jsou k dispozici v profilech publikování najdete v další části.

## <a name="publish-your-service-fabric-application-to-azure-with-eclipse"></a>Publikování aplikace Service Fabric do Azure pomocí Eclipse

Můžete publikovat svoji aplikaci do cloudu, postupujte podle těchto kroků:

1. Můžete publikovat svoji aplikaci k zabezpečenému clusteru v cloudu, budete potřebovat certifikát X.509 používat ke komunikaci s vaším clusterem. V testovací a vývojová prostředí je certifikát používaný často certifikát clusteru. V produkčním prostředí certifikát by měl být klientský certifikát, který se liší od certifikátu clusteru. Budete potřebovat certifikát a privátní klíč. Soubor certifikátu (a klíč) musí být ve formátu PEM. Můžete vytvořit soubor PEM, který obsahuje certifikát a privátní klíč ze souboru PFX pomocí následujícího příkazu openssl:

    ```bash
    openssl pkcs12 -in your-cert-file.pfx -out your-cert-file.pem -nodes -passin pass:your-pfx-password
    ```

   Pokud není soubor PFX chráněný heslem, použijte `--passin pass:` pro poslední parametr.

2. Otevřít **Cloud.json** soubor **PublishProfiles** adresáře. Musíte nakonfigurovat koncový bod a zabezpečení přihlašovací údaje clusteru správně pro váš cluster.

   - `ConnectionIPOrURL` Pole obsahuje IP adresu nebo adresy URL vašeho clusteru. Všimněte si, že hodnota neobsahuje schéma adresy URL (`https://`).
   - Ve výchozím nastavení `ConnectionPort` pole by mělo být `19080`, pokud výslovně nezměníte tento port pro váš cluster.
   - `ClientKey` Pole by měla odkazovat na ve formátu PEM .pem nebo .key soubor na místním počítači, který obsahuje privátní klíč certifikátu klienta nebo v clusteru.
   - `ClientCert` Pole by měl přejděte formátem PEM soubor .pem nebo CRT v místním počítači, který obsahuje data certifikátu klienta nebo clusteru. certifikát. 

    ```bash
    {
         "ClusterConnectionParameters":
         {
            "ConnectionIPOrURL": "lnxxug0tlqm5.westus.cloudapp.azure.com",
            "ConnectionPort": "19080",
            "ClientKey": "[path_to_your_pem_file_on_local_machine]",
            "ClientCert": "[path_to_your_pem_file_on_local_machine]"
         }
    }
    ```

2. Klikněte pravým tlačítkem na aplikaci Service Fabric a potom vyberte **Service Fabric**.
3. V místní nabídce klikněte na tlačítko **publikovat aplikaci...** .
3. V **publikovat aplikaci** okně zvolte **PublishProfiles/Cloud.json** cílový profil a klikněte na **publikovat**.

    ![Dialogové okno publikování – cloud](./media/service-fabric-get-started-eclipse/cloudjson.png)

4.  Můžete sledovat průběh operace publikování v okně konzoly.
5.  Pokud chcete ověřit, že je aplikace spuštěná, otevřete Service Fabric Explorer na clusteru Azure v okně prohlížeče. Pro výše uvedeném příkladu by toto byl: `https://lnxxug0tlqm5.westus.cloudapp.azure.com:19080/Explorer`. Rozbalte **aplikací** uzlu a ujistěte se, že je aplikace spuštěná. 


Na zabezpečené clusterech s Linuxem Pokud vaše aplikace obsahuje služby Reliable Services je také potřeba nakonfigurovat certifikát, který vaše služby můžete použít k volání rozhraní API modulu runtime Service Fabric. Další informace najdete v tématu [konfigurace aplikace Reliable Services ke spuštění na clusterech s Linuxem](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters).

Rychlé procházení pomocí toho, jak nasadit aplikaci Service Fabric Reliable Services napsané v jazyce Java do zabezpečeného clusteru s Linuxem, naleznete v tématu [Quckstart: nasazení aplikace Reliable Services v Javě](./service-fabric-quickstart-java-reliable-services.md).

## <a name="deploy-a-service-fabric-application-by-using-eclipse-run-configurations"></a>Nasazení aplikace Service Fabric pomocí konfigurací spuštění Eclipse

Existuje alternativní způsob nasazení aplikace Service Fabric pomocí konfigurací spuštění Eclipse.

1. V Eclipse, přejděte na **spustit** > **konfigurace spuštění**.
2. Vyberte konfiguraci spuštění **ServiceFabricDeployer** v části **Gradle Project** (Projekt Gradle).
3. V pravém podokně klikněte na **argumenty** kartu, ujistěte se, že **ip**, **port**, **clientCert**, a **clientKey**parametry nastavené odpovídajícím způsobem pro vaše nasazení. Ve výchozím nastavení parametry jsou sadě pro nasazení do místního clusteru jako na následujícím snímku obrazovky. K publikování aplikace do Azure můžete změnit parametry obsahuje podrobnosti o koncovém bodu a zabezpečovacích přihlašovacích údajů pro váš cluster Azure. Další informace najdete v předchozí části [publikovat aplikaci Service Fabric do Azure pomocí Eclipse](#publish-your-service-fabric-application-to-azure-with-eclipse).

    ![Místní dialogové okno Konfigurace spuštění](./media/service-fabric-get-started-eclipse/run-config-local.png)

5. Ujistěte se, že **pracovní adresář** odkazuje na aplikaci, kterou chcete nasadit. Pokud chcete aplikaci změnit, stačí kliknout na **Workspace** (Pracovní prostor) a vybrat požadovanou aplikaci.
6. Klikněte na **Apply** (Použít) a potom na **Run** (Spustit).

Vaše aplikace se během chvilky sestaví a nasadí. Stav nasazení můžete sledovat v Service Fabric Exploreru.  

## <a name="add-a-service-fabric-service-to-your-service-fabric-application"></a>Přidání služby Service Fabric do aplikace Service Fabric

Přidat službu Service Fabric do stávající aplikace Service Fabric je možné pomocí následujících kroků:

1.  Klikněte pravým tlačítkem na projekt, ke kterému chcete přidat službu, a potom klikněte na **Service Fabric**.

    ![Přidat službu Service Fabric – stránka 1][add-service/p1]

2.  Klikněte na **Add Service Fabric Service** (Přidat službu Service Fabric) a dokončete kroky pro přidání služby k tomuto projektu.
3.  Vyberte šablonu služby, kterou chcete přidat do projektu, a potom klikněte na **Next** (Další).

    ![Přidat službu Service Fabric – stránka 2][add-service/p2]

4.  Zadejte název služby (a další podrobnosti podle potřeby) a potom klikněte na **Add Service** (Přidat službu).  

    ![Přidat službu Service Fabric – stránka 3][add-service/p3]

5.  Celková struktura projektu po přidání služby vypadá podobně jako u tohoto projektu:

    ![Přidat službu Service Fabric – stránka 4][add-service/p4]

## <a name="edit-manifest-versions-of-your-service-fabric-java-application"></a>Úprava verzí manifestu aplikace Service Fabric v jazyce Java

Pokud chcete upravit verze manifestu, klikněte pravým tlačítkem na projekt, přejděte na **Service Fabric** a z rozevírací nabídky vyberte **Upravit verze manifestu...**. V průvodci můžete aktualizovat verze manifestu pro manifest aplikace, manifest služby a verze pro balíčky **Code**, **Config** a **Data**.

Pokud zaškrtnete políčko **Automaticky aktualizovat verze aplikací a služeb** a pak aktualizujete verzi, verze manifestu se zaktualizují automaticky. Když například nejprve zaškrtnete toto políčko a pak zaktualizujete verzi pro verzi **Code** z 0.0.0 na 0.0.1 a klikněte na **Dokončit**, verze manifestu služby a verze manifestu aplikace se automaticky aktualizují na 0.0.1.

## <a name="upgrade-your-service-fabric-java-application"></a>Upgrade aplikace Service Fabric v Javě

Pro scénář upgradu předpokládejme, že jste pomocí modulu plug-in Service Fabric v Eclipse vytvořili projekt **App1**. Pomocí modulu plug-in jste ho nasadili a vytvořili aplikaci s názvem **fabric: / App1Application**. Tato aplikace je typu **App1AppicationType** a verze této aplikace je 1.0. Nyní chcete provést upgrade této aplikace bez přerušení dostupnosti.

Nejdřív proveďte úpravy aplikace a znovu sestavte upravenou službu. Aktualizujte soubor manifestu upravené služby (ServiceManifest.xml) s použitím aktualizovaných verzí pro službu (a podle potřeby i verzí kódu, konfigurace nebo dat). Dál upravte manifest aplikace (ApplicationManifest.xml) s použitím čísla aktualizované verze pro aplikaci a upravenou službu.  

Pokud chcete k upgradu aplikace použít Eclipse, můžete vytvořit duplicitní profil konfigurace spuštění. Potom ho podle potřeby využijte k upgradu vaší aplikace.

1.  Přejděte k části **Run** > **Run Configurations** (Spustit > Konfigurace spuštění). v levém podokně klikněte na malou šipku nalevo od možnosti **Gradle Project** (Projekt Gradle).
2.  Klikněte pravým tlačítkem na **ServiceFabricDeployer** a potom vyberte **Duplicate** (Duplikovat). Zadejte nový název pro tuto konfiguraci, třeba **ServiceFabricUpgrader**.
3.  Na pravém panelu na kartě **Arguments** (Argumenty) změňte **-Pconfig='deploy'** na **-Pconfig=upgrade** a potom klikněte na **Apply** (Použít).

Tento proces vytvoří a uloží profil konfigurace spuštění, který můžete kdykoliv použít k upgradu vaší aplikace. Postará se také o získání nejnovější aktualizované verze typu aplikace ze souboru manifestu aplikace.

Upgrade aplikace trvá několik minut. Průběh upgradu aplikace můžete monitorovat pomocí Service Fabric Exploreru.

## <a name="migrating-old-service-fabric-java-applications-to-be-used-with-maven"></a>Migrace starých aplikací Service Fabric Java pro použití s Mavenem
Nedávno jsme přesunuli knihovny Service Fabric Java ze sady Service Fabric Java SDK do úložiště Maven. Zatímco nové aplikace, které vygenerujete pomocí Eclipse, vygenerují nejnovější aktualizované projekty (které budou moct pracovat s Mavenem), můžete vaše stávající bezstavové aplikace nebo aplikace objektu actor v Javě pro Service Fabric, které dřív používali sadu Service Fabric Java SDK, aktualizovat tak, aby používaly závislosti Service Fabric Java z Mavenu. Abyste zajistili fungování starších aplikací s Mavenem, postupujte podle kroků uvedených [tady](service-fabric-migrate-old-javaapp-to-use-maven.md).

## <a name="next-steps"></a>Další postup

- Rychlé kroky na vytváření spolehlivých Java aplikace služby a jeho nasazení místně a do Azure, najdete v článku [Quckstart: nasazení aplikace Reliable Services v Javě](./service-fabric-quickstart-java-reliable-services.md).
- Informace o ladění aplikace v Javě v místním clusteru najdete v tématu [ladění služby v Javě v prostředí Eclipse](./service-fabric-debugging-your-application-java.md).
- Informace o monitorování a Diagnostika aplikace Service Fabric najdete v tématu [monitorování a Diagnostika služeb v instalačním programu místním počítači vývoje](./service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).

<!-- Images -->

[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-eclipse/service-fabric-eclipse-plugin.png

[create-application/p1]:./media/service-fabric-get-started-eclipse/create-application/p1.png
[create-application/p2]:./media/service-fabric-get-started-eclipse/create-application/p2.png
[create-application/p3]:./media/service-fabric-get-started-eclipse/create-application/p3.png
[create-application/p4]:./media/service-fabric-get-started-eclipse/create-application/p4.png
[create-application/p5]:./media/service-fabric-get-started-eclipse/create-application/p5.png
[create-application/p6]:./media/service-fabric-get-started-eclipse/create-application/p6.png

[publish/Publish]: ./media/service-fabric-get-started-eclipse/publish/Publish.png
[publish/RightClick]: ./media/service-fabric-get-started-eclipse/publish/RightClick.png

[add-service/p1]: ./media/service-fabric-get-started-eclipse/add-service/p1.png
[add-service/p2]: ./media/service-fabric-get-started-eclipse/add-service/p2.png
[add-service/p3]: ./media/service-fabric-get-started-eclipse/add-service/p3.png
[add-service/p4]: ./media/service-fabric-get-started-eclipse/add-service/p4.png

<!-- Links -->
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship
