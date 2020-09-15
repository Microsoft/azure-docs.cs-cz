---
title: 'Šablona Azure ExpressRoute: vytvoření okruhu ExpressRoute'
description: Přečtěte si, jak vytvořit okruh Azure ExpressRoute nasazením šablony Azure Resource Manager pomocí Azure PowerShell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 11/13/2019
ms.author: duau
ms.openlocfilehash: 3428f092c4b2356e9ea6afa54d1a03fc8b4f9f05
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2020
ms.locfileid: "89566173"
---
# <a name="create-an-expressroute-circuit-by-using-azure-resource-manager-template"></a>Vytvoření okruhu ExpressRoute pomocí šablony Azure Resource Manager

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Šablona Azure Resource Manageru](expressroute-howto-circuit-resource-manager-template.md)
> * [Video – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-circuit-classic.md)
>

Naučte se vytvořit okruh ExpressRoute nasazením šablony Azure Resource Manager pomocí Azure PowerShell. Další informace o vývoji šablon Správce prostředků naleznete v [dokumentaci správce prostředků](/azure/azure-resource-manager/) a odkazu na [šablonu](/azure/templates/microsoft.network/expressroutecircuits).

## <a name="before-you-begin"></a>Než začnete

* Než začnete s konfigurací, Projděte si [požadavky](expressroute-prerequisites.md) a [pracovní postupy](expressroute-workflows.md) .
* Ujistěte se, že máte oprávnění k vytváření nových síťových prostředků. Pokud nemáte správná oprávnění, obraťte se na správce účtu.
* Než začnete tento postup lépe pochopit, můžete si [video prohlédnout](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) .

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Vytvoření a zřízení okruhu ExpressRoute

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

   * **Úroveň SKU** určuje, jestli je okruh ExpressRoute [místní](expressroute-faqs.md#expressroute-local), Standard nebo [Premium](expressroute-faqs.md#expressroute-premium). Můžete zadat *místní*, * Standard nebo *Premium*. SKU nemůžete změnit z *úrovně Standard/Premium* na *místní*.
   * **Rodina SKU** určuje typ fakturace. Můžete zadat *Metereddata* pro měřený datový tarif a *Unlimiteddata* pro neomezený datový tarif. Typ fakturace můžete změnit z *Metereddata* na *Unlimiteddata*, ale nemůžete změnit typ z *Unlimiteddata* na *Metereddata*. *Místní* okruh je jenom *Unlimiteddata* .
   * **Umístění partnerského vztahu** je fyzické umístění, kde se s Microsoftem pracujete jako partnerský vztah.

     > [!IMPORTANT]
     > Umístění partnerského vztahu indikuje [fyzické umístění](expressroute-locations.md) , kde se s Microsoftem pracujete. Toto není **propojeno s vlastností** "umístění", která odkazuje na geografickou oblast, kde se nachází poskytovatel síťových prostředků Azure. I když se netýkají, je vhodné zvolit poskytovatele síťových prostředků geograficky blízko umístění partnerského vztahu okruhu.

    Název skupiny prostředků je název oboru názvů Service Bus s připojeným **RG** .

2. Vyberte **Kopírovat** a zkopírujte skript prostředí PowerShell.
3. Klikněte pravým tlačítkem na konzolu prostředí a pak vyberte **Vložit**.

Vytvoření centra událostí chvíli trvá.

Azure PowerShell slouží k nasazení šablony v tomto kurzu. Další metody nasazení šablony naleznete zde:

* [Pomocí Azure Portal](../azure-resource-manager/templates/deploy-portal.md).
* [Pomocí Azure CLI](../azure-resource-manager/templates/deploy-cli.md).
* [Pomocí REST API](../azure-resource-manager/templates/deploy-rest.md).

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>Zrušení zřízení a odstranění okruhu ExpressRoute

Okruh ExpressRoute můžete odstranit tak, že vyberete ikonu **Odstranit** . Všimněte si následujících informací:

* Od okruhu ExpressRoute je potřeba odpojit všechny virtuální sítě. Pokud tato operace neproběhne úspěšně, ověřte, zda jsou pro daný okruh připojeny nějaké virtuální sítě.
* Pokud je stav zřizování poskytovatele služby okruhu ExpressRoute **zřizování** nebo **zřízené** , musíte s vaším poskytovatelem služeb spolupracovat a zrušit zřízení okruhu na jejich straně. I nadále vyhradíme prostředky a účtujeme vám, dokud poskytovatel služeb nedokončí zrušení zřízení okruhu a pošle nám upozornění.
* Pokud poskytovatel služby zrušil zřízení okruhu (stav zřizování poskytovatele služeb je nastavený na **není zřízený**), můžete okruh odstranit. Tím se zastaví účtování okruhu.

Okruh ExpressRoute můžete odstranit spuštěním následujícího příkazu PowerShellu:

```azurepowershell-interactive
$circuitName = Read-Host -Prompt "Enter the same circuit name that you used earlier"
$resourceGroupName = "${circuitName}rg"

Remove-AzExpressRouteCircuit -ResourceGroupName $resourceGroupName -Name $circuitName
```

## <a name="next-steps"></a>Další kroky

Po vytvoření okruhu pokračujte následujícími dalšími kroky:

* [Vytvoření a úprava směrování pro okruh ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Propojení virtuální sítě s okruhem ExpressRoute](expressroute-howto-linkvnet-arm.md)
