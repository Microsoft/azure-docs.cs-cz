---
title: Kurz – nasazení aplikací Azure Spring Cloud pomocí IntelliJ
description: Použití IntelliJ k nasazení aplikací do Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/26/2020
ms.openlocfilehash: 8e473a5692c3fa2be3b0f2d823d2a36ba768d661
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731361"
---
# <a name="use-intellij-to-deploy-azure-spring-cloud-applications"></a>Použití IntelliJ k nasazování aplikací Azure Spring Cloudu
Modul plug-in IntelliJ pro Azure Spring Cloud podporuje nasazení aplikací z IntelliJ IDEA.  

## <a name="prerequisites"></a>Požadavky
* [JDK 8 Azul Zulu](https://docs.microsoft.com/java/azure/jdk/java-jdk-install?view=azure-java-stable)
* [Maven 3.5.0+](https://maven.apache.org/download.cgi)
* [IntelliJ IDEA, Community/Ultimate Edition, verze 2020.1/2019.3](https://www.jetbrains.com/idea/download/#section=windows)

## <a name="install-the-plug-in"></a>Instalace modulu plug-in
Sadu nástrojů Azure Toolkit for IntelliJ IDEA 3.35.0 **Plugins** můžete přidat z uj.

1. Spusťte IntelliJ.  Pokud jste dříve otevřeli projekt, zavřete projekt a zobrazte uvítací dialog. Vyberte **Konfigurovat** z odkazu vpravo dole a pak klepnutím na **tlačítko Moduly plug-in** otevřete dialogové okno konfigurace modulu plug-in a vyberte **Instalovat moduly plug-in z disku**.

    ![Vyberte Konfigurovat](media/spring-cloud-intellij-howto/configure-plugin-1.png)

1. Vyhledejte sadu nástrojů Azure pro IntelliJ.  Klepněte na tlačítko **Instalovat**.

    ![Instalace pluginu](media/spring-cloud-intellij-howto/install-plugin.png)

1. Klepněte na **tlačítko Restartovat službu IDE**.

## <a name="tutorial-procedures"></a>Výukové postupy
Následující postupy nasadit hello world aplikace pomocí IntelliJ IDEA.

* Otevřený projekt gs-spring-boot
* Nasazení do Azure Spring Cloud
* Zobrazit protokoly datových proudů

## <a name="open-gs-spring-boot-project"></a>Otevřený projekt gs-spring-boot

1. Stažení a rozbalení zdrojového úložiště pro tento kurz nebo jeho klonování pomocí Git: git clonehttps://github.com/spring-guides/gs-spring-boot.git 
1. cd do gs-spring-boot\complete.
1. Otevřete **úvodní** dialogové okno IntelliJ, vyberte **Importovat projekt** a otevřete průvodce importem.
1. Vyberte `gs-spring-boot\complete` složku.

    ![Importovat projekt](media/spring-cloud-intellij-howto/import-project-1.png)

## <a name="deploy-to-azure-spring-cloud"></a>Nasazení do Azure Spring Cloud
Abyste se mohli nasadit do Azure, musíte se přihlásit pomocí svého účtu Azure a zvolit předplatné.  Podrobnosti o přihlášení najdete v [tématu Instalace a přihlášení](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in).

1. Klikněte pravým tlačítkem myši na projekt v Průzkumníku projektu IntelliJ a vyberte **Azure** -> **Deploy to Azure Spring Cloud**.

    ![Nasazení do Azure 1](media/spring-cloud-intellij-howto/deploy-to-azure-1.png)

1. Přijměte název aplikace v poli **Název.** **Název** odkazuje na konfiguraci, nikoli název aplikace. Uživatelé obvykle nemusí měnit.
1. Přijměte identifikátor z projektu pro **artefakt**.
1. Vyberte **Aplikace:** klikněte na **Vytvořit aplikaci...**.

    ![Nasazení do Azure 2](media/spring-cloud-intellij-howto/deploy-to-azure-2.png)

1. Zadejte **název aplikace**a klepněte na tlačítko **OK**.

    ![Nasazení do Azure ok](media/spring-cloud-intellij-howto/deploy-to-azure-2a.png)

1. Spusťte nasazení kliknutím na **tlačítko Spustit.** 

    ![Nasazení do Azure 3](media/spring-cloud-intellij-howto/deploy-to-azure-3.png)

1. Modul plug-in spustí `mvn package` příkaz v projektu a potom vytvoří novou aplikaci `package` a nasadí nádobu vygenerovanou příkazem.

1. Pokud se adresa URL aplikace nezobrazuje ve výstupním okně, získejte ji z webu Azure Portal. Přejděte ze skupiny prostředků do instance Azure Spring Cloud.  Pak klepněte na **položku Aplikace**.  Spuštěná aplikace bude uvedena.

    ![Získat testovací adresu URL](media/spring-cloud-intellij-howto/get-test-url.png)

1. Přejděte na adresu URL v prohlížeči.

    ![Navigace v prohlížeči 2](media/spring-cloud-intellij-howto/navigate-in-browser-2.png)

## <a name="show-streaming-logs"></a>Zobrazit protokoly datových proudů
Chcete-li získat protokoly:
1. Vyberte **Průzkumníka Azure**a pak **Spring Cloud**.
1. Klikněte pravým tlačítkem myši na spuštěnou aplikaci.
1. V rozevíracím seznamu vyberte **Protokoly datových proudů.**

    ![Výběr protokolů datových proudů](media/spring-cloud-intellij-howto/streaming-logs.png)

1. Vyberte instanci.

    ![Vybrat instanci](media/spring-cloud-intellij-howto/select-instance.png)

1. Protokol streamování bude viditelný ve výstupním okně.

    ![Výstup protokolu streamování](media/spring-cloud-intellij-howto/streaming-log-output.png)

## <a name="next-steps"></a>Další kroky
* [Příprava jarní aplikace pro Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-prepare-app-deployment)
* [Další informace o Azure Toolkit pro IntelliJ](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/)
