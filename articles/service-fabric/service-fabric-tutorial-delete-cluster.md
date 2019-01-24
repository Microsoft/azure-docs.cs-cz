---
title: Odstranění clusteru Service Fabric v Azure | Microsoft Docs
description: V tomto kurzu se dozvíte, jak odstranit cluster Service Fabric hostovaný v Azure a všechny jeho prostředky. Můžete odstranit skupinu prostředků obsahující cluster nebo selektivně odstranit jednotlivé prostředky.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/26/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 5174bb696b456a66cfc0666ac5c475437e441d82
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54848848"
---
# <a name="tutorial-remove-a-service-fabric-cluster-running-in-azure"></a>Kurz: Odebrání clusteru Service Fabric běžící v Azure

V tomto kurzu, který je čtvrtou částí série, se dozvíte, jak odstranit cluster Service Fabric běžící v Azure. Pokud chcete cluster Service Fabric úplně odstranit, musíte odstranit také prostředky, které cluster používá. Máte dvě možnosti: buď odstranit skupinu prostředků, ve které se cluster nachází (čímž odstraníte tento prostředek clusteru a všechny další prostředky v této skupině), nebo jednotlivě odstranit daný prostředek clusteru a jeho přidružené prostředky (ale ne jiné prostředky v této skupině).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Odstranění skupiny prostředků se všemi jejími prostředky
> * Selektivní odstranění prostředků ze skupiny prostředků

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * Vytvoření zabezpečeného [clusteru s Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) nebo [clusteru s Linuxem](service-fabric-tutorial-create-vnet-and-linux-cluster.md) v Azure pomocí šablony
> * [Horizontální snížení nebo navýšení kapacity clusteru](service-fabric-tutorial-scale-cluster.md)
> * [Upgrade modulu runtime clusteru](service-fabric-tutorial-upgrade-cluster.md)
> * Odstranění clusteru

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem:

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Nainstalujte si [modul Azure PowerShell verze 4.1 nebo novější ](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) nebo [Azure CLI](/cli/azure/install-azure-cli).
* Vytvořte zabezpečený [cluster s Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) nebo [cluster s Linuxem](service-fabric-tutorial-create-vnet-and-linux-cluster.md) v Azure.

## <a name="delete-the-resource-group-containing-the-service-fabric-cluster"></a>Odstranění skupiny prostředků obsahující cluster Service Fabric
Nejjednodušší způsob, jak odstranit cluster a všechny prostředky, které využívá, je odstranit příslušnou skupinu prostředků.

Přihlaste se k Azure a vyberte ID předplatného, pro které chcete cluster odebrat.  Své ID předplatného můžete zjistit po přihlášení k webu [Azure Portal](http://portal.azure.com). Odstraňte skupinu prostředků a všechny prostředky clusteru pomocí rutiny [Remove-AzureRMResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) nebo příkazu [az group delete](/cli/azure/group?view=azure-cli-latest).

```powershell
Connect-AzureRmAccount
Set-AzureRmContext -SubscriptionId <guid>
$groupname = "sfclustertutorialgroup"
Remove-AzureRmResourceGroup -Name $groupname -Force
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
Connect-AzureRmAccount
Set-AzureRmContext -SubscriptionId <guid>
$groupname = "sfclustertutorialgroup"
Get-AzureRmResource -ResourceGroupName $groupname | ft
```

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="sfclustertutorialgroup"
az resource list --resource-group $ResourceGroupName
```

U každého prostředku, který chcete odstranit, spusťte následující skript:

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "<Resource Type>" -ResourceGroupName $groupname -Force
```

```azurecli
az resource delete --name "<name of the Resource>" --resource-type "<Resource Type>" --resource-group $ResourceGroupName
```

Pokud chcete prostředek clusteru odstranit, spusťte následující skript:

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "Microsoft.ServiceFabric/clusters" -ResourceGroupName $groupname -Force
```

```azurecli
az resource delete --name "<name of the Resource>" --resource-type "Microsoft.ServiceFabric/clusters" --resource-group $ResourceGroupName
```

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Odstranění skupiny prostředků se všemi jejími prostředky
> * Selektivní odstranění prostředků ze skupiny prostředků

Teď když jste kurz dokončili, zkuste toto:
* Zjistěte, jak zkontrolovat a spravovat cluster Service Fabric pomocí [Service Fabric Exploreru](service-fabric-visualizing-your-cluster.md).
* Zjistěte, jak [opravit operační systém Windows](service-fabric-patch-orchestration-application.md) nebo [opravit operační systém Linux](service-fabric-patch-orchestration-application-linux.md) v uzlech clusteru.
* Zjistěte, jak agregovat a shromažďovat události u [clusterů s Windows](service-fabric-diagnostics-event-aggregation-wad.md) nebo [clusterů s Linuxem](service-fabric-diagnostics-event-aggregation-lad.md) a [nastavit Log Analytics](service-fabric-diagnostics-oms-setup.md) pro monitorování událostí v clusteru.
