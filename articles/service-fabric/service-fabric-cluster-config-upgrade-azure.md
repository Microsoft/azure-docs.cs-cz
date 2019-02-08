---
title: Upgradovat konfiguraci clusteru služby Azure Service Fabric | Dokumentace Microsoftu
description: Zjistěte, jak upgradovat konfiguraci, která se spouští cluster Service Fabric v Azure pomocí šablony Resource Manageru.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: 66296cc6-9524-4c6a-b0a6-57c253bdf67e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 621dc22ead8fbb6882c692851d39b658da043c55
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55894679"
---
# <a name="upgrade-the-configuration-of-a-cluster-in-azure"></a>Upgradovat konfiguraci clusteru v Azure 

Tento článek popisuje, jak přizpůsobit různá nastavení prostředků infrastruktury pro cluster Service Fabric. Pro clustery hostovaných v Azure, můžete upravit pomocí nastavení [webu Azure portal](https://portal.azure.com) nebo s použitím šablony Azure Resource Manageru.

> [!NOTE]
> Ne všechna nastavení jsou k dispozici na portálu a jde [osvědčený postup a přizpůsobit pomocí šablony Azure Resource Manageru](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code); Portál je pro Service Fabric Dev\Test ve scénáři pouze.> 

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>Nastavení clusteru pomocí šablon Resource Manageru
Clustery Azure je možné nakonfigurovat pomocí šablony JSON Resource Manageru. Další informace o různých nastaveních najdete v tématu [nastavení konfigurace pro clustery](service-fabric-cluster-fabric-settings.md). Například následující kroky ukazují, jak přidat nové nastavení *MaxDiskQuotaInMB* k *diagnostiky* části pomocí Průzkumníka prostředků Azure.

1. Přejděte na https://resources.azure.com.
2. Přejděte ke svému předplatnému tak, že rozbalíte **předplatná** -> **\<vašeho předplatného >** -> **resourceGroups**  ->   **\<Vaše skupina prostředků >** -> **poskytovatelé** -> **Microsoft.ServiceFabric**  ->  **clustery** -> **\<si název clusteru >**
3. V pravém horním rohu, vyberte **čtení a zápisu.**
4. Vyberte **upravit** a aktualizovat `fabricSettings` elementu JSON a přidejte nový prvek:

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

V jednom z následujících způsobů pomocí Azure Resource Manageru můžete také upravit nastavení clusteru:

- Použití [webu Azure portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template) k exportu a aktualizace šablony správce prostředků.
- Použití [Powershellu](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-powershell) k exportu a aktualizovat šablonu Resource Manageru.
- Použití [rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-cli) k exportu a aktualizovat šablonu Resource Manageru.
- Použití Azure RM Powershellu [Set-AzureRmServiceFabricSetting](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/Set-AzureRmServiceFabricSetting) a [odebrat AzureRmServiceFabricSetting](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/Remove-AzureRmServiceFabricSetting) příkazů ke změně nastavení přímo.
- Pomocí Azure CLI [az sf cluster nastavení](https://docs.microsoft.com/cli/azure/sf/cluster/setting) příkazů ke změně nastavení přímo.

## <a name="next-steps"></a>Další postup
* Další informace o [nastavení clusteru Service Fabric](service-fabric-cluster-fabric-settings.md).
* Zjistěte, jak [škálování clusteru dovnitř a ven](service-fabric-cluster-scale-up-down.md).
