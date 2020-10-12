---
title: Automatizace NSG auditování – zobrazení skupiny zabezpečení
titleSuffix: Azure Network Watcher
description: Tato stránka poskytuje pokyny, jak nakonfigurovat auditování skupiny zabezpečení sítě.
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
ms.openlocfilehash: 10abd1065fe47556109ed69d36493c165dec1418
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84738205"
---
# <a name="automate-nsg-auditing-with-azure-network-watcher-security-group-view"></a>Automatizace NSG auditování pomocí zobrazení skupiny zabezpečení Azure Network Watcher

Zákazníci se často setkávají s výzvou k ověření stav zabezpečení jejich infrastruktury. Tato výzva není pro své virtuální počítače v Azure odlišná. Je důležité mít podobný profil zabezpečení na základě použitých pravidel skupiny zabezpečení sítě (NSG). Pomocí zobrazení skupiny zabezpečení teď můžete získat seznam pravidel použitých pro virtuální počítač v rámci NSG. Můžete definovat zlatý NSG profil zabezpečení a spustit zobrazení skupiny zabezpečení na týdenní tempo a porovnat výstup s zlatým profilem a vytvořit sestavu. Tímto způsobem můžete snadno identifikovat všechny virtuální počítače, které neodpovídají předepsanému profilu zabezpečení.

Pokud neznáte skupiny zabezpečení sítě, přečtěte si téma [Přehled zabezpečení sítě](../virtual-network/security-overview.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Než začnete

V tomto scénáři porovnáte známý dobrý směrný plán k výsledkům zobrazení ve skupině zabezpečení vráceným pro virtuální počítač.

V tomto scénáři se předpokládá, že už jste postupovali podle kroků v části [vytvoření Network Watcher](network-watcher-create.md) k vytvoření Network Watcher. Scénář také předpokládá, že je k dispozici skupina prostředků s platným virtuálním počítačem.

## <a name="scenario"></a>Scénář

Scénář popsaný v tomto článku získá zobrazení skupiny zabezpečení pro virtuální počítač.

V tomto scénáři provedete tyto kroky:

- Načtení známé funkční sady pravidel
- Načtení virtuálního počítače pomocí rozhraní REST API
- Získat zobrazení skupiny zabezpečení pro virtuální počítač
- Vyhodnotit odpověď

## <a name="retrieve-rule-set"></a>Načíst sadu pravidel

Prvním krokem v tomto příkladu je práce s existujícím směrným plánem. Následující příklad je ukázkový kód JSON extrahovaný z existující skupiny zabezpečení sítě pomocí `Get-AzNetworkSecurityGroup` rutiny, která se používá jako základ pro tento příklad.

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

## <a name="convert-rule-set-to-powershell-objects"></a>Převod sady pravidel na objekty prostředí PowerShell

V tomto kroku čteme soubor JSON, který byl vytvořen dříve s pravidly, která by měla být ve skupině zabezpečení sítě v tomto příkladu.

```powershell
$nsgbaserules = Get-Content -Path C:\temp\testvm1-nsg.json | ConvertFrom-Json
```

## <a name="retrieve-network-watcher"></a>Načíst Network Watcher

Dalším krokem je načtení instance Network Watcher. `$networkWatcher`Proměnná je předána `AzNetworkWatcherSecurityGroupView` rutině.

```powershell
$networkWatcher = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
```

## <a name="get-a-vm"></a>Získání virtuálního počítače

Pro spuštění rutiny se vyžaduje virtuální počítač `Get-AzNetworkWatcherSecurityGroupView` . Následující příklad načte objekt virtuálního počítače.

```powershell
$VM = Get-AzVM -ResourceGroupName "testrg" -Name "testvm1"
```

## <a name="retrieve-security-group-view"></a>Načíst zobrazení skupiny zabezpečení

Dalším krokem je načtení výsledku zobrazení skupiny zabezpečení. Tento výsledek je porovnán s kódem JSON "směrný plán", který byl zobrazen dříve.

```powershell
$secgroup = Get-AzNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="analyzing-the-results"></a>Analýza výsledků

Odpověď je seskupena podle síťových rozhraní. U různých typů vrácených pravidel jsou platná a výchozí pravidla zabezpečení. Výsledek je dále rozepsán podle toho, jak je použit, a to buď v podsíti, nebo ve virtuální síťové kartě.

Následující skript prostředí PowerShell porovná výsledky zobrazení skupiny zabezpečení s existujícím výstupem NSG. Následující příklad představuje jednoduchý příklad, jak lze výsledky porovnat s `Compare-Object` rutinou.

```powershell
Compare-Object -ReferenceObject $nsgbaserules `
-DifferenceObject $secgroup.NetworkInterfaces[0].NetworkInterfaceSecurityRules `
-Property Name,Description,Protocol,SourcePortRange,DestinationPortRange,SourceAddressPrefix,DestinationAddressPrefix,Access,Priority,Direction
```

Následující příklad je výsledkem. V porovnání se zobrazí dvě pravidla, která byla uvedena v první sadě pravidel.

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

Pokud se nastavení změnila, přečtěte si téma [Správa skupin zabezpečení](../virtual-network/manage-network-security-group.md) sítě a sledujte příslušné skupiny zabezpečení sítě a pravidla zabezpečení.













