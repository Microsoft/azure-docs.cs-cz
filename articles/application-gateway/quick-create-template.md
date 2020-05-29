---
title: 'Rychlý Start: směrování webového provozu pomocí šablony Správce prostředků'
titleSuffix: Azure Application Gateway
description: Naučte se používat šablonu Správce prostředků k vytvoření Application Gateway Azure, která směruje webový provoz do virtuálních počítačů v back-endu fondu.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 05/28/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 986e061c03634fe33af985c9d11569848500862c
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/29/2020
ms.locfileid: "84170490"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---resource-manager-template"></a>Rychlý Start: směrování webového provozu pomocí šablony Azure Application Gateway-Správce prostředků

V tomto rychlém startu použijete šablonu Správce prostředků k vytvoření Application Gateway Azure. Pak otestujete Aplikační bránu a ujistěte se, že funguje správně.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Tento rychlý Start můžete také dokončit pomocí [Azure Portal](quick-create-portal.md), [Azure PowerShell](quick-create-powershell.md)nebo rozhraní příkazového [řádku Azure CLI](quick-create-cli.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-application-gateway"></a>Vytvoření služby Application Gateway

V zájmu zjednodušení Tato šablona vytvoří jednoduché nastavení s veřejnou front-end IP adresou, základní naslouchací proces, který hostí jednu lokalitu v aplikační bráně, pravidlo základního směrování požadavku a dva virtuální počítače ve fondu back-end.

### <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je ze [šablon Azure pro rychlý Start](https://github.com/Azure/azure-quickstart-templates/blob/master/ag-docs-qs/azuredeploy.json) .

:::code language="json" source="~/quickstart-templates/ag-docs-qs/azuredeploy.json" range="001-343" highlight="197-297":::

V šabloně je definováno víc prostředků Azure:

- [**Microsoft. Network/applicationgateways**](/azure/templates/microsoft.network/applicationgateways)
- [**Microsoft. Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses) : jedna pro aplikační bránu a dvě pro virtuální počítače.
- [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft. COMPUTE/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines) : dva virtuální počítače
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) : dvě pro virtuální počítače
- [**Microsoft. COMPUTE/VirtualMachine/Extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions) : Konfigurace služby IIS a webových stránek


### <a name="deploy-the-template"></a>Nasazení šablony

Nasadit šablonu Správce prostředků do Azure:

1. Vyberte **nasadit do Azure** a přihlaste se k Azure a otevřete šablonu. Šablona vytvoří Aplikační bránu, síťovou infrastrukturu a dva virtuální počítače ve fondu back-end se službou IIS.

   [![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-qs%2Fazuredeploy.json)

2. Vyberte nebo vytvořte skupinu prostředků, zadejte uživatelské jméno a heslo správce virtuálního počítače.
3. Vyberte **zkontrolovat + vytvořit** a pak vyberte **vytvořit**.

   Dokončení nasazení může trvat až 20 minut nebo i déle.

## <a name="validate-the-deployment"></a>Ověření nasazení

I když služba IIS není nutná k vytvoření aplikační brány, nainstaluje se a ověří, jestli Azure úspěšně vytvořil Aplikační bránu. Použijte službu IIS k otestování služby Application Gateway:

1. Na stránce s **přehledem** vyhledejte veřejnou IP adresu pro aplikační bránu. ![ Zaznamenejte si veřejnou IP adresu aplikační brány ](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) nebo vyberte **všechny prostředky**, do vyhledávacího pole zadejte *myAGPublicIPAddress* a potom ho vyberte ve výsledcích hledání. Azure zobrazí veřejnou IP adresu na stránce **Přehled** .
2. Zkopírujte veřejnou IP adresu a vložte ji do adresního řádku prohlížeče, abyste mohli procházet tuto IP adresu.
3. Ověřte odpověď. Platná odpověď ověří, že se služba Application Gateway úspěšně vytvořila, a může se úspěšně připojit k back-endu.

   ![Otestování aplikační brány](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

   Aktualizujte prohlížeč několikrát a měli byste vidět připojení k myVM1 i myVM2.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už nepotřebujete prostředky, které jste vytvořili v rámci služby Application Gateway, odstraňte skupinu prostředků. Tím odeberete Aplikační bránu a všechny související prostředky.

Pokud chcete odstranit skupinu prostředků, zavolejte `Remove-AzResourceGroup` rutinu:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Správa webového provozu pomocí aplikační brány a Azure CLI](./tutorial-manage-web-traffic-cli.md)
