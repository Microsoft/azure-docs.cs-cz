---
title: Analyzovat zabezpečení sítě s Azure sítě sledovacích procesů zabezpečení skupiny zobrazení – prostředí PowerShell | Microsoft Docs
description: Tento článek popisuje, jak pomocí prostředí PowerShell k analýze zabezpečení virtuálních počítačů s zobrazení skupiny zabezpečení.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 04e76b49-6a1b-4d0f-9a9b-51cf2f4df5a2
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 3983055cd580c263d39b908c61a16ed14353c9a4
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2017
ms.locfileid: "23864172"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-powershell"></a>Analýza zabezpečení vašeho virtuálního počítače s zobrazení skupiny zabezpečení pomocí prostředí PowerShell

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [CLI 1.0](network-watcher-security-group-view-cli-nodejs.md)
> - [CLI 2.0](network-watcher-security-group-view-cli.md)
> - [REST API](network-watcher-security-group-view-rest.md)

Zobrazení skupiny zabezpečení vrátí pravidla zabezpečení sítě nakonfigurované a efektivní, která se použijí k virtuálnímu počítači. Tato možnost je užitečná k auditování a diagnostice skupin zabezpečení sítě a pravidel, které jsou nakonfigurované na virtuálním počítači zajistit provoz se správně povolený nebo zakázaný. V tomto článku jsme ukazují, jak načíst pravidla zabezpečení nakonfigurované a efektivní k virtuálnímu počítači pomocí prostředí PowerShell

## <a name="before-you-begin"></a>Než začnete

V tomto scénáři můžete spustit `Get-AzureRmNetworkWatcherSecurityGroupView` rutiny k načtení informací o pravidlo zabezpečení.

Tento scénář předpokládá, že už jste udělali kroky v [vytvořit sledovací proces sítě](network-watcher-create.md) vytvořit sledovací proces sítě.

## <a name="scenario"></a>Scénář

Scénář popsaná v tomto článku načte pravidla nakonfigurované a efektivní zabezpečení pro daný virtuální počítač.

## <a name="retrieve-network-watcher"></a>Načtení sledovací proces sítě

Prvním krokem je pro získání instance sledovací proces sítě. Tato proměnná je předána `Get-AzureRmNetworkWatcherSecurityGroupView` rutiny.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName
```

## <a name="get-a-vm"></a>Získat virtuální počítač

Virtuální počítač je potřeba spustit `Get-AzureRmNetworkWatcherSecurityGroupView` rutiny proti. Následující příklad načte objektu virtuálního počítače.

```powershell
$VM = Get-AzurermVM -ResourceGroupName testrg -Name testvm1
```

## <a name="retrieve-security-group-view"></a>Načtení zobrazení skupiny zabezpečení

Dalším krokem je načíst výsledky zobrazení skupiny zabezpečení.

```powershell
$secgroup = Get-AzureRmNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="viewing-the-results"></a>Zobrazení výsledků

V následujícím příkladu je zkrácení odpověď výsledky vrácené. Výsledky zobrazit všechna pravidla zabezpečení efektivní a použitých na virtuálním počítači rozdělení v skupiny **NetworkInterfaceSecurityRules**, **DefaultSecurityRules**, a **EffectiveSecurityRules**.

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

Navštivte [auditování zabezpečení sítě skupiny (NSG) s sledovací proces sítě](network-watcher-nsg-auditing-powershell.md) se dozvíte, jak automatizovat ověření skupin zabezpečení sítě.


