---
title: Konfigurace privátních IP adres pro virtuální počítače (Classic) – Azure PowerShell | Microsoft Docs
description: Přečtěte si, jak nakonfigurovat privátní IP adresy pro virtuální počítače (Classic) pomocí PowerShellu.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
tags: azure-service-management
ms.assetid: 60c7b489-46ae-48af-a453-2b429a474afd
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2e798f81102017b4d34c4b1b219a9f23035b0359
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98222832"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-powershell"></a>Konfigurace privátních IP adres pro virtuální počítač (Classic) pomocí PowerShellu

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Tento článek se týká modelu nasazení Classic. [V modelu nasazení Správce prostředků můžete také spravovat statickou privátní IP adresu](virtual-networks-static-private-ip-arm-ps.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Následující ukázkové příkazy prostředí PowerShell očekávají, že už je vytvořené jednoduché prostředí. Pokud chcete spustit příkazy, které se zobrazují v tomto dokumentu, nejdříve Sestavte testovací prostředí popsané v tématu [vytvoření virtuální](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-netcfg-ps)sítě.

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>Jak ověřit, zda je k dispozici konkrétní IP adresa
Pokud chcete ověřit, jestli je IP adresa *192.168.1.101* k dispozici ve virtuální síti s názvem *TestVNet*, spusťte následující příkaz PowerShellu a ověřte, jestli je hodnota k *dispozici*:

```azurepowershell
Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 192.168.1.101 
```

Očekávaný výstup:

```output
IsAvailable          : True
AvailableAddresses   : {}
OperationDescription : Test-AzureStaticVNetIP
OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
OperationStatus      : Succeeded
```

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Jak zadat statickou privátní IP adresu při vytváření virtuálního počítače
Níže uvedený skript PowerShellu vytvoří novou cloudovou službu s názvem *TestService* a potom načte image z Azure, vytvoří virtuální počítač s názvem *DNS01* v nové cloudové službě pomocí načtené image, nastaví virtuální počítač jako v podsíti s názvem *front-endu* a nastaví *192.168.1.7* jako statickou privátní IP adresu pro virtuální počítač:

```azurepowershell
New-AzureService -ServiceName TestService -Location "Central US"
$image = Get-AzureVMImage | where {$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
New-AzureVMConfig -Name DNS01 -InstanceSize Small -ImageName $image.ImageName |
    Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! |
    Set-AzureSubnet –SubnetNames FrontEnd |
    Set-AzureStaticVNetIP -IPAddress 192.168.1.7 |
    New-AzureVM -ServiceName TestService –VNetName TestVNet
```

Očekávaný výstup:

```output
WARNING: No deployment found in service: 'TestService'.
OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
New-AzureService     fcf705f1-d902-011c-95c7-b690735e7412 Succeeded      
New-AzureVM          3b99a86d-84f8-04e5-888e-b6fc3c73c4b9 Succeeded  
```

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Jak načíst informace o statických privátních IP adresách pro virtuální počítač
Pokud si chcete zobrazit informace o statických privátních IP adresách pro virtuální počítač vytvořený pomocí skriptu uvedeného výše, spusťte následující příkaz PowerShellu a sledujte hodnoty *IPAddress*:

```azurepowershell
Get-AzureVM -Name DNS01 -ServiceName TestService
```

Očekávaný výstup:

```output
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
```

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Jak odebrat statickou privátní IP adresu z virtuálního počítače
Pokud chcete ve skriptu výše odebrat statickou privátní IP adresu, kterou jste přidali do virtuálního počítače, spusťte následující příkaz PowerShellu:

```azurepowershell
Get-AzureVM -ServiceName TestService -Name DNS01 |
    Remove-AzureStaticVNetIP |
    Update-AzureVM
```

Očekávaný výstup:

```output
OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureVM       052fa6f6-1483-0ede-a7bf-14f91f805483 Succeeded
```

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Postup přidání statické privátní IP adresy do existujícího virtuálního počítače
Pokud chcete do virtuálního počítače, který jste vytvořili pomocí skriptu uvedeného výše, přidat statickou privátní IP adresu, spusťte následující příkaz:

```azurepowershell
Get-AzureVM -ServiceName TestService -Name DNS01 |
    Set-AzureStaticVNetIP -IPAddress 192.168.1.7 |
    Update-AzureVM
```

Očekávaný výstup:

```output
OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureVM       77d8cae2-87e6-0ead-9738-7c7dae9810cb Succeeded 
```

## <a name="set-ip-addresses-within-the-operating-system"></a>Nastavení IP adres v operačním systému

V případě potřeby doporučujeme, abyste privátní IP adresu přiřazenou k virtuálnímu počítači Azure nepřiřadili staticky v operačním systému virtuálního počítače. Pokud ručně nastavíte privátní IP adresu v operačním systému, ujistěte se, že se jedná o stejnou adresu jako soukromá IP adresa přiřazená k virtuálnímu počítači Azure, nebo můžete ztratit připojení k virtuálnímu počítači. Nikdy byste neměli ručně přiřadit veřejnou IP adresu přiřazenou k virtuálnímu počítači Azure v operačním systému virtuálního počítače.

## <a name="next-steps"></a>Další kroky
* Seznamte se s [rezervovanými veřejnými IP](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) adresami.
* Přečtěte si informace o [veřejných IP adresách na úrovni instance (ILPIP)](/previous-versions/azure/virtual-network/virtual-networks-instance-level-public-ip) .
* Projděte si [vyhrazená IP adresa rozhraní REST API](/previous-versions/azure/reference/dn722420(v=azure.100)).