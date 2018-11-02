---
title: Statické interní privátní IP – virtuální počítač Azure – Classic
description: Principy statické interní IP adresy (vyhrazené IP adresy) a jejich správě
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
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 7977dc55d101c99bf1f850a529083916367308b4
ms.sourcegitcommit: 6678e16c4b273acd3eaf45af310de77090137fa1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50747809"
---
# <a name="how-to-set-a-static-internal-private-ip-address-using-powershell-classic"></a>Postup nastavení statické interní privátní IP adresy pomocí prostředí PowerShell (Classic)
Ve většině případů nebudete muset zadat statické interní IP adresu pro váš virtuální počítač. Virtuální počítače ve virtuální síti automaticky přijme vnitřní IP adresu z rozsahu, který zadáte. Ale v některých případech, určení statickou IP adresu pro konkrétní virtuální počítač má smysl. Pokud například váš virtuální počítač je teď spustíme DNS nebo bude řadič domény. Statické interní IP adresa zůstane s virtuálním Počítačem, klidně i prostřednictvím stavu zastavení nebo zrušení zřízení. 

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi: [Resource Manager a klasický model](../azure-resource-manager/resource-manager-deployment-model.md). Tento článek se věnuje použití klasického modelu nasazení. Společnost Microsoft doporučuje, aby většina nových nasazení používala [modelu nasazení Resource Manager](virtual-networks-static-private-ip-arm-ps.md).
> 
> 
## <a name="install-the-azure-powershell-service-management-module"></a>Instalace modulu správy služeb prostředí Azure PowerShell

Před spuštěním následujících příkazů, ujistěte se, že [modulu správy služeb prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0
) je na počítači nainstalovaný. Historie verzí modulu správy služeb prostředí Azure PowerShell, najdete v části [modul Azure v galerii prostředí PowerShell](https://www.powershellgallery.com/packages/Azure/5.3.0).

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>Jak ověřit, zda je k dispozici konkrétní IP adresu
Chcete-li ověřit, pokud IP adresa *10.0.0.7* je k dispozici ve virtuální síti s názvem *TestVnet*, spusťte následující příkaz prostředí PowerShell a potom ověřte hodnotu pro *IsAvailable*.


    Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 10.0.0.7 

    IsAvailable          : True
    AvailableAddresses   : {}
    OperationDescription : Test-AzureStaticVNetIP
    OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
    OperationStatus      : Succeeded

> [!NOTE]
> Pokud chcete otestovat příkaz výše v bezpečné prostředí postupujte podle pokynů v [vytvořit virtuální síť (klasická)](virtual-networks-create-vnet-classic-pportal.md) chcete vytvořit síť vnet s názvem *TestVnet* a ujistěte se používá *10.0.0.0/8*  adresní prostor.
> 
> 

## <a name="how-to-specify-a-static-internal-ip-when-creating-a-vm"></a>Jak zadat statické IP adresy interní při vytváření virtuálního počítače
Následující skript Powershellu vytvoří novou cloudovou službu s názvem *TestService*, načte bitovou kopii z Azure a pak vytvoří virtuální počítač s názvem *TestVM* v nové cloudové službě pomocí načtený obrázek, nastaví služba WSUS Virtuální počítač byl v podsíti s názvem *Subnet-1*a nastaví *10.0.0.7* jako statické interní IP adresy pro virtuální počítač:

    New-AzureService -ServiceName TestService -Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
    | Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
    | Set-AzureSubnet –SubnetNames Subnet-1 `
    | Set-AzureStaticVNetIP -IPAddress 10.0.0.7 `
    | New-AzureVM -ServiceName "TestService" –VNetName TestVnet

## <a name="how-to-retrieve-static-internal-ip-information-for-a-vm"></a>Jak načíst statické interních informací IP pro virtuální počítač
Chcete-li zobrazit informace o statické interní IP pro virtuální počítač vytvořen pomocí výše uvedeného skriptu, spusťte následující příkaz prostředí PowerShell a sledovat hodnoty pro *IpAddress*:

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

## <a name="how-to-remove-a-static-internal-ip-from-a-vm"></a>Postup odebrání z virtuálního počítače statické interní IP adresa
Pokud chcete odebrat statické interní IP adresa přidá do virtuálního počítače ve výše uvedené skriptu, spusťte následující příkaz Powershellu:

    Get-AzureVM -ServiceName TestService -Name TestVM `
    | Remove-AzureStaticVNetIP `
    | Update-AzureVM

## <a name="how-to-add-a-static-internal-ip-to-an-existing-vm"></a>Jak přidat do existujícího virtuálního počítače statické interní IP adresa
Chcete-li přidat statické interní IP adresa na virtuální počítač vytvořený pomocí výše uvedeného skriptu, spusťte následující příkaz:

    Get-AzureVM -ServiceName TestService000 -Name TestVM `
    | Set-AzureStaticVNetIP -IPAddress 10.10.0.7 `
    | Update-AzureVM

## <a name="next-steps"></a>Další postup
[Vyhrazená IP adresa](virtual-networks-reserved-public-ip.md)

[Veřejné IP na úrovni instance (ILPIP)](virtual-networks-instance-level-public-ip.md)

[Vyhrazenou IP adresu REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx)

