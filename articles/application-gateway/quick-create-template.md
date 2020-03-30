---
title: 'Úvodní příručka: Přímý webový provoz pomocí šablony Správce prostředků'
titleSuffix: Azure Application Gateway
description: Zjistěte, jak pomocí šablony Správce prostředků vytvořit aplikační bránu Azure, která směruje webový provoz do virtuálních počítačů v back-endovém fondu.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 03/23/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 0612371605f6b216eadeef49d9adb1497ba31591
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80135969"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---resource-manager-template"></a>Úvodní příručka: Přímý webový provoz pomocí azure application gateway – šablona Správce prostředků

V tomto rychlém startu použijete šablonu Správce prostředků k vytvoření brány aplikací Azure. Potom otestujete bránu aplikace a ujistěte se, že funguje správně.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Tento rychlý start můžete taky dokončit pomocí [portálu Azure](quick-create-portal.md)Portal , [Azure PowerShellu](quick-create-powershell.md)nebo [Azure CLI](quick-create-cli.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-application-gateway"></a>Vytvoření služby Application Gateway

Z důvodu jednoduchosti tato šablona vytvoří jednoduché nastavení s veřejnou front-endovou IP adresou, základním naslouchacím procesem pro hostování jedné sítě na aplikační bráně, základního pravidla směrování požadavků a dvou virtuálních počítačů v back-endovém fondu.

### <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je ze [šablon Azure QuickStart](https://github.com/Azure/azure-quickstart-templates/blob/master/ag-docs-qs/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/ag-docs-qs/azuredeploy.json" range="001-343" highlight="197-297":::

V šabloně je definováno více prostředků Azure:

- [**Microsoft.Network/applicationgateways**](/azure/templates/microsoft.network/applicationgateways)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses) : jeden pro aplikační bránu a dva pro virtuální počítače.
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines) : dva virtuální počítače
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) : dva pro virtuální počítače
- [**Microsoft.Compute/virtualMachine/extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions) : konfigurace služby IIS a webových stránek


### <a name="deploy-the-template"></a>Nasazení šablony

Nasazení šablony Správce prostředků do Azure:

1. Vyberte **Nasazení do Azure,** abyste se přihlásili do Azure a otevřeli šablonu. Šablona vytvoří aplikační bránu, síťovou infrastrukturu a dva virtuální počítače v back-endovém fondu se službou IIS.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-qs%2Fazuredeploy.json"><img src="./media/quick-create-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Vyberte nebo vytvořte skupinu prostředků, zadejte uživatelské jméno a heslo správce virtuálního počítače.
3. Vyberte **Souhlasím s výše uvedenými podmínkami** a pak vyberte **možnost Koupit**. Dokončení nasazení může trvat 20 minut nebo déle.

## <a name="validate-the-deployment"></a>Ověření nasazení

I když služba IIS není nutné k vytvoření brány aplikace, je nainstalována k ověření, pokud Azure úspěšně vytvořil aplikační bránu. Pomocí služby IIS otestujte bránu aplikace:

1. Nastránce **Přehled** najděte veřejnou IP adresu aplikační brány. ![Zaznamenejte](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) veřejnou IP adresu aplikační brány Nebo můžete do vyhledávacího pole vybrat **všechny prostředky**, zadat *adresu myAGPublicIPAddress* a poté ji vybrat ve výsledcích hledání. Azure zobrazí veřejnou IP adresu na stránce **Přehled.**
2. Zkopírujte veřejnou IP adresu a vložte ji do adresního řádku prohlížeče a projděte tuto IP adresu.
3. Zkontrolujte odpověď. Platná odpověď ověří, zda byla aplikační brána úspěšně vytvořena a může se úspěšně připojit k back-endu.

   ![Otestování aplikační brány](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

   Aktualizujte prohlížeč vícekrát a měli byste vidět připojení k myVM1 a myVM2.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete prostředky, které jste vytvořili pomocí aplikační brány, odstraňte skupinu prostředků. Tím odeberete bránu aplikace a všechny související prostředky.

Chcete-li odstranit skupinu `Remove-AzResourceGroup` prostředků, zavolejte rutinu:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Správa webového provozu pomocí aplikační brány a Azure CLI](./tutorial-manage-web-traffic-cli.md)
