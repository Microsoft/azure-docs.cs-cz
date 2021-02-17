---
title: 'Rychlý Start: vytvoření Azure Firewall a zásady brány firewall – šablona Správce prostředků'
description: V tomto rychlém startu nasadíte Azure Firewall a zásadu brány firewall.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: 26d7336d60449db11122c9921a3a59807bd82911
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100562179"
---
# <a name="quickstart-create-an-azure-firewall-and-a-firewall-policy---arm-template"></a>Rychlý Start: vytvoření Azure Firewall a šablony pro zásady brány firewall – ARM

V tomto rychlém startu použijete šablonu Azure Resource Manager (šablona ARM) k vytvoření Azure Firewall a zásady brány firewall. Zásady brány firewall obsahují pravidlo aplikace, které umožňuje připojení k, `www.microsoft.com` a pravidlo, které umožňuje připojení web Windows Update pomocí značky pro **windowsupdate** plně kvalifikovaný název domény. Síťové pravidlo umožňuje připojení UDP k časovému serveru na adrese 13.86.101.172.

V pravidlech se taky používají skupiny IP adres k definování **zdrojových** IP adres.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Informace o Azure Firewall Manageru najdete v tématu [co je Azure firewall Manager?](overview.md).

Informace o Azure Firewall najdete v tématu [co je Azure firewall?](../firewall/overview.md).

Informace o skupinách IP adres najdete v tématu [skupiny IP adres v Azure firewall](../firewall/ip-groups.md).

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Kontrola šablony

Tato šablona vytvoří zabezpečeného virtuálního rozbočovače pomocí nástroje Azure Firewall Manager spolu s nezbytnými prostředky pro podporu tohoto scénáře.

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups/).

:::code language="json" source="~/quickstart-templates/101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups/azuredeploy.json":::

V šabloně je definováno víc prostředků Azure:

- [**Microsoft. Network/ipGroups**](/azure/templates/microsoft.network/ipGroups)
- [**Microsoft. Network/firewallPolicies**](/azure/templates/microsoft.network/firewallPolicies)
- [**Microsoft. Network/firewallPolicies/ruleCollectionGroups**](/azure/templates/microsoft.network/firewallPolicies/ruleCollectionGroups)
- [**Microsoft. Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft. Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)

## <a name="deploy-the-template"></a>Nasazení šablony

Nasazení šablony ARM do Azure:

1. Vyberte **nasadit do Azure** a přihlaste se k Azure a otevřete šablonu. Šablona vytvoří Azure Firewall, virtuální síť WAN a virtuální rozbočovač, síťovou infrastrukturu a dva virtuální počítače.

   [![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups%2Fazuredeploy.json)

2. Na portálu na stránce **vytvořit bránu firewall a FirewallPolicy s pravidly a Ipgroups** zadejte nebo vyberte následující hodnoty:
   - Předplatné: vyberte z existujících předplatných.
   - Skupina prostředků: vyberte z existujících skupin prostředků nebo vyberte **vytvořit novou** a vyberte **OK**.
   - Oblast: Vyberte oblast.
   - Název brány firewall: zadejte název brány firewall.

3. Vyberte **Zkontrolovat a vytvořit** a potom **Vytvořit**. Dokončení nasazení může trvat 10 minut nebo i déle.

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

Po dokončení nasazení se zobrazí následující podobné prostředky.

:::image type="content" source="media/quick-firewall-policy/qs-deployed-resources.png" alt-text="Nasazené prostředky":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už nepotřebujete prostředky, které jste vytvořili v bráně firewall, odstraňte skupinu prostředků. Tím se odebere brána firewall a všechny související prostředky.

Pokud chcete odstranit skupinu prostředků, zavolejte `Remove-AzResourceGroup` rutinu:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přehled zásad Azure Firewall Manageru](policy-overview.md)
