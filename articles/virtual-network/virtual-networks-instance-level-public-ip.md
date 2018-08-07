---
title: Azure úrovni Instance adresy veřejné IP adresy (klasické) | Dokumentace Microsoftu
description: Pochopit instance adresy úrovně veřejné IP (ILPIP) a jak je spravovat pomocí prostředí PowerShell.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
ms.assetid: 07eef6ec-7dfe-4c4d-a2c2-be0abfb48ec5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/03/2018
ms.author: genli
ms.openlocfilehash: cb8ba5169a6ebfbb11ba0acfa9b9f463b7cdf6a1
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39520798"
---
# <a name="instance-level-public-ip-classic-overview"></a>Instance přehled úrovně veřejných IP adres (Classic)
Instance úrovně veřejné IP (ILPIP) je veřejnou IP adresu můžete přiřadit přímo k instanci role virtuálního počítače nebo cloudové služby, nikoli do cloudové služby, který váš virtuální počítač nebo instanci role jsou umístěny v. ILPIP nepřijímá místo z virtuální IP (VIP), který je přiřazen ke cloudové službě. Místo toho je další IP adresu, můžete použít pro připojení přímo k vaší instanci virtuálního počítače nebo role.

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi: [Resource Manager a klasický model](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Tento článek se věnuje použití klasického modelu nasazení. Společnost Microsoft doporučuje vytváření virtuálních počítačů prostřednictvím Resource Manageru. Ujistěte se, že rozumíte jak [IP adresy](virtual-network-ip-addresses-overview-classic.md) fungovat v Azure.

![Rozdíl mezi ILPIP a virtuální IP adresy](./media/virtual-networks-instance-level-public-ip/Figure1.png)

Jak je znázorněno na obrázku 1, cloudovou službu se přistupuje pomocí virtuální IP adresy, zatímco jednotlivé virtuální počítače jsou obvykle přístupné pomocí virtuální IP adresy:&lt;číslo portu&gt;. Po přiřazení ILPIP konkrétnímu virtuálnímu počítači, tento virtuální počítač je přístupný přímo pomocí této IP adresy.

Při vytváření cloudové služby v Azure odpovídající záznamy DNS automaticky vytvoří pro povolení přístupu k této služby prostřednictvím plně kvalifikovaný název domény (FQDN), namísto použití skutečné virtuální IP adresy. Pro ILPIP umožňuje přístup k instanci role nebo virtuálního počítače podle plně kvalifikovaného názvu domény namísto ILPIP se stane stejného procesu. Například pokud vytvořit cloudovou službu s názvem *contosoadservice*, a nakonfigurovat webovou roli s názvem *contosoweb* se dvěma instancemi Azure zaregistruje následující záznamy pro instance:

* contosoweb\_IN_0.contosoadservice.cloudapp.net
* contosoweb\_IN_1.contosoadservice.cloudapp.net 

> [!NOTE]
> Můžete přiřadit pouze jeden ILPIP pro každý virtuální počítač nebo instanci role. Můžete použít až pro 5 ILPIPs na jedno předplatné. ILPIPs nejsou podporovány pro virtuální počítače s několika síťovými Kartami.
> 
> 

## <a name="why-would-i-request-an-ilpip"></a>Proč byste požádat ILPIP?
Pokud chcete být schopni připojit k vaší instanci role virtuálního počítače nebo IP adresa přiřazená k němu, místo použití cloudu služby virtuálních IP adres:&lt;číslo portu&gt;, žádosti ILPIP pro váš virtuální počítač nebo role instance.

* **Aktivní FTP** -přiřazením ILPIP k virtuálnímu počítači, může přijímat přenosy přes libovolný port. Koncové body nejsou nutné pro virtuální počítač přijímat přenosy.  Najdete v článku [přehled protokolu FTP] (https://en.wikipedia.org/wiki/File_Transfer_Protocol#Protocol_overview) podrobné informace o protokolu FTP.
* **Odchozí IP** – odchozí přenos dat pocházejících z virtuálního počítače je namapovaná na ILPIP jako zdroj a ILPIP jednoznačně identifikuje virtuální počítač na externí entity.

> [!NOTE]
> V minulosti ILPIP adresy se označuje jako veřejné adresy IP adresy (PIP).
> 

## <a name="manage-an-ilpip-for-a-vm"></a>Správa ILPIP pro virtuální počítač
Následující úlohy umožňují vytvořit, přiřadit a odebrat ILPIPs z virtuálních počítačů:

### <a name="how-to-request-an-ilpip-during-vm-creation-using-powershell"></a>Tom, jak požádat ILPIP během vytváření virtuálních počítačů pomocí Powershellu
Následující skript Powershellu vytvoří cloudovou službu s názvem *FTPService*, načte bitovou kopii z Azure, vytvoří virtuální počítač s názvem *FTPInstance* pomocí načtené image nastaví virtuální počítač určený ILPIP a přidá virtuální počítač do nové služby:

```powershell
New-AzureService -ServiceName FTPService -Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

#Set "current" storage account for the subscription. It will be used as the location of new VM disk

Set-AzureSubscription -SubscriptionName <SubName> -CurrentStorageAccountName <StorageAccountName>

#Create a new VM configuration object

New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"

```
Pokud chcete zadat jiný účet úložiště jako umístění nového disku virtuálního počítače, můžete použít **MediaLocation** parametr:

```powershell
    New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
     -MediaLocation https://management.core.windows.net/<SubscriptionID>/services/storageservices/<StorageAccountName> `
    | Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
    | Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"
```

### <a name="how-to-retrieve-ilpip-information-for-a-vm"></a>Jak načíst informace ILPIP pro virtuální počítač
Chcete-li zobrazit informace ILPIP pro virtuální počítač vytvořen s předchozí skript, pomocí následujícího příkazu prostředí PowerShell a podívejte se hodnoty *PublicIPAddress* a *PublicIPName*:

```powershell
Get-AzureVM -Name FTPInstance -ServiceName FTPService
```

Očekávaný výstup:
 
    DeploymentName              : FTPService
    Name                        : FTPInstance
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : ReadyRole
    IpAddress                   : 100.74.118.91
    InstanceStateDetails        : 
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : FTPInstance
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : FTPInstance
    AvailabilitySetName         : 
    DNSName                     : http://ftpservice888.cloudapp.net/
    Status                      : ReadyRole
    GuestAgentStatus            :   Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 104.43.142.188
    PublicIPName                : ftpip
    NetworkInterfaces           : {}
    ServiceName                 : FTPService
    OperationDescription        : Get-AzureVM
    OperationId                 : 568d88d2be7c98f4bbb875e4d823718e
    OperationStatus             : OK

### <a name="how-to-remove-an-ilpip-from-a-vm"></a>Postup odebrání ILPIP z virtuálního počítače
Pokud chcete odebrat ILPIP přidá do virtuálního počítače v předchozím scénáři, spusťte následující příkaz Powershellu:

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Remove-AzurePublicIP | Update-AzureVM
```

### <a name="how-to-add-an-ilpip-to-an-existing-vm"></a>Postup přidání ILPIP do existujícího virtuálního počítače
Přidat ILPIP k virtuálnímu počítači vytvořenému pomocí předchozího skriptu, spusťte následující příkaz:

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Set-AzurePublicIP -PublicIPName ftpip2 | Update-AzureVM
```

## <a name="manage-an-ilpip-for-a-cloud-services-role-instance"></a>Správa ILPIP pro instanci role Cloud Services

Pokud chcete přidat ILPIP instance role Cloud Services, proveďte následující kroky:

1. Stáhnout pomocí kroků v souboru .cscfg pro cloudovou službu [jak konfigurovat Cloud Services](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg) článku.
2. Aktualizovat přidáním souboru .cscfg `InstanceAddress` elementu. Následující příklad přidá ILPIP s názvem *MyPublicIP* role instance s názvem *WebRole1*: 

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ILPIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
          <ConfigurationSettings>
        <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
          </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <AddressAssignments>
          <InstanceAddress roleName="WebRole1">
        <PublicIPs>
          <PublicIP name="MyPublicIP" domainNameLabel="MyPublicIP" />
            </PublicIPs>
          </InstanceAddress>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>
    ```
3. Nahrát pomocí kroků v souboru .cscfg pro cloudovou službu [jak konfigurovat Cloud Services](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg) článku.

### <a name="how-to-retrieve-ilpip-information-for-a-cloud-service"></a>Jak načíst informace ILPIP pro Cloudovou službu
Chcete-li zobrazit informace ILPIP za role instance, spusťte následující příkaz prostředí PowerShell a sledovat hodnoty pro *PublicIPAddress* a *PublicIPName*:

```powershell
$roles = Get-AzureRole -ServiceName PaaSFTPService -Slot Production -RoleName WorkerRole1 -InstanceDetails

$roles[0].PublicIPAddress
$roles[1].PublicIPAddress
```

## <a name="next-steps"></a>Další postup
* Pochopit, jak [přidělování IP adres](virtual-network-ip-addresses-overview-classic.md) funguje v modelu nasazení classic.
* Další informace o [vyhrazené IP adresy](virtual-networks-reserved-public-ip.md).
