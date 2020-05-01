---
title: 'Rychlý Start: vytvoření Azure Firewall se šablonou Zóny dostupnosti-Správce prostředků'
description: Nasaďte Azure Firewall pomocí šablony. Virtuální síť má jednu virtuální síť se třemi podsítěmi. Nasadí se dva virtuální počítače s Windows serverem. pole s odkazem a serverem.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.date: 04/30/2020
ms.author: victorh
ms.openlocfilehash: 17ab693033b61c25ba2f5b5bd588ef52caf8c046
ms.sourcegitcommit: acc558d79d665c8d6a5f9e1689211da623ded90a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2020
ms.locfileid: "82597701"
---
# <a name="quickstart-deploy-azure-firewall-with-availability-zones---resource-manager-template"></a>Rychlý Start: nasazení Azure Firewall se šablonou Zóny dostupnosti-Správce prostředků

V tomto rychlém startu použijete šablonu Správce prostředků k nasazení Azure Firewall ve třech Zóny dostupnosti. 

Šablona vytvoří testovací síťové prostředí s bránou firewall. Síť má jednu virtuální síť se třemi podsítěmi: *AzureFirewallSubnet*, *ServersSubnet*a *JumpboxSubnet*. Každá podsíť *ServersSubnet* a *JumpboxSubnet* má jeden virtuální počítač se dvěma jádry Windows serveru.

Brána firewall je v podsíti *AzureFirewallSubnet* a má kolekci pravidel aplikace s jedním pravidlem, které umožňuje přístup k `www.microsoft.com`.

Uživatelem definovaná trasa směrování v síti z podsítě *ServersSubnet* přes bránu firewall, kde se používají pravidla brány firewall.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Další informace o Azure Firewall najdete v tématu [nasazení a konfigurace Azure firewall použití Azure Portal](tutorial-firewall-deploy-portal.md).

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-azure-firewall-with-availability-zones"></a>Vytvoření Azure Firewall s Zóny dostupnosti

Tato šablona vytvoří Azure Firewall s Zóny dostupnosti, včetně nezbytných prostředků pro podporu Azure Firewall.

### <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je ze [šablon Azure pro rychlý Start](https://github.com/Azure/azure-quickstart-templates/blob/master/101-azurefirewall-with-zones-sandbox/azuredeploy.json).

:::code language="json" source="~/quickstart-templates/101-azurefirewall-with-zones-sandbox/azuredeploy.json" range="001-444" highlight="369-442":::

V šabloně je definováno víc prostředků Azure:

- [**Microsoft. Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft. Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft. Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft. Network/routeTables**](/azure/templates/microsoft.network/routeTables)

### <a name="deploy-the-template"></a>Nasazení šablony

Nasazení šablony Správce prostředků do Azure:

1. Vyberte **nasadit do Azure** a přihlaste se k Azure a otevřete šablonu. Šablona vytvoří Azure Firewall, síťovou infrastrukturu a dva virtuální počítače.

   [![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-with-zones-sandbox%2Fazuredeploy.json)

2. Na portálu na stránce **vytvořit nastavení izolovaného prostoru Azure firewall se zónami** zadejte nebo vyberte následující hodnoty:
   - **Skupina prostředků**: vyberte **vytvořit novou**, zadejte název skupiny prostředků a vyberte **OK**. 
   - **Virtual Network název**: zadejte název nové virtuální sítě. 
   - **Uživatelské jméno správce**: zadejte uživatelské jméno pro uživatelský účet správce.
   - **Heslo správce**: zadejte heslo správce. 

3. Přečtěte si podmínky a ujednání a potom vyberte Souhlasím **s výše uvedenými podmínkami a ujednáními** a pak vyberte **koupit**. Dokončení nasazení může trvat 10 minut nebo i déle.

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

Prozkoumejte prostředky, které byly vytvořeny pomocí brány firewall.

Další informace o syntaxi a vlastnostech JSON pro bránu firewall v šabloně najdete v článku [Microsoft. Network/azureFirewalls](/azure/templates/microsoft.network/azurefirewalls).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud je už nepotřebujete, můžete skupinu prostředků, bránu firewall a všechny související prostředky odebrat spuštěním příkazu `Remove-AzResourceGroup` PowerShellu. Pokud chcete odebrat skupinu prostředků s názvem *MyResourceGroup*, spusťte: 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```
Neodstraňujte skupinu prostředků a bránu firewall, pokud máte v plánu pokračovat na kurz monitorování brány firewall. 

## <a name="next-steps"></a>Další kroky

Dále můžete monitorovat protokoly Azure Firewall:

[Kurz: Monitorování protokolů brány Azure Firewall](./tutorial-diagnostics.md)
