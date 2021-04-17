---
title: 'Rychlý Start: vytvoření Azure DNS zóny a šablony Azure Resource Manager záznamu (šablona ARM)'
titleSuffix: Azure DNS
description: Naučíte se vytvořit zónu a záznam DNS v DNS Azure. Toto je podrobný rychlý Start k vytvoření a správě první zóny a záznamu DNS pomocí šablony Azure Resource Manager (šablona ARM).
services: dns
author: duongau
ms.author: duau
ms.date: 09/8/2020
ms.topic: quickstart
ms.service: dns
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 34e810fa8244cf3ff00e57f193624a2c1fa4d755
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107539021"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-an-arm-template"></a>Rychlý Start: vytvoření zóny Azure DNS a záznamu pomocí šablony ARM

V tomto rychlém startu se dozvíte, jak pomocí šablony Azure Resource Manager (šablony ARM) vytvořit zónu DNS se `A` záznamem.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-dns-new-zone%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-azure-dns-new-zone).

V tomto rychlém startu vytvoříte jedinečnou zónu DNS s příponou `azurequickstart.org` . `A`Záznam ukazující na dvě IP adresy bude také umístěn v zóně.

:::code language="json" source="~/quickstart-templates/101-azure-dns-new-zone/azuredeploy.json":::

V šabloně byly definovány dva prostředky Azure:

- [**Microsoft. Network/dnsZones**](/azure/templates/microsoft.network/dnsZones)
- [**Microsoft. Network/dnsZones/A**](/azure/templates/microsoft.network/dnsZones/A): používá se k vytvoření `A` záznamu v zóně.

Další šablony, které souvisejí s Azure Traffic Manager, najdete v tématu [šablony pro rychlý Start Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Nasazení šablony

1. Vyberte **vyzkoušet** z následujícího bloku kódu a otevřete Azure Cloud Shell a pak postupujte podle pokynů pro přihlášení k Azure.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-azure-dns-new-zone/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Počkejte, dokud se nezobrazí výzva z konzoly.

1. Pro zkopírování skriptu PowerShellu vyberte **Kopírovat** z předchozího bloku kódu.

1. Klikněte pravým tlačítkem na Podokno konzole prostředí a pak vyberte **Vložit**.

1. Zadejte hodnoty.

    Nasazení šablony vytvoří zónu s jedním `A` záznamem ukazujícím na dvě IP adresy. Název skupiny prostředků je název projektu s připojeným **RG** .

    Nasazení šablony trvá několik sekund. Výstup je po dokončení podobný tomuto:

    :::image type="content" source="./media/dns-getstarted-template/create-dns-zone-powershell-output.png" alt-text="Výstup nasazení prostředí PowerShell pro Správce prostředků Azure DNS zóny":::

Azure PowerShell slouží k nasazení šablony. Kromě Azure PowerShell můžete použít také Azure Portal, Azure CLI a REST API. Další informace o dalších metodách nasazení najdete v tématu [Nasazení šablon](../azure-resource-manager/templates/deploy-portal.md).

## <a name="validate-the-deployment"></a>Ověření nasazení

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. V levém podokně vyberte **skupiny prostředků** .

1. Vyberte skupinu prostředků, kterou jste vytvořili v předchozí části. Výchozí název skupiny prostředků je název projektu s připojeným **RG** .

1. Skupina prostředků by měla obsahovat následující zdroje informací:

    :::image type="content" source="./media/dns-getstarted-template/resource-group-dns-zone.png" alt-text="Skupina prostředků nasazení zóny DNS":::

1. Vyberte zónu DNS s příponou, `azurequickstart.org` abyste ověřili, jestli je zóna správně vytvořená, s použitím záznamu, který `A` odkazuje na hodnotu `1.2.3.4` a `1.2.3.5` .

    :::image type="content" source="./media/dns-getstarted-template/dns-zone-overview.png" alt-text="Nasazení zóny DNS":::

1. Zkopírujte jeden z názvů názvového serveru z předchozího kroku.

1. Otevřete příkazový řádek a spusťte následující příkaz:

   ```cmd
   nslookup www.<dns zone name> <name server name>
   ```

   Například:

   ```cmd
   nslookup www.2lwynbseszpam.azurequickstart.org ns1-09.azure-dns.com.
   ```

   Měl by se zobrazit něco podobného jako na následujícím snímku obrazovky:

    :::image type="content" source="./media/dns-getstarted-template/dns-zone-validation.png" alt-text="Nástroj nslookup pro zónu DNS":::

Název hostitele se `www.2lwynbseszpam.azurequickstart.org` překládá na `1.2.3.4` a `1.2.3.5` stejně jako jste nakonfigurovali. Tento výsledek ověří, že překlad názvů funguje správně.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete prostředky, které jste vytvořili v zóně DNS, odstraňte skupinu prostředků. Tím se odstraní zóna DNS a všechny související prostředky.

Pokud chcete odstranit skupinu prostředků, zavolejte `Remove-AzResourceGroup` rutinu:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili:

- Zóna DNS
- `A` zapisovací

Teď, když jste vytvořili svou první zónu a záznam DNS pomocí šablony ARM, můžete vytvořit záznamy pro webovou aplikaci ve vlastní doméně.

> [!div class="nextstepaction"]
> [Vytvoření záznamů DNS pro webovou aplikaci ve vlastní doméně](./dns-web-sites-custom-domain.md)
