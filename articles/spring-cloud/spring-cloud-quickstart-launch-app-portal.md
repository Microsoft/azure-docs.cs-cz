---
title: Úvodní příručka – spuštění existující aplikace Azure Spring Cloud pomocí portálu Azure
description: V tomto rychlém startu nasadit jarní cloudové aplikace do Azure Spring Cloud pomocí portálu Azure.
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 02/15/2020
ms.author: brendm
ms.openlocfilehash: 9cd59fdf81e9b5d56872d20c76e8ea177b3c8577
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79470890"
---
# <a name="quickstart-launch-an-existing-azure-spring-cloud-application-using-the-azure-portal"></a>Úvodní příručka: Spuštění existující aplikace Azure Spring Cloud pomocí portálu Azure

Tento rychlý start ukazuje, jak nasadit existující jarní cloudovou aplikaci do Azure. Azure Spring Cloud umožňuje snadno spouštět aplikace mikroslužeb založené na Spring Cloudu v Azure. 

Ukázkový kód aplikace použitý v tomto kurzu najdete v našem [úložišti ukázek GitHub](https://github.com/Azure-Samples/PiggyMetrics). Po dokončení bude poskytnutá ukázková aplikace přístupná online a připravená ke spravované přes portál Azure.

Po tomto rychlém startu se dozvíte, jak:

> [!div class="checklist"]
> * Zřízení instance služby
> * Nastavení konfiguračního serveru pro instanci
> * Místní vytvoření aplikace mikroslužeb
> * Nasazení jednotlivých mikroslužeb
> * Přiřazení veřejného koncového bodu pro vaši aplikaci

## <a name="prerequisites"></a>Požadavky

>[!Note]
> Azure Spring Cloud se momentálně nabízí jako veřejná verze Preview. Nabídky ve verzi Public Preview umožňují zákazníkům experimentovat s novými funkcemi před jejich oficiálním vydáním.  Funkce a služby veřejné verze Preview nejsou určeny pro produkční použití.  Další informace o podpoře během náhledů najdete v [nejčastějších dotazech](https://azure.microsoft.com/support/faq/) nebo najdete [žádost o podporu,](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) kde se dozvíte další informace.

>[!TIP]
> Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku.  Má společné nástroje Azure předinstalované, včetně nejnovějších verzí Git, JDK, Maven a Azure CLI. Pokud jste přihlášení k předplatnému Azure, spusťte [Azure Cloud Shell](https://shell.azure.com) z shell.azure.com.  Další informace o Azure Cloud Shellu najdete v [naší dokumentaci](../cloud-shell/overview.md)

K provedení kroků v tomto kurzu Rychlý start je potřeba:

1. [Nainstalovat Git](https://git-scm.com/).
2. [Instalace JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
3. [Instalace maven 3.0 nebo vyšší](https://maven.apache.org/download.cgi)
4. [Instalace azure CLI verze 2.0.67 nebo vyšší](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Registrace předplatného Azure](https://azure.microsoft.com/free/)

## <a name="install-the-azure-cli-extension"></a>Instalace rozšíření Azure CLI

Instalace rozšíření Azure Spring Cloud pro azure cli pomocí následujícího příkazu

```azurecli
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Zřízení instance služby na webu Azure Portal

1. Na nové kartě otevřete [portál Azure](https://ms.portal.azure.com/). 

2. V horním vyhledávacím poli vyhledejte **Azure Spring Cloud**.

3. Z výsledků vyberte **Azure Spring Cloud.**

 ![Ikona ASC](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

4. Na stránce Azure Spring Cloud klikněte na **+ Add**.

 ![Ikona ASC](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

5. Vyplňte formulář na stránce **Azure** Spring Cloud Create .  Zvažte následující pokyny:
    - **Předplatné**: Vyberte předplatné, které se má účtovat pro tento prostředek.  Ujistěte se, že toto předplatné bylo přidáno do našeho seznamu povolených položek pro Azure Spring Cloud.
    - **Skupina prostředků**: Je vhodné vytvořit nové skupiny prostředků pro nové zdroje.
    - **Podrobnosti/název služby**: Zadejte název instance služby.  Název musí být dlouhý 4 až 32 znaků a může obsahovat pouze malá písmena, číslice a pomlčky.  První znak názvu služby musí být písmeno a poslední znak musí být písmeno nebo číslo.
    - **Umístění**: Vyberte umístění pro instanci služby. Mezi aktuálně podporované lokality patří východní USA, západní USA 2, západní Evropa a jihovýchodní Asie.

    ![Spuštění portálu ASC](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. Klepnutím na kartu **Nastavení diagnostiky** otevřete následující dialogové okno.

7. Můžete nastavit **Povolit protokoly** na *ano* nebo *ne* podle vašich požadavků.

    ![Povolení protokolů](media/spring-cloud-quickstart-launch-app-portal/diagnostic-setting.png)

8. Klikněte na kartu **Trasování.**

9. Můžete nastavit **povolit trasování** na *ano* nebo *ne* podle vašich požadavků.  Pokud nastavíte **Povolit trasování** na ano, vyberte také existující přehled aplikace nebo vytvořte nový. Bez specifikace **Application Insights** dojde k chybě ověření.


    ![Trasování](media/spring-cloud-quickstart-launch-app-portal/tracing.png)

10. Klikněte na **Zkontrolovat a vytvořit**.

11. Ověřte specifikace a klepněte na tlačítko **Vytvořit**.

Nasazení služby trvá přibližně 5 minut.  Po nasazení se zobrazí stránka **Přehled** pro instanci služby.

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=provision)


## <a name="set-up-your-configuration-server"></a>Nastavení konfiguračního serveru

1. Přejděte na stránku **Přehled** služby a vyberte **Konfigurační server**.

2. V části **Výchozí úložiště** nastavte identifikátorhttps://github.com/Azure-Samples/piggymetrics-config **URI** na " ".

3. Výběrem možnosti **Použít** změny uložte.

    ![Snímek obrazovky s portálem ASC](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=config-server)

## <a name="build-and-deploy-microservice-applications"></a>Vytváření a nasazování aplikací mikroslužeb

1. Otevřete [Azure Cloud Shell](https://shell.azure.com) a naklonovat ukázkové úložiště aplikací do místního počítače.  Zde nejprve vytvoříme dočasný `source-code` adresář volaný před klonováním aplikace.

    ```console
    mkdir source-code
    cd source-code
    git clone https://github.com/Azure-Samples/piggymetrics
    ```

2. Vytvořte klonovaný balíček.

    ```console
    cd piggymetrics
    mvn clean package -DskipTests
    ```

3. Přiřaďte názvy skupině prostředků a službě. Nezapomeňte nahradit zástupné symboly níže názvem skupiny prostředků a názvem služby, které jste zřídit dříve v tomto kurzu.

    ```azurecli
    az configure --defaults group=<resource group name>
    az configure --defaults spring-cloud=<service instance name>
    ```

4. Vytvořte `gateway` aplikaci a nasaďte soubor JAR.

    ```azurecli
    az spring-cloud app create -n gateway
    az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
    ```

5. Podle stejného vzoru `account-service` vytvořte aplikace a `auth-service` nasadit jejich soubory JAR.

    ```azurecli
    az spring-cloud app create -n account-service
    az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
    az spring-cloud app create -n auth-service
    az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
    ```

6. Dokončení nasazení aplikací trvá několik minut. Pokud chcete potvrdit, že se nasazují, přejděte do okna **Aplikace** na webu Azure Portal. Měli byste vidět řádek každé ze tří aplikací.

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=deploy)

## <a name="assign-a-public-endpoint-to-gateway"></a>Přiřazení veřejného koncového bodu k bráně

1. Otevřete kartu **Aplikace** v nabídce vlevo.

2. Vyberte `gateway` aplikaci, která zobrazí stránku **Přehled.**

3. Vyberte **Přiřadit koncový bod,** chcete-li vstupní bráně přiřadit veřejný koncový bod. Může to trvat několik minut.

    ![Snímek obrazovky s portálem ASC](media/spring-cloud-quickstart-launch-app-portal/portal-endpoint.png)

4. Zadejte přiřazený veřejný koncový bod (označený **URL)** do prohlížeče a zobrazte spuštěnou aplikaci.

    ![Snímek obrazovky s portálem ASC](media/spring-cloud-quickstart-launch-app-portal/sample-app.png)

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=public-endpoint)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili:

> [!div class="checklist"]
> * Zřízení instance služby
> * Nastavení konfiguračního serveru pro instanci
> * Místní vytvoření aplikace mikroslužeb
> * Nasazení jednotlivých mikroslužeb
> * Přiřazení veřejného koncového bodu pro bránu aplikace

> [!div class="nextstepaction"]
> [Příprava aplikace Azure Spring Cloud pro nasazení](spring-cloud-tutorial-prepare-app-deployment.md)

Další ukázky jsou k dispozici na [GitHubu: Ukázky Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
