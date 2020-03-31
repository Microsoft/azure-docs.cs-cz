---
title: Automatizace auditování skupiny zabezpečení - zobrazení skupiny zabezpečení
titleSuffix: Azure Network Watcher
description: Tato stránka obsahuje pokyny ke konfiguraci auditování skupiny zabezpečení sítě.
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
ms.openlocfilehash: 59c1b6e6c281a736a79d110bd7d943344bcd5130
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840974"
---
# <a name="automate-nsg-auditing-with-azure-network-watcher-security-group-view"></a>Automatizace auditování skupinn nsg pomocí zobrazení skupiny Sledování sítě Azure

Zákazníci se často potýkají s výzvou ověření stavu zabezpečení své infrastruktury. Tato výzva se nijak neliší pro jejich virtuální počítače v Azure. Je důležité mít podobný profil zabezpečení založený na použitých pravidlech skupiny zabezpečení sítě (NSG). Pomocí zobrazení skupiny zabezpečení teď můžete získat seznam pravidel použitých pro virtuální hod v rámci skupiny zabezpečení. Můžete definovat zlatý profil zabezpečení skupiny nsg a zahájit zobrazení skupiny zabezpečení na týdenní kadenci a porovnat výstup se zlatým profilem a vytvořit sestavu. Tímto způsobem můžete snadno identifikovat všechny virtuální aplikace, které neodpovídají předepsanému profilu zabezpečení.

Pokud nejste obeznámeni se skupinami zabezpečení sítě, přečtěte si informace [o přehledu zabezpečení sítě](../virtual-network/security-overview.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Než začnete

V tomto scénáři porovnáte známé dobré směrný plán s výsledky zobrazení skupiny zabezpečení vrácené pro virtuální počítač.

Tento scénář předpokládá, že jste již postupovali podle kroků v [části Vytvoření sledovacího programu sítě](network-watcher-create.md) k vytvoření sledovacího programu sítě. Scénář také předpokládá, že skupina prostředků s platným virtuálním počítačem existuje pro použití.

## <a name="scenario"></a>Scénář

Scénář, který je popsán v tomto článku získá zobrazení skupiny zabezpečení pro virtuální počítač.

V tomto scénáři budete:

- Načtení známé sady osvědčených pravidel
- Načtení virtuálního počítače pomocí rozhraní Rest API
- Získání zobrazení skupiny zabezpečení pro virtuální počítač
- Vyhodnotit odpověď

## <a name="retrieve-rule-set"></a>Načíst sadu pravidel

Prvním krokem v tomto příkladu je práce s existující směrný plán. Následující příklad je některé json extrahované z `Get-AzNetworkSecurityGroup` existující skupiny zabezpečení sítě pomocí rutiny, která se používá jako směrný plán pro tento příklad.

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

## <a name="convert-rule-set-to-powershell-objects"></a>Převést sadu pravidel na objekty PowerShellu

V tomto kroku čteme soubor json, který byl vytvořen dříve s pravidly, která se očekává, že bude ve skupině zabezpečení sítě v tomto příkladu.

```powershell
$nsgbaserules = Get-Content -Path C:\temp\testvm1-nsg.json | ConvertFrom-Json
```

## <a name="retrieve-network-watcher"></a>Načíst sledovací modul sítě

Dalším krokem je načtení instance Sledování sítě. Proměnná `$networkWatcher` je předána rutině. `AzNetworkWatcherSecurityGroupView`

```powershell
$networkWatcher = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
```

## <a name="get-a-vm"></a>Získání virtuálního virtuálního mě

Virtuální počítač je nutné `Get-AzNetworkWatcherSecurityGroupView` spustit rutinu proti. Následující příklad získá objekt virtuálního soudu.

```powershell
$VM = Get-AzVM -ResourceGroupName "testrg" -Name "testvm1"
```

## <a name="retrieve-security-group-view"></a>Načíst zobrazení skupiny zabezpečení

Dalším krokem je načtení výsledku zobrazení skupiny zabezpečení. Tento výsledek je porovnán s "směrným plánem" json, který byl zobrazen dříve.

```powershell
$secgroup = Get-AzNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="analyzing-the-results"></a>Analýza výsledků

Odpověď je seskupena podle síťových rozhraní. Různé typy vrácených pravidel jsou efektivní a výchozí pravidla zabezpečení. Výsledek je dále rozdělen podle způsobu jeho použití, a to buď v podsíti, nebo ve virtuální síťové síti.

Následující skript prostředí PowerShell porovnává výsledky zobrazení skupiny zabezpečení s existujícím výstupem skupiny zabezpečení. Následující příklad je jednoduchý příklad toho, jak lze `Compare-Object` porovnat výsledky s rutinou.

```powershell
Compare-Object -ReferenceObject $nsgbaserules `
-DifferenceObject $secgroup.NetworkInterfaces[0].NetworkInterfaceSecurityRules `
-Property Name,Description,Protocol,SourcePortRange,DestinationPortRange,SourceAddressPrefix,DestinationAddressPrefix,Access,Priority,Direction
```

Následující příklad je výsledek. Můžete vidět dvě z pravidel, která byla v první sadě pravidel nebyly přítomny v porovnání.

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

## <a name="next-steps"></a>Další kroky

Pokud byla změněna nastavení, [přečtěte si](../virtual-network/manage-network-security-group.md) informace o sledování dané skupiny zabezpečení sítě v tématu Správa skupin zabezpečení sítě a pravidel zabezpečení.













