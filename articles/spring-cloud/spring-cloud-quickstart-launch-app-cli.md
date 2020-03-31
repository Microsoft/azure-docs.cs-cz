---
title: 'Úvodní příručka: Spuštění aplikace Java Spring pomocí azure CLI'
description: V tomto rychlém startu nasadíte ukázkovou aplikaci do Azure Spring Cloud na Azure CLI.
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 02/15/2020
ms.author: brendm
ms.openlocfilehash: c05e53bd8ad8ade8c1e42729f46c99a0059c4dce
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79470856"
---
# <a name="quickstart-launch-a-java-spring-application-using-the-azure-cli"></a>Úvodní příručka: Spuštění aplikace Java Spring pomocí azure CLI

Azure Spring Cloud umožňuje snadno spustit aplikaci mikroslužeb založenou na jarním spuštění v Azure.

Tento rychlý start ukazuje, jak nasadit existující java jarní cloudovou aplikaci do Azure. Až budete hotovi, můžete pokračovat ve správě aplikace prostřednictvím azure cli nebo přepnout na pomocí portálu Azure.

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

## <a name="provision-a-service-instance-on-the-azure-cli"></a>Zřízení instance služby v příkazovém příkazu k dispozici azure

1. Login to the Azure CLI and choose your active subscription. Nezapomeňte si vybrat aktivní předplatné, které je na seznamu povolených pro Azure Spring Cloud.

    ```azurecli
        az login
        az account list -o table
        az account set --subscription <Name or ID of subscription from the last step>
    ```

2. Připravte si název pro vaši službu Azure Spring Cloud.  Název musí být dlouhý 4 až 32 znaků a může obsahovat pouze malá písmena, číslice a pomlčky.  První znak názvu služby musí být písmeno a poslední znak musí být písmeno nebo číslo.

3. Vytvořte skupinu prostředků, která bude obsahovat vaši službu Azure Spring Cloud.

    ```azurecli
        az group create --location eastus --name <resource group name>
    ```

    Další informace o [skupinách prostředků Azure](../azure-resource-manager/management/overview.md).

4. Otevřete okno Azure CLI a spusťte následující příkazy pro zřízení instance Azure Spring Cloud.

    ```azurecli
        az spring-cloud create -n <service instance name> -g <resource group name>
    ```

    Nasazení instance služby bude trvat přibližně pět minut.

5. Nastavte výchozí název skupiny prostředků a název clusteru pomocí následujících příkazů:

    ```azurecli
        az configure --defaults group=<resource group name>
        az configure --defaults spring-cloud=<service instance name>
    ```

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=provision)

## <a name="setup-your-configuration-server"></a>Nastavení konfiguračního serveru

Aktualizujte svůj config-server s umístěním git repozitáře pro náš projekt:

```azurecli
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/Azure-Samples/piggymetrics-config
```

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=config-server)

## <a name="build-the-microservices-applications-locally"></a>Místní vytváření aplikací mikroslužeb

1. Vytvořte novou složku a naklonujte ukázkové úložiště aplikací do svého účtu Azure Cloud.  

    ```console
        mkdir source-code
        git clone https://github.com/Azure-Samples/piggymetrics
    ```

2. Změňte adresář a vytvořte projekt.

    ```console
        cd piggymetrics
        mvn clean package -D skipTests
    ```

Sestavení projektu trvá asi 5 minut.  Po dokončení byste měli mít jednotlivé soubory JAR pro každou službu v příslušných složkách.

## <a name="create-the-microservices"></a>Vytvoření mikroslužeb

Vytvořte mikroslužby Spring Cloud pomocí souborů JAR vytvořených v předchozím kroku. Vytvoříte tři mikroslužby: **brána**, **auth-service**a **account-service**.

```azurecli
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

## <a name="deploy-applications-and-set-environment-variables"></a>Nasazení aplikací a nastavení proměnných prostředí

Musíme skutečně nasadit naše aplikace do Azure. K nasazení všech tří aplikací použijte následující příkazy:

```azurecli
az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
```

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=deploy)

## <a name="assign-public-endpoint-to-gateway"></a>Přiřazení veřejného koncového bodu k bráně

Potřebujeme způsob, jak přistupovat k aplikaci prostřednictvím webového prohlížeče. Naše aplikace brány potřebuje veřejný koncový bod.

1. Přiřaďte koncový bod pomocí následujícího příkazu:

```azurecli
az spring-cloud app update -n gateway --is-public true
```

2. Dotaz na aplikaci **brány** pro jeho veřejnou IP adresu, abyste mohli ověřit, že je aplikace spuštěná:

Linux:

```azurecli
az spring-cloud app show --name gateway | grep url
```

Windows:

```azurecli
az spring-cloud app show -s <service name> -g <resource group> -n gateway -o table
```

3. Přejděte na adresu URL poskytovanou předchozím příkazem a spusťte aplikaci PiggyMetrics.
    ![Snímek obrazovky s piggymetrics běží](media/spring-cloud-quickstart-launch-app-cli/launch-app.png)

Můžete také procházet portál Azure a najít adresu URL. 
1. Přechod na službu
2. Vybrat **aplikace**
3. Vybrat **bránu**

    ![Snímek obrazovky s piggymetrics běží](media/spring-cloud-quickstart-launch-app-cli/navigate-app1.png)
    
4. Nalezení adresy URL **gateway Overview** na ![stránce Přehled brány Snímek obrazovky piggymetrics běží](media/spring-cloud-quickstart-launch-app-cli/navigate-app2-url.png)

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=public-endpoint)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili aplikaci Spring Cloud z azure CLI.  Další informace o Azure Spring Cloud, pokračujte v kurzu o přípravě aplikace pro nasazení.

> [!div class="nextstepaction"]
> [Příprava aplikace Azure Spring Cloud pro nasazení](spring-cloud-tutorial-prepare-app-deployment.md)

Další ukázky jsou k dispozici na [GitHubu: Ukázky Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
