---
title: 'Šablona Azure ExpressRoute: vytvoření okruhu ExpressRoute'
description: Vytvoření, zřízení, odstranění a zrušení zřízení okruhu ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 11/13/2019
ms.author: cherylmc
ms.reviewer: ganesr
ms.openlocfilehash: 78da84a462566cca1a2800174849159ace8dd6dc
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981139"
---
# <a name="create-an-expressroute-circuit-by-using-azure-resource-manager-template"></a>Vytvoření okruhu ExpressRoute pomocí šablony Azure Resource Manager

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Šablona Azure Resource Manageru](expressroute-howto-circuit-resource-manager-template.md)
> * [Video – Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-circuit-classic.md)
>

Naučte se vytvořit okruh ExpressRoute nasazením šablony Azure Resource Manager pomocí Azure PowerShell. Další informace o vývoji šablon Správce prostředků naleznete v [dokumentaci správce prostředků](/azure/azure-resource-manager/) a odkazu na [šablonu](/azure/templates/microsoft.network/expressroutecircuits).

## <a name="before-you-begin"></a>Než začnete

* Zkontrolujte [požadavky](expressroute-prerequisites.md) a [pracovních postupů](expressroute-workflows.md) předtím, než začnete s konfigurací.
* Ujistěte se, že máte oprávnění k vytvoření nových síťových prostředků. Pokud nemáte správná oprávnění, obraťte se na svého správce účtu.
* Je možné [zobrazení videa](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) před zahájením Chcete-li lépe pochopit kroky.

## <a name="create"></a>Vytvoření a zřízení okruhu ExpressRoute

[Šablony pro rychlý Start Azure](https://azure.microsoft.com/resources/templates/) mají dobrou kolekci správce prostředků šablon. Pomocí jedné z [existujících šablon](https://azure.microsoft.com/resources/templates/101-expressroute-circuit-create/) vytvoříte okruh ExpressRoute.

[!code-json[create-azure-expressroute-circuit](~/quickstart-templates/101-expressroute-circuit-create/azuredeploy.json)]

Pokud chcete zobrazit další související šablony, vyberte [sem](https://azure.microsoft.com/resources/templates/?term=expressroute).

Vytvoření okruhu ExpressRoute nasazením šablony:

1. Vyberte **vyzkoušet** z následujícího bloku kódu a pak podle pokynů se přihlaste ke službě Azure Cloud Shell.

    ```azurepowershell-interactive
    $circuitName = Read-Host -Prompt "Enter a circuit name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${circuitName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-expressroute-circuit-create/azuredeploy.json"

    $serviceProviderName = "Equinix"
    $peeringLocation = "Silicon Valley"
    $bandwidthInMbps = 500
    $sku_tier = "Premium"
    $sku_family = "MeteredData"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -circuitName $circuitName -serviceProviderName $serviceProviderName -peeringLocation $peeringLocation -bandwidthInMbps $bandwidthInMbps -sku_tier $sku_tier -sku_family $sku_family

    Write-Host "Press [ENTER] to continue ..."
    ```

   * **Úroveň SKU** určuje, jestli okruh ExpressRoute je [místní](expressroute-faqs.md#expressroute-local), Standard nebo [Premium](expressroute-faqs.md#expressroute-premium). Můžete zadat *Local*, *Standard* nebo *Premium*.
   * **Rodina SKU** určuje typ fakturace. Můžete zadat *Metereddata* pro tarif podle objemu dat a *Unlimiteddata* pro tarif s neomezenými daty. Můžete změnit typ fakturační z *Metereddata* k *Unlimiteddata*, ale nemůže změnit typ z *Unlimiteddata* k *Metereddata*. *Místní* okruh je jenom *Unlimiteddata* .
   * **Umístění partnerského vztahu** je fyzického umístění, kde se partnerský vztah Microsoftu.

     > [!IMPORTANT]
     > Určuje umístění partnerského vztahu [fyzické umístění](expressroute-locations.md) kde jste partnerského vztahu se společností Microsoft. Toto je **není** propojené s "Umístění" vlastnost, která odkazuje na zeměpisné oblasti, kde se nachází poskytovatel síťových prostředků Azure. Zatímco nesouvisí, je vhodné zvolit poskytovatel síťových prostředků geograficky v blízkosti umístění partnerského vztahu okruhu.

    Název skupiny prostředků je název oboru názvů Service Bus s připojeným **RG** .

2. Vyberte **kopírování** zkopírujte skript prostředí PowerShell.
3. Klikněte pravým tlačítkem na konzolu prostředí a pak vyberte **Vložit**.

Vytvoření centra událostí chvíli trvá.

Azure PowerShell slouží k nasazení šablony v tomto kurzu. Další metody nasazení šablony naleznete zde:

* [Pomocí Azure Portal](../azure-resource-manager/templates/deploy-portal.md).
* [Pomocí Azure CLI](../azure-resource-manager/templates/deploy-cli.md).
* [Pomocí REST API](../azure-resource-manager/templates/deploy-rest.md).

## <a name="delete"></a>Zrušení zřízení a odstranění okruhu ExpressRoute

Váš okruh ExpressRoute můžete odstranit tak, že vyberete **odstranit** ikonu. Všimněte si následujících informací:

* Od okruhu ExpressRoute je potřeba odpojit všechny virtuální sítě. Pokud tato operace se nezdaří, zkontrolujte, zda jsou propojeny žádné virtuální sítě k okruhu.
* Pokud je stav zřizování poskytovatele služeb okruh ExpressRoute **zřizování** nebo **zřízená** , musíte pracovat se svým poskytovatelem služeb zrušit zřízení okruhu na své straně. Pokračujeme v rezervovat prostředky a účtovat až do dokončení zrušení zřízení okruhu a informuje nás poskytovatelem služeb.
* Pokud poskytovatel služeb okruh zruší (poskytovatel služeb Stav zřizování je nastavena na **nezřízeno**), můžete odstranit okruh. Tím se zastaví účtování okruhu.

Okruh ExpressRoute můžete odstranit spuštěním následujícího příkazu PowerShellu:

```azurepowershell-interactive
$circuitName = Read-Host -Prompt "Enter the same circuit name that you used earlier"
$resourceGroupName = "${circuitName}rg"

Remove-AzExpressRouteCircuit -ResourceGroupName $resourceGroupName -Name $circuitName
```

## <a name="next-steps"></a>Další kroky

Po vytvoření váš okruh, pokračujte následující další kroky:

* [Vytvoření a úprava směrování pro okruh ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Propojení virtuální sítě pro váš okruh ExpressRoute](expressroute-howto-linkvnet-arm.md)
