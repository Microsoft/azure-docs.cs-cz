---
title: Odstranění clusteru služby Azure a jeho prostředků | Microsoft Docs
description: Zjistěte, jak úplně odstranit cluster Service Fabric odstranění skupiny prostředků obsahující cluster nebo selektivně odstraněním prostředky.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: de422950-2d22-4ddb-ac47-dd663a946a7e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/24/2017
ms.author: aljo
ms.openlocfilehash: 7da2277fef224ff7859cac1ad5a2290c9dc56a85
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="delete-a-service-fabric-cluster-on-azure-and-the-resources-it-uses"></a>Odstranění clusteru Service Fabric na Azure a prostředky, které používá
Cluster Service Fabric se skládá z mnoha dalším prostředkům služby Azure kromě prostředek clusteru sám sebe. Proto je ke kompletnímu odstranění clusteru Service Fabric potřeba odstranit taky prostředky, které ho tvoří.
Máte dvě možnosti: buď odstranit skupinu prostředků, který je cluster v (která odstraňuje prostředek clusteru a všechny další prostředky ve skupině prostředků) nebo konkrétně odstranit prostředek clusteru a je přiřazen prostředky (ale ne další prostředky ve skupině prostředků).

> [!NOTE]
> Odstranění prostředku clusteru **nemá** odstranit všechny ostatní prostředky, které váš cluster Service Fabric tvoří.
> 
> 

## <a name="delete-the-entire-resource-group-rg-that-the-service-fabric-cluster-is-in"></a>Odstraňte skupinu prostředků celý (RG), který je cluster Service Fabric v
Toto je nejjednodušší způsob, jak zajistit, že odstraníte všechny prostředky přidruženého k vašemu clusteru, včetně skupiny prostředků. Odstraněním skupiny prostředků pomocí prostředí PowerShell nebo prostřednictvím portálu Azure. Pokud vaše skupina prostředků obsahuje prostředky, které nesouvisí se Service fabric cluster, můžete odstranit konkrétní prostředky.

### <a name="delete-the-resource-group-using-azure-powershell"></a>Odstraňte skupinu prostředků pomocí Azure PowerShell
Můžete také odstranit skupinu prostředků spuštěním následující rutiny prostředí Azure PowerShell. Ujistěte se, že Azure PowerShell 1.0 nebo vyšší je v počítači nainstalována. Pokud jste to před neudělali, postupujte podle kroků uvedených v [postup instalace a konfigurace prostředí Azure PowerShell.](/powershell/azure/overview)

Otevřete okno prostředí PowerShell a spusťte následující rutiny PS:

```powershell
Login-AzureRmAccount

Remove-AzureRmResourceGroup -Name <name of ResouceGroup> -Force
```

Zobrazí se výzva k potvrzení odstranění, pokud jste nepoužili *-Force* možnost. Na potvrzení RG a všechny prostředky, které obsahuje, se odstraní.

### <a name="delete-a-resource-group-in-the-azure-portal"></a>Odstranit skupinu prostředků na portálu Azure
1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
2. Přejděte ke clusteru Service Fabric, který chcete odstranit.
3. Klikněte na název skupiny prostředků na stránce essentials clusteru.
4. Po výběru této možnosti **Essentials skupiny prostředků** stránky.
5. Klikněte na **Odstranit**.
6. Postupujte podle pokynů na této stránce dokončení odstranění skupiny prostředků.

![Odstranění skupiny prostředků][ResourceGroupDelete]

## <a name="delete-the-cluster-resource-and-the-resources-it-uses-but-not-other-resources-in-the-resource-group"></a>Odstranit prostředek clusteru a prostředky, které používá, ale ne další prostředky ve skupině prostředků
Pokud vaše skupina prostředků má jenom prostředky, které se vztahují k cluster Service Fabric, které chcete odstranit, je snazší Odstraňte skupinu prostředků celý. Pokud chcete selektivně odstranění prostředků po jednom ve vaší skupině prostředků, postupujte podle těchto kroků.

Pokud jste nasadili cluster pomocí portálu nebo pomocí jedné z šablon služby Správce prostředků infrastruktury z Galerie šablon, jsou všechny prostředky, které cluster používá označené s těmito dvěma značkami. Můžete je používat k rozhodování o prostředky, ke kterým chcete odstranit.

***Značka #1:*** klíč = clusterName, hodnota = "název clusteru.

***Značka #2:*** klíč = resourceName, hodnota = ServiceFabric

### <a name="delete-specific-resources-in-the-azure-portal"></a>Odstranit konkrétní prostředky na portálu Azure
1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
2. Přejděte ke clusteru Service Fabric, který chcete odstranit.
3. Přejděte na **všechna nastavení** v okně essentials.
4. Klikněte na **značky** pod **Správa prostředků** v okně nastavení.
5. Klikněte na jednu z **značky** v okně značky k získání seznamu všech prostředků, které jsou s touto značkou.
   
    ![Značky prostředku][ResourceTags]
6. Jakmile máte seznam prostředků s příznakem, klikněte na jednotlivých zdrojích a odstraňte je.
   
    ![Prostředky s příznakem][TaggedResources]

### <a name="delete-the-resources-using-azure-powershell"></a>Odstranění prostředků pomocí Azure PowerShell
Spuštěním následující rutiny prostředí Azure PowerShell můžete odstranit prostředky po jednom. Ujistěte se, že Azure PowerShell 1.0 nebo vyšší je v počítači nainstalována. Pokud jste to před neudělali, postupujte podle kroků uvedených v [postup instalace a konfigurace prostředí Azure PowerShell.](/powershell/azure/overview)

Otevřete okno prostředí PowerShell a spusťte následující rutiny PS:

```powershell
Login-AzureRmAccount
```
Pro všechny prostředky, které chcete odstranit spusťte následující skript:

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "<Resource Type>" -ResourceGroupName "<name of the resource group>" -Force
```

Pokud chcete odstranit prostředek clusteru, spusťte následující skript:

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "Microsoft.ServiceFabric/clusters" -ResourceGroupName "<name of the resource group>" -Force
```

## <a name="next-steps"></a>Další postup
Přečtěte si následující také další informace o upgradu clusteru a rozdělení do oddílů služby:

* [Další informace o upgrade clusteru](service-fabric-cluster-upgrade.md)
* [Další informace o vytváření oddílů stavové služby pro maximální měřítko](service-fabric-concepts-partitioning.md)

<!--Image references-->
[ResourceGroupDelete]: ./media/service-fabric-cluster-delete/ResourceGroupDelete.PNG

[ResourceTags]: ./media/service-fabric-cluster-delete/ResourceTags.png

[TaggedResources]: ./media/service-fabric-cluster-delete/TaggedResources.PNG
