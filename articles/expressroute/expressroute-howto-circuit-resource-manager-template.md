---
title: 'Šablona Azure ExpressRoute: Vytvoření okruhu ExpressRoute'
description: Vytvořte, zřiďte, odstraňte a zrušte zřízení okruhu ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 11/13/2019
ms.author: cherylmc
ms.reviewer: ganesr
ms.openlocfilehash: 78da84a462566cca1a2800174849159ace8dd6dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981139"
---
# <a name="create-an-expressroute-circuit-by-using-azure-resource-manager-template"></a>Vytvoření okruhu ExpressRoute pomocí šablony Azure Resource Manager

> [!div class="op_single_selector"]
> * [Portál Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Šablona Azure Resource Manageru](expressroute-howto-circuit-resource-manager-template.md)
> * [Video – portál Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-circuit-classic.md)
>

Zjistěte, jak vytvořit okruh ExpressRoute nasazením šablony Azure Resource Manager pomocí Azure PowerShellu. Další informace o vývoji šablon Správce prostředků naleznete v [dokumentaci ke Správci prostředků](/azure/azure-resource-manager/) a [v odkazu na šablonu](/azure/templates/microsoft.network/expressroutecircuits).

## <a name="before-you-begin"></a>Než začnete

* Před zahájením konfigurace zkontrolujte [požadavky](expressroute-prerequisites.md) a [pracovní postupy.](expressroute-workflows.md)
* Ujistěte se, že máte oprávnění k vytváření nových síťových prostředků. Pokud nemáte správná oprávnění, obraťte se na správce účtu.
* Můžete [zobrazit video](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) před začátkem, abyste lépe porozuměli krokům.

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Vytvoření a zřízení okruhu ExpressRoute

[Šablony Azure Quickstart](https://azure.microsoft.com/resources/templates/) má dobrou kolekci šablony Správce prostředků. K vytvoření okruhu ExpressRoute se používá jedna z [existujících šablon.](https://azure.microsoft.com/resources/templates/101-expressroute-circuit-create/)

[!code-json[create-azure-expressroute-circuit](~/quickstart-templates/101-expressroute-circuit-create/azuredeploy.json)]

Chcete-li zobrazit další související šablony, vyberte [zde](https://azure.microsoft.com/resources/templates/?term=expressroute).

Postup vytvoření okruhu ExpressRoute nasazením šablony:

1. Vyberte **Vyzkoušet** z následujícího bloku kódu a postupujte podle pokynů pro přihlášení do prostředí Azure Cloud.

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

   * **Úroveň Skladové položky určuje,** zda je okruh ExpressRoute [místní](expressroute-faqs.md#expressroute-local), standardní nebo [premium](expressroute-faqs.md#expressroute-premium). Můžete zadat *místní*, *standardní* nebo *premium*.
   * **Skladová položka** určuje typ fakturace. Můžete zadat *Metereddata* pro datový tarif účtovaný účtárna a *Unlimiteddata* pro neomezený datový tarif. Typ fakturace můžete změnit z *Metereddata* na *Unlimiteddata*, ale nemůžete změnit typ z *Unlimiteddata* na *Metereddata*. Místní *Local* okruh je pouze *Unlimiteddata.*
   * **Umístění partnerského vztahu** je fyzické umístění, kde spolupracujete se společností Microsoft.

     > [!IMPORTANT]
     > Umístění partnerského vztahu označuje [fyzické umístění,](expressroute-locations.md) kde jste partnerského vztahu se společností Microsoft. To **není** propojena s vlastností "Umístění", která odkazuje na zeměpisné oblasti, kde je umístěn zprostředkovatele síťových prostředků Azure. I když nejsou ve spojení, je vhodné zvolit poskytovatele síťových prostředků geograficky v blízkosti umístění partnerského vztahu okruhu.

    Název skupiny prostředků je název oboru názvů servisní sběrnice s připojeným **rg.**

2. Vyberte **Kopírovat,** chcete-li zkopírovat skript PowerShellu.
3. Klepněte pravým tlačítkem myši na konzolu prostředí a vyberte příkaz **Vložit**.

Vytvoření centra událostí trvá několik okamžiků.

Azure PowerShell se používá k nasazení šablony v tomto kurzu. Další metody nasazení šablony naleznete v tématu:

* [Pomocí portálu Azure](../azure-resource-manager/templates/deploy-portal.md).
* [Pomocí azure cli](../azure-resource-manager/templates/deploy-cli.md).
* [Pomocí rozhraní REST API](../azure-resource-manager/templates/deploy-rest.md).

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>Zrušení zřízení a odstranění okruhu ExpressRoute

Okruh ExpressRoute můžete odstranit výběrem ikony **odstranění.** Všimněte si následujících informací:

* Od okruhu ExpressRoute je potřeba odpojit všechny virtuální sítě. Pokud se tato operace nezdaří, zkontrolujte, zda jsou všechny virtuální sítě propojeny s okruhem.
* Pokud je stav zřizování zprostředkovatele okruhů ExpressRoute **Provisioning** nebo **Provisioned,** musíte spolupracovat s poskytovatelem služeb, abyste zrušili zřízení okruhu na jejich straně. Budeme i nadále rezervovat zdroje a účtovat vám, dokud poskytovatel služeb dokončí zrušení zřízení okruhu a upozorní nás.
* Pokud poskytovatel služeb zrušil okruh (stav zřizování poskytovatele služeb je nastaven na **Není zřízeno**), můžete okruh odstranit. Tím se zastaví účtování okruhu.

Okruh ExpressRoute můžete odstranit spuštěním následujícího příkazu PowerShellu:

```azurepowershell-interactive
$circuitName = Read-Host -Prompt "Enter the same circuit name that you used earlier"
$resourceGroupName = "${circuitName}rg"

Remove-AzExpressRouteCircuit -ResourceGroupName $resourceGroupName -Name $circuitName
```

## <a name="next-steps"></a>Další kroky

Po vytvoření okruhu pokračujte následujícími kroky:

* [Vytvoření a úprava směrování okruhu ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Propojení virtuální sítě s okruhem ExpressRoute](expressroute-howto-linkvnet-arm.md)
