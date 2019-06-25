---
title: Automatizace NSG auditování se zobrazením skupin zabezpečení Azure Network Watcher | Dokumentace Microsoftu
description: Tahle stránka poskytuje pokyny o tom, jak nakonfigurovat auditování skupinu zabezpečení sítě
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 78a01bcf-74fe-402a-9812-285f3501f877
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 016d68de90088314250fef1fcfdb57d7f155ef79
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64707157"
---
# <a name="automate-nsg-auditing-with-azure-network-watcher-security-group-view"></a>Automatizace NSG auditování se zobrazením skupin zabezpečení Azure Network Watcher

Zákazníci se často potýkají s otázkou, ověřovat stav zabezpečení svých infrastruktury. Tato výzva se nijak neliší pro své virtuální počítače v Azure. Je důležité mít podobného profilu zabezpečení na základě pravidel skupiny zabezpečení sítě (NSG) použita. Pomocí zobrazení skupin zabezpečení, můžete nyní získat seznam pravidel u virtuálních počítačů ve skupině zabezpečení sítě. Můžete definovat zlaté profil zabezpečení skupiny zabezpečení sítě a zahájit zobrazení skupin zabezpečení týdenní čím dál a porovnat výstup do zlaté profil a vytvořit sestavu. Tímto způsobem můžete identifikovat s lehkostí a elegancí všechny virtuální počítače, které není v souladu s předepsanými zabezpečení profilu.

Pokud nejste obeznámeni s použitím skupin zabezpečení sítě, přečtěte si téma [Přehled zabezpečení sítě](../virtual-network/security-overview.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Než začnete

V tomto scénáři můžete porovnat známé dobré podklad pro zobrazení výsledků skupiny zabezpečení pro virtuální počítač.

Tento scénář předpokládá, že už jste udělali kroky v [vytvořit Network Watcher](network-watcher-create.md) vytvořit Network Watcher. Tento scénář také předpokládá, že skupina prostředků se platný virtuální počítač existuje má být použit.

## <a name="scenario"></a>Scénář

Scénáře popsané v tomto článku získá zobrazení skupin zabezpečení pro virtuální počítač.

V tomto scénáři provedete následující:

- Načíst sadu pravidel známé dobré
- Načíst virtuální počítač pomocí rozhraní Rest API
- Získat zobrazení skupin zabezpečení pro virtuální počítač
- Hodnocení odpovědí

## <a name="retrieve-rule-set"></a>Načíst sadu pravidel

Prvním krokem v tomto příkladu je pro práci s existující směrného plánu. V následujícím příkladu je některé json extrahují z existující skupiny zabezpečení sítě pomocí `Get-AzNetworkSecurityGroup` rutinu, která se používá jako základ pro účely tohoto příkladu.

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

## <a name="convert-rule-set-to-powershell-objects"></a>Sada pravidel pro převod na objektů prostředí PowerShell

V tomto kroku jsme čtete soubor json, který jste vytvořili pomocí pravidla, která se očekává, že se na skupinu zabezpečení sítě pro účely tohoto příkladu.

```powershell
$nsgbaserules = Get-Content -Path C:\temp\testvm1-nsg.json | ConvertFrom-Json
```

## <a name="retrieve-network-watcher"></a>Načíst Network Watcher

Dalším krokem je načtení instance Network Watcheru. `$networkWatcher` Proměnná je předána `AzNetworkWatcherSecurityGroupView` rutiny.

```powershell
$nw = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="get-a-vm"></a>Získání virtuálního počítače

Virtuální počítač se vyžaduje pro spuštění `Get-AzNetworkWatcherSecurityGroupView` rutiny proti. Následující příklad načte objekt virtuálního počítače.

```powershell
$VM = Get-AzVM -ResourceGroupName "testrg" -Name "testvm1"
```

## <a name="retrieve-security-group-view"></a>Načtení zobrazení skupin zabezpečení

Dalším krokem je načtení výsledný objekt zobrazení skupiny zabezpečení. Tento výsledek je ve srovnání s json "základní", které se zobrazilo dříve.

```powershell
$secgroup = Get-AzNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="analyzing-the-results"></a>Analýza výsledků

Odpověď je seskupený podle síťová rozhraní. Různé typy pravidel vrátil efektivně a výchozí pravidla zabezpečení. Výsledek se dále člení podle způsob použití, buď na podsíť, nebo virtuální síťový adaptér.

Následující skript prostředí PowerShell porovnává výsledky zobrazení skupin zabezpečení do výstupu stávající skupina zabezpečení sítě. V následujícím příkladu je jednoduchý příklad porovnání výsledků s `Compare-Object` rutiny.

```powershell
Compare-Object -ReferenceObject $nsgbaserules `
-DifferenceObject $secgroup.NetworkInterfaces[0].NetworkInterfaceSecurityRules `
-Property Name,Description,Protocol,SourcePortRange,DestinationPortRange,SourceAddressPrefix,DestinationAddressPrefix,Access,Priority,Direction
```

Následující příklad je výsledkem. Uvidíte, že dvě pravidla, které byly v první pravidlo nastavené nebyly přítomny v porovnání.

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

Změně nastavení naleznete v tématu [spravovat skupiny zabezpečení sítě](../virtual-network/manage-network-security-group.md) vysledovat pravidla zabezpečení sítě skupiny a zabezpečení, které jsou nejistá.













