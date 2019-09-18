---
title: Veřejné IP adresy na úrovni instance Azure (Classic) | Microsoft Docs
description: Pochopení adres veřejných IP adres (ILPIP) na úrovni instance a jejich správa pomocí PowerShellu
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
ms.assetid: 07eef6ec-7dfe-4c4d-a2c2-be0abfb48ec5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/03/2018
ms.author: genli
ms.openlocfilehash: d92832d1eee995e8883dc6c8ed0f58c9755e40f8
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058409"
---
# <a name="instance-level-public-ip-classic-overview"></a>Přehled veřejné IP adresy na úrovni instance (Classic)
Veřejná IP adresa na úrovni instance (ILPIP) je veřejná IP adresa, kterou můžete přímo přiřadit k virtuálnímu počítači nebo instanci role Cloud Services, nikoli ke cloudové službě, ve které se nachází váš virtuální počítač nebo instance role. ILPIP nebere místo virtuální IP adresy (VIP), která je přiřazená k vaší cloudové službě. Místo toho je to další IP adresa, kterou můžete použít k přímému připojení k VIRTUÁLNÍmu počítači nebo instanci role.

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi:  [Správce prostředků a klasický](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Tento článek se věnuje použití klasického modelu nasazení. Microsoft doporučuje vytvářet virtuální počítače prostřednictvím Správce prostředků. Ujistěte se, že rozumíte tomu, jak [IP adresy](virtual-network-ip-addresses-overview-classic.md) fungují v Azure.

![Rozdíl mezi ILPIP a VIP](./media/virtual-networks-instance-level-public-ip/Figure1.png)

Jak je znázorněno na obrázku 1, ke cloudové službě se dostanete pomocí virtuální IP adresy, zatímco k jednotlivým virtuálním počítačům&lt;se běžně&gt;používá virtuální IP adresa: číslo portu. Přiřazením ILPIP ke konkrétnímu virtuálnímu počítači se k tomuto virtuálnímu počítači dá získat přímý pøístup pomocí této IP adresy.

Když v Azure vytvoříte cloudovou službu, vytvoří se automaticky odpovídající záznamy DNS A, které umožní přístup ke službě prostřednictvím plně kvalifikovaného názvu domény (FQDN) místo použití skutečné virtuální IP adresy. Ke stejnému procesu dochází pro ILPIP, což umožňuje přístup k virtuálnímu počítači nebo instanci role podle plně kvalifikovaného názvu domény namísto ILPIP. Pokud například vytvoříte cloudovou službu s názvem *contosoadservice*a nakonfigurujete webovou roli s názvem *contosoweb* se dvěma instancemi a v souboru. cscfg `domainNameLabel` je nastavená na *WebPublicIP*, Azure zaregistruje následující záznamy pro instance


* WebPublicIP.0.contosoadservice.cloudapp.net
* WebPublicIP.1.contosoadservice.cloudapp.net
* ...


> [!NOTE]
> Každému virtuálnímu počítači nebo instanci role můžete přiřadit jenom jeden ILPIP. U každého předplatného můžete použít až 5 ILPIPs. ILPIPs se pro virtuální počítače s více síťovými kartami nepodporují.
> 
> 

## <a name="why-would-i-request-an-ilpip"></a>Proč bych chtěl požádat o ILPIP?
Pokud se chcete připojit ke svému virtuálnímu počítači nebo instanci role prostřednictvím IP adresy přiřazené přímo k této adrese, místo použití cloudové služby VIP:&lt;číslo&gt;portu, požádejte o ILPIP pro váš virtuální počítač nebo instanci role.

* **Aktivní FTP** – přiřazením ILPIP k virtuálnímu počítači může přijímat přenosy z libovolného portu. Koncové body nejsou pro příjem provozu virtuálního počítače vyžadovány.  Podrobnosti o protokolu FTP najdete v tématu [Přehled protokolu FTP](https://en.wikipedia.org/wiki/File_Transfer_Protocol#Protocol_overview) .
* **Odchozí IP adresa** – odchozí přenosy pocházející z virtuálního počítače se MAPUJÍ na ILPIP jako zdroj a ILPIP jedinečně IDENTIFIKUJE virtuální počítač externím entitám.

> [!NOTE]
> V minulosti se adresa ILPIP odkazovala jako adresa veřejné IP adresy (PIP).
> 

## <a name="manage-an-ilpip-for-a-vm"></a>Správa ILPIP pro virtuální počítač
Následující úlohy umožňují vytvářet, přiřazovat a odebírat ILPIPs z virtuálních počítačů:

### <a name="how-to-request-an-ilpip-during-vm-creation-using-powershell"></a>Jak požádat o ILPIP při vytváření virtuálního počítače pomocí PowerShellu
Následující skript PowerShellu vytvoří cloudovou službu s názvem *FTPService*, načte image z Azure, vytvoří virtuální počítač s názvem *FTPInstance* pomocí načtené image, nastaví virtuální počítač tak, aby používal ILPIP, a přidá virtuální počítač do nové služby:

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
Pokud chcete jako umístění nového disku virtuálního počítače zadat jiný účet úložiště, můžete použít parametr **MediaLocation** :

```powershell
    New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
     -MediaLocation https://management.core.windows.net/<SubscriptionID>/services/storageservices/<StorageAccountName> `
    | Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
    | Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"
```

### <a name="how-to-retrieve-ilpip-information-for-a-vm"></a>Jak načíst informace ILPIP pro virtuální počítač
Pokud chcete zobrazit ILPIP informace o virtuálním počítači vytvořeném pomocí předchozího skriptu, spusťte následující příkaz PowerShellu a sledujte hodnoty pro *PublicIPAddress* a *PublicIPName*:

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

### <a name="how-to-remove-an-ilpip-from-a-vm"></a>Jak odebrat ILPIP z virtuálního počítače
Pokud chcete odebrat ILPIP přidaný do virtuálního počítače v předchozím skriptu, spusťte následující příkaz PowerShellu:

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Remove-AzurePublicIP | Update-AzureVM
```

### <a name="how-to-add-an-ilpip-to-an-existing-vm"></a>Postup přidání ILPIP do existujícího virtuálního počítače
Pokud chcete přidat ILPIP k virtuálnímu počítači vytvořenému pomocí předchozího skriptu, spusťte následující příkaz:

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Set-AzurePublicIP -PublicIPName ftpip2 | Update-AzureVM
```

## <a name="manage-an-ilpip-for-a-cloud-services-role-instance"></a>Správa ILPIP pro instanci role Cloud Services

Chcete-li přidat ILPIP do instance role Cloud Services, proveďte následující kroky:

1. Stáhněte soubor. cscfg pro cloudovou službu provedením kroků v článku [Postup konfigurace Cloud Services](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg) .
2. Aktualizujte soubor. cscfg přidáním `InstanceAddress` elementu. Následující příklad přidá ILPIP s názvem *MyPublicIP* do instance role s názvem *WebRole1*: 

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
          <PublicIP name="MyPublicIP" domainNameLabel="WebPublicIP" />
            </PublicIPs>
          </InstanceAddress>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>
    ```
3. Nahrajte soubor. cscfg pro cloudovou službu provedením kroků v článku [Postup konfigurace Cloud Services](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg) .

### <a name="how-to-retrieve-ilpip-information-for-a-cloud-service"></a>Jak načíst ILPIP informace pro cloudovou službu
Chcete-li zobrazit informace o ILPIP na instanci role, spusťte následující příkaz prostředí PowerShell a sledujte hodnoty pro *PublicIPAddress*, *PublicIPName*, *PublicIPDomainNameLabel* a *PublicIPFqdns*:

```powershell
Add-AzureAccount

$roles = Get-AzureRole -ServiceName <Cloud Service Name> -Slot Production -RoleName WebRole1 -InstanceDetails

$roles[0].PublicIPAddress
$roles[1].PublicIPAddress
```

Můžete se také použít `nslookup` k dotazování na záznam A v dílčí doméně:

```batch
nslookup WebPublicIP.0.<Cloud Service Name>.cloudapp.net
``` 

## <a name="next-steps"></a>Další kroky
* Pochopte, jak [přidělování IP adres](virtual-network-ip-addresses-overview-classic.md) funguje v modelu nasazení Classic.
* Přečtěte si o [rezervovaných IP adresách](virtual-networks-reserved-public-ip.md).
