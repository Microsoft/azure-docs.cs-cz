---
title: Konfigurace privátních IP adres pro virtuální počítače (Classic) – Azure PowerShell | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat privátní IP adresy pro virtuální počítače (Classic) pomocí Powershellu.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
tags: azure-service-management
ms.assetid: 60c7b489-46ae-48af-a453-2b429a474afd
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f99e67341d46e858cee7dd6a22f16fe06ad5b88a
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38678591"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-powershell"></a>Konfigurace privátních IP adres pro virtuální počítač (Classic) pomocí Powershellu

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Tento článek se týká modelu nasazení Classic. Můžete také [spravovat statickou privátní IP adresou v modelu nasazení Resource Manager](virtual-networks-static-private-ip-arm-ps.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Vzorové příkazy prostředí PowerShell následující očekávat jednoduché prostředí už vytvořili. Pokud chcete spustit příkazy, jak jsou zobrazeny v tomto dokumentu, nejprve vytvořit testovací prostředí popsané v [vytvořit síť VNet](virtual-networks-create-vnet-classic-netcfg-ps.md).

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>Jak ověřit, zda je k dispozici konkrétní IP adresu
Chcete-li ověřit, pokud IP adresa *192.168.1.101* je k dispozici ve virtuální síti s názvem *TestVNet*, spusťte následující příkaz prostředí PowerShell a potom ověřte hodnotu pro *IsAvailable*:

    Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 192.168.1.101 

Očekávaný výstup:

    IsAvailable          : True
    AvailableAddresses   : {}
    OperationDescription : Test-AzureStaticVNetIP
    OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
    OperationStatus      : Succeeded

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Jak zadat statickou privátní IP adresu při vytváření virtuálního počítače
Následující skript Powershellu vytvoří novou cloudovou službu s názvem *TestService*, potom načte obrázek z Azure, vytvoří virtuální počítač s názvem *DNS01* v novou cloudovou službu pomocí načtené image nastaví virtuální počítač v podsíti s názvem *front-endu*a nastaví *192.168.1.7* jako statickou privátní IP adresu pro virtuální počítač:

    New-AzureService -ServiceName TestService -Location "Central US"
    $image = Get-AzureVMImage | where {$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name DNS01 -InstanceSize Small -ImageName $image.ImageName |
      Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! |
      Set-AzureSubnet –SubnetNames FrontEnd |
      Set-AzureStaticVNetIP -IPAddress 192.168.1.7 |
      New-AzureVM -ServiceName TestService –VNetName TestVNet

Očekávaný výstup:

    WARNING: No deployment found in service: 'TestService'.
    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    New-AzureService     fcf705f1-d902-011c-95c7-b690735e7412 Succeeded      
    New-AzureVM          3b99a86d-84f8-04e5-888e-b6fc3c73c4b9 Succeeded  

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Jak načíst statické privátní informace o IP adrese virtuálního počítače
Chcete-li zobrazit statické privátní informace o IP adresu pro virtuální počítač vytvořen pomocí výše uvedeného skriptu, spusťte následující příkaz prostředí PowerShell a podívejte se hodnoty *IpAddress*:

    Get-AzureVM -Name DNS01 -ServiceName TestService

Očekávaný výstup:

    DeploymentName              : TestService
    Name                        : DNS01
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : Provisioning
    IpAddress                   : 192.168.1.7
    InstanceStateDetails        : Windows is preparing your computer for first use...
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : DNS01
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

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Postup odebrání statickou privátní IP adresu z virtuálního počítače
Odeberte statickou privátní IP adresu přidat do virtuálního počítače ve skriptu vyšší, spusťte následující příkaz Powershellu:

    Get-AzureVM -ServiceName TestService -Name DNS01 |
      Remove-AzureStaticVNetIP |
      Update-AzureVM

Očekávaný výstup:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Update-AzureVM       052fa6f6-1483-0ede-a7bf-14f91f805483 Succeeded

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Postup přidání statické privátní IP adresu do existujícího virtuálního počítače
Přidat statickou privátní IP adresa na virtuální počítač vytvořený pomocí skriptu výše, o následující příkaz:

    Get-AzureVM -ServiceName TestService -Name DNS01 |
      Set-AzureStaticVNetIP -IPAddress 192.168.1.7 |
      Update-AzureVM

Očekávaný výstup:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Update-AzureVM       77d8cae2-87e6-0ead-9738-7c7dae9810cb Succeeded 

## <a name="set-ip-addresses-within-the-operating-system"></a>Nastavení IP adresy v rámci operačního systému

Doporučuje se, že nepřiřadíte staticky privátní IP adresa přiřazená virtuální počítač Azure v rámci operačního systému virtuálního počítače, není-li nezbytné. Pokud jste ručně nastavili privátní IP adresu v rámci operačního systému, ujistěte se, že se jedná o stejnou adresu jako privátní IP adresa přiřazená k virtuálnímu počítači Azure nebo ztratíte připojení k virtuálnímu počítači. Veřejná IP adresa přidělená k virtuálnímu počítači Azure v rámci operačního systému virtuálního počítače by nikdy ručně přiřadit.

## <a name="next-steps"></a>Další postup
* Další informace o [vyhrazené veřejné IP adresy](virtual-networks-reserved-public-ip.md) adresy.
* Další informace o [veřejné IP (ILPIP) na úrovni instance](virtual-networks-instance-level-public-ip.md) adresy.
* Poraďte [vyhrazené IP rozhraní REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx).

