---
title: Přesunout virtuální počítač (Classic) nebo instance role cloudové služby do jiné podsítě – Azure PowerShell | Dokumentace Microsoftu
description: Zjistěte, jak přesunout virtuální počítače (klasické) a instance rolí cloudové služby do jiné podsítě pomocí Powershellu.
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
ms.openlocfilehash: 787a50a0cbf16089cd15f922b494cd12d680cb43
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60640391"
---
# <a name="move-a-vm-classic-or-cloud-services-role-instance-to-a-different-subnet-using-powershell"></a>Přesunout virtuální počítač (Classic) nebo instance role cloudové služby do jiné podsítě pomocí Powershellu
Přesunout virtuální počítače (Classic) z jedné podsítě do jiné ve stejné virtuální síti (VNet) můžete použít PowerShell. Instance rolí se dají přesunout pomocí úpravy souboru CSCFG, nikoli pomocí Powershellu.

> [!NOTE]
> Tento článek vysvětluje, jak přesunout virtuální počítače nasazené prostřednictvím modelu nasazení classic.
> 
> 

Proč přesunout virtuální počítače k jiné podsíti? Podsíť migrace je užitečné, když starší podsíť je příliš malá a nelze rozšířit z důvodu stávající spuštěné virtuální počítače v této podsíti. V takovém případě můžete vytvořit nové, větší podsítě a virtuální počítače migrovat na novou podsíť a pak po dokončení migrace můžete odstranit staré prázdnou podsíť.

## <a name="how-to-move-a-vm-to-another-subnet"></a>Postup přesunutí virtuálního počítače do jiné podsítě
Přesunutí virtuálního počítače, spusťte rutinu prostředí PowerShell Set-AzureSubnet, pomocí níže uvedeném příkladu jako šablony. V následujícím příkladu upouštíme TestVM z jeho podsítě k dispozici na podsíť-2. Nezapomeňte upravit v příkladu, aby se zohlednilo vaše prostředí. Mějte na paměti, vždy, když spustíte rutinu Update-AzureVM jako součást řízení, bude jako součást procesu aktualizace restartování vašeho virtuálního počítače.

    Get-AzureVM –ServiceName TestVMCloud –Name TestVM `
    | Set-AzureSubnet –SubnetNames Subnet-2 `
    | Update-AzureVM

Pokud jste zadali statické interní privátní IP adresy pro virtuální počítač, budete muset vymazat toto nastavení virtuálního počítače můžete přesunout do nové podsítě. V takovém případě použijte následující:

    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
    | Remove-AzureStaticVNetIP `
    | Update-AzureVM
    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
    | Set-AzureSubnet -SubnetNames Subnet-2 `
    | Update-AzureVM

## <a name="to-move-a-role-instance-to-another-subnet"></a>Role instance přesunout do jiné podsítě
Přesunutí role instance, upravte soubor .CSCFG. V následujícím příkladu upouštíme "Role0" ve virtuální síti *VNETName* z této podsítě *podsíť 2*. Proto již byla nasazena role instance, jenom změníte název podsítě = 2 podsítě. Nezapomeňte upravit v příkladu, aby se zohlednilo vaše prostředí.

    <NetworkConfiguration>
        <VirtualNetworkSite name="VNETName" />
        <AddressAssignments>
           <InstanceAddress roleName="Role0">
                <Subnets><Subnet name="Subnet-2" /></Subnets>
           </InstanceAddress>
        </AddressAssignments>
    </NetworkConfiguration> 
