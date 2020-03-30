---
title: Modul plug-in Azure Service Fabric pro eclipse
description: Přečtěte si o tom, jak začít pracovat s Azure Service Fabric v Javě pomocí eclipse a service fabric za předpokladu, plug-in.
author: rapatchi
ms.topic: conceptual
ms.date: 04/06/2018
ms.author: rapatchi
ms.openlocfilehash: b779873488f1fff754d4105249b28f545738c11b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258418"
---
# <a name="service-fabric-plug-in-for-eclipse-java-application-development"></a>Modul plug-in Service Fabric pro vývoj aplikací v Eclipse Javě
Eclipse je jedním z nejčastěji používaných integrovaných vývojových prostředí (IDE) pro vývojáře v jazyce Java. V tomto článku probereme možnosti nastavení vývojového prostředí Eclipse pro práci s Azure Service Fabric. Zjistíte, jak v Eclipse nainstalovat modul plug-in Service Fabric, vytvořit aplikaci Service Fabric a nasadit ji do místního nebo vzdáleného clusteru Service Fabric. 

> [!NOTE]
> Modul plug-in Eclipse se v systému Windows aktuálně nepodporuje. 

> [!IMPORTANT]
> Ujistěte se, že JDK 8 je nainstalován v systému a vybrán v Eclipse.

## <a name="install-or-update-the-service-fabric-plug-in-in-eclipse"></a>Instalace a aktualizace modulu plug-in Service Fabric v Eclipse
Modul plug-in Service Fabric můžete nainstalovat do Eclipse. Tento modul plug-in může zjednodušit proces vytváření a nasazování služeb v Javě.

