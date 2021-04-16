---
title: 'Rychlý Start: vytvoření služby Azure front-dveří pomocí šablony Azure Resource Manager (šablona ARM)'
description: V tomto rychlém startu se dozvíte, jak vytvořit službu Azure front-Dvířks pomocí šablony Azure Resource Manager (šablona ARM).
services: front-door
documentationcenter: ''
author: duongau
ms.author: duau
editor: ''
ms.date: 09/14/2020
ms.topic: quickstart
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: de8a592f6eecbb43b58a044096e8ba2e0f9b5973
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107539004"
---
# <a name="quickstart-create-a-front-door-using-an-arm-template"></a>Rychlý Start: Vytvoření front-dveří pomocí šablony ARM

V tomto rychlém startu se dozvíte, jak pomocí šablony Azure Resource Manager (šablony ARM) vytvořit přední dveře pro nastavení vysoké dostupnosti pro webový koncový bod.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-front-door-create-basic%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
* IP adresa nebo plně kvalifikovaný název domény webu nebo webové aplikace.

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-front-door-create-basic).

V tomto rychlém startu vytvoříte konfiguraci front-endu pomocí jediného back-endu a jedné výchozí cesty, která odpovídá `/*` .

:::code language="json" source="~/quickstart-templates/101-front-door-create-basic/azuredeploy.json":::

V této šabloně je definovaný jeden prostředek Azure:

* [**Microsoft. Network/frontDoors**](/azure/templates/microsoft.network/frontDoors)

## <a name="deploy-the-template"></a>Nasazení šablony

1. Vyberte **vyzkoušet** z následujícího bloku kódu a otevřete Azure Cloud Shell a pak postupujte podle pokynů pro přihlášení k Azure.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-front-door-create-basic/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Počkejte, dokud se nezobrazí výzva z konzoly.

1. Pro zkopírování skriptu PowerShellu vyberte **Kopírovat** z předchozího bloku kódu.

1. Klikněte pravým tlačítkem na Podokno konzole prostředí a pak vyberte **Vložit**.

1. Zadejte hodnoty.

    Nasazení šablony vytváří přední dvířka s jedním back-endu. V tomto příkladu `microsoft.com` se používá jako **backendAddress**.

    Název skupiny prostředků je název projektu s připojeným **RG** .

    > [!NOTE]
    > **frontDoorName** musí být globálně jedinečný název, aby bylo možné šablonu úspěšně nasadit. Pokud nasazení neproběhne úspěšně, začněte znovu s krokem 1.

    Nasazení šablony trvá několik minut. Výstup je po dokončení podobný tomuto:

    :::image type="content" source="./media/quickstart-create-front-door-template/front-door-template-deployment-powershell-output.png" alt-text="Výstup nasazení na front-Správce prostředků šablon PowerShellu":::

Azure PowerShell slouží k nasazení šablony. Kromě Azure PowerShell můžete použít také Azure Portal, Azure CLI a REST API. Další informace o dalších metodách nasazení najdete v tématu [Nasazení šablon](../azure-resource-manager/templates/deploy-portal.md).

## <a name="validate-the-deployment"></a>Ověření nasazení

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. V levém podokně vyberte **skupiny prostředků** .

1. Vyberte skupinu prostředků, kterou jste vytvořili v předchozí části. Výchozí název skupiny prostředků je název projektu s připojeným **RG** .

1. Vyberte přední dvířka, která jste vytvořili dříve, a klikněte na odkaz **hostitel s front-endu** . Odkaz otevře webový prohlížeč, který vás přesměruje na váš plně kvalifikovaný název domény, který jste definovali během vytváření.

    :::image type="content" source="./media/quickstart-create-front-door-template/front-door-overview.png" alt-text="Přehled portálu front-dveří":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už službu front-Dvířk nepotřebujete, odstraňte skupinu prostředků. Tím se odstraní přední dveře a všechny související prostředky.

Pokud chcete odstranit skupinu prostředků, zavolejte `Remove-AzResourceGroup` rutinu:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili přední dveře.

Další informace o tom, jak přidat vlastní doménu do předních dveří, najdete v výukových kurzech pro přední dveře.

> [!div class="nextstepaction"]
> [Kurzy front-dveří](front-door-custom-domain.md)
