---
title: Rychlý Start – spuštění aplikace pomocí Maven s využitím služby Azure Pramenitého cloudu
description: V tomto rychlém startu spustíte ukázkovou aplikaci pomocí Maven.
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 7496ffcc365a3e9178bcc3293b518ea790e56c78
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607067"
---
# <a name="quickstart-launch-an-azure-spring-cloud-app-using-the-maven-plug-in"></a>Rychlý Start: spuštění aplikace pro jarní Cloud z Azure pomocí modulu plug-in Maven

Pomocí modulu plug-in Azure jaře Cloud Maven můžete snadno vytvářet a aktualizovat svoje aplikace v cloudu Azure pro jaře. Když nasadíte konfiguraci, můžete nasadit aplikace do své stávající služby pro jarní cloudy Azure. V tomto článku můžete tuto funkci předvést pomocí ukázkové aplikace s názvem PiggyMetrics.

Po tomto rychlém startu se dozvíte, jak:

> [!div class="checklist"]
> * Zřízení instance služby
> * Nastavení konfiguračního serveru pro instanci
> * Naklonování a vytváření aplikací mikroslužeb v místním prostředí
> * Nasazení jednotlivých mikroslužeb
> * Přiřazení veřejného koncového bodu pro vaši aplikaci

>[!Note]
> Jarní cloud Azure se teď nabízí jako verze Public Preview. Nabídky veřejné verze Preview umožňují zákazníkům experimentovat s novými funkcemi před jejich oficiální verzí.  Funkce a služby verze Public Preview nejsou určeny pro produkční použití.  Další informace o podpoře v rámci verzí Preview najdete v našich [nejčastějších dotazech](https://azure.microsoft.com/support/faq/) nebo v souboru o [support Request](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) , kde se dozvíte víc.


>[!TIP]
> Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít ke spuštění příkazů v tomto článku. Má předinstalované běžné nástroje Azure, včetně nejnovějších verzí Gitu, sady Java Development Kit (JDK), Maven a Azure CLI. Pokud jste přihlášeni ke svému předplatnému Azure, spusťte [Azure Cloud Shell](https://shell.azure.com). Další informace najdete v tématu [přehled Azure Cloud Shell](../cloud-shell/overview.md).

K provedení kroků v tomto kurzu Rychlý start je potřeba:

1. [Nainstalujte Git](https://git-scm.com/).
2. [Nainstalujte JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable).
3. [Nainstalujte Maven 3,0 nebo novější](https://maven.apache.org/download.cgi).
4. [Zaregistrujte si bezplatné předplatné Azure](https://azure.microsoft.com/free/).

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Zřízení instance služby na Azure Portal

1. Ve webovém prohlížeči otevřete [Tento odkaz na jarní cloud Azure v Azure Portal](https://ms.portal.azure.com/#create/Microsoft.AppPlatform)a přihlaste se ke svému účtu.

1. Zadejte **Podrobnosti projektu** pro ukázkovou aplikaci následujícím způsobem:

    1. Vyberte **předplatné** , ke kterému se bude aplikace přidružit.
    1. Vyberte nebo vytvořte skupinu prostředků pro aplikaci. Doporučujeme vytvořit novou skupinu prostředků.  Následující příklad ukazuje novou skupinu prostředků s názvem `myspringservice` .
    1. Zadejte název pro novou jarní cloudovou službu Azure.  Název musí být dlouhý 4 až 32 znaků a může obsahovat jenom malá písmena, číslice a spojovníky. První znak názvu služby musí být písmeno a poslední znak musí být písmeno nebo číslo.  Služba v následujícím příkladu má název `contosospringcloud` .
    1. Z poskytnutých možností vyberte umístění pro vaši aplikaci.  V tomto příkladu vybereme `East US` .
    1. Vyberte **zkontrolovat + vytvořit** a podívejte se na Shrnutí nové služby.  Pokud vše vypadá správně, vyberte **vytvořit**.

    > [!div class="mx-imgBorder"]
    > ![Vybrat kontrolu + vytvořit](media/maven-qs-review-create.jpg)

Nasazení služby trvá asi 5 minut. Po nasazení služby vyberte **Přejít na prostředek** a zobrazí se stránka **Přehled** pro instanci služby.

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/javae2e?tutorial=asc-maven-quickstart&step=provision)

## <a name="set-up-your-configuration-server"></a>Nastavení konfiguračního serveru

1. Na stránce **Přehled** služby vyberte možnost **konfigurační server**.
1. V části **výchozí úložiště** nastavte **identifikátor URI** na **https://github.com/Azure-Samples/piggymetrics-config** a pak kliknutím na **použít** uložte změny.

    > [!div class="mx-imgBorder"]
    > ![Definování a použití nastavení konfigurace](media/maven-qs-apply-config.jpg)

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/javae2e?tutorial=asc-maven-quickstart&step=config-server)

## <a name="clone-and-build-the-sample-application-repository"></a>Klonování a sestavení ukázkového úložiště aplikace

1. Spusťte [Azure Cloud Shell](https://shell.azure.com).

1. Naklonujte úložiště Git spuštěním následujícího příkazu:

    ```console
    git clone https://github.com/Azure-Samples/piggymetrics
    ```
  
1. Změňte adresář a sestavte projekt spuštěním následujícího příkazu:

    ```console
    cd piggymetrics
    mvn clean package -DskipTests
    ```

## <a name="generate-configurations-and-deploy-to-the-azure-spring-cloud"></a>Generování konfigurací a nasazení do jarního cloudu Azure

1. Generujte konfigurace spuštěním následujícího příkazu v kořenové složce PiggyMetrics obsahující nadřazený POM:

    ```console
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:1.1.0:config
    ```

    a. Vyberte moduly `gateway` , `auth-service` a `account-service` .

    b. Vyberte své předplatné a cluster jarních cloudových služeb Azure.

    c. V seznamu poskytnutých projektů zadejte číslo, které odpovídá za `gateway` účelem udělení veřejného přístupu.
    
    d. Potvrďte konfiguraci.

1. POM nyní obsahuje závislosti a konfigurace modulu plug-in. Nasaďte aplikace pomocí následujícího příkazu:

   ```console
   mvn azure-spring-cloud:deploy
   ```

1. Po dokončení nasazení můžete k PiggyMetrics přistupovat pomocí adresy URL, která je k dispozici ve výstupu z předchozího příkazu.

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/javae2e?tutorial=asc-maven-quickstart&step=deploy)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili jarní cloudovou aplikaci z úložiště Maven. Další informace o jarním cloudu Azure najdete v kurzu o přípravě aplikace na nasazení.

> [!div class="nextstepaction"]
> [Příprava aplikace pro jarní cloudy Azure pro nasazení](spring-cloud-tutorial-prepare-app-deployment.md) 
>  Další [informace o modulech plug-in Maven pro Azure](https://github.com/microsoft/azure-maven-plugins)

Další ukázky jsou k dispozici na GitHubu: [ukázky Azure pro jarní Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
