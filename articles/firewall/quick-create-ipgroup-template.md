---
title: 'Úvodní příručka: Vytvoření azure firewallu a skupin IP – šablona Správce prostředků'
description: Přečtěte si, jak pomocí šablony Správce prostředků vytvořit bránu Azure firewall a skupiny IP.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.date: 04/06/2020
ms.author: victorh
ms.openlocfilehash: 767e91af7fb5ff3de517a79ac4df55af5057c76f
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679832"
---
# <a name="quickstart-create-an-azure-firewall-and-ip-groups---resource-manager-template"></a>Úvodní příručka: Vytvoření azure firewallu a skupin IP – šablona Správce prostředků

V tomto rychlém startu použijete šablonu Správce prostředků k nasazení brány Azure Firewall s ukázkovými skupinami IP používanými v pravidle sítě a pravidlu aplikace.

Skupina IP je prostředek nejvyšší úrovně, který umožňuje definovat a seskupit ip adresy, rozsahy a podsítě do jednoho objektu. To je užitečné pro správu IP adres v pravidlech Azure Firewall. Můžete buď ručně zadat IP adresy nebo je importovat ze souboru.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-azure-firewall-and-ip-groups"></a>Vytvoření azure firewall a skupin ip

Tato šablona vytvoří Azure Firewall a IP skupiny, spolu s potřebné prostředky pro podporu Azure Firewall.

### <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je ze [šablon Azure QuickStart](https://github.com/Azure/azure-quickstart-templates/blob/master/101-azurefirewall-create-with-ipgroups-and-linux-jumpbox/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-azurefirewall-create-with-ipgroups-and-linux-jumpbox/azuredeploy.json" range="001-512" highlight="118-141":::

V šabloně je definováno více prostředků Azure:

- [**Soubor Microsoft.Network/ipGroups**](/azure/templates/microsoft.network/ipGroups)
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

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-ipgroups-and-linux-jumpbox%2Fazuredeploy.json"><img src="./media/quick-create-ipgroup-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Na portálu na stránce **Vytvořit bránu firewall Azure s IpGroups** zadejte nebo vyberte následující hodnoty:
   - Předplatné: Výběr ze stávajících předplatných 
   - Skupina prostředků: Vyberte z existujících skupin prostředků nebo vyberte **Vytvořit nový**a vyberte **OK**.
   - Lokalita: Výběr lokality
   - Název virtuální sítě: Zadejte název nové virtuální sítě (VNet) 
   - Název skupiny IP 1: Název typu pro skupinu IP 1 
   - Název skupiny IP 2: Název typu pro skupinu IP 2 
   - Uživatelské jméno správce: Zadejte uživatelské jméno uživatelského účtu správce. 
   - Ověřování: Vyberte sshPublicKey nebo heslo 
   - Heslo správce: Zadejte heslo nebo klíč správce.

3. Vyberte **Souhlasím s výše uvedenými podmínkami** a pak vyberte **možnost Koupit**. Dokončení nasazení může trvat 10 minut nebo déle.

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

Na webu Azure Portal zkontrolujte nasazené prostředky, zejména pravidla brány firewall, která používají SKUPINY IP.

:::image type="content" source="media/quick-create-ipgroup-template/ipgroups.png" alt-text="Skupiny IP.":::

:::image type="content" source="media/quick-create-ipgroup-template/network-rule.png" alt-text="Pravidla sítě.":::

Informace o syntaxi JSON a vlastnostech brány firewall v šabloně najdete v [tématu Microsoft.Network azureFirewalls reference šablony](https://docs.microsoft.com/azure/templates/Microsoft.Network/2019-11-01/azureFirewalls).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již nepotřebujete prostředky, které jste vytvořili pomocí brány firewall, odstraňte skupinu prostředků. Tím odeberete bránu firewall a všechny související prostředky.

Chcete-li odstranit skupinu `Remove-AzResourceGroup` prostředků, zavolejte rutinu:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: Nasazení a konfigurace Azure Firewall v hybridní síti pomocí portálu Azure](tutorial-hybrid-portal.md)