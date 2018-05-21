---
title: Statické interní privátní IP - virtuální počítač Azure – Classic
description: Princip statické interní IP adresy (vyhrazené) a způsobu jejich správy
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
ms.assetid: 93444c6f-af1b-41f8-a035-77f5c0302bf0
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2018
ms.author: genli
ms.openlocfilehash: 661d2f789ace8da68b6d65609d4584a11967a01f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="how-to-set-a-static-internal-private-ip-address-using-powershell-classic"></a>Jak nastavit statickou interní privátní IP adresu pomocí prostředí PowerShell (klasické)
Ve většině případů nebude muset zadat statické interní IP adresu pro virtuální počítač. Virtuální počítače ve virtuální síti se automaticky zobrazí interní IP adresu z rozsahu, který určíte. Ale v některých případech, zadat statickou IP adresu pro konkrétní virtuální počítač má smysl. Například pokud virtuální počítač se blíží ke spuštění DNS nebo bude řadič domény. Statické interní IP adresu zůstává s virtuálním Počítačem i prostřednictvím stavu zastavení nebo deprovision. 

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi: [Resource Manager a klasický model](../azure-resource-manager/resource-manager-deployment-model.md). Tento článek se věnuje použití klasického modelu nasazení. Společnost Microsoft doporučuje, aby většina nových nasazení používala [modelu nasazení Resource Manager](virtual-networks-static-private-ip-arm-ps.md).
> 
> 

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>Postup ověření, pokud je k dispozici konkrétní IP adresu
Chcete-li ověřit, pokud IP adresa *10.0.0.7* je k dispozici ve virtuální síti s názvem *TestVnet*, spusťte následující příkaz prostředí PowerShell a potom ověřte hodnotu pro *IsAvailable*:

    Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 10.0.0.7 

    IsAvailable          : True
    AvailableAddresses   : {}
    OperationDescription : Test-AzureStaticVNetIP
    OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
    OperationStatus      : Succeeded

> [!NOTE]
> Pokud chcete testovat příkaz výše v bezpečné prostředí, postupujte podle pokynů v [vytvoření virtuální sítě (klasické)](virtual-networks-create-vnet-classic-pportal.md) chcete vytvořit síť vnet s názvem *TestVnet* a ujistěte se, použije *10.0.0.0/8* adresní prostor.
> 
> 

## <a name="how-to-specify-a-static-internal-ip-when-creating-a-vm"></a>Určení statické interní IP při vytvoření virtuálního počítače
Následující skript prostředí PowerShell vytvoří novou cloudovou službu s názvem *TestService*, načte bitovou kopii z Azure a pak vytvoří virtuální počítač s názvem *TestVM* v novou cloudovou službu pomocí načtené bitové kopie, nastaví virtuálního počítače v podsíti s názvem *Subnet-1*a nastaví *10.0.0.7* jako statické interní IP adresu pro virtuální počítač:

    New-AzureService -ServiceName TestService -Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
    | Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
    | Set-AzureSubnet –SubnetNames Subnet-1 `
    | Set-AzureStaticVNetIP -IPAddress 10.0.0.7 `
    | New-AzureVM -ServiceName "TestService" –VNetName TestVnet

## <a name="how-to-retrieve-static-internal-ip-information-for-a-vm"></a>Jak načíst interní informace o statických IP pro virtuální počítač
Chcete-li zobrazit informace o statické interní IP pro virtuální počítač vytvořený pomocí skriptu pro výše uvedené, spusťte následující příkaz prostředí PowerShell a sledovat hodnoty *IpAddress*:

    Get-AzureVM -Name TestVM -ServiceName TestService

    DeploymentName              : TestService
    Name                        : TestVM
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : Provisioning
    IpAddress                   : 10.0.0.7
    InstanceStateDetails        : Windows is preparing your computer for first use...
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : TestVM
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : rsR2-797
    AvailabilitySetName         : 
    DNSName                     : http://testservice000.cloudapp.net/
    Status                      : Provisioning
    GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 
    PublicIPName                : 
    NetworkInterfaces           : {}
    ServiceName                 : TestService
    OperationDescription        : Get-AzureVM
    OperationId                 : 34c1560a62f0901ab75cde4fed8e8bd1
    OperationStatus             : OK

## <a name="how-to-remove-a-static-internal-ip-from-a-vm"></a>Postup odebrání statické interní IP z virtuálního počítače
Chcete-li odebrat statické interní IP přidat do virtuálního počítače ve výše uvedené skriptu, spusťte následující příkaz prostředí PowerShell:

    Get-AzureVM -ServiceName TestService -Name TestVM `
    | Remove-AzureStaticVNetIP `
    | Update-AzureVM

## <a name="how-to-add-a-static-internal-ip-to-an-existing-vm"></a>Jak přidat do stávajícího virtuálního počítače statické interní IP
Pokud chcete přidat statické interní IP na virtuální počítač vytvořený skript výše, spusťte následující příkaz:

    Get-AzureVM -ServiceName TestService000 -Name TestVM `
    | Set-AzureStaticVNetIP -IPAddress 10.10.0.7 `
    | Update-AzureVM

## <a name="next-steps"></a>Další postup
[Vyhrazená IP adresa](virtual-networks-reserved-public-ip.md)

[Úrovni instance veřejnou IP adresu (splnění)](virtual-networks-instance-level-public-ip.md)

[Vyhrazená IP adresa REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx)

