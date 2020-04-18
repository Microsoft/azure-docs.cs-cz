---
title: 'Úvodní příručka: Vytvoření brány Azure Firewall s více veřejnými IP adresami – šablona Správce prostředků'
description: Přečtěte si, jak pomocí šablony Správce prostředků vytvořit bránu Azure firewall s více veřejnými IP adresami.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: victorh
ms.openlocfilehash: 3d58173d239e7a9249b588ff038ea46cfedb27a3
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605209"
---
# <a name="quickstart-create-an-azure-firewall-with-multiple-public-ip-addresses---resource-manager-template"></a>Úvodní příručka: Vytvoření brány Azure Firewall s více veřejnými IP adresami – šablona Správce prostředků

V tomto rychlém startu použijete šablonu Správce prostředků k nasazení brány Azure Firewall s více veřejnými IP adresami.

Nasazená brána firewall obsahuje pravidla shromažďování pravidel NAT, která umožňují připojení RDP ke dvěma virtuálním počítačům se systémem Windows Server 2019.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Další informace o Azure Firewall s více veřejnými IP adresami najdete [v tématu Nasazení brány Azure firewall s více veřejnými IP adresami pomocí Azure PowerShellu](deploy-multi-public-ip-powershell.md).

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-azure-firewall"></a>Vytvoření brány Azure Firewall

Tato šablona vytvoří bránu Azure Firewall se dvěma veřejnými IP adresami spolu s potřebnými prostředky pro podporu brány Azure Firewall.

### <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je ze [šablon Azure QuickStart](https://github.com/Azure/azure-quickstart-templates/blob/master/fw-docs-qs/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/fw-docs-qs/azuredeploy.json" range="001-391" highlight="238-370":::

V šabloně je definováno více prostředků Azure:

- [**Microsoft.Network/publicIPAdresy**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.network/routeTables)


### <a name="deploy-the-template"></a>Nasazení šablony

Nasazení šablony Správce prostředků do Azure:

1. Vyberte **Nasazení do Azure,** abyste se přihlásili do Azure a otevřeli šablonu. Šablona vytvoří bránu Azure Firewall, síťovou infrastrukturu a dva virtuální počítače.

   [![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffw-docs-qs%2Fazuredeploy.json)

2. Na portálu na stránce **Vytvořit bránu firewall Azure s více ip veřejnými adresami** zadejte nebo vyberte následující hodnoty:
   - Předplatné: Výběr ze stávajících předplatných 
   - Skupina prostředků: Vyberte z existujících skupin prostředků nebo vyberte **Vytvořit nový**a vyberte **OK**.
   - Lokalita: Výběr lokality
   - Uživatelské jméno správce: Zadejte uživatelské jméno uživatelského účtu správce. 
   - Heslo správce: Zadejte heslo nebo klíč správce.

3. Vyberte **Souhlasím s výše uvedenými podmínkami** a pak vyberte **možnost Koupit**. Dokončení nasazení může trvat 10 minut nebo déle.

## <a name="validate-the-deployment"></a>Ověření nasazení

Na webu Azure Portal zkontrolujte nasazené prostředky. Poznamenejte si veřejné IP adresy brány firewall.  

Připojení k veřejným IP adresám brány firewall použijte připojení ke vzdálené ploše. Úspěšná připojení demonstruje pravidla naváděcí síť firewall, která umožňují připojení k serverům back-endu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již nepotřebujete prostředky, které jste vytvořili pomocí brány firewall, odstraňte skupinu prostředků. Tím odeberete bránu firewall a všechny související prostředky.

Chcete-li odstranit skupinu `Remove-AzResourceGroup` prostředků, zavolejte rutinu:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: Nasazení a konfigurace Azure Firewall v hybridní síti pomocí portálu Azure](tutorial-hybrid-portal.md)