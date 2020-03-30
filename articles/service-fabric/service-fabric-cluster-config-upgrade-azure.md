---
title: Upgrade konfigurace clusteru Azure Service Fabric
description: Zjistěte, jak upgradovat konfiguraci, která spouští cluster Service Fabric v Azure pomocí šablony Správce prostředků.
author: dkkapur
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 476a2d910b916ea29132b108478d06f756454813
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75463286"
---
# <a name="upgrade-the-configuration-of-a-cluster-in-azure"></a>Upgrade konfigurace clusteru v Azure 

Tento článek popisuje, jak přizpůsobit různá nastavení prostředků infrastruktury pro cluster service fabric. U clusterů hostovaných v Azure můžete nastavení přizpůsobit na [webu Azure Portal](https://portal.azure.com) nebo pomocí šablony Azure Resource Manager.

> [!NOTE]
> Ne všechna nastavení jsou k dispozici na portálu a je [osvědčeným postupem přizpůsobit pomocí šablony Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code); Portál je pouze pro scénář Service Fabric Dev\Test.
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>Přizpůsobení nastavení clusteru pomocí šablon Správce prostředků
Clustery Azure lze nakonfigurovat pomocí šablony Správce prostředků JSON. Další informace o různých nastaveních naleznete v [tématu Nastavení konfigurace clusterů](service-fabric-cluster-fabric-settings.md). Jako příklad níže uvedené kroky ukazují, jak přidat nové nastavení *MaxDiskQuotaInMB* do části *Diagnostika* pomocí Průzkumníka prostředků Azure.

1. Přejděte na https://resources.azure.com.
2. Přejděte k předplatnému rozšířením **předplatného** -> **\<Vaše předplatné>**  ->  **skupiny** -> **\< **prostředků Vaše skupina prostředků> ->  **zprostředkovatelům** -> **clusterů** -> **\<** **Microsoft.ServiceFabric** -> Název clusteru>
3. V pravém horním rohu vyberte **Číst/psát.**
4. Vyberte **Upravit** `fabricSettings` a aktualizujte prvek JSON a přidejte nový prvek:

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

Pomocí Správce prostředků Azure můžete také přizpůsobit nastavení clusteru jedním z následujících způsobů:

- Pomocí [portálu Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template) exportujte a aktualizujte šablonu Správce prostředků.
- Pomocí [prostředí PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-powershell) exportujte a aktualizujte šablonu Správce prostředků.
- Pomocí [azure cli](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-cli) exportovat a aktualizovat šablonu Správce prostředků.
- Pomocí příkazů Azure PowerShell [Set-AzServiceFabricSetting](https://docs.microsoft.com/powershell/module/az.servicefabric/Set-azServiceFabricSetting) a [Remove-AzServiceFabricSetting](https://docs.microsoft.com/powershell/module/az.servicefabric/Remove-azServiceFabricSetting) upravte nastavení přímo.
- Pomocí příkazů nastavení clusteru Azure CLI [az sf](https://docs.microsoft.com/cli/azure/sf/cluster/setting) můžete nastavení upravit přímo.

## <a name="next-steps"></a>Další kroky
* Informace o [nastavení clusteru Service Fabric](service-fabric-cluster-fabric-settings.md).
* Přečtěte si, jak [škálovat cluster y dovnitř a ven](service-fabric-cluster-scale-up-down.md).
