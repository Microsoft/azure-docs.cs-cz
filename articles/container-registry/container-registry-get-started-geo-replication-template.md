---
title: Rychlý Start – vytvoření geograficky replikované šablony registru-Správce prostředků
description: Naučte se vytvářet geograficky replikovaný registr kontejnerů Azure pomocí šablony Azure Resource Manager.
services: azure-resource-manager
author: dlepow
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: danlep
ms.date: 05/26/2020
ms.openlocfilehash: 6e76e2966cd2bcba2606f6b87c99cb34208f8f24
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/31/2020
ms.locfileid: "84234454"
---
# <a name="quickstart-create-a-geo-replicated-container-registry-by-using-a-resource-manager-template"></a>Rychlý Start: vytvoření geograficky replikovaného registru kontejnerů pomocí šablony Správce prostředků

V tomto rychlém startu se dozvíte, jak vytvořit instanci Azure Container Registry pomocí Azure Resource Manager šablony. Šablona nastaví [geograficky replikovaný](container-registry-geo-replication.md) registr, který automaticky synchronizuje obsah registru ve více než jedné oblasti Azure. Geografická replikace umožňuje přístup k obrázkům z regionálních nasazení v rámci sítě a současně zajišťuje jednotné prostředí pro správu. Je to funkce úrovně služby [Premium](container-registry-skus.md) Registry. 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Žádné

## <a name="create-a-geo-replicated-registry"></a>Vytvoření geograficky replikovaného registru

### <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-container-registry-geo-replication/). Šablona nastaví registr a dodatečnou místní repliku.

:::code language="json" source="~/quickstart-templates/101-container-registry-geo-replication/azuredeploy.json" range="1-81" highlight="45-74" :::

V šabloně jsou definované následující prostředky:

* **[Microsoft. ContainerRegistry/Registry](/azure/templates/microsoft.containerregistry/registries)**: vytvoření služby Azure Container Registry
* **[Microsoft. ContainerRegistry/Registry/Replication](/azure/templates/microsoft.containerregistry/registries/replications)**: vytvoření repliky registru kontejnerů

Další příklady šablon Azure Container Registry najdete v [galerii šablon rychlý Start](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Containerregistry&pageNumber=1&sort=Popular).

### <a name="deploy-the-template"></a>Nasazení šablony

 1. Vyberte následující obrázek a přihlaste se k Azure a otevřete šablonu.

    [![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-container-registry-geo-replication%2Fazuredeploy.json)

 2. Vyberte nebo zadejte následující hodnoty.

    * **Předplatné:** Vyberte předplatné Azure.
    * **Skupina prostředků**: vyberte **vytvořit novou**, zadejte jedinečný název pro skupinu prostředků a pak vyberte **OK**.
    * **Umístění:**: Vyberte umístění pro skupinu prostředků. Příklad: **střed USA**.
    * **Název ACR**: přijměte vygenerovaný název registru nebo zadejte název. Musí být globálně jedinečný.
    * **Umístění**: přijměte vygenerované umístění pro domovskou repliku registru nebo zadejte umístění, jako je například **střed USA**. 
    * **Umístění repliky ACR**: zadejte umístění repliky registru s použitím krátkého názvu oblasti. Musí se lišit od umístění v domovském registru. Příklad: **westeurope**.
    * **Souhlasím s podmínkami a ujednáními uvedenými nahoře**: vyberte.

        :::image type="content" source="media/container-registry-get-started-geo-replication-template/template-properties.png" alt-text="Vlastnosti šablony":::

 3. Pokud souhlasíte s podmínkami a ujednáními, vyberte **koupit**. Po úspěšném vytvoření registru se zobrazí oznámení:

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

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili Azure Container Registry se šablonou Správce prostředků a nakonfigurovali jste repliku registru v jiném umístění. Pokračujte Azure Container Registry výukové kurzy, kde najdete hlubší přehled na ACR.

> [!div class="nextstepaction"]
> [Kurzy Azure Container Registry](container-registry-tutorial-prepare-registry.md)

Podrobný kurz, který vás provede procesem vytvoření šablony, najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Kurz: vytvoření a nasazení první šablony Azure Resource Manager](../azure-resource-manager/templates/template-tutorial-create-first-template.md)