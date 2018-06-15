---
title: Zobrazit topologii sledovací proces sítě Azure – prostředí PowerShell | Microsoft Docs
description: Tento článek popisuje, jak k dotazování vaší topologie sítě pomocí prostředí PowerShell.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: bd0e882d-8011-45e8-a7ce-de231a69fb85
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 84e925b4461e55e570e9848bf03d3d352bfff898
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2017
ms.locfileid: "23864375"
---
# <a name="view-network-watcher-topology-with-powershell"></a>Zobrazit sledovací proces sítě topologie pomocí prostředí PowerShell

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-topology-powershell.md)
> - [CLI 1.0](network-watcher-topology-cli-nodejs.md)
> - [CLI 2.0](network-watcher-topology-cli.md)
> - [REST API](network-watcher-topology-rest.md)

Topologie funkci sledovací proces sítě poskytuje vizuální reprezentace síťových prostředků v předplatném. Na portálu pro tuto vizualizaci se zobrazují automaticky. Informace pro zobrazení topologie na portálu se dají získat pomocí prostředí PowerShell.
Díky této vlastnosti je rozmanitější jako data mohou být spotřebovávána další nástroje pro sestavení se vizualizaci informací o topologii.

Propojení je modelován v dva vztahy.

- **Členství ve skupině** – příklad: obsahuje podsíť virtuální sítě obsahuje síťový adaptér
- **Související** – příklad: síťový adaptér je přidružený virtuální počítač

V následujícím seznamu je vlastnosti, které jsou vráceny při dotazování topologie REST API.

* **název** -název prostředku
* **ID** – identifikátor uri prostředku.
* **umístění** -umístění, kde existuje prostředek.
* **přidružení** – seznam přidružení k odkazovaného objektu.
    * **název** -název odkazovaného prostředku.
    * **resourceId** -resourceId je identifikátor uri prostředku, kterou se odkazuje v přidružení.
    * **Třída associationType** – tato hodnota se odkazuje vztah mezi podřízený objekt a nadřazený. Platné hodnoty jsou **obsahuje** nebo **přidružené**.

## <a name="before-you-begin"></a>Než začnete

V tomto scénáři použijete `Get-AzureRmNetworkWatcherTopology` rutiny k načtení informací o topologii. O tom, jak je také článek [načíst topologie sítě pomocí rozhraní REST API](network-watcher-topology-rest.md).

Tento scénář předpokládá, že už jste udělali kroky v [vytvořit sledovací proces sítě](network-watcher-create.md) vytvořit sledovací proces sítě.

## <a name="scenario"></a>Scénář

Scénář popsaná v tomto článku načte odpověď topologie pro danou skupinu prostředků.

## <a name="retrieve-network-watcher"></a>Načtení sledovací proces sítě

Prvním krokem je pro získání instance sledovací proces sítě. `$networkWatcher` Proměnné je předána `Get-AzureRmNetworkWatcherTopology` rutiny.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName
```

## <a name="retrieve-topology"></a>Načtení topologie

`Get-AzureRmNetworkWatcherTopology` Rutina načte topologie pro danou skupinu prostředků.

```powershell
Get-AzureRmNetworkWatcherTopology -NetworkWatcher $networkWatcher -TargetResourceGroupName testrg
```

## <a name="results"></a>Výsledky

Výsledky vrácené mít vlastnost name "zdroje", které obsahuje těla odpovědi json pro `Get-AzureRmNetworkWatcherTopology` rutiny.  Odpověď obsahuje prostředky ve skupině zabezpečení sítě a jejich přidružení (který je obsahuje, přidružené).

```json
Id              : 00000000-0000-0000-0000-000000000000
CreatedDateTime : 2/1/2017 7:52:21 PM
LastModified    : 2/1/2017 7:46:18 PM
Resources       : [
                    {
                      "Name": "testrg-vnet",
                      "Id":
                  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet",
                      "Location": "westcentralus",
                      "Associations": [
                        {
                          "AssociationType": "Contains",
                          "Name": "default",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworks/testrg-vnet/subnets/default"
                        }
                      ]
                    },
                    {
                      "Name": "default",
                      "Id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testr
                  g-vnet/subnets/default",
                      "Location": "westcentralus",
                      "Associations": []
                    },
                    {
                      "Name": "testrg-vnet2",
                      "Id": 
                  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet2",
                      "Location": "westcentralus",
                      "Associations": [
                        {
                          "AssociationType": "Contains",
                          "Name": "default",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworks/testrg-vnet2/subnets/default"
                        },
                        {
                          "AssociationType": "Contains",
                          "Name": "GatewaySubnet",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworks/testrg-vnet2/subnets/GatewaySubnet"
                        },
                        {
                          "AssociationType": "Contains",
                          "Name": "gateway2",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworkGateways/gateway2"
                        }
                      ]
                    },
                    ...
                  ]
```

## <a name="next-steps"></a>Další kroky

Zjistěte, jak toku protokolů NSG s Power BI vizualizovat navštivte stránky [vizualizovat NSG toků protokoly s Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)


