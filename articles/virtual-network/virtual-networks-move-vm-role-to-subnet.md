---
title: Přesuňte virtuální počítač Azure (Classic) nebo Cloud Services instance role do jiné podsítě.
description: Přečtěte si, jak přesunout virtuální počítače (Classic) a Cloud Services instance rolí do jiné podsítě pomocí prostředí PowerShell.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
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
ms.openlocfilehash: e2a92355632ef0d7059c9e5294448de0ca591b82
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196691"
---
# <a name="move-a-vm-classic-or-cloud-services-role-instance-to-a-different-subnet-using-powershell"></a>Přesunutí virtuálního počítače (Classic) nebo Cloud Services instance role do jiné podsítě pomocí prostředí PowerShell
K přesunu virtuálních počítačů (Classic) z jedné podsítě do jiné ve stejné virtuální síti (VNet) můžete použít PowerShell. Instance rolí lze přesunout úpravou souboru. CSCFG namísto použití prostředí PowerShell.

> [!NOTE]
> Tento článek vysvětluje, jak přesunout virtuální počítače nasazené prostřednictvím modelu nasazení Classic.
> 
> 

Proč přesunout virtuální počítače do jiné podsítě? Migrace podsítě je užitečná v případě, že je starší podsíť příliš malá a nedá se rozšířit kvůli existujícím spuštěným virtuálním počítačům v této podsíti. V takovém případě můžete vytvořit novou, větší podsíť a migrovat virtuální počítače do nové podsítě. po dokončení migrace můžete odstranit starou prázdnou podsíť.

## <a name="how-to-move-a-vm-to-another-subnet"></a>Postup přesunutí virtuálního počítače do jiné podsítě
Pokud chcete přesunout virtuální počítač, spusťte rutinu Set-AzureSubnet prostředí PowerShell a použijte níže uvedený příklad jako šablonu. V následujícím příkladu přesouváme TestVM z této podsítě do podsítě 2. Nezapomeňte tento příklad upravit tak, aby odrážel vaše prostředí. Všimněte si, že při každém spuštění rutiny Update-AzureVM v rámci postupu se virtuální počítač restartuje jako součást procesu aktualizace.

    Get-AzureVM –ServiceName TestVMCloud –Name TestVM `
    | Set-AzureSubnet –SubnetNames Subnet-2 `
    | Update-AzureVM

Pokud jste pro svůj virtuální počítač zadali statickou interní privátní IP adresu, musíte toto nastavení vymazat, abyste mohli virtuální počítač přesunout do nové podsítě. V takovém případě použijte následující:

    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
    | Remove-AzureStaticVNetIP `
    | Update-AzureVM
    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
    | Set-AzureSubnet -SubnetNames Subnet-2 `
    | Update-AzureVM

## <a name="to-move-a-role-instance-to-another-subnet"></a>Přesunutí instance role do jiné podsítě
Chcete-li přesunout instanci role, upravte soubor CSCFG. V následujícím příkladu přesouváme "Role0" ve službě Virtual Network *VNETName* z této podsítě do *podsítě 2*. Vzhledem k tomu, že instance role již byla nasazena, stačí změnit název podsítě = podsíť-2. Nezapomeňte tento příklad upravit tak, aby odrážel vaše prostředí.

    <NetworkConfiguration>
        <VirtualNetworkSite name="VNETName" />
        <AddressAssignments>
           <InstanceAddress roleName="Role0">
                <Subnets><Subnet name="Subnet-2" /></Subnets>
           </InstanceAddress>
        </AddressAssignments>
    </NetworkConfiguration> 
