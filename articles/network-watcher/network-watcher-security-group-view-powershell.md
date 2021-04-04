---
title: Analýza zabezpečení sítě – zobrazení skupiny zabezpečení – Azure PowerShell
titleSuffix: Azure Network Watcher
description: Tento článek popisuje, jak pomocí prostředí PowerShell analyzovat zabezpečení virtuálních počítačů pomocí zobrazení skupin zabezpečení.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 2da4ebfa6a9ce64b03e6e1d29956f740f6cb3c1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94960602"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-powershell"></a>Analýza zabezpečení virtuálního počítače pomocí zobrazení skupiny zabezpečení pomocí prostředí PowerShell

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [Azure CLI](network-watcher-security-group-view-cli.md)
> - [REST API](network-watcher-security-group-view-rest.md)

> [!NOTE]
> Rozhraní API pro zobrazení skupiny zabezpečení již není udržováno a bude brzy zastaralé. Použijte prosím [funkci platná pravidla zabezpečení](./network-watcher-security-group-view-overview.md) , která poskytuje stejné funkce. 

Zobrazení skupiny zabezpečení vrací nakonfigurovaná a efektivní pravidla zabezpečení sítě, která se používají pro virtuální počítač. Tato funkce je užitečná pro audit a diagnostiku skupin zabezpečení sítě a pravidel nakonfigurovaných na virtuálním počítači, aby se zajistilo správné povolení nebo odepření provozu. V tomto článku vám ukážeme, jak načíst nakonfigurovaná a platná pravidla zabezpečení pro virtuální počítač pomocí prostředí PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Než začnete

V tomto scénáři spustíte `Get-AzNetworkWatcherSecurityGroupView` rutinu, která načte informace o pravidlu zabezpečení.

V tomto scénáři se předpokládá, že už jste postupovali podle kroků v části [vytvoření Network Watcher](network-watcher-create.md) k vytvoření Network Watcher.

## <a name="scenario"></a>Scenario

Scénář popsaný v tomto článku načte nakonfigurovaná a platná pravidla zabezpečení pro daný virtuální počítač.

## <a name="retrieve-network-watcher"></a>Načíst Network Watcher

Prvním krokem je načtení instance Network Watcher. Tato proměnná je předána `Get-AzNetworkWatcherSecurityGroupView` rutině.

```powershell
$networkWatcher = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
```

## <a name="get-a-vm"></a>Získání virtuálního počítače

Pro spuštění rutiny se vyžaduje virtuální počítač `Get-AzNetworkWatcherSecurityGroupView` . Následující příklad načte objekt virtuálního počítače.

```powershell
$VM = Get-AzVM -ResourceGroupName testrg -Name testvm1
```

## <a name="retrieve-security-group-view"></a>Načíst zobrazení skupiny zabezpečení

Dalším krokem je načtení výsledku zobrazení skupiny zabezpečení.

```powershell
$secgroup = Get-AzNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="viewing-the-results"></a>Zobrazení výsledků

Následující příklad je zkrácená odezva vrácených výsledků. Ve výsledcích se zobrazí všechna platná a použitá pravidla zabezpečení na virtuálním počítači rozdělená do skupin **NetworkInterfaceSecurityRules**, **DefaultSecurityRules** a **EffectiveSecurityRules**.

```
NetworkInterfaces : [
                      {
                        "NetworkInterfaceSecurityRules": [
                          {
                            "Name": "default-allow-rdp",
                            "Etag": "W/\"d4c411d4-0d62-49dc-8092-3d4b57825740\"",
                            "Id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg2/providers/Microsoft.Network/networkSecurityGroups/testvm2-nsg/securityRules/default-allow-rdp",
                            "Protocol": "TCP",
                            "SourcePortRange": "*",
                            "DestinationPortRange": "3389",
                            "SourceAddressPrefix": "*",
                            "DestinationAddressPrefix": "*",
                            "Access": "Allow",
                            "Priority": 1000,
                            "Direction": "Inbound",
                            "ProvisioningState": "Succeeded"
                          }
                          ...
                        ],
                        "DefaultSecurityRules": [
                          {
                            "Name": "AllowVnetInBound",
                            "Id": "/subscriptions00000000-0000-0000-0000-000000000000/resourceGroups/testrg2/providers/Microsoft.Network/networkSecurityGroups/testvm2-nsg/defaultSecurityRules/",
                            "Description": "Allow inbound traffic from all VMs in VNET",
                            "Protocol": "*",
                            "SourcePortRange": "*",
                            "DestinationPortRange": "*",
                            "SourceAddressPrefix": "VirtualNetwork",
                            "DestinationAddressPrefix": "VirtualNetwork",
                            "Access": "Allow",
                            "Priority": 65000,
                            "Direction": "Inbound",
                            "ProvisioningState": "Succeeded"
                          }
                          ...
                        ],
                        "EffectiveSecurityRules": [
                          {
                            "Name": "DefaultOutboundDenyAll",
                            "Protocol": "All",
                            "SourcePortRange": "0-65535",
                            "DestinationPortRange": "0-65535",
                            "SourceAddressPrefix": "*",
                            "DestinationAddressPrefix": "*",
                            "ExpandedSourceAddressPrefix": [],
                            "ExpandedDestinationAddressPrefix": [],
                            "Access": "Deny",
                            "Priority": 65500,
                            "Direction": "Outbound"
                          },
                          ...
                        ]
                      }
                    ]
```

## <a name="next-steps"></a>Další kroky

Informace o tom, jak automatizovat ověřování skupin zabezpečení sítě, najdete [v tématu auditování skupin zabezpečení sítě (NSG) s Network Watcher](network-watcher-nsg-auditing-powershell.md) .