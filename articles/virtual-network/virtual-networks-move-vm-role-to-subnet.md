---
title: Přesunutí virtuálního počítače (klasické) nebo instance role cloudové služby na jiné podsíti - prostředí Azure PowerShell | Microsoft Docs
description: Zjistěte, jak přesunout virtuální počítače (klasické) a instancí rolí cloudové služby na jiné podsíti pomocí prostředí PowerShell.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
ms.assetid: de4135c7-dc5b-4ffa-84cc-1b8364b7b427
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: eb1576b7b842537dbdc49e6b9d402ef47ad9fae9
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
ms.locfileid: "31792643"
---
# <a name="move-a-vm-classic-or-cloud-services-role-instance-to-a-different-subnet-using-powershell"></a>Přesunutí virtuálního počítače (klasické) nebo instance role cloudové služby na jiné podsíti pomocí prostředí PowerShell
Můžete použít PowerShell k přesunutí virtuálních počítačů (klasické) z jedné podsítě do jiné ve stejné virtuální síti (VNet). Instance role se dají přesunout úpravy soubor .CSCFG, nikoli pomocí prostředí PowerShell.

> [!NOTE]
> Tento článek vysvětluje, jak přesunout virtuální počítače nasazené prostřednictvím model nasazení classic.
> 
> 

Proč virtuální počítače přesunout do jiné podsítě? Podsíť migrace je užitečné, když starší podsíť je příliš malá a nelze rozšířit z důvodu existující virtuální počítače běžící v této podsíti. V takovém případě můžete vytvořit nový, větší podsíť a migrovat virtuální počítače do nové podsítě a potom po dokončení migrace, můžete odstranit staré prázdný podsíť.

## <a name="how-to-move-a-vm-to-another-subnet"></a>Postup přesunutí virtuálního počítače do jiné podsítě
Přesunout virtuální počítač, spusťte rutinu Set-AzureSubnet prostředí PowerShell, pomocí níže uvedeném příkladu jako šablona. V následujícím příkladu jsme přecházíte TestVM z jeho přítomen podsíti na podsíť-2. Nezapomeňte upravit třeba, aby se zohlednilo vaše prostředí. Všimněte si, že vždy, když spustíte rutinu Update-AzureVM jako součást postupu, bude restartován virtuální počítač jako součást procesu aktualizace.

    Get-AzureVM –ServiceName TestVMCloud –Name TestVM `
    | Set-AzureSubnet –SubnetNames Subnet-2 `
    | Update-AzureVM

Pokud jste zadali interní statickou privátní IP pro virtuální počítač, budete muset zrušit výběr tohoto nastavení můžete přesunout virtuální počítač novou podsíť. V takovém případě použijte tento příkaz:

    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
    | Remove-AzureStaticVNetIP `
    | Update-AzureVM
    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
    | Set-AzureSubnet -SubnetNames Subnet-2 `
    | Update-AzureVM

## <a name="to-move-a-role-instance-to-another-subnet"></a>Chcete-li přesunout instanci role na jinou podsítí
Chcete-li přesunout instanci role, upravte soubor .CSCFG. V následujícím příkladu jsme přesouváte "Role0" ve virtuální síti *VNETName* z jeho přítomen podsítě k *podsíť 2*. Proto již byla nasazena role instance, změníte právě název podsítě = Podsíť 2. Nezapomeňte upravit třeba, aby se zohlednilo vaše prostředí.

    <NetworkConfiguration>
        <VirtualNetworkSite name="VNETName" />
        <AddressAssignments>
           <InstanceAddress roleName="Role0">
                <Subnets><Subnet name="Subnet-2" /></Subnets>
           </InstanceAddress>
        </AddressAssignments>
    </NetworkConfiguration> 
