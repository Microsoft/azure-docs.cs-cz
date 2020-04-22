---
title: Úvodní příručka – spuštění aplikace pomocí mavenu s Azure Spring Cloud
description: V tomto rychlém startu spusťte ukázkovou aplikaci pomocí aplikace Maven
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 4eddf34f4887f0e2bad08eb5700d83eaeae0ef7e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687094"
---
# <a name="quickstart-launch-an-azure-spring-cloud-app-using-the-maven-plug-in"></a>Úvodní příručka: Spuštění aplikace Azure Spring Cloud pomocí modulu plug-in Maven

Pomocí modulu plug-in Azure Spring Cloud Maven můžete snadno vytvářet a aktualizovat aplikace Azure Spring Cloud. Předdefinováním konfigurace můžete nasadit aplikace do stávající služby Azure Spring Cloud. V tomto článku použijete ukázkovou aplikaci s názvem PiggyMetrics k předvedení této funkce.

Po tomto rychlém startu se dozvíte, jak:

> [!div class="checklist"]
> * Zřízení instance služby
> * Nastavení konfiguračního serveru pro instanci
> * Klonování a vytváření aplikací mikroslužeb místně
> * Nasazení jednotlivých mikroslužeb
> * Přiřazení veřejného koncového bodu pro vaši aplikaci

>[!Note]
> Azure Spring Cloud se momentálně nabízí jako veřejná verze Preview. Nabídky ve verzi Public Preview umožňují zákazníkům experimentovat s novými funkcemi před jejich oficiálním vydáním.  Funkce a služby veřejné verze Preview nejsou určeny pro produkční použití.  Další informace o podpoře během náhledů najdete v [nejčastějších dotazech](https://azure.microsoft.com/support/faq/) nebo najdete [žádost o podporu,](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) kde se dozvíte další informace.


>[!TIP]
> Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít ke spuštění příkazů v tomto článku. Má společné nástroje Azure předinstalované, včetně nejnovějších verzí Git, Java Development Kit (JDK), Maven a Azure CLI. Pokud jste přihlášení k předplatnému Azure, spusťte [Azure Cloud Shell](https://shell.azure.com). Další informace najdete v [tématu Přehled Azure Cloud Shell](../cloud-shell/overview.md).

K provedení kroků v tomto kurzu Rychlý start je potřeba:

1. [Nainstalujte Git](https://git-scm.com/).
2. [Nainstalujte JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable).
3. [Nainstalujte Maven 3.0 nebo novější](https://maven.apache.org/download.cgi).
4. [Zaregistrujte si bezplatné předplatné Azure](https://azure.microsoft.com/free/).

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Zřízení instance služby na webu Azure Portal

1. Ve webovém prohlížeči otevřete [tento odkaz na Azure Spring Cloud na webu Azure Portal](https://ms.portal.azure.com/#create/Microsoft.AppPlatform)a přihlaste se ke svému účtu.

1. Zadejte **podrobnosti projektu** pro ukázkovou aplikaci takto:

    1. Vyberte **předplatné,** ke kterému bude aplikace přidružena.
    1. Vyberte nebo vytvořte skupinu prostředků pro aplikaci. Doporučujeme vytvořit novou skupinu prostředků.  Následující příklad ukazuje novou skupinu prostředků nazvanou `myspringservice`.
    1. Zadejte název nové služby Azure Spring Cloud.  Název musí být dlouhý 4 až 32 znaků a může obsahovat pouze malá písmena, číslice a pomlčky. První znak názvu služby musí být písmeno a poslední znak musí být písmeno nebo číslo.  Služba v příkladu níže `contosospringcloud`má název .
    1. Z uvedených možností vyberte umístění aplikace.  V tomto příkladu `East US`vybereme .
    1. Chcete-li zkontrolovat souhrn nové služby, vyberte **možnost Zkontrolovat + vytvořit.**  Pokud vše vypadá správně, vyberte **Vytvořit**.

    > [!div class="mx-imgBorder"]
    > ![Vybrat zkontrolovat + vytvořit](media/maven-qs-review-create.jpg)

Nasazení služby trvá přibližně 5 minut. Po nasazení služby vyberte **Přejít na prostředek** a zobrazí se stránka **Přehled** pro instanci služby.

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/javae2e?tutorial=asc-maven-quickstart&step=provision)

## <a name="set-up-your-configuration-server"></a>Nastavení konfiguračního serveru

1. Na stránce **Přehled** služby vyberte **Config Server**.
1. V části **Výchozí úložiště** nastavte **https://github.com/Azure-Samples/piggymetrics-config**identifikátor **URI** na a pak vyberte **Použít,** chcete-li změny uložit.

    > [!div class="mx-imgBorder"]
    > ![Definování a použití nastavení konfigurace](media/maven-qs-apply-config.jpg)

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/javae2e?tutorial=asc-maven-quickstart&step=config-server)

## <a name="clone-and-build-the-sample-application-repository"></a>Klonovat a vytvářet ukázkové úložiště aplikací

1. Spusťte [Prostředí Azure Cloud Shell](https://shell.azure.com).

1. Klonovat úložiště Git spuštěním následujícího příkazu:

    ```console
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. Změňte adresář a sestavte projekt spuštěním následujícího příkazu:

    ```console
    cd piggymetrics
    mvn clean package -DskipTests
    ```

## <a name="generate-configurations-and-deploy-to-the-azure-spring-cloud"></a>Generování konfigurací a nasazení do Azure Spring Cloud

1. Generovat konfigurace spuštěním následujícího příkazu v kořenové složce PiggyMetrics obsahující nadřazené POM:

    ```console
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:1.1.0:config
    ```

    a. Vyberte `gateway`moduly`auth-service`, `account-service`a .

    b. Vyberte předplatné a cluster služeb Azure Spring Cloud.

    c. Do seznamu poskytnutých projektů zadejte číslo, `gateway` které odpovídá, aby měl přístup veřejnosti.
    
    d. Potvrďte konfiguraci.

1. POM nyní obsahuje závislosti a konfigurace pluginů. Nasazení aplikací pomocí následujícího příkazu:

   ```console
   mvn azure-spring-cloud:deploy
   ```

1. Po dokončení nasazení můžete přistupovat KPraiggyMetrics pomocí adresy URL uvedené ve výstupu z předchozího příkazu.

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/javae2e?tutorial=asc-maven-quickstart&step=deploy)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili aplikaci Spring Cloud z úložiště Maven. Další informace o Azure Spring Cloud, pokračujte v kurzu o přípravě aplikace pro nasazení.

> [!div class="nextstepaction"]
> [Příprava aplikace Azure Spring Cloud na nasazení](spring-cloud-tutorial-prepare-app-deployment.md)
> [Další informace o zásuvných modulech Maven pro Azure](https://github.com/microsoft/azure-maven-plugin)

Další ukázky jsou k dispozici na [GitHubu: Ukázky Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
