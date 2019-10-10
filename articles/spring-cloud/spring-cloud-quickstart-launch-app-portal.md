---
title: Spuštění aplikace pro jarní Cloud v Azure pomocí Azure Portal
description: Nasaďte ukázkovou aplikaci do jarního cloudu Azure pomocí Azure Portal.
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/04/2019
ms.author: v-vasuke
ms.openlocfilehash: 11e9fadc240b90ceb7e4a0e6690978dac9bae859
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255078"
---
# <a name="quickstart-launch-an-azure-spring-cloud-application-using-the-azure-portal"></a>Rychlý Start: spuštění aplikace ve jarním cloudu Azure pomocí Azure Portal

Jarní cloud Azure umožňuje snadné spouštění aplikací mikroslužeb založených na jarních cloudech v Azure.

V tomto rychlém startu se dozvíte, jak nasadit existující jarní cloudovou aplikaci do Azure. [Tady je odkaz](https://github.com/Azure-Samples/PiggyMetrics) na ukázkový kód aplikace použitý v tomto kurzu. Jakmile budete hotovi, bude poskytnutá ukázková aplikace dostupná online a bude připravená ji spravovat prostřednictvím Azure Portal.

Po tomto rychlém startu se dozvíte, jak:

> [!div class="checklist"]
> * Zřízení instance služby
> * Nastavení konfiguračního serveru pro instanci
> * Místní sestavení aplikace mikroslužeb
> * Nasazení jednotlivých mikroslužeb
> * Přiřazení veřejného koncového bodu vaší aplikaci

## <a name="prerequisites"></a>Požadované součásti

>[!Note]
> Před zahájením tohoto rychlého startu se ujistěte, že vaše předplatné Azure má přístup k jarnímu cloudu Azure.  Jako služba ve verzi Preview požádáme zákazníky, aby se na nás dostali, abychom mohli přidat vaše předplatné do našeho seznamu povolených.  Pokud chcete prozkoumat možnosti Azure jarního cloudu, obraťte se na nás e-mailem: azure-spring-cloud@service.microsoft.com.

>[!TIP]
> Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít ke spuštění kroků v tomto článku.  Má předinstalované běžné nástroje Azure, včetně nejnovějších verzí Git, JDK, Maven a Azure CLI. Pokud jste přihlášeni ke svému předplatnému Azure, spusťte [Azure Cloud Shell](https://shell.azure.com) z Shell.Azure.com.  Další informace o Azure Cloud Shell najdete v [naší dokumentaci](../cloud-shell/overview.md) .

K dokončení tohoto rychlého startu:

1. [Nainstalovat git](https://git-scm.com/)
2. [Nainstalovat JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
3. [Nainstalujte Maven 3,0 nebo novější.](https://maven.apache.org/download.cgi)
4. [Instalace rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Registrace předplatného Azure](https://azure.microsoft.com/free/)

## <a name="install-the-azure-cli-extension"></a>Instalace rozšíření Azure CLI

Pomocí následujícího příkazu nainstalujte rozšíření Azure jaře Cloud pro rozhraní příkazového řádku Azure.

```Azure CLI
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Zřízení instance služby na Azure Portal

1. Ve webovém prohlížeči otevřete [Tento odkaz na jarní cloud Azure v Azure Portal](https://ms.portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=AppPlatformExtension#blade/Microsoft_Azure_Marketplace/MarketplaceOffersBlade/selectedMenuItemId/home/searchQuery/Azure%20Spring%20Cloud).

1. Pokud chcete přejít na stránku Přehled, vyberte **Azure jaře Cloud** . Začněte tím, že vyberete tlačítko **vytvořit** .

1. Vyplňte formulář a zvažte následující pokyny:
    - Název služby: zadejte název instance služby.  Název musí být dlouhý 4 až 32 znaků a může obsahovat jenom malá písmena, číslice a spojovníky.  První znak názvu služby musí být písmeno a poslední znak musí být písmeno nebo číslo.
    - Předplatné: vyberte předplatné, které chcete pro tento prostředek fakturovat.  Zajistěte, aby bylo toto předplatné přidané do našeho seznamu povolených pro Azure jaře Cloud.
    - Skupina prostředků: vytváření nových skupin prostředků pro nové prostředky je osvědčeným postupem.
    - Umístění: vyberte umístění pro instanci služby. Aktuálně podporovaná umístění zahrnují Východní USA, Západní USA 2, Západní Evropa a jihovýchodní Asie.
    
Nasazení služby trvá asi 5 minut.  Po nasazení se zobrazí stránka s **přehledem** instance služby.

## <a name="set-up-your-configuration-server"></a>Nastavení konfiguračního serveru

1. Přejít na stránku **Přehled** služby a vyberte možnost **konfigurační server**.

1. V části **výchozí úložiště** nastavte možnost **URI** na "https://github.com/Azure-Samples/piggymetrics", nastavte **jmenovku** na "config" a pak změny uložte kliknutím na **použít** .

## <a name="build-and-deploy-microservice-applications"></a>Sestavování a nasazování aplikací mikroslužeb

1. Otevřete okno příkazového řádku a spusťte následující příkaz, který naklonuje úložiště ukázkové aplikace do místního počítače.

    ```cli
    git clone https://github.com/Azure-Samples/piggymetrics
    ```

1. Sestavte projekt spuštěním příkazu níže.

    ```cli
    cd PiggyMetrics
    mvn clean package -DskipTests
    ```

1. Přihlaste se ke službě Azure CLI a nastavte své aktivní předplatné.

    ```cli
    # Login to Azure CLI
    az login

    # List all subscriptions
    az account list -o table

    # Set active subscription
    az account set --subscription <target subscription ID>
    ```

1. Přiřaďte názvy ke svojí skupině prostředků a službě. Nezapomeňte nahradit níže uvedené zástupné symboly názvem skupiny prostředků a názvem služby, který jste zřídili dříve v tomto kurzu.

    ```azurecli
    az configure --defaults group=<resource group name>
    az configure --defaults spring-cloud=<service instance name>
    ```

1. Vytvořte aplikaci `gateway` a nasaďte soubor JAR.

    ```azurecli
    az spring-cloud app create -n gateway
    az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
    ```

1. Podle stejného vzoru vytvořte aplikace `account-service` a `auth-service` a nasaďte jejich soubory JAR.

    ```cli
    az spring-cloud app create -n account-service
    az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
    az spring-cloud app create -n auth-service
    az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
    ```

1. Dokončení nasazování aplikací trvá několik minut. Pokud chcete potvrdit, že se nasadili, vyberte v Azure Portal okno **aplikace** . Mělo by se zobrazit řádek každé ze tří aplikací.

## <a name="assign-a-public-endpoint-to-gateway"></a>Přiřazení veřejného koncového bodu k bráně

1. Otevřete stránku **řídicího panelu aplikace** .
2. Vyberte aplikaci `gateway` pro zobrazení stránky s **podrobnostmi o aplikaci** .
3. Vyberte **přiřadit doménu** a přiřaďte k bráně veřejný koncový bod. Může to pár minut trvat. 
4. Pokud chcete zobrazit spuštěnou aplikaci, zadejte přiřazenou veřejnou IP adresu do prohlížeče.


## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se dozvěděli, jak:

> [!div class="checklist"]
> * Zřízení instance služby
> * Nastavení konfiguračního serveru pro instanci
> * Místní sestavení aplikace mikroslužeb
> * Nasazení jednotlivých mikroslužeb
> * Přiřazení veřejného koncového bodu k aplikační bráně

> [!div class="nextstepaction"]
> [Příprava aplikace pro jarní cloudy Azure pro nasazení](spring-cloud-tutorial-prepare-app-deployment.md)
