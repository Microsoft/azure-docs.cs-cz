---
title: Rychlý Start – zřízení cloudové služby Azure na jaře
description: Popisuje vytvoření instance služby Azure jaře Cloud Service pro nasazení aplikace.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java
ms.openlocfilehash: c91237e3a14c60e477f58be0bf62f634b462960b
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2020
ms.locfileid: "88951916"
---
# <a name="quickstart-provision-azure-spring-cloud-service"></a>Rychlý Start: zřízení služby pro jarní cloudovou službu Azure

Pomocí Azure Portal nebo rozhraní příkazového řádku Azure můžete vytvořit instanci Azure pružinového cloudu.  Obě metody jsou vysvětleny v následujících postupech.
## <a name="prerequisites"></a>Předpoklady

* [Nainstalovat JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
* [Registrace předplatného Azure](https://azure.microsoft.com/free/)
* Volitelné [Nainstalujte rozhraní příkazového řádku Azure CLI 2.0.67 nebo vyšší verze](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) a nainstalujte rozšíření pro jarní cloud Azure pomocí příkazu: `az extension add --name spring-cloud`
* Volitelné [Instalace Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) a [přihlášení](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="provision-an-instance-of-azure-spring-cloud"></a>Zřízení instance Azure jarního cloudu

#### <a name="portal"></a>[Azure Portal](#tab/Azure-portal)

Následující postup vytvoří instanci Azure jarního cloudu pomocí Azure Portal.

1. Na nové kartě otevřete [Azure Portal](https://ms.portal.azure.com/). 

2. V horním vyhledávacím poli vyhledejte **Azure jaře Cloud**.

3. Z výsledků vyberte **Azure jaře Cloud** .

    ![Ikona ASC – začátek](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

4. Na stránce jarní cloud Azure klikněte na **+ Přidat**.

    ![Ikona ASC přidat](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

5. Vyplňte formulář na stránce **Vytvoření** jarního cloudu Azure.  Vezměte v úvahu následující pokyny:
    - **Předplatné**: vyberte předplatné, které chcete pro tento prostředek fakturovat.
    - **Skupina prostředků**: vytváření nových skupin prostředků pro nové prostředky je osvědčeným postupem. Všimněte si, že se použije v pozdějších krocích jako **\<resource group name\>** .
    - **Podrobnosti o službě/název**: zadejte **\<service instance name\>** .  Název musí být dlouhý 4 až 32 znaků a může obsahovat jenom malá písmena, číslice a spojovníky.  První znak názvu služby musí být písmeno a poslední znak musí být písmeno nebo číslo.
    - **Umístění**: vyberte umístění pro instanci služby.

    ![Začátek portálu ASC](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. Klikněte na **Zkontrolovat a vytvořit**.

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=public-endpoint)

#### <a name="cli"></a>[Rozhraní příkazového řádku](#tab/Azure-CLI)

Následující postup používá rozšíření Azure CLI ke zřízení instance služby Azure jaře Cloud.

1. Přihlaste se k Azure CLI a vyberte své aktivní předplatné. Nezapomeňte zvolit aktivní předplatné, které je na seznamu povolených pro Azure jaře Cloud.

    ```azurecli
    az login
    az account list -o table
    az account set --subscription <Name or ID of subscription, skip if you only have 1 subscription>
    ```

1. Připravte si název služby pro jarní cloudovou službu Azure.  Název musí být dlouhý 4 až 32 znaků a může obsahovat jenom malá písmena, číslice a spojovníky.  První znak názvu služby musí být písmeno a poslední znak musí být písmeno nebo číslo.

1. Vytvořte skupinu prostředků, která bude obsahovat službu pro jarní cloudovou službu Azure.

    ```azurecli
    az group create --location eastus --name <resource group name>
    ```

    Další informace o [skupinách prostředků Azure](../azure-resource-manager/management/overview.md).

1. Otevřete okno Azure CLI a spusťte následující příkazy, abyste zřídili instanci Azure Pramenitého cloudu.

    ```azurecli
    az spring-cloud create -n <service instance name> -g <resource group name>
    ```

    Nasazení instance služby bude trvat přibližně pět minut.
---

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Nastavení konfiguračního serveru](spring-cloud-quickstart-setup-config-server.md)


