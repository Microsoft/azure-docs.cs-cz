---
title: Vytvoření účtu Azure Maps pomocí šablony ARM | Mapy Microsoft Azure
description: Naučte se vytvořit účet Azure Maps pomocí šablony Azure Resource Manager (ARM).
author: philmea
ms.author: philmea
ms.date: 10/20/2020
ms.topic: how-to
ms.service: azure-maps
ms.openlocfilehash: c715c0639e962f76f669515c1d2c826c8cf6cc9e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92525078"
---
# <a name="create-your-azure-maps-account-using-an-arm-template"></a>Vytvoření účtu Azure Maps pomocí šablony ARM

Účet Azure Maps můžete vytvořit pomocí šablony Azure Resource Manager (ARM). Po použití účtu můžete rozhraní API implementovat na svém webu nebo v mobilní aplikaci.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-maps-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

Postup dokončení tohoto článku:

* Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-maps-create/).

:::code language="json" source="~/quickstart-templates/101-maps-create/azuredeploy.json":::

V této šabloně je definován prostředek účtu Azure Maps:

* [**Microsoft. Maps/Accounts**](/azure/templates/microsoft.maps/accounts): vytvořte účet Azure Maps.

## <a name="deploy-the-template"></a>Nasazení šablony

1. Vyberte následující obrázek a přihlaste se k Azure a otevřete šablonu. Šablona vytvoří účet Azure Maps.

    [![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-maps-create%2Fazuredeploy.json)

2. Vyberte nebo zadejte následující hodnoty.

    ![Portál pro nasazení šablony ARM](./media/how-to-create-template/create-account-using-template-portal.png)

    Pokud není zadaný, použijte k vytvoření účtu Azure Maps výchozí hodnotu.

    * **Předplatné:** Vyberte předplatné Azure.
    * **Skupina prostředků**: vyberte **vytvořit novou**, zadejte jedinečný název skupiny prostředků a pak klikněte na **OK**.
    * **Umístění:** Vyberte prosím umístění. Například **západní USA 2**.
    * **Název účtu**: zadejte název účtu Azure Maps, který musí být globálně jedinečný.
    * **Cenová úroveň**: vyberte příslušnou cenovou úroveň. výchozí hodnota pro šablonu je S0.

3. Vyberte **Zkontrolovat a vytvořit**. 
4. Potvrďte nastavení na stránce Kontrola a klikněte na **vytvořit**. Po úspěšném nasazení Azure Maps obdržíte oznámení:

    ![Oznámení portálu pro nasazení šablony ARM](./media/how-to-create-template/resource-manager-template-portal-deployment-notification.png)

Azure Portal slouží k nasazení šablony. Můžete také použít Azure PowerShell, Azure CLI a REST API. Další informace o dalších metodách nasazení najdete v tématu [Nasazení šablon](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

Azure Portal můžete použít ke kontrole účtu Azure Maps a zobrazení klíčů. K vypsání klíčů účtu můžete také použít následující skript Azure CLI.

```azurecli-interactive
az maps account keys list --name MyMapsAccount --resource-group MyResourceGroup
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků, která také odstraní účet Azure Maps. Odstranění skupiny prostředků pomocí Azure CLI:

```azurecli-interactive
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>Další kroky

Další informace o Azure Maps a Azure Resource Manager najdete dál v článcích níže.

- Vytvoření [ukázkové aplikace](quick-demo-map-app.md) Azure Maps
- Další informace o [šablonách ARM](../azure-resource-manager/templates/overview.md)