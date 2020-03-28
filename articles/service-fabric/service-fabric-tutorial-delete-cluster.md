---
title: Odstranění clusteru Service Fabric v Azure
description: V tomto kurzu se dozvíte, jak odstranit cluster Service Fabric hostovaný v Azure a všechny jeho prostředky. Můžete odstranit skupinu prostředků obsahující cluster nebo selektivně odstranit jednotlivé prostředky.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: 802fdfc46344929930b1ffb015b364b4e2360cca
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75465366"
---
# <a name="tutorial-remove-a-service-fabric-cluster-running-in-azure"></a>Kurz: Odebrání clusteru Service Fabric běžícího v Azure

Tento kurz je součástí páté řady a ukazuje, jak odstranit cluster Service Fabric spuštěný v Azure. Pokud chcete cluster Service Fabric úplně odstranit, musíte odstranit také prostředky, které cluster používá. Máte dvě možnosti: buď odstranit skupinu prostředků, ve které se cluster nachází (čímž odstraníte tento prostředek clusteru a všechny další prostředky v této skupině), nebo jednotlivě odstranit daný prostředek clusteru a jeho přidružené prostředky (ale ne jiné prostředky v této skupině).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Odstranění skupiny prostředků se všemi jejími prostředky
> * Selektivní odstranění prostředků ze skupiny prostředků

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * Vytvoření zabezpečeného [clusteru Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) v Azure pomocí šablony
> * [Sledování clusteru](service-fabric-tutorial-monitor-cluster.md)
> * [Horizontální snížení nebo navýšení kapacity clusteru](service-fabric-tutorial-scale-cluster.md)
> * [Upgrade modulu runtime clusteru](service-fabric-tutorial-upgrade-cluster.md)
> * Odstranění clusteru


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem:

* Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Nainstalujte [Azure Powershell](https://docs.microsoft.com/powershell/azure//install-Az-ps) nebo [Azure CLI](/cli/azure/install-azure-cli).
* Vytvoření zabezpečeného [clusteru Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) v Azure

## <a name="delete-the-resource-group-containing-the-service-fabric-cluster"></a>Odstranění skupiny prostředků obsahující cluster Service Fabric
Nejjednodušší způsob, jak odstranit cluster a všechny prostředky, které využívá, je odstranit příslušnou skupinu prostředků.

Přihlaste se k Azure a vyberte ID předplatného, se kterým chcete cluster odebrat.  Své ID předplatného můžete zjistit po přihlášení k webu [Azure Portal](https://portal.azure.com). Odstraňte skupinu prostředků a všechny prostředky clusteru pomocí příkazu [Odstranit azResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) nebo [az group delete.](/cli/azure/group?view=azure-cli-latest)

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <guid>
$groupname = "sfclustertutorialgroup"
Remove-AzResourceGroup -Name $groupname -Force
```

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="sfclustertutorialgroup"
az group delete --name $ResourceGroupName
```

## <a name="selectively-delete-the-cluster-resource-and-the-associated-resources"></a>Selektivní odstranění prostředku clusteru a souvisejících prostředků
Pokud vaše skupina prostředků obsahuje jen prostředky přidružené ke clusteru Service Fabric, který chcete odstranit, pak je jednodušší odstranit celou skupinu. Pokud chcete prostředky ve skupině prostředků odstranit selektivně a zachovat prostředky, které ke clusteru přidružené nejsou, postupujte podle následujících kroků.

Vytvořte seznam prostředků ve skupině prostředků:

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <guid>
$groupname = "sfclustertutorialgroup"
Get-AzResource -ResourceGroupName $groupname | ft
```

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="sfclustertutorialgroup"
az resource list --resource-group $ResourceGroupName
```

U každého prostředku, který chcete odstranit, spusťte následující skript:

```powershell
Remove-AzResource -ResourceName "<name of the Resource>" -ResourceType "<Resource Type>" -ResourceGroupName $groupname -Force
```

```azurecli
az resource delete --name "<name of the Resource>" --resource-type "<Resource Type>" --resource-group $ResourceGroupName
```

Pokud chcete prostředek clusteru odstranit, spusťte následující skript:

```powershell
Remove-AzResource -ResourceName "<name of the Resource>" -ResourceType "Microsoft.ServiceFabric/clusters" -ResourceGroupName $groupname -Force
```

```azurecli
az resource delete --name "<name of the Resource>" --resource-type "Microsoft.ServiceFabric/clusters" --resource-group $ResourceGroupName
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Odstranění skupiny prostředků se všemi jejími prostředky
> * Selektivní odstranění prostředků ze skupiny prostředků

Teď když jste kurz dokončili, zkuste toto:
* Zjistěte, jak zkontrolovat a spravovat cluster Service Fabric pomocí [Service Fabric Exploreru](service-fabric-visualizing-your-cluster.md).
* Přečtěte si, jak [opravit operační systém Windows](service-fabric-patch-orchestration-application.md) uzlů clusteru.
* Přečtěte si, jak agregovat a shromažďovat události pro [clustery Windows](service-fabric-diagnostics-event-aggregation-wad.md) a [nastavit Analýzu protokolů](service-fabric-diagnostics-oms-setup.md) za účelem sledování událostí clusteru.
