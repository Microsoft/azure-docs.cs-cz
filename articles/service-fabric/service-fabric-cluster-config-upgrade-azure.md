---
title: Upgrade konfigurace clusteru Azure Service Fabric
description: Naučte se upgradovat konfiguraci, která spouští Cluster Service Fabric v Azure pomocí šablony Správce prostředků.
author: dkkapur
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 476a2d910b916ea29132b108478d06f756454813
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75463286"
---
# <a name="upgrade-the-configuration-of-a-cluster-in-azure"></a>Upgrade konfigurace clusteru v Azure 

Tento článek popisuje, jak přizpůsobit různá nastavení prostředků infrastruktury pro cluster Service Fabric. Pro clustery hostované v Azure můžete nastavení přizpůsobit prostřednictvím [Azure Portal](https://portal.azure.com) nebo pomocí Azure Resource Manager šablony.

> [!NOTE]
> V portálu nejsou k dispozici všechna nastavení a je [osvědčeným postupem jejich přizpůsobení pomocí šablony Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code); Portál je určen pouze Service Fabric Dev\Test scénář.
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>Přizpůsobení nastavení clusteru pomocí šablon Správce prostředků
Clustery Azure je možné nakonfigurovat prostřednictvím šablony Správce prostředků JSON. Další informace o různých nastaveních najdete v tématu [nastavení konfigurace pro clustery](service-fabric-cluster-fabric-settings.md). Například následující postup ukazuje, jak přidat nové nastavení *MaxDiskQuotaInMB* do části *Diagnostika* pomocí Azure Resource Explorer.

1. Přejděte na https://resources.azure.com.
2. Přejděte do svého předplatného rozšířením **předplatných** ->  **\<svého předplatného >**  -> **ResourceGroups** -> \<**vaší skupiny prostředků** > -> **poskytovatelé** -> **clustery** **Microsoft. ServiceFabric** ->  -> \<**název clusteru** >
3. V pravém horním rohu vyberte možnost **čtení/zápis.**
4. Vyberte **Upravit** a aktualizujte `fabricSettings` element JSON a přidejte nový element:

```json
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

Nastavení clusteru můžete také přizpůsobit jedním z následujících způsobů pomocí Azure Resource Manager:

- Pomocí [Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template) můžete exportovat a aktualizovat šablonu správce prostředků.
- K exportu a aktualizaci šablony Správce prostředků použijte [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-powershell) .
- K exportu a aktualizaci šablony Správce prostředků použijte rozhraní příkazového [řádku Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-cli) .
- Použijte příkazy Azure PowerShell [set-AzServiceFabricSetting](https://docs.microsoft.com/powershell/module/az.servicefabric/Set-azServiceFabricSetting) a [Remove-AzServiceFabricSetting](https://docs.microsoft.com/powershell/module/az.servicefabric/Remove-azServiceFabricSetting) , abyste nastavení změnili přímo.
- Pomocí příkazů Azure CLI [AZ SF cluster Setting](https://docs.microsoft.com/cli/azure/sf/cluster/setting) upravíte nastavení přímo.

## <a name="next-steps"></a>Další kroky
* Přečtěte si o [nastaveních clusteru Service Fabric](service-fabric-cluster-fabric-settings.md).
* Přečtěte si, jak [škálovat cluster na úrovni a](service-fabric-cluster-scale-up-down.md).
