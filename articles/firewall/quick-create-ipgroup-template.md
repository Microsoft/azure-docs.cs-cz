---
title: 'Rychlý Start: vytvoření Azure Firewall a skupin IP – šablona Správce prostředků'
description: V tomto rychlém startu se naučíte používat šablonu Azure Resource Manager (šablonu ARM) k vytvoření Azure Firewall a skupin IP adres.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 08/28/2020
ms.author: victorh
ms.openlocfilehash: 7966cbe44fc8984cf3c3d9e2d08a11bf599ff158
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "89079128"
---
# <a name="quickstart-create-an-azure-firewall-and-ip-groups---arm-template"></a>Rychlý Start: vytvoření Azure Firewall a skupin IP – šablona ARM

V tomto rychlém startu použijete šablonu Azure Resource Manager (šablona ARM) k nasazení Azure Firewall se vzorovými skupinami IP použitými v pravidle sítě a v pravidle aplikace. Skupina IP je prostředek nejvyšší úrovně, který umožňuje definovat a seskupovat IP adresy, rozsahy a podsítě do jednoho objektu. To je užitečné pro správu IP adres v pravidlech Azure Firewall. Můžete buď ručně zadat IP adresy, nebo je importovat ze souboru.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-ipgroups-and-linux-jumpbox%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Kontrola šablony

Tato šablona vytvoří Azure Firewall a skupiny IP adres, včetně nezbytných prostředků pro podporu Azure Firewall.

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-azurefirewall-create-with-ipgroups-and-linux-jumpbox).

:::code language="json" source="~/quickstart-templates/101-azurefirewall-create-with-ipgroups-and-linux-jumpbox/azuredeploy.json":::

V šabloně je definováno víc prostředků Azure:

- [**Microsoft. Network/ipGroups**](/azure/templates/microsoft.network/ipGroups)
- [**Microsoft. Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft. Network/routeTables**](/azure/templates/microsoft.network/routeTables)
- [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft. Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft. Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)

## <a name="deploy-the-template"></a>Nasazení šablony

Nasazení šablony ARM do Azure:

1. Vyberte **nasadit do Azure** a přihlaste se k Azure a otevřete šablonu. Šablona vytvoří Azure Firewall, síťovou infrastrukturu a dva virtuální počítače.

   [![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-ipgroups-and-linux-jumpbox%2Fazuredeploy.json)

2. Na portálu na stránce **vytvořit Azure firewall se IpGroups** zadejte nebo vyberte následující hodnoty:
   - Předplatné: vyberte z existujících předplatných. 
   - Skupina prostředků: vyberte z existujících skupin prostředků nebo vyberte **vytvořit novou**a vyberte **OK**.
   - Umístění: vyberte umístění.
   - Virtual Network název: zadejte název nové virtuální sítě (VNet). 
   - Název skupiny IP adres 1: název typu pro skupinu IP adres 1 
   - Název skupiny IP adres 2: název typu pro skupinu IP adres 2 
   - Uživatelské jméno správce: zadejte uživatelské jméno pro uživatelský účet správce. 
   - Ověřování: vyberte sshPublicKey nebo heslo 
   - Heslo správce: zadejte heslo nebo klíč správce.

3. Vyberte Souhlasím **s podmínkami a ujednáními uvedenými nahoře** a pak vyberte **koupit**. Dokončení nasazení může trvat 10 minut nebo i déle.

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

V Azure Portal zkontrolujte nasazené prostředky, zejména pravidla brány firewall, která používají skupiny IP adres.

:::image type="content" source="media/quick-create-ipgroup-template/ipgroups.png" alt-text="Skupiny IP adres.":::

:::image type="content" source="media/quick-create-ipgroup-template/network-rule.png" alt-text="Skupiny IP adres.":::

Další informace o syntaxi a vlastnostech JSON pro bránu firewall v šabloně najdete v referenčních informacích k [šabloně Microsoft. Network azureFirewalls](/azure/templates/Microsoft.Network/2019-11-01/azureFirewalls).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už nepotřebujete prostředky, které jste vytvořili v bráně firewall, odstraňte skupinu prostředků. Tím se odebere brána firewall a všechny související prostředky.

Pokud chcete odstranit skupinu prostředků, zavolejte `Remove-AzResourceGroup` rutinu:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: nasazení a konfigurace Azure Firewall v hybridní síti pomocí Azure Portal](tutorial-hybrid-portal.md)