---
title: Automatizovat NSG auditování s zobrazení skupiny zabezpečení sledovací proces sítě Azure | Microsoft Docs
description: Tato stránka obsahuje pokyny, jak nakonfigurovat auditování skupinu zabezpečení sítě
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 78a01bcf-74fe-402a-9812-285f3501f877
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 0c9450099ad3561611796a9b45157932f9ac85a9
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="automate-nsg-auditing-with-azure-network-watcher-security-group-view"></a>Automatizovat NSG auditování s zobrazení skupiny zabezpečení sledovací proces sítě Azure

Zákazníci jsou často potýkají s otázkou, ověřovat postavení zabezpečení svoji infrastrukturu. Tento problém se neliší pro jejich virtuální počítače v Azure. Je důležité mají podobné profil zabezpečení na základě pravidel skupiny zabezpečení sítě (NSG) použít. Pomocí zobrazení skupiny zabezpečení, můžete nyní získat seznam pravidel, které u virtuálních počítačů v rámci skupiny NSG. Můžete definovat zlaté profil zabezpečení NSG a zahájit zobrazení skupiny zabezpečení na týdenní cadence a porovnání výstup zlaté profil a vytvořit sestavu. Tímto způsobem můžete identifikovat snadno všechny virtuální počítače, které nejsou v souladu s profilem předepsaných zabezpečení.

Pokud jste obeznámeni s skupin zabezpečení sítě, navštivte [Přehled zabezpečení sítě](../virtual-network/virtual-networks-nsg.md)

## <a name="before-you-begin"></a>Než začnete

V tomto scénáři můžete porovnat známé dobré standardní hodnoty pro zobrazení výsledků skupiny zabezpečení pro virtuální počítač vrátí.

Tento scénář předpokládá, že už jste udělali kroky v [vytvořit sledovací proces sítě](network-watcher-create.md) vytvořit sledovací proces sítě. Tento scénář také předpokládá, že skupina prostředků se platný virtuální počítač existuje má být použit.

## <a name="scenario"></a>Scénář

Scénář popsaná v tomto článku získá zobrazení skupiny zabezpečení pro virtuální počítač.

V tomto scénáři provedete následující:

- Načíst sadu známé dobré pravidel
- Načíst virtuální počítač s Rest API
- Získat zobrazení skupiny zabezpečení pro virtuální počítač
- Vyhodnocení odpovědi

## <a name="retrieve-rule-set"></a>Načtení sady pravidel

Prvním krokem v tomto příkladu je pro práci s stávajících standardních hodnot. Následující příklad je některé json extrahovat z existující skupinu zabezpečení sítě pomocí `Get-AzureRmNetworkSecurityGroup` rutinu, která se používá jako Směrný plán pro tento příklad.

```json
[
    {
        "Description":  null,
        "Protocol":  "TCP",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "3389",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1000,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "default-allow-rdp",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/default-allow-rdp"
    },
    {
        "Description":  null,
        "Protocol":  "*",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "111",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1010,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "MyRuleDoNotDelete",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/MyRuleDoNotDelete"
    },
    {
        "Description":  null,
        "Protocol":  "*",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "112",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1020,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "My2ndRuleDoNotDelete",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/My2ndRuleDoNotDelete"
    },
    {
        "Description":  null,
        "Protocol":  "TCP",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "5672",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Deny",
        "Priority":  1030,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "ThisRuleNeedsToStay",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/ThisRuleNeedsToStay"
    }
]
```

## <a name="convert-rule-set-to-powershell-objects"></a>Převést sada pravidel pro objekty prostředí PowerShell

V tomto kroku jsme čtete soubor json, který jste vytvořili pravidla, která se očekává, že se na skupinu zabezpečení sítě v tomto příkladu.

```powershell
$nsgbaserules = Get-Content -Path C:\temp\testvm1-nsg.json | ConvertFrom-Json
```

## <a name="retrieve-network-watcher"></a>Načtení sledovací proces sítě

Dalším krokem je pro získání instance sledovací proces sítě. `$networkWatcher` Proměnné je předána `AzureRmNetworkWatcherSecurityGroupView` rutiny.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="get-a-vm"></a>Získat virtuální počítač

Virtuální počítač je potřeba spustit `Get-AzureRmNetworkWatcherSecurityGroupView` rutiny proti. Následující příklad načte objektu virtuálního počítače.

```powershell
$VM = Get-AzurermVM -ResourceGroupName "testrg" -Name "testvm1"
```

## <a name="retrieve-security-group-view"></a>Načtení zobrazení skupiny zabezpečení

Dalším krokem je načíst výsledky zobrazení skupiny zabezpečení. Tento výsledek se porovnává se "základní" formátu json, který byl dříve vidět.

```powershell
$secgroup = Get-AzureRmNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="analyzing-the-results"></a>Analýza výsledků

Odpověď se seskupují po síťových rozhraní. Různé typy pravidel vrátil jsou platné a výchozí pravidla zabezpečení. Výsledkem je další členěné podle jak se používají, buď na podsíť, nebo virtuální síťový adaptér.

Následující skript prostředí PowerShell porovná výsledky zobrazení skupiny zabezpečení k existující výstup skupinu NSG. Následující příklad je jednoduchý příklad, jak je možné porovnávat výsledky s `Compare-Object` rutiny.

```powershell
Compare-Object -ReferenceObject $nsgbaserules `
-DifferenceObject $secgroup.NetworkInterfaces[0].NetworkInterfaceSecurityRules `
-Property Name,Description,Protocol,SourcePortRange,DestinationPortRange,SourceAddressPrefix,DestinationAddressPrefix,Access,Priority,Direction
```

V následujícím příkladu je výsledek. Uvidíte dvě pravidla, které byly v první pravidlo nastavené nebyly nalezeny v porovnání.

```
Name                     : My2ndRuleDoNotDelete
Description              : 
Protocol                 : *
SourcePortRange          : *
DestinationPortRange     : 112
SourceAddressPrefix      : *
DestinationAddressPrefix : *
Access                   : Allow
Priority                 : 1020
Direction                : Inbound
SideIndicator            : <=

Name                     : ThisRuleNeedsToStay
Description              : 
Protocol                 : TCP
SourcePortRange          : *
DestinationPortRange     : 5672
SourceAddressPrefix      : *
DestinationAddressPrefix : *
Access                   : Deny
Priority                 : 1030
Direction                : Inbound
SideIndicator            : <=
```

## <a name="next-steps"></a>Další postup

Pokud se změnila nastavení, najdete v části [spravovat skupiny zabezpečení sítě](../virtual-network/manage-network-security-group.md) sledovat pravidla zabezpečení sítě skupiny a zabezpečení, které jsou v.













