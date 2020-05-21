---
title: 'Rychlý Start: zabezpečení virtuálního centra pomocí Azure Firewall Manager Preview – šablona Správce prostředků'
description: Přečtěte si, jak zabezpečit virtuální rozbočovač pomocí správce Azure Firewall ve verzi Preview.
services: firewall-manager
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.date: 05/19/2020
ms.author: victorh
ms.openlocfilehash: b9839e51fcea1e8fe4adc4760e16ae2d73b163ee
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83694233"
---
# <a name="quickstart-secure-your-virtual-hub-using-azure-firewall-manager---resource-manager-template"></a>Rychlý Start: zabezpečení virtuálního rozbočovače pomocí nástroje Azure Firewall Manager – šablona Správce prostředků

V tomto rychlém startu použijete šablonu Správce prostředků k zabezpečení virtuálního centra pomocí nástroje Azure Firewall Manager Preview.

Nasazená brána firewall má pravidlo aplikace, které umožňuje připojení `www.microsoft.com` . K otestování brány firewall jsou nasazeny dva virtuální počítače s Windows serverem 2019. K připojení k serveru úloh se používá jeden server skoků. Ze serveru úloh se můžete připojit pouze k `www.microsoft.com` .

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Další informace o nástroji Azure Firewall Manager Preview najdete v tématu [co je verze preview Azure firewall Manageru?](overview.md).

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-secured-virtual-hub"></a>Vytvoření zabezpečeného virtuálního rozbočovače

Tato šablona vytvoří zabezpečeného virtuálního rozbočovače pomocí nástroje Azure Firewall Manager Preview společně s nezbytnými prostředky pro podporu tohoto scénáře.

### <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je ze [šablon Azure pro rychlý Start](https://github.com/Azure/azure-quickstart-templates/blob/master/fwm-docs-qs/azuredeploy.json).

:::code language="json" source="~/quickstart-templates/fwm-docs-qs/azuredeploy.json" range="001-477" highlight="47-76":::

V šabloně je definováno víc prostředků Azure:

- [**Microsoft. Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft. Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft. Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft. Network/firewallPolicies**](/azure/templates/microsoft.network/firewallPolicies)
- [**Microsoft. Network/routeTables**](/azure/templates/microsoft.network/routeTables)
- [**Microsoft. Network/virtualWans**](/azure/templates/microsoft.network/virtualWans)
- [**Microsoft. Network/virtualHubs**](/azure/templates/microsoft.network/virtualHubs)

### <a name="deploy-the-template"></a>Nasazení šablony

Nasadit šablonu Správce prostředků do Azure:

1. Vyberte **nasadit do Azure** a přihlaste se k Azure a otevřete šablonu. Šablona vytvoří Azure Firewall, virtuální síť WAN a virtuální rozbočovač, síťovou infrastrukturu a dva virtuální počítače.

   [![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffwm-docs-qs%2Fazuredeploy.json)

2. Na portálu na stránce **Zabezpečená virtuální centra** zadejte nebo vyberte následující hodnoty:
   - Předplatné: vyberte z existujících předplatných. 
   - Skupina prostředků: vyberte z existujících skupin prostředků nebo vyberte **vytvořit novou**a vyberte **OK**.
   - Umístění: vyberte umístění.
   - Uživatelské jméno správce: zadejte uživatelské jméno pro uživatelský účet správce. 
   - Heslo správce: zadejte heslo nebo klíč správce.

3. Vyberte **Zkontrolovat a vytvořit** a potom **Vytvořit**. Dokončení nasazení může trvat 10 minut nebo i déle.

## <a name="validate-the-deployment"></a>Ověření nasazení

Nyní otestujte pravidla brány firewall a potvrďte, že funguje podle očekávání.

1. V Azure Portal zkontrolujte nastavení sítě pro virtuální počítač **SRV** a zaznamenejte si privátní IP adresu.
2. Připojte vzdálenou plochu k virtuálnímu počítači **skoku** a přihlaste se. Odtud otevřete připojení ke vzdálené ploše v rámci **úlohy-SRV** privátní IP adresa.

3. Otevřete prohlížeč Internet Explorer a přejděte na adresu `www.microsoft.com`.
4. **OK**  >  V okně výstrahy zabezpečení aplikace Internet Explorer vyberte OK**Zavřít** .

   Měla by se zobrazit Domovská stránka společnosti Microsoft.

5. Přejděte na `www.google.com`.

   Brána firewall by vás měla zablokovat.

Takže teď ověříte, že pravidla brány firewall fungují:

* Můžete přejít na jediný povolený plně kvalifikovaný název domény, ale jinam už ne.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už nepotřebujete prostředky, které jste vytvořili v bráně firewall, odstraňte skupinu prostředků. Tím se odebere brána firewall a všechny související prostředky.

Pokud chcete odstranit skupinu prostředků, zavolejte `Remove-AzResourceGroup` rutinu:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o důvěryhodných partnerech zabezpečení](trusted-security-partners.md)
