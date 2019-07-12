---
title: 'Vytvoření okruhu ExpressRoute – šablona Resource Manageru: Azure | Dokumentace Microsoftu'
description: Vytvoření, zřízení, odstranit a zrušit zřízení okruhu ExpressRoute.
services: expressroute;azure-resource-manager
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 07/05/2019
ms.author: cherylmc;ganesr
ms.openlocfilehash: bf56145d0a8cd3b01d0d74fcaf3348c1916cee5a
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659681"
---
# <a name="create-an-expressroute-circuit-by-using-azure-resource-manager-template"></a>Vytvoření okruhu ExpressRoute pomocí šablony Azure Resource Manageru

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Šablona Azure Resource Manager](expressroute-howto-circuit-resource-manager-template.md)
> * [Video – Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-circuit-classic.md)
>

Zjistěte, jak vytvořit okruh ExpressRoute pomocí nasazení šablony Azure Resource Manageru pomocí prostředí Azure PowerShell. Další informace o vývoji šablon Resource Manageru najdete v tématu [dokumentace ke službě Správce prostředků](/azure/azure-resource-manager/) a [referenčními informacemi k šablonám](/azure/templates/microsoft.network/expressroutecircuits).

## <a name="before-you-begin"></a>Před zahájením

* Zkontrolujte [požadavky](expressroute-prerequisites.md) a [pracovních postupů](expressroute-workflows.md) předtím, než začnete s konfigurací.
* Ujistěte se, že máte oprávnění k vytvoření nových síťových prostředků. Pokud nemáte správná oprávnění, obraťte se na svého správce účtu.
* Je možné [zobrazení videa](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) před zahájením Chcete-li lépe pochopit kroky.

## <a name="create"></a>Vytvoření a zřízení okruhu ExpressRoute

[Šablony Azure Quickstart](https://azure.microsoft.com/resources/templates/) má dobré kolekci šablony Resource Manageru. Použijte jednu z [existujících šablon](https://azure.microsoft.com/resources/templates/101-expressroute-circuit-create/) vytvořit okruh ExpressRoute.

[!code-json[create-azure-expressroute-circuit](~/quickstart-templates/101-expressroute-circuit-create/azuredeploy.json)]

Pokud chcete zobrazit informace týkající se šablony, vyberte [tady](https://azure.microsoft.com/resources/templates/?term=expressroute).

Pokud chcete vytvořit okruh ExpressRoute nasazením šablony:

1. Vyberte **vyzkoušet** z následující blok kódu a pak postupujte podle pokynů pro přihlášení ke službě Azure Cloud shell.

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

   * **Úroveň** Určuje, zda je povolen ExpressRoute standard nebo doplněk ExpressRoute premium. Můžete zadat **standardní** získat standardní SKU nebo **Premium** pořídit si doplněk premium.

   * **Umístění partnerského vztahu** je fyzického umístění, kde se partnerský vztah Microsoftu.

     > [!IMPORTANT]
     > Určuje umístění partnerského vztahu [fyzické umístění](expressroute-locations.md) kde jste partnerského vztahu se společností Microsoft. Toto je **není** propojené s "Umístění" vlastnost, která odkazuje na zeměpisné oblasti, kde se nachází poskytovatel síťových prostředků Azure. Zatímco nesouvisí, je vhodné zvolit poskytovatel síťových prostředků geograficky v blízkosti umístění partnerského vztahu okruhu.

    Název skupiny prostředků je název oboru názvů služby Service bus s **rg** připojí.

2. Vyberte **kopírování** zkopírujte skript prostředí PowerShell.
3. Klikněte pravým tlačítkem na konzolu prostředí a pak vyberte **vložit**.

Trvá několik minut pro vytvoření centra událostí.

Prostředí Azure PowerShell slouží k nasazení šablony v tomto kurzu. Další způsoby nasazení šablony najdete v tématu:

* [Pomocí webu Azure portal](../azure-resource-manager/resource-group-template-deploy-portal.md).
* [Pomocí Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md).
* [Pomocí rozhraní REST API](../azure-resource-manager/resource-group-template-deploy-rest.md).

## <a name="delete"></a>Zrušení zřízení a odstranění okruhu ExpressRoute

Váš okruh ExpressRoute můžete odstranit tak, že vyberete **odstranit** ikonu. Všimněte si následujících informací:

* Od okruhu ExpressRoute je potřeba odpojit všechny virtuální sítě. Pokud tato operace se nezdaří, zkontrolujte, zda jsou propojeny žádné virtuální sítě k okruhu.
* Pokud je stav zřizování poskytovatele služeb okruh ExpressRoute **zřizování** nebo **zřízená** , musíte pracovat se svým poskytovatelem služeb zrušit zřízení okruhu na své straně. Pokračujeme v rezervovat prostředky a účtovat až do dokončení zrušení zřízení okruhu a informuje nás poskytovatelem služeb.
* Pokud poskytovatel služeb okruh zruší (poskytovatel služeb Stav zřizování je nastavena na **nezřízeno**), můžete odstranit okruh. Tím se zastaví účtování okruhu.

Váš okruh ExpressRoute můžete odstranit spuštěním následujícího příkazu Powershellu:

```azurepowershell-interactive
$circuitName = Read-Host -Prompt "Enter the same circuit name that you used earlier"
$resourceGroupName = "${circuitName}rg"

Remove-AzExpressRouteCircuit -ResourceGroupName $resourceGroupName -Name $circuitName
```

## <a name="next-steps"></a>Další postup

Po vytvoření váš okruh, pokračujte následující další kroky:

* [Vytvoření a úprava směrování pro okruh ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Propojení virtuální sítě pro váš okruh ExpressRoute](expressroute-howto-linkvnet-arm.md)
