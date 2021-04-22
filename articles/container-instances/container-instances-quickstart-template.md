---
title: Rychlý Start – vytvoření instance kontejneru – Azure Resource Manager šablona
description: V tomto rychlém startu použijete šablonu Azure Resource Manager k rychlému nasazení kontejnerové webové aplikace, která běží v izolované instanci kontejneru Azure.
services: azure-resource-manager
ms.date: 04/30/2020
ms.topic: quickstart
ms.service: azure-resource-manager
ms.custom:
- subject-armqs
- devx-track-js
- mode-arm
ms.openlocfilehash: 2f57e86421f7522467a3f4adf4a4e9a21456ba4c
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107878735"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-an-arm-template"></a>Rychlý Start: nasazení instance kontejneru v Azure pomocí šablony ARM

Použijte Azure Container Instances ke spouštění kontejnerů Docker bez serveru v Azure s využitím jednoduchosti a rychlosti. Pokud nepotřebujete úplnou platformu orchestrace kontejnerů, jako je třeba služba Azure Kubernetes, nasaďte aplikaci na vyžádání do instance kontejneru na vyžádání. V tomto rychlém startu použijete šablonu Azure Resource Manager (šablona ARM) k nasazení izolovaného kontejneru Docker a zpřístupnění jeho webové aplikace pomocí veřejné IP adresy.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aci-linuxcontainer-public-ip%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-aci-linuxcontainer-public-ip/).

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.containerinstance/aci-linuxcontainer-public-ip/azuredeploy.json":::

Následující prostředek je definován v šabloně:

* **[Microsoft. ContainerInstance/containerGroups](/azure/templates/microsoft.containerinstance/containergroups)**: Vytvořte skupinu kontejnerů Azure. Tato šablona definuje skupinu sestávající z jedné instance kontejneru.

Další příklady šablon Azure Container Instances najdete v [galerii šablon rychlý Start](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Containerinstance&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Nasazení šablony

 1. Vyberte následující obrázek a přihlaste se k Azure a otevřete šablonu. Šablona vytvoří registr a repliku v jiném umístění.

    [![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aci-linuxcontainer-public-ip%2Fazuredeploy.json)

 2. Vyberte nebo zadejte následující hodnoty.

    * **Předplatné:** Vyberte předplatné Azure.
    * **Skupina prostředků**: vyberte **vytvořit novou**, zadejte jedinečný název pro skupinu prostředků a pak vyberte **OK**.
    * **Umístění:**: Vyberte umístění pro skupinu prostředků. Příklad: **střed USA**.
    * **Název**: přijměte vygenerovaný název instance nebo zadejte název.
    * **Image**: přijměte výchozí název Image. Tato ukázková bitová kopie systému Linux zapisuje malou webovou aplikaci napsanou v Node.js, která slouží jako statická stránka HTML. 

    Přijměte výchozí hodnoty pro zbývající vlastnosti.

    Přečtěte si podmínky a ujednání. Pokud souhlasíte, vyberte Souhlasím **s výše uvedenými podmínkami a ujednáními**.

    ![Vlastnosti šablony](media/container-instances-quickstart-template/template-properties.png)

 3. Po úspěšném vytvoření instance obdržíte oznámení:

    ![Oznámení portálu](media/container-instances-quickstart-template/deployment-notification.png)

 K nasazení šablony se použije Azure Portal. Kromě Azure Portal můžete použít Azure PowerShell, Azure CLI a REST API. Další informace o dalších metodách nasazení najdete v tématu [Nasazení šablon](../azure-resource-manager/templates/deploy-cli.md).

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

Pomocí Azure Portal nebo nástroje, jako je [Azure CLI](container-instances-quickstart.md) , zkontrolujte vlastnosti instance kontejneru.

1. Na portálu vyhledejte Container Instances a vyberte vytvořenou instanci kontejneru.

1. Na stránce **Přehled** si poznamenejte **stav** instance a její **IP adresa**.

    ![Přehled instancí](media/container-instances-quickstart-template/aci-overview.png)

2. Jakmile je jeho stav *spuštěný*, přejděte v prohlížeči na IP adresu. 

    ![Aplikace nasazená pomocí služby Azure Container Instances zobrazená v prohlížeči](media/container-instances-quickstart-template/view-application-running-in-an-azure-container-instance.png)

### <a name="view-container-logs"></a>Zobrazení protokolů kontejneru

Prohlížení protokolů pro instanci kontejneru je užitečné při řešení problémů s kontejnerem nebo aplikací, která se v něm spouští.

Chcete-li zobrazit protokoly kontejneru, v části **Nastavení** vyberte **protokoly kontejnerů**  >  . Měl by se zobrazit požadavek HTTP GET, který se vygeneroval, když jste aplikaci zobrazili v prohlížeči.

![Protokoly kontejneru na webu Azure Portal](media/container-instances-quickstart-template/aci-logs.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete s kontejnerem hotovi, vyberte na stránce **Přehled** pro instanci kontejneru možnost **Odstranit**. Po zobrazení výzvy potvrďte odstranění.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili službu Azure Container instance z veřejné image Microsoft. Pokud si chcete sestavit image kontejneru a nasadit ji z privátního registru kontejnerů Azure, pokračujte ke kurzu služby Azure Container Instances.

> [!div class="nextstepaction"]
> [Kurz: vytvoření image kontejneru pro nasazení do Azure Container Instances](./container-instances-tutorial-prepare-app.md)

Podrobný kurz, který vás provede procesem vytvoření šablony, najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Kurz: vytvoření a nasazení první šablony ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
