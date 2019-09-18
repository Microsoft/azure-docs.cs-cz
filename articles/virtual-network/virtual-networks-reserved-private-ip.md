---
title: Statická interní privátní IP adresa – virtuální počítač Azure – klasický
description: Principy statických interních IP adres (DIP) a jejich správy
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
ms.assetid: 93444c6f-af1b-41f8-a035-77f5c0302bf0
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: c37c49d8f7e09334014af290bf3a8c8e6d35f04b
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058355"
---
# <a name="how-to-set-a-static-internal-private-ip-address-using-powershell-classic"></a>Jak nastavit statickou interní privátní IP adresu pomocí PowerShellu (Classic)
Ve většině případů nebudete muset pro svůj virtuální počítač zadávat statickou interní IP adresu. Virtuální počítače ve virtuální síti automaticky obdrží interní IP adresu z rozsahu, který zadáte. V některých případech ale určením statické IP adresy pro konkrétní virtuální počítač dává smysl. Například pokud váš virtuální počítač bude používat DNS nebo se bude jednat o řadič domény. Statická interní IP adresa zůstává s virtuálním počítačem i přes stav zastavení nebo zrušení zřízení. 

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi:  [Správce prostředků a klasický](../azure-resource-manager/resource-manager-deployment-model.md). Tento článek se věnuje použití klasického modelu nasazení. Microsoft doporučuje, aby většina nových nasazení používala [model nasazení Správce prostředků](virtual-networks-static-private-ip-arm-ps.md).
> 
> 
> ## <a name="install-the-azure-powershell-service-management-module"></a>Instalace modulu Azure PowerShell Service Management

Před spuštěním následujících příkazů se ujistěte, že [je v počítači nainstalován modul](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0
) správy služby Azure PowerShell. Historii verzí modulu správy služeb Azure PowerShell najdete [v tématu modul Azure v Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/Azure/5.3.0).

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>Jak ověřit, zda je k dispozici konkrétní IP adresa
Pokud chcete ověřit, jestli je IP adresa *10.0.0.7* dostupná ve virtuální síti s názvem *TestVnet*, spusťte následující příkaz PowerShellu a ověřte, jestli je hodnota *dostupná*.


    Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 10.0.0.7 

    IsAvailable          : True
    AvailableAddresses   : {}
    OperationDescription : Test-AzureStaticVNetIP
    OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
    OperationStatus      : Succeeded

> [!NOTE]
> Pokud chcete otestovat výše uvedený příkaz v bezpečném prostředí, postupujte podle pokynů v části [vytvoření virtuální sítě (Classic)](virtual-networks-create-vnet-classic-pportal.md) k vytvoření virtuální sítě s názvem *TestVnet* a zajistěte, aby používala adresní prostor *10.0.0.0/8* .
> 
> 

## <a name="how-to-specify-a-static-internal-ip-when-creating-a-vm"></a>Jak zadat statickou interní IP adresu při vytváření virtuálního počítače
Skript PowerShellu níže vytvoří novou cloudovou službu s názvem *TestService*, potom načte image z Azure a pak vytvoří virtuální počítač s názvem *TestVM* v nové cloudové službě pomocí načtené image, nastaví virtuální počítač jako v podsíti s názvem *podsíť-1*, a nastaví *10.0.0.7* jako statickou interní IP adresu pro virtuální počítač:

    New-AzureService -ServiceName TestService -Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
    | Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
    | Set-AzureSubnet –SubnetNames Subnet-1 `
    | Set-AzureStaticVNetIP -IPAddress 10.0.0.7 `
    | New-AzureVM -ServiceName "TestService" –VNetName TestVnet

## <a name="how-to-retrieve-static-internal-ip-information-for-a-vm"></a>Načtení statických informací o interních IP adresách pro virtuální počítač
Pokud chcete zobrazit informace o statických interních IP adresách pro virtuální počítač vytvořený pomocí skriptu uvedeného výše, spusťte následující příkaz PowerShellu a sledujte hodnoty pro *IPAddress*:

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

## <a name="how-to-remove-a-static-internal-ip-from-a-vm"></a>Jak odebrat statickou interní IP adresu z virtuálního počítače
Pokud chcete odebrat statickou interní IP adresu přidanou do virtuálního počítače ve výše uvedeném skriptu, spusťte následující příkaz PowerShellu:

    Get-AzureVM -ServiceName TestService -Name TestVM `
    | Remove-AzureStaticVNetIP `
    | Update-AzureVM

## <a name="how-to-add-a-static-internal-ip-to-an-existing-vm"></a>Postup přidání statické interní IP adresy do existujícího virtuálního počítače
Pokud chcete přidat statickou interní IP adresu k virtuálnímu počítači vytvořenému pomocí skriptu uvedeného výše, spusťte následující příkaz:

    Get-AzureVM -ServiceName TestService000 -Name TestVM `
    | Set-AzureStaticVNetIP -IPAddress 10.10.0.7 `
    | Update-AzureVM

## <a name="next-steps"></a>Další postup
[Vyhrazená IP adresa](virtual-networks-reserved-public-ip.md)

[Veřejná IP adresa na úrovni instance (ILPIP)](virtual-networks-instance-level-public-ip.md)

[Rozhraní REST API pro Vyhrazená IP adresa](https://msdn.microsoft.com/library/azure/dn722420.aspx)

