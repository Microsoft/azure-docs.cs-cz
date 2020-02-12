---
title: Rychlý Start – spuštění stávající cloudové aplikace Azure na jaře pomocí Azure Portal
description: V tomto rychlém startu nasadíte jarní cloudovou aplikaci do jarního cloudu Azure pomocí Azure Portal.
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 02/03/2020
ms.author: brendm
ms.openlocfilehash: 67ad112021c2fadb5ea93ffefcf5e58e9404e92a
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137756"
---
# <a name="quickstart-launch-an-existing-azure-spring-cloud-application-using-the-azure-portal"></a>Rychlý Start: spuštění stávající cloudové aplikace Azure na jaře pomocí Azure Portal

V tomto rychlém startu se dozvíte, jak nasadit existující jarní cloudovou aplikaci do Azure. Jarní cloud Azure umožňuje snadné spouštění aplikací mikroslužeb založených na jarních cloudech v Azure. 

Vzorový kód aplikace použitý v tomto kurzu najdete v našem [úložišti ukázek GitHubu](https://github.com/Azure-Samples/PiggyMetrics). Jakmile budete hotovi, bude poskytnutá ukázková aplikace dostupná online a bude připravená ji spravovat prostřednictvím Azure Portal.

Po tomto rychlém startu se dozvíte, jak:

> [!div class="checklist"]
> * Zřízení instance služby
> * Nastavení konfiguračního serveru pro instanci
> * Místní sestavení aplikace mikroslužeb
> * Nasazení jednotlivých mikroslužeb
> * Přiřazení veřejného koncového bodu vaší aplikaci

## <a name="prerequisites"></a>Předpoklady

>[!Note]
> Jarní cloud Azure se teď nabízí jako verze Public Preview. Nabídky veřejné verze Preview umožňují zákazníkům experimentovat s novými funkcemi před jejich oficiální verzí.  Funkce a služby verze Public Preview nejsou určeny pro produkční použití.  Další informace o podpoře v rámci verzí Preview najdete v našich [nejčastějších dotazech](https://azure.microsoft.com/support/faq/) nebo v souboru o [support Request](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) , kde se dozvíte víc.

>[!TIP]
> Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku.  Má předinstalované běžné nástroje Azure, včetně nejnovějších verzí Git, JDK, Maven a Azure CLI. Pokud jste přihlášeni ke svému předplatnému Azure, spusťte [Azure Cloud Shell](https://shell.azure.com) z Shell.Azure.com.  Další informace o Azure Cloud Shell najdete v [naší dokumentaci](../cloud-shell/overview.md) .

K provedení kroků v tomto kurzu Rychlý start je potřeba:

1. [Nainstalovat Git](https://git-scm.com/).
2. [Nainstalovat JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
3. [Nainstalujte Maven 3,0 nebo novější.](https://maven.apache.org/download.cgi)
4. [Instalace rozhraní příkazového řádku Azure CLI 2.0.67 nebo vyšší verze](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Registrace předplatného Azure](https://azure.microsoft.com/free/)

## <a name="install-the-azure-cli-extension"></a>Instalace rozšíření Azure CLI

Pomocí následujícího příkazu nainstalujte rozšíření Azure jaře Cloud pro rozhraní příkazového řádku Azure.

```Azure CLI
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Zřízení instance služby na Azure Portal

1. Ve webovém prohlížeči otevřete [Tento odkaz na jarní cloud Azure v Azure Portal](https://ms.portal.azure.com/#create/Microsoft.AppPlatform).

1. Vyplňte formulář na stránce **Vytvoření** jarního cloudu Azure.  Vezměte v úvahu následující pokyny:
    - Název služby: zadejte název instance služby.  Název musí být dlouhý 4 až 32 znaků a může obsahovat jenom malá písmena, číslice a spojovníky.  První znak názvu služby musí být písmeno a poslední znak musí být písmeno nebo číslo.
    - Předplatné: vyberte předplatné, které chcete pro tento prostředek fakturovat.  Zajistěte, aby bylo toto předplatné přidané do našeho seznamu povolených pro Azure jaře Cloud.
    - Skupina prostředků: vytváření nových skupin prostředků pro nové prostředky je osvědčeným postupem.
    - Umístění: vyberte umístění pro instanci služby. Aktuálně podporovaná umístění zahrnují Východní USA, Západní USA 2, Západní Evropa a jihovýchodní Asie.

1. Klikněte na tlačítko **zkontrolovat a vytvořit**.

1. Ověřte vaše specifikace a klikněte na **vytvořit**.

Nasazení služby trvá asi 5 minut.  Po nasazení se zobrazí stránka s **přehledem** instance služby.

> [!div class="nextstepaction"]
> [Narazili jsme na problém](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=provision)


## <a name="set-up-your-configuration-server"></a>Nastavení konfiguračního serveru

1. Přejít na stránku **Přehled** služby a vyberte možnost **konfigurační server**.

1. V části **výchozí úložiště** nastavte **URI** na https\://GitHub.com/Azure-Samples/piggymetrics.

1. Nastavte **popisek** na "config".

1. Pokud chcete změny uložit, vyberte **použít** .

    ![Snímek obrazovky s portálem ASC](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

> [!div class="nextstepaction"]
> [Narazili jsme na problém](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=config-server)

## <a name="build-and-deploy-microservice-applications"></a>Sestavování a nasazování aplikací mikroslužeb

1. Otevřete [Azure Cloud Shell](https://shell.azure.com) a naklonujte úložiště ukázkové aplikace do místního počítače.  V tomto příkladu vytvoříme před klonováním aplikace dočasný adresář s názvem `source-code`.

    ```azurecli
    mkdir source-code
    cd source-code
    git clone https://github.com/Azure-Samples/piggymetrics
    ```

1. Sestavte Klonovaný balíček.

    ```azurecli
    cd piggymetrics
    mvn clean package -DskipTests
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

1. Podle stejného vzoru vytvořte `account-service` a `auth-service` aplikace a nasaďte jejich soubory JAR.

    ```azurecli
    az spring-cloud app create -n account-service
    az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
    az spring-cloud app create -n auth-service
    az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
    ```

1. Dokončení nasazování aplikací trvá několik minut. Pokud chcete potvrdit, že se nasadili, vyberte v Azure Portal okno **aplikace** . Mělo by se zobrazit řádek každé ze tří aplikací.

> [!div class="nextstepaction"]
> [Narazili jsme na problém](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=deploy)

## <a name="assign-a-public-endpoint-to-gateway"></a>Přiřazení veřejného koncového bodu k bráně

1. Otevřete kartu **aplikace** v nabídce na levé straně.

1. Vyberte aplikaci `gateway`, aby se zobrazila stránka s **přehledem** .

1. Vyberte **přiřadit doménu** a přiřaďte k bráně veřejný koncový bod. Tato možnost může trvat několik minut.

    ![Snímek obrazovky s portálem ASC](media/spring-cloud-quickstart-launch-app-portal/portal-endpoint.png)

1. Pokud chcete zobrazit spuštěnou aplikaci, zadejte přiřazený veřejný koncový bod (označený jako **URL**) do prohlížeče.

    ![Snímek obrazovky s portálem ASC](media/spring-cloud-quickstart-launch-app-portal/sample-app.png)

> [!div class="nextstepaction"]
> [Narazili jsme na problém](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=public-endpoint)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili:

> [!div class="checklist"]
> * Zřízení instance služby
> * Nastavení konfiguračního serveru pro instanci
> * Místní sestavení aplikace mikroslužeb
> * Nasazení jednotlivých mikroslužeb
> * Přiřazení veřejného koncového bodu k aplikační bráně

> [!div class="nextstepaction"]
> [Příprava aplikace pro jarní cloudy Azure pro nasazení](spring-cloud-tutorial-prepare-app-deployment.md)

Další ukázky jsou k dispozici na GitHubu: [ukázky Azure pro jarní Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
