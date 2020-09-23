---
title: 'Kurz: použití IntelliJ k nasazení aplikací pro jarní cloudy Azure'
description: Pomocí IntelliJ nasaďte aplikace do Azure jarního cloudu.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/26/2020
ms.custom: devx-track-java
ms.openlocfilehash: e5b8dc325fa7eaceaa0274029049f546db5b6995
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90888533"
---
# <a name="use-intellij-to-deploy-azure-spring-cloud-applications"></a>Použití IntelliJ k nasazování aplikací Azure Spring Cloudu

**Tento článek se týká:** ✔️ Java

Modul plug-in IntelliJ pro jarní cloud Azure podporuje nasazení aplikací z NÁPADu IntelliJ.  

Před spuštěním tohoto příkladu můžete vyzkoušet [základní rychlý Start](spring-cloud-quickstart.md).

## <a name="prerequisites"></a>Požadavky
* [JDK 8 Azul Zulu](https://docs.microsoft.com/java/azure/jdk/java-jdk-install?view=azure-java-stable&preserve-view=true)
* [Maven 3.5.0 +](https://maven.apache.org/download.cgi)
* [IntelliJ nápad, komunita/Ultimate Edition, verze 2020.1/2019.3](https://www.jetbrains.com/idea/download/#section=windows)

## <a name="install-the-plug-in"></a>Instalace modulu plug-in
Můžete přidat Azure Toolkit for IntelliJ nápad 3.35.0 z uživatelského rozhraní **modulů plug-in** IntelliJ.

1. Spusťte IntelliJ.  Pokud jste projekt otevřeli dřív, zavřete projekt, aby se zobrazil uvítací dialog. Vyberte možnost **Konfigurovat** v pravém dolním rohu a potom kliknutím na **moduly plug** -in otevřete dialogové okno Konfigurace modulu plug-in a vyberte možnost **instalovat moduly plug-in z disku**.

    ![Vyberte konfigurovat.](media/spring-cloud-intellij-howto/configure-plugin-1.png)

1. Vyhledejte Azure Toolkit for IntelliJ.  Klikněte na **Install** (Nainstalovat).

    ![Nainstalovat modul plug-in](media/spring-cloud-intellij-howto/install-plugin.png)

1. Klikněte na **restartovat IDE**.

## <a name="tutorial-procedures"></a>Výukové postupy
Následující postupy nasadí Hello World aplikace s využitím NÁPADu IntelliJ.

* Otevřít projekt GS – pružina spuštění
* Nasazení do jarního cloudu Azure
* Zobrazit protokoly streamování

## <a name="open-gs-spring-boot-project"></a>Otevřít projekt GS – pružina spuštění

1. Stáhněte a rozbalte zdrojové úložiště pro tento kurz nebo ho naklonujte pomocí Gitu: git clone https://github.com/spring-guides/gs-spring-boot.git 
1. CD do GS-Spring-boot\complete.
1. Otevřete **uvítací** dialog IntelliJ a výběrem **Importovat projekt** otevřete Průvodce importem.
1. Vyberte `gs-spring-boot\complete` složku.

    ![Importovat projekt](media/spring-cloud-intellij-howto/import-project-1.png)

## <a name="deploy-to-azure-spring-cloud"></a>Nasazení do jarního cloudu Azure
Aby bylo možné nasadit nástroj do Azure, musíte se přihlásit pomocí účtu Azure a vybrat své předplatné.  Podrobnosti o přihlášení najdete v tématu [instalace a přihlášení](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in).

1. V Project Exploreru klikněte pravým tlačítkem na projekt a vyberte **Azure**  ->  **Deploy do Azure jaře Cloud**.

    ![Nasazení do Azure 1](media/spring-cloud-intellij-howto/deploy-to-azure-1.png)

1. V poli **název** přijměte název aplikace. **Název** odkazuje na konfiguraci, nikoli na název aplikace. Uživatelé je obvykle nemusí měnit.
1. Přijměte identifikátor z projektu pro **artefakt**.
1. Vyberte **aplikaci:** pak klikněte na **vytvořit aplikaci...**.

    ![Nasazení do Azure 2](media/spring-cloud-intellij-howto/deploy-to-azure-2.png)

1. Zadejte **název aplikace**a pak klikněte na **OK**.

    ![Nasadit do Azure v pořádku](media/spring-cloud-intellij-howto/deploy-to-azure-2a.png)

1. Spusťte nasazení kliknutím na tlačítko **Spustit** . 

    ![Nasazení do Azure 3](media/spring-cloud-intellij-howto/deploy-to-azure-3.png)

1. Modul plug-in spustí příkaz `mvn package` v projektu a pak vytvoří novou aplikaci a nasadí jar vygenerovanou `package` příkazem.

1. Pokud adresa URL aplikace není zobrazená v okně výstup, Získejte ji z Azure Portal. Přejděte ze skupiny prostředků do instance služby Azure jaře Cloud.  Pak klikněte na **aplikace**.  Bude uvedena běžící aplikace.

    ![Získat testovací adresu URL](media/spring-cloud-intellij-howto/get-test-url.png)

1. V prohlížeči přejděte na adresu URL.

    ![Navigace v prohlížeči 2](media/spring-cloud-intellij-howto/navigate-in-browser-2.png)

## <a name="show-streaming-logs"></a>Zobrazit protokoly streamování
Postup získání protokolů:
1. Vyberte **Azure Explorer**a pak **jarní Cloud**.
1. Klikněte pravým tlačítkem na spuštěnou aplikaci.
1. V rozevíracím seznamu vyberte možnost **protokoly streamování** .

    ![Výběr protokolů streamování](media/spring-cloud-intellij-howto/streaming-logs.png)

1. Vyberte instanci.

    ![Vybrat instanci](media/spring-cloud-intellij-howto/select-instance.png)

1. Protokol streamování se zobrazí v okně výstup.

    ![Výstup protokolu streamování](media/spring-cloud-intellij-howto/streaming-log-output.png)

## <a name="next-steps"></a>Další kroky
* [Příprava aplikace pružiny pro jarní cloud Azure](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-prepare-app-deployment)
* [Další informace o Azure Toolkit for IntelliJ](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/)
