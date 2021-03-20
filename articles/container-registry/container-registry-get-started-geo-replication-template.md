---
title: Rychlý Start – vytvoření geograficky replikované šablony registru-Azure Resource Manager
description: Naučte se vytvářet geograficky replikovaný registr kontejnerů Azure pomocí šablony Azure Resource Manager.
services: azure-resource-manager
author: dlepow
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: danlep
ms.date: 10/06/2020
ms.openlocfilehash: 97b556e0329644b973def8333ddb5e70e370b0bc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91827008"
---
# <a name="quickstart-create-a-geo-replicated-container-registry-by-using-an-arm-template"></a>Rychlý Start: vytvoření geograficky replikovaného registru kontejnerů pomocí šablony ARM

V tomto rychlém startu se dozvíte, jak vytvořit instanci Azure Container Registry pomocí šablony Azure Resource Manager (šablona ARM). Šablona nastaví [geograficky replikovaný](container-registry-geo-replication.md) registr, který automaticky synchronizuje obsah registru ve více než jedné oblasti Azure. Geografická replikace umožňuje přístup k obrázkům z regionálních nasazení v rámci sítě a současně zajišťuje jednotné prostředí pro správu. Je to funkce úrovně služby [Premium](container-registry-skus.md) Registry.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-container-registry-geo-replication%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-container-registry-geo-replication/). Šablona nastaví registr a dodatečnou místní repliku.

:::code language="json" source="~/quickstart-templates/101-container-registry-geo-replication/azuredeploy.json":::

V šabloně jsou definované následující prostředky:

* **[Microsoft. ContainerRegistry/Registry](/azure/templates/microsoft.containerregistry/registries)**: vytvoření služby Azure Container Registry
* **[Microsoft. ContainerRegistry/Registry/Replication](/azure/templates/microsoft.containerregistry/registries/replications)**: vytvoření repliky registru kontejnerů

Další příklady šablon Azure Container Registry najdete v [galerii šablon rychlý Start](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Containerregistry&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Nasazení šablony

 1. Vyberte následující obrázek a přihlaste se k Azure a otevřete šablonu.

    [![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-container-registry-geo-replication%2Fazuredeploy.json)

 1. Vyberte nebo zadejte následující hodnoty.

    * **Předplatné:** Vyberte předplatné Azure.
    * **Skupina prostředků**: vyberte **vytvořit novou**, zadejte jedinečný název pro skupinu prostředků a pak vyberte **OK**.
    * **Oblast**: vyberte umístění pro skupinu prostředků. Příklad: **střed USA**.
    * **Název ACR**: přijměte vygenerovaný název registru nebo zadejte název. Musí být globálně jedinečný.
    * **Uživatel s rolí správce ACR povolen**: přijměte výchozí hodnotu.
    * **Umístění**: přijměte vygenerované umístění pro domovskou repliku registru nebo zadejte umístění, jako je například **střed USA**. 
    * **SKU ACR**: přijměte výchozí hodnotu.
    * **Umístění repliky ACR**: zadejte umístění repliky registru s použitím krátkého názvu oblasti. Musí se lišit od umístění v domovském registru. Příklad: **westeurope**.

        :::image type="content" source="media/container-registry-get-started-geo-replication-template/template-properties.png" alt-text="Vlastnosti šablony":::

1. Vyberte **zkontrolovat + vytvořit** a pak si prohlédněte podmínky a ujednání. Pokud souhlasíte, vyberte **vytvořit**.

1. Po úspěšném vytvoření registru se zobrazí oznámení:

     :::image type="content" source="media/container-registry-get-started-geo-replication-template/deployment-notification.png" alt-text="Oznámení portálu":::

 K nasazení šablony se použije Azure Portal. Kromě Azure Portal můžete použít Azure PowerShell, Azure CLI a REST API. Další informace o dalších metodách nasazení najdete v tématu [Nasazení šablon](../azure-resource-manager/templates/deploy-cli.md).

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

Pomocí Azure Portal nebo nástroje, jako je Azure CLI, si můžete prohlédnout vlastnosti registru kontejneru.

1. Na portálu vyhledejte Registry kontejneru a vyberte registr kontejnerů, který jste vytvořili.

1. Na stránce **Přehled** si poznamenejte **přihlašovací server** registru. Tento identifikátor URI použijte, když použijete Docker k označení a vložení obrázků do registru. Informace najdete v tématu [vložení první Image pomocí Docker CLI](container-registry-get-started-docker-cli.md).

    :::image type="content" source="media/container-registry-get-started-geo-replication-template/registry-overview.png" alt-text="Přehled registru":::

1. Na stránce **replikace** potvrďte umístění domovské repliky a repliku přidanou prostřednictvím šablony. V případě potřeby přidejte na tuto stránku další repliky.

    :::image type="content" source="media/container-registry-get-started-geo-replication-template/registry-replications.png" alt-text="Replikace registru":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud je už nepotřebujete, odstraňte skupinu prostředků, registr a repliku registru. Provedete to tak, že přejdete na Azure Portal, vyberete skupinu prostředků, která obsahuje registr, a pak vyberete **Odstranit skupinu prostředků**.

:::image type="content" source="media/container-registry-get-started-geo-replication-template/delete-resource-group.png" alt-text="Odstranění skupiny prostředků":::

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili Azure Container Registry se šablonou ARM a nakonfigurovali repliku registru v jiném umístění. Pokračujte Azure Container Registry výukové kurzy, kde najdete hlubší přehled na ACR.

> [!div class="nextstepaction"]
> [Kurzy Azure Container Registry](container-registry-tutorial-prepare-registry.md)

Podrobný kurz, který vás provede procesem vytvoření šablony, najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Kurz: vytvoření a nasazení první šablony ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
