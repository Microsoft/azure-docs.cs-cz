---
title: Spuštění aplikace pružiny v jazyce Java pomocí rozhraní příkazového řádku Azure
description: V tomto rychlém startu nasadíte ukázkovou aplikaci do služby Azure jaře Cloud v Azure CLI.
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/04/2019
ms.author: v-vasuke
ms.openlocfilehash: 6d399f04015140477af17f718c3e2205b8c3855f
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170545"
---
# <a name="quickstart-launch-a-java-spring-application-using-the-azure-cli"></a>Rychlý Start: spuštění aplikace pružiny v jazyce Java pomocí rozhraní příkazového řádku Azure

Jarní cloud Azure umožňuje snadno spustit aplikaci mikroslužeb založenou na jarním startu v Azure.

V tomto rychlém startu se dozvíte, jak nasadit stávající cloudovou aplikaci Java do Azure. Až budete hotovi, můžete pokračovat v správě aplikace prostřednictvím rozhraní příkazového řádku Azure nebo pomocí Azure Portal.

Po tomto rychlém startu se dozvíte, jak:

> [!div class="checklist"]
> * Zřízení instance služby
> * Nastavení konfiguračního serveru pro instanci
> * Místní sestavení aplikace mikroslužeb
> * Nasazení jednotlivých mikroslužeb
> * Přiřazení veřejného koncového bodu vaší aplikaci

## <a name="prerequisites"></a>Předpoklady

>[!Note]
> Před zahájením tohoto rychlého startu se ujistěte, že vaše předplatné Azure má přístup k jarnímu cloudu Azure.  Jako služba ve verzi Preview se na nás zeptáme, abychom vám mohli přidat vaše předplatné do seznamu povolených adres.  Pokud chcete prozkoumat možnosti Azure jarního cloudu, [vyplňte prosím tento formulář](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR-LA2geqX-ZLhi-Ado1LD3tUNDk2VFpGUzYwVEJNVkhLRlcwNkZFUFZEUS4u
).

>[!TIP]
> Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku.  Má předinstalované běžné nástroje Azure, včetně nejnovějších verzí Git, JDK, Maven a Azure CLI. Pokud jste přihlášeni ke svému předplatnému Azure, spusťte [Azure Cloud Shell](https://shell.azure.com) z Shell.Azure.com.  Další informace o Azure Cloud Shell najdete v [naší dokumentaci](../cloud-shell/overview.md) .

K provedení kroků v tomto kurzu Rychlý start je potřeba:

1. [Nainstalovat Git](https://git-scm.com/).
2. [Nainstalovat JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
3. [Nainstalujte Maven 3,0 nebo novější.](https://maven.apache.org/download.cgi)
4. [Instalace rozhraní příkazového řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Registrace předplatného Azure](https://azure.microsoft.com/free/)

## <a name="install-the-azure-cli-extension"></a>Instalace rozšíření Azure CLI

Pomocí následujícího příkazu nainstalujte rozšíření Azure jaře Cloud pro rozhraní příkazového řádku Azure.

```azurecli
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

## <a name="provision-a-service-instance-on-the-azure-cli"></a>Zřízení instance služby v Azure CLI

1. Přihlaste se k Azure CLI a vyberte své aktivní předplatné. Nezapomeňte zvolit aktivní předplatné, které je na seznamu povolených pro Azure jaře Cloud.

    ```azurecli
        az login
        az account list -o table
        az account set --subscription
    ```

2. Připravte si název služby pro jarní cloudovou službu Azure.  Název musí být dlouhý 4 až 32 znaků a může obsahovat jenom malá písmena, číslice a spojovníky.  První znak názvu služby musí být písmeno a poslední znak musí být písmeno nebo číslo.

3. Vytvořte skupinu prostředků, která bude obsahovat službu pro jarní cloudovou službu Azure.

    ```azurecli
        az group create --location eastus --name <resource group name>
    ```
    Další informace o [skupinách prostředků Azure](../azure-resource-manager/resource-group-overview.md).

4. Otevřete okno Azure CLI a spusťte následující příkazy, abyste zřídili instanci Azure Pramenitého cloudu.

    ```azurecli
        az spring-cloud create -n <service name> -g <resource group name>
    ```

    Nasazení instance služby bude trvat přibližně pět minut.

5. Pomocí následujících příkazů nastavte výchozí název skupiny prostředků a název clusteru:

    ```azurecli
        az configure --defaults group=<service group name>
        az configure --defaults spring-cloud=<service instance name>
    ```

## <a name="setup-your-configuration-server"></a>Nastavení konfiguračního serveru

Aktualizujte konfiguraci-server s umístěním úložiště Git pro náš projekt:

```git
az spring-cloud config-server git set -n <your-service-name> --uri https://github.com/Azure-Samples/piggymetrics --label config
```

## <a name="build-the-microservices-applications-locally"></a>Místní sestavení aplikací mikroslužeb

1. Vytvořte novou složku a naklonujte úložiště ukázkové aplikace do svého cloudového účtu Azure.  

    ```azurecli
        mkdir source-code
        git clone https://github.com/Azure-Samples/piggymetrics
    ```

2. Změňte adresář a sestavte projekt.

    ```azurecli
        cd PiggyMetrics
        mvn clean package -D skipTests
    ```

Kompilace projektu trvá přibližně 5 minut.  Po dokončení byste měli mít jednotlivé soubory JAR pro každou službu v příslušných složkách.

## <a name="create-the-microservices"></a>Vytvoření mikroslužeb

Vytvářejte jarní cloudové mikroslužby pomocí souborů JAR vytvořených v předchozím kroku. Vytvoříte tři mikroslužby: **Brána**, **auth-Service**a **account-Service**.

```azurecli
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

>[!NOTE]
> Názvy aplikací musí odpovídat názvům jar přesně pro zadaný konfigurační server, aby správně fungoval.

## <a name="deploy-applications-and-set-environment-variables"></a>Nasazení aplikací a nastavení proměnných prostředí

Musíme skutečně nasadit naše aplikace do Azure. K nasazení všech tří aplikací použijte následující příkazy:

```azurecli
az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
```

## <a name="assign-public-endpoint-to-gateway"></a>Přiřazení veřejného koncového bodu k bráně

Potřebujeme způsob, jak získat přístup k aplikaci přes webový prohlížeč. Naše aplikace brány potřebuje veřejný koncový bod, který se dá přiřadit pomocí tohoto příkazu:

```azurecli
az spring-cloud app update -n gateway --is-public true
```

Nakonec pro veřejnou IP adresu spusťte dotaz na aplikaci **brány** , abyste mohli ověřit, jestli je aplikace spuštěná:

```azurecli
az spring-cloud app show --name gateway | grep url
```

Pokud chcete zobrazit spuštěnou aplikaci PiggyMetrics, přejděte na adresu URL poskytnutou předchozím příkazem.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili jarní cloudovou aplikaci z Azure CLI.  Další informace o jarním cloudu Azure najdete v kurzu Příprava aplikace na nasazení.

> [!div class="nextstepaction"]
> [Příprava aplikace pro jarní cloudy Azure pro nasazení](spring-cloud-tutorial-prepare-app-deployment.md)
