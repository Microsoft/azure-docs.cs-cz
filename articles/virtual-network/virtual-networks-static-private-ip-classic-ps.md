---
title: Konfigurace privátních IP adres pro virtuální počítače (klasické) – Azure PowerShell | Dokumenty společnosti Microsoft
description: Zjistěte, jak nakonfigurovat privátní IP adresy pro virtuální počítače (Classic) pomocí PowerShellu.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
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
ms.openlocfilehash: 00a4f282da3a943516ffce584247ac20935526b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71058711"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-powershell"></a>Konfigurace privátních IP adres pro virtuální počítač (Classic) pomocí PowerShellu

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Tento článek se týká modelu nasazení Classic. Statickou [privátní IP adresu](virtual-networks-static-private-ip-arm-ps.md)můžete spravovat také v modelu nasazení Správce prostředků .

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Ukázkové příkazy prostředí PowerShell níže očekávají jednoduché prostředí, které již bylo vytvořeno. Pokud chcete spustit příkazy tak, jak jsou zobrazeny v tomto dokumentu, nejprve vytvořte testovací prostředí popsané v [části Vytvoření virtuální sítě](virtual-networks-create-vnet-classic-netcfg-ps.md).

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>Jak ověřit, zda je k dispozici určitá ADRESA IP
Chcete-li ověřit, zda je adresa IP *192.168.1.101* dostupná ve virtuální síti s názvem *TestVNet*, spusťte následující příkaz PowerShell a ověřte hodnotu *IsAvailable*:

    Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 192.168.1.101 

Očekávaný výstup:

    IsAvailable          : True
    AvailableAddresses   : {}
    OperationDescription : Test-AzureStaticVNetIP
    OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
    OperationStatus      : Succeeded

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Jak zadat statickou privátní IP adresu při vytváření virtuálního počítačů
Skript Prostředí PowerShell níže vytvoří novou cloudovou službu s názvem *TestService*, pak načte image z Azure, vytvoří virtuální počítač s názvem *DNS01* v nové cloudové službě pomocí načtené image, nastaví virtuální počítač, aby se v podsíti s názvem *FrontEnd*, a nastaví *192.168.1.7* jako statickou privátní IP adresu pro virtuální počítač:

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

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Jak načíst statické informace o privátní IP adrese pro virtuální hod
Chcete-li zobrazit informace o statické privátní IP adrese pro virtuální hod vytvořený pomocí výše uvedeného skriptu, spusťte následující příkaz Prostředí PowerShell a dodržujte hodnoty pro *IpAddress*:

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

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Jak odebrat statickou privátní IP adresu z virtuálního počítačů
Chcete-li odebrat statickou privátní IP adresu přidanou k virtuálnímu virtuálnímu počítačům ve výše uvedeném skriptu, spusťte následující příkaz Prostředí PowerShell:

    Get-AzureVM -ServiceName TestService -Name DNS01 |
      Remove-AzureStaticVNetIP |
      Update-AzureVM

Očekávaný výstup:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Update-AzureVM       052fa6f6-1483-0ede-a7bf-14f91f805483 Succeeded

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Jak přidat statickou privátní IP adresu do existujícího virtuálního počítačů
Chcete-li přidat statickou privátní IP adresu do virtuálního počítačů vytvořeného pomocí výše uvedeného skriptu, spusťte následující příkaz:

    Get-AzureVM -ServiceName TestService -Name DNS01 |
      Set-AzureStaticVNetIP -IPAddress 192.168.1.7 |
      Update-AzureVM

Očekávaný výstup:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Update-AzureVM       77d8cae2-87e6-0ead-9738-7c7dae9810cb Succeeded 

## <a name="set-ip-addresses-within-the-operating-system"></a>Nastavení ADRES IP v operačním systému

Doporučujeme, abyste staticky nepřiřazovali privátní IP adresu přiřazenou virtuálnímu počítači Azure v rámci operačního systému virtuálního počítače, pokud to není nutné. Pokud ručně nastavíte privátní IP adresu v rámci operačního systému, ujistěte se, že se jedná o stejnou adresu jako privátní IP adresa přiřazená virtuálnímu počítači Azure, nebo můžete ztratit připojení k virtuálnímu počítači. Nikdy byste neměli ručně přiřadit veřejnou IP adresu přiřazenou virtuálnímu počítači Azure v rámci operačního systému virtuálního počítače.

## <a name="next-steps"></a>Další kroky
* Informace o [vyhrazených veřejných IP](virtual-networks-reserved-public-ip.md) adresách.
* Další informace o [veřejných adresách IP (ILPIP) na úrovni instance.](virtual-networks-instance-level-public-ip.md)
* Prohlédněte si [rezervovaná práva API IP REST](https://msdn.microsoft.com/library/azure/dn722420.aspx).

