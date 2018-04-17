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
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/21/2016
ms.author: rapatchi
ms.openlocfilehash: d415c3eb540056dc7ad6f1ab14fc8250903d6744
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
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

1.  Ujistěte se, že používáte verzi Eclipse Neon nebo novější a že máte nainstalovanou nejnovější verzi Buildship (1.0.17 nebo novější):
    -   Verze nainstalovaných komponent můžete v Eclipse zkontrolovat v části **Help** > **Installation Details** (Nápověda > Podrobnosti o instalaci).
    -   Pokud chcete aktualizovat Buildship, přečtěte si téma [Eclipse Buildship: Moduly plug-in Eclipse pro Gradle][buildship-update].
    -   Pokud chcete zkontrolovat a nainstalovat aktualizace pro Eclipse, přejděte do části **Help** > **Check for Updates** (Nápověda > Vyhledat aktualizace).

2.  Pokud chcete nainstalovat modul plug-in Service Fabric v Eclipse, přejděte do části **Help** > **Install New Software** (Nápověda > Instalace nového softwaru).
  1.    Do pole **Work with** (Pracovat s) zadejte **http://dl.microsoft.com/eclipse**.
  2.    Klikněte na tlačítko **Add** (Přidat).

         ![Modul plug-in Service Fabric pro Eclipse][sf-eclipse-plugin-install]
  3.    Vyberte modul plug-in Service Fabric a potom klikněte na **Další**.
  4.    Dokončete instalaci a přijměte licenční podmínky pro software společnosti Microsoft.

Pokud už máte modul plug-in Service Fabric nainstalovaný, ověřte, že používáte nejnovější verzi. Pokud chcete vyhledat dostupné aktualizace, přejděte k části **Help** > **Installation Details** (Nápověda => Podrobnosti o instalaci). V seznamu nainstalovaných modulů plug-in vyberte Service Fabric a potom klikněte na **Aktualizovat**. Nainstalují se dostupné aktualizace.

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

## <a name="build-and-deploy-a-service-fabric-application-in-eclipse"></a>Vytvoření a nasazení aplikace Service Fabric v Eclipse

1.  Klikněte na novou aplikaci Service Fabric pravým tlačítkem a potom vyberte **Service Fabric**.

    ![Nabídka Service Fabric zobrazená po kliknutí pravým tlačítkem][publish/RightClick]

2. V podnabídce vyberte požadovanou možnost:
    -   **Build Application** (Sestavit aplikaci) sestaví aplikaci bez vyčištění.
    -   **Rebuild Application** (Znovu sestavit aplikaci) provede vyčištění a nové sestavení aplikace.
    -   **Clean Application** (Vyčistit aplikaci) vyčistí aplikaci od artefaktů sestavení.

3.  V této nabídce můžete zvolit také nasazení, zrušení nasazení nebo publikování aplikace:
    -   **Deploy Application** (Nasadit aplikaci) nasadí aplikaci na místní cluster.
    -   V dialogovém okně **Publish Application** (Publikovat aplikaci) vyberte profil publikování:
        -  **Local.json**
        -  **Cloud.json**

     Tyto soubory JSON (JavaScript Object Notation) se používají k uložení informací (jako jsou koncové body připojení a informace o zabezpečení) potřebných pro připojení k místnímu nebo cloudovému (Azure) clusteru.

  ![Nabídka Publish (Publikovat) v Service Fabric][publish/Publish]

Existuje alternativní způsob nasazení aplikace Service Fabric pomocí konfigurací spuštění Eclipse.

  1.    Přejděte k části **Run** > **Run Configurations** (Spustit > Konfigurace spuštění).
  2.    Vyberte konfiguraci spuštění **ServiceFabricDeployer** v části **Gradle Project** (Projekt Gradle).
  3.    Na kartě **Arguments** (Argumenty) v pravém podokně jako **publishProfile** (Profil publikování) zadejte **local** (místní) nebo **cloud** (cloudový).  Výchozí hodnota je **local** (místní). Pro nasazení na vzdálený nebo cloudový cluster vyberte **cloud** (cloudový).
  4.    Aby se zajistilo, že v profilech publikování jsou zadané správné informace, upravte **Local.json** nebo **Cloud.json** podle potřeby. Můžete přidat nebo aktualizovat podrobnosti o koncových bodech a zabezpečovací přihlašovací údaje.
  5.    Ujistěte se, že **Working Directory** (Pracovní adresář) odkazuje na aplikaci, kterou chcete nasadit. Pokud chcete aplikaci změnit, stačí kliknout na **Workspace** (Pracovní prostor) a vybrat požadovanou aplikaci.
  6.    Klikněte na **Apply** (Použít) a potom na **Run** (Spustit).

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