> [!IMPORTANT]
> Modul plug-in Service Fabric vyžaduje verzi Eclipse Neon nebo novější. Návod k ověření verze Eclipse najdete v pokynech pod touto poznámkou. Pokud máte nainstalovanou starší verzi Eclipse, můžete si stáhnout novější verzi z [webu Eclipse](https://www.eclipse.org). Instalace přes stávající instalaci Eclipse (její přepsání) se nedoporučuje. Před spuštěním instalačního programu ji můžete odebrat nebo můžete novou verzi nainstalovat do jiného adresáře. 
> 
> V Ubuntu doporučujeme provést instalaci přímo z webu Eclipse, a nepoužívat instalační program balíčků (`apt` nebo `apt-get`). Tím zajistíte, že budete mít nejnovější verzi Eclipse. 

Nainstalujte Eclipse Neon nebo novější verzi z [webu Eclipse](https://www.eclipse.org).  Nainstalujte také Buildship verze 2.2.1 nebo novější (modul plug-in Service Fabric není kompatibilní se staršími verzemi Buildshipu):
-   Chcete-li zkontrolovat verze nainstalovaných součástí, v části Eclipse najdete v **části Nápověda** > **k podrobnostem instalace****eclipse** > .
-   Pokud chcete aktualizovat Buildship, přečtěte si téma [Eclipse Buildship: Moduly plug-in Eclipse pro Gradle][buildship-update].
-   Chcete-li vyhledat a nainstalovat aktualizace programu Eclipse, přejděte na odkaz Vyhledat aktualizace **v nápovědě** > **Check for Updates**.

Nainstalujte modul plug-in Service Fabric v části Eclipse **napomoc při** > **instalaci nového softwaru**.
1. Do pole **Práce s** zadejte https:\//dl.microsoft.com/eclipse.
2. Klikněte na **Přidat**.

   ![Modul plug-in Service Fabric pro Eclipse][sf-eclipse-plugin-install]
3. Vyberte modul plug-in Service Fabric a potom klikněte na **Další**.
4. Dokončete instalaci a přijměte licenční podmínky pro software společnosti Microsoft.
  
Pokud už máte modul plug-in Service Fabric nainstalovaný, nainstalujte nejnovější verzi. 
1. Dostupné aktualizace naleznete v **nápovědě k** > **podrobnostem instalace****programu Eclipse** > . 
2. V seznamu nainstalovaných modulů plug-in vyberte Service Fabric a potom klikněte na **Aktualizovat**. Nainstalují se dostupné aktualizace.
3. Jakmile aktualizujete modul plug-in Service Fabric, aktualizujte také projekt Gradle.  Klikněte pravým tlačítkem na **build.gradle** a vyberte **Aktualizovat**.

> [!NOTE]
> Pokud je instalace nebo aktualizace modulu plug-in Service Fabric pomalá, může být důvodem nastavení Eclipse. Eclipse shromažďuje metadata o všech změnách, aby aktualizoval weby, které jsou registrované pro vaši instanci Eclipse. Pokud chcete proces vyhledávání a instalace aktualizace modulu plug-in Service Fabric urychlit, přejděte k části **Available Software Sites** (Dostupné softwarové servery). Zrušte zaškrtnutí políček pro všechny weby s výjimkou webu, který\/odkazuje na umístění modulu plug-in Service Fabric (https: /dl.microsoft.com/eclipse/azure/servicefabric).

> [!NOTE]
>Pokud Eclipse nefunguje na vašem počítači Mac podle očekávání nebo vyžaduje spuštění od superuživatele, přepněte se do složky **ECLIPSE_INSTALLATION_PATH** a přejděte do podsložky **Eclipse.app/Contents/MacOS**. Spusťte Eclipse spuštěním příkazu `./eclipse`.


## <a name="create-a-service-fabric-application-in-eclipse"></a>Vytvoření aplikace Service Fabric pomocí Eclipse

1.  V eclipse, přejděte na **soubor** > **nové** > **jiné**. Vyberte **Service Fabric Project** (Projekt Service Fabric) a potom klikněte na **Next** (Další).

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

## <a name="build-a-service-fabric-application-in-eclipse"></a>Vytvoření aplikace Service Fabric v Eclipse

1.  Klikněte na novou aplikaci Service Fabric pravým tlačítkem a potom vyberte **Service Fabric**.

    ![Nabídka Service Fabric zobrazená po kliknutí pravým tlačítkem][publish/RightClick]

2. V místní nabídce vyberte jednu z následujících možností:
    -   **Build Application** (Sestavit aplikaci) sestaví aplikaci bez vyčištění.
    -   **Rebuild Application** (Znovu sestavit aplikaci) provede vyčištění a nové sestavení aplikace.
    -   **Clean Application** (Vyčistit aplikaci) vyčistí aplikaci od artefaktů sestavení.
     
## <a name="deploy-a-service-fabric-application-to-the-local-cluster-with-eclipse"></a>Nasazení aplikace Service Fabric do místního clusteru s Eclipse

Po vytvoření aplikace Service Fabric, postupujte podle následujících kroků k nasazení do místního clusteru.

1. Pokud jste místní cluster nespustili, postupujte podle pokynů [v části Nastavení místního clusteru](./service-fabric-get-started-linux.md#set-up-a-local-cluster) a spusťte místní cluster a ujistěte se, že je spuštěný.
2. Klepněte pravým tlačítkem myši na aplikaci Service Fabric a vyberte příkaz **Service Fabric**.

    ![Nabídka Service Fabric zobrazená po kliknutí pravým tlačítkem][publish/RightClick]

3.  V místní nabídce klikněte na **nasadit aplikaci**.
4.  Průběh operace nasazení můžete sledovat v okně konzoly.
5.  Chcete-li ověřit, zda je aplikace spuštěná, otevřete [http://localhost:19080/Explorer](http://localhost:19080/Explorer)v místním clusteru v okně prohlížeče aplikaci Service Fabric Explorer . Rozbalte uzel **Aplikace** a ujistěte se, že je aplikace spuštěná. 

Informace o ladění aplikace v aplikaci Eclipse pomocí místního clusteru naleznete [v tématu Ladění služby Java v aplikaci Eclipse](./service-fabric-debugging-your-application-java.md).

Aplikaci můžete také nasadit do místního clusteru pomocí příkazu **Publikovat aplikaci:**

1. Klepněte pravým tlačítkem myši na aplikaci Service Fabric a vyberte příkaz **Service Fabric**.
2. V místní nabídce klikněte na **Publikovat aplikaci...**.
3. V okně **Publikovat aplikaci** zvolte **PublishProfiles/Local.json** jako cílový profil a klepněte na tlačítko **Publikovat**.

    ![Dialogové okno publikování – místní prostředí](./media/service-fabric-get-started-eclipse/localjson.png)

    Ve výchozím nastavení je profil publikování Local.json nastaven tak, aby se publikoval v místním clusteru. Další informace o parametrech připojení a koncového bodu v profilech publikování naleznete v další části.

## <a name="publish-your-service-fabric-application-to-azure-with-eclipse"></a>Publikování aplikace Service Fabric do Azure s Eclipse

Pokud chcete publikovat aplikaci do cloudu, postupujte takto:

1. Chcete-li publikovat aplikaci do zabezpečeného clusteru v cloudu, potřebujete certifikát X.509, který chcete použít ke komunikaci s clusterem. V testovacích a vývojových prostředích je použitým certifikátem často certifikát clusteru. V produkčním prostředí by měl být certifikát klientským certifikátem, který se liší od certifikátu clusteru. Potřebujete certifikát i soukromý klíč. Soubor certifikátu (a klíče) musí být ve formátu PEM. Můžete vytvořit soubor PEM, který obsahuje certifikát a soukromý klíč ze souboru PFX, pomocí následujícího příkazu openssl:

    ```bash
    openssl pkcs12 -in your-cert-file.pfx -out your-cert-file.pem -nodes -passin pass:your-pfx-password
    ```

   Pokud soubor PFX není chráněn `--passin pass:` heslem, použijte pro poslední parametr.

2. Otevřete soubor **Cloud.json** v adresáři **PublishProfiles.** Je třeba odpovídajícím způsobem nakonfigurovat koncový bod clusteru a pověření zabezpečení pro váš cluster.

   - Toto `ConnectionIPOrURL` pole obsahuje ADRESU IP nebo adresu URL clusteru. Všimněte si, že hodnota neobsahuje schéma adresy URL (`https://`).
   - Ve výchozím `ConnectionPort` nastavení `19080`by pole mělo být , pokud jste explicitně nezměnili tento port pro cluster.
   - Pole `ClientKey` by mělo směřovat k souboru PEM nebo .key ve formátu PEM v místním počítači, který obsahuje soukromý klíč pro klientský nebo clusterový certifikát.
   - Pole `ClientCert` by mělo v místním počítači s odkazem na soubor PEM ve formátu PEM nebo CRT, který obsahuje data certifikátu pro klienta nebo cluster. Certifikát. 

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

2. Klepněte pravým tlačítkem myši na aplikaci Service Fabric a vyberte příkaz **Service Fabric**.
3. V místní nabídce klikněte na **Publikovat aplikaci...**.
3. V okně **Publikovat aplikaci** zvolte **PublishProfiles/Cloud.json** jako cílový profil a klepněte na tlačítko **Publikovat**.

    ![Dialogové okno publikování – cloud](./media/service-fabric-get-started-eclipse/cloudjson.png)

4. Průběh operace publikování můžete sledovat v okně konzoly.
5. Chcete-li ověřit, že vaše aplikace běží, otevřete Service Fabric Explorer v clusteru Azure v okně prohlížeče. Pro výše uvedený příklad by `https://lnxxug0tlqm5.westus.cloudapp.azure.com:19080/Explorer`to bylo: . Rozbalte uzel **Aplikace** a ujistěte se, že je aplikace spuštěná. 


V zabezpečených clusterech Linux, pokud vaše aplikace obsahuje služby spolehlivé služby, budete také muset nakonfigurovat certifikát, který vaše služby můžete použít k volání Service Fabric runtime rozhraní API. Další informace najdete [v tématu Konfigurace aplikace Spolehlivé služby pro spuštění v clusterech Linux](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters).

Pro rychlou procházku, jak nasadit service fabric spolehlivé služby aplikace napsaná v Jazyce Java do zabezpečeného clusteru Linux, naleznete v [tématu Úvodní příručka: Nasazení java spolehlivé služby aplikace](./service-fabric-quickstart-java-reliable-services.md).

## <a name="deploy-a-service-fabric-application-by-using-eclipse-run-configurations"></a>Nasazení aplikace Service Fabric pomocí konfigurace spuštění eclipse

Existuje alternativní způsob nasazení aplikace Service Fabric pomocí konfigurací spuštění Eclipse.

1. V eclipse přejděte na **Spustit** > **konfigurace spuštění**.
2. Vyberte konfiguraci spuštění **ServiceFabricDeployer** v části **Gradle Project** (Projekt Gradle).
3. V pravém podokně na kartě Argumenty zkontrolujte, zda jsou **parametry** **IP**, **port**, **clientCert**a **clientKey** pro vaše nasazení nastaveny odpovídajícím způsobem. Ve výchozím nastavení jsou parametry nastaveny na nasazení do místního clusteru jako na následujícím snímku obrazovky. Chcete-li publikovat aplikaci do Azure, můžete upravit parametry tak, aby obsahovaly podrobnosti o koncovém bodu a pověření zabezpečení pro váš cluster Azure. Další informace najdete v předchozí části [Publikování aplikace Service Fabric do Azure s Eclipse](#publish-your-service-fabric-application-to-azure-with-eclipse).

    ![Spustit dialogové okno konfigurace místní](./media/service-fabric-get-started-eclipse/run-config-local.png)

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

Pro scénář upgradu předpokládejme, že jste pomocí modulu plug-in Service Fabric v Eclipse vytvořili projekt **App1**. Pomocí modulu plug-in jste ho nasadili a vytvořili aplikaci s názvem **fabric: / App1Application**. Typ aplikace je **App1ApplicationType**a verze aplikace je 1.0. Nyní chcete provést upgrade této aplikace bez přerušení dostupnosti.

Nejdřív proveďte úpravy aplikace a znovu sestavte upravenou službu. Aktualizujte soubor manifestu upravené služby (ServiceManifest.xml) s použitím aktualizovaných verzí pro službu (a podle potřeby i verzí kódu, konfigurace nebo dat). Dál upravte manifest aplikace (ApplicationManifest.xml) s použitím čísla aktualizované verze pro aplikaci a upravenou službu.  

Pokud chcete k upgradu aplikace použít Eclipse, můžete vytvořit duplicitní profil konfigurace spuštění. Potom ho podle potřeby využijte k upgradu vaší aplikace.

1.  Přejděte na **spustit** > **konfiguraci spuštění**. v levém podokně klikněte na malou šipku nalevo od možnosti **Gradle Project** (Projekt Gradle).
2.  Klikněte pravým tlačítkem na **ServiceFabricDeployer** a potom vyberte **Duplicate** (Duplikovat). Zadejte nový název pro tuto konfiguraci, třeba **ServiceFabricUpgrader**.
3.  Na pravém panelu na kartě **Arguments** (Argumenty) změňte **-Pconfig='deploy'** na **-Pconfig=upgrade** a potom klikněte na **Apply** (Použít).

Tento proces vytvoří a uloží profil konfigurace spuštění, který můžete kdykoliv použít k upgradu vaší aplikace. Postará se také o získání nejnovější aktualizované verze typu aplikace ze souboru manifestu aplikace.

Upgrade aplikace trvá několik minut. Průběh upgradu aplikace můžete monitorovat pomocí Service Fabric Exploreru.

## <a name="migrating-old-service-fabric-java-applications-to-be-used-with-maven"></a>Migrace starých aplikací Service Fabric Java pro použití s Mavenem
Nedávno jsme přesunuli knihovny Service Fabric Java ze sady Service Fabric Java SDK do úložiště Maven. Zatímco nové aplikace, které vygenerujete pomocí Eclipse, vygenerují nejnovější aktualizované projekty (které budou moct pracovat s Mavenem), můžete vaše stávající bezstavové aplikace nebo aplikace objektu actor v Javě pro Service Fabric, které dřív používali sadu Service Fabric Java SDK, aktualizovat tak, aby používaly závislosti Service Fabric Java z Mavenu. Abyste zajistili fungování starších aplikací s Mavenem, postupujte podle kroků uvedených [tady](service-fabric-migrate-old-javaapp-to-use-maven.md).

## <a name="next-steps"></a>Další kroky

- Rychlé kroky při vytváření aplikace služby Java Reliable a jejich nasazení místně a do Azure najdete v [tématu Úvodní příručka: Nasazení aplikace spolehlivých služeb Java](./service-fabric-quickstart-java-reliable-services.md).
- Informace o ladění aplikace Jazyka Java v místním clusteru naleznete [v tématu Ladění služby Java v aplikaci Eclipse](./service-fabric-debugging-your-application-java.md).
- Informace o monitorování a diagnostice aplikací Service Fabric naleznete [v tématu Sledování a diagnostika služeb v nastavení vývoje místního počítače](./service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).

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
