---
title: Rychlý Start – spuštění stávající cloudové aplikace Azure na jaře pomocí Azure Portal
description: V tomto rychlém startu nasadíte jarní cloudovou aplikaci do jarního cloudu Azure pomocí Azure Portal.
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 02/15/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 163940095c4a3a25123706ca47a7d059897cad20
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2020
ms.locfileid: "89046863"
---
# <a name="quickstart-launch-an-existing-azure-spring-cloud-application-using-the-azure-portal"></a>Rychlý Start: spuštění stávající cloudové aplikace Azure na jaře pomocí Azure Portal

V tomto rychlém startu se dozvíte, jak nasadit existující jarní cloudovou aplikaci do Azure. Jarní cloud Azure umožňuje snadné spouštění aplikací mikroslužeb založených na jarních cloudech v Azure. 

Před spuštěním tohoto příkladu můžete vyzkoušet [základní rychlý Start](spring-cloud-quickstart.md).

Vzorový kód aplikace použitý v tomto kurzu najdete v našem [úložišti ukázek GitHubu](https://github.com/Azure-Samples/PiggyMetrics). Jakmile budete hotovi, bude poskytnutá ukázková aplikace dostupná online a bude připravená ji spravovat prostřednictvím Azure Portal.

Po tomto rychlém startu se dozvíte, jak:

> [!div class="checklist"]
> * Zřízení instance služby
> * Nastavení konfiguračního serveru pro instanci
> * Místní sestavení aplikace mikroslužeb
> * Nasazení jednotlivých mikroslužeb
> * Přiřazení veřejného koncového bodu vaší aplikaci

## <a name="prerequisites"></a>Požadavky

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

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Zřízení instance služby na Azure Portal

1. Na nové kartě otevřete [Azure Portal](https://ms.portal.azure.com/). 

2. V horním vyhledávacím poli vyhledejte **Azure jaře Cloud**.

3. Z výsledků vyberte **Azure jaře Cloud** .

 ![Začátek ASC](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

4. Na stránce jarní cloud Azure klikněte na **+ Přidat**.

 ![Přidat ASC](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

5. Vyplňte formulář na stránce **Vytvoření** jarního cloudu Azure.  Vezměte v úvahu následující pokyny:
    - **Předplatné**: vyberte předplatné, které chcete pro tento prostředek fakturovat.  Zajistěte, aby bylo toto předplatné přidané do našeho seznamu povolených pro Azure jaře Cloud.
    - **Skupina prostředků**: vytváření nových skupin prostředků pro nové prostředky je osvědčeným postupem.
    - **Podrobnosti o službě/název**: zadejte název instance služby.  Název musí být dlouhý 4 až 32 znaků a může obsahovat jenom malá písmena, číslice a spojovníky.  První znak názvu služby musí být písmeno a poslední znak musí být písmeno nebo číslo.
    - **Umístění**: vyberte umístění pro instanci služby. Aktuálně podporovaná umístění zahrnují Východní USA, Západní USA 2, Západní Evropa a jihovýchodní Asie.

    ![Začátek portálu ASC](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. Kliknutím na kartu **nastavení diagnostiky** otevřete následující dialog.

7. Podle vašich požadavků můžete nastavit **Povolit protokoly** na *Ano* nebo *ne* .

    ![Povolení protokolů](media/spring-cloud-quickstart-launch-app-portal/diagnostic-setting.png)

8. Klikněte na kartu **trasování** .

9. Podle vašich požadavků můžete nastavit **Povolit trasování** na *Ano* nebo *ne* .  Pokud nastavíte možnost **Povolit trasování** na Ano, vyberte taky existující Přehled aplikace nebo vytvořte nový. Bez specifikace **Application Insights** dojde k chybě ověření.


    ![Zobrazení trasování](media/spring-cloud-quickstart-launch-app-portal/tracing.png)

10. Klikněte na **Zkontrolovat a vytvořit**.

11. Ověřte vaše specifikace a klikněte na **vytvořit**.

Nasazení služby trvá asi 5 minut.  Po nasazení se zobrazí stránka s **přehledem** instance služby.

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=provision)


## <a name="set-up-your-configuration-server"></a>Nastavení konfiguračního serveru

1. Přejít na stránku **Přehled** služby a vyberte možnost **konfigurační server**.

2. V části **výchozí úložiště** nastavte možnost **URI** na " https://github.com/Azure-Samples/piggymetrics-config ".

3. Výběrem možnosti **Použít** změny uložte.

    ![Portál ASC](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=config-server)

## <a name="build-and-deploy-microservice-applications"></a>Sestavování a nasazování aplikací mikroslužeb

1. Otevřete [Azure Cloud Shell](https://shell.azure.com) nebo místní prostředí s nainstalovaným rozhraním Azure CLI. Tady před klonováním ukázkové aplikace vytvoříme dočasný adresář s názvem `source-code` .

    ```console
    mkdir source-code
    cd source-code
    git clone https://github.com/Azure-Samples/piggymetrics
    ```

2. Sestavte Klonovaný balíček.

    ```console
    cd piggymetrics
    mvn clean package -DskipTests
    ```

3. Pomocí následujícího příkazu nainstalujte rozšíření Azure jaře Cloud pro rozhraní příkazového řádku Azure.

    ```azurecli
    az extension add --name spring-cloud
    ```

4. Přiřaďte názvy ke svojí skupině prostředků a službě. Nezapomeňte nahradit níže uvedené zástupné symboly názvem skupiny prostředků a názvem služby, který jste zřídili dříve v tomto kurzu.

    ```azurecli
    az configure --defaults group=<resource group name>
    az configure --defaults spring-cloud=<service instance name>
    ```

5. Vytvořte `gateway` aplikaci a nasaďte soubor JAR.

    Pomocí rozšíření jarní cloud vytvořte aplikaci:

    ```azurecli
    az spring-cloud app create -n gateway
    az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
    ```

6. Podle stejného vzoru vytvořte `account-service` `auth-service` aplikace a a nasaďte jejich soubory jar.

    ```azurecli
    az spring-cloud app create -n account-service
    az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
    az spring-cloud app create -n auth-service
    az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
    ```

7. Dokončení nasazování aplikací trvá několik minut. Pokud chcete potvrdit, že se nasadili, vyberte v Azure Portal okno **aplikace** . Mělo by se zobrazit řádek každé ze tří aplikací.

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=deploy)

## <a name="assign-a-public-endpoint-to-gateway"></a>Přiřazení veřejného koncového bodu k bráně

1. Otevřete kartu **aplikace** v nabídce na levé straně.

2. Vyberte `gateway` aplikaci, aby se zobrazila stránka s **přehledem** .

3. Vyberte **přiřadit koncový bod** pro přiřazení veřejného koncového bodu k bráně. Může to trvat několik minut.

    ![Koncový bod portálu ASC](media/spring-cloud-quickstart-launch-app-portal/portal-endpoint.png)

4. Pokud chcete zobrazit spuštěnou aplikaci, zadejte přiřazený veřejný koncový bod (označený jako **URL**) do prohlížeče.

    ![Ukázková aplikace portálu ASC](media/spring-cloud-quickstart-launch-app-portal/sample-app.png)

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=public-endpoint)

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
