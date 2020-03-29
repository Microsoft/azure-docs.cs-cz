---
title: Analýza zabezpečení sítě – zobrazení skupiny zabezpečení – Azure PowerShell
titleSuffix: Azure Network Watcher
description: Tento článek popisuje, jak pomocí prostředí PowerShell analyzovat zabezpečení virtuálních počítačů pomocí zobrazení skupiny zabezpečení.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 4cba2c7e25b5f76b0638da1c551514f102247ae0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840787"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-powershell"></a>Analýza zabezpečení virtuálního počítače pomocí zobrazení skupiny zabezpečení pomocí PowerShellu

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [Azure CLI](network-watcher-security-group-view-cli.md)
> - [ROZHRANÍ API PRO ODPOČINEK](network-watcher-security-group-view-rest.md)

Zobrazení skupiny zabezpečení vrátí nakonfigurovaná a efektivní pravidla zabezpečení sítě, která jsou použita pro virtuální počítač. Tato funkce je užitečná pro audit a diagnostiku skupin zabezpečení sítě a pravidel, která jsou nakonfigurována na virtuálním počítači, aby bylo zajištěno, že provoz je správně povolen nebo odepřen. V tomto článku vám ukážeme, jak načíst nakonfigurovaná a efektivní pravidla zabezpečení do virtuálního počítače pomocí Prostředí PowerShell


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Než začnete

V tomto scénáři `Get-AzNetworkWatcherSecurityGroupView` spustíte rutinu načíst informace pravidla zabezpečení.

Tento scénář předpokládá, že jste již postupovali podle kroků v [části Vytvoření sledovacího programu sítě](network-watcher-create.md) k vytvoření sledovacího programu sítě.

## <a name="scenario"></a>Scénář

Scénář, který je popsán v tomto článku načte nakonfigurovaná a efektivní pravidla zabezpečení pro daný virtuální počítač.

## <a name="retrieve-network-watcher"></a>Načíst sledovací modul sítě

Prvním krokem je načíst instanci Network Watcher. Tato proměnná je `Get-AzNetworkWatcherSecurityGroupView` předána rutině.

```powershell
$networkWatcher = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
```

## <a name="get-a-vm"></a>Získání virtuálního virtuálního mě

Virtuální počítač je nutné `Get-AzNetworkWatcherSecurityGroupView` spustit rutinu proti. Následující příklad získá objekt virtuálního soudu.

```powershell
$VM = Get-AzVM -ResourceGroupName testrg -Name testvm1
```

## <a name="retrieve-security-group-view"></a>Načíst zobrazení skupiny zabezpečení

Dalším krokem je načtení výsledku zobrazení skupiny zabezpečení.

```powershell
$secgroup = Get-AzNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="viewing-the-results"></a>Zobrazení výsledků

Následující příklad je zkrácená odpověď na vrácené výsledky. Výsledky ukazují všechna platná a použitá pravidla zabezpečení virtuálního počítače rozdělená do skupin **NetworkInterfaceSecurityRules**, **DefaultSecurityRules**a **EffectiveSecurityRules**.

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

Navštivte [auditování skupin zabezpečení sítě (NSG) s sledovacím procesem sítě,](network-watcher-nsg-auditing-powershell.md) kde se dozvíte, jak automatizovat ověřování skupin zabezpečení sítě.


