---
title: Řeší Azure úrovni Instance veřejnou IP adresu (klasická) | Microsoft Docs
description: Pochopit instance adresy úrovně veřejné IP (splnění) a jak spravovat pomocí prostředí PowerShell.
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
ms.date: 02/10/2016
ms.author: genli
ms.openlocfilehash: 4b4350e6b1616450ce45f9e947cc3b639a341ae7
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="instance-level-public-ip-classic-overview"></a>Přehled veřejné IP (klasické) instance
Instance úrovně veřejné IP (splnění) je veřejná IP adresa, kterou můžete přiřadit přímo k instanci role virtuálního počítače nebo cloudové služby, a nikoli pro cloudovou službu, která jsou umístěny ve vaší instanci virtuálního počítače nebo role. SPLNĚNÍ neberou v místě z virtuální IP (VIP) přiřazený ke cloudové službě. Místo toho je další IP adresu, která můžete použít pro připojení přímo k vaší instanci virtuálního počítače nebo role.

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi: [Resource Manager a klasický model](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Tento článek se věnuje použití klasického modelu nasazení. Společnost Microsoft doporučuje vytváření virtuálních počítačů prostřednictvím Správce prostředků. Musíte rozumět jak [IP adresy](virtual-network-ip-addresses-overview-classic.md) pracovní v Azure.

![Rozdíl mezi splnění a virtuální IP adresy](./media/virtual-networks-instance-level-public-ip/Figure1.png)

Jak je znázorněno na obrázku 1, Cloudová služba přistupuje pomocí virtuální IP adresu, když jednotlivé virtuální počítače jsou obvykle získat přístup pomocí virtuálních IP adres:&lt;číslo portu&gt;. Přiřazením splnění konkrétní virtuální počítač, tento virtuální počítač je přístupný přímo pomocí tuto IP adresu.

Při vytváření cloudové služby v Azure, odpovídající záznamy DNS A automaticky vytvoří pro povolení přístupu ke službě prostřednictvím platný plně kvalifikovaný název domény (FQDN), místo použití skutečné VIP. Stejný postup se stane pro splnění umožňuje přístup k instanci virtuálního počítače nebo role ve plně kvalifikovaný název domény místo splnění. Například pokud vytvoříte cloudové služby s názvem *contosoadservice*, a konfigurace webové role s názvem *contosoweb* se dvěma instancemi Azure zaregistruje následující záznamů A instancí:

* contosoweb\_IN_0.contosoadservice.cloudapp.net
* contosoweb\_IN_1.contosoadservice.cloudapp.net 

> [!NOTE]
> Můžete přiřadit pouze jeden splnění pro každou instanci virtuálního počítače nebo role. Můžete použít až 5 ILPIPs za předplatné. ILPIPs nejsou podporovány u virtuálních počítačů více síťovými Kartami.
> 
> 

## <a name="why-would-i-request-an-ilpip"></a>Proč se splnění požadavku?
Pokud chcete být schopni se připojit k instanci virtuálního počítače nebo role pomocí přiřazené přímo na IP adresy, místo cloudu pomocí služby virtuální IP adresy:&lt;číslo portu&gt;, žádosti splnění virtuálního počítače nebo role instance.

* **Aktivní FTP** -přiřazením splnění pro virtuální počítač mohl přijímat provoz z jakéhokoli portu. Koncové body nejsou nutné pro virtuální počítač přijímat provoz.  V tématu (https://en.wikipedia.org/wiki/File_Transfer_Protocol#Protocol_overview)[přehled protokolu FTP] podrobnosti v protokolu FTP.
* **Odchozí IP** – odchozí přenosy pocházející z virtuálního počítače je namapovaná na splnění jako zdroj a splnění jednoznačně identifikuje virtuálního počítače na externí entity.

> [!NOTE]
> V minulosti adresu splnění se označuje jako veřejná adresa IP (PIP).
> 

## <a name="manage-an-ilpip-for-a-vm"></a>Správa splnění pro virtuální počítač
Následující úlohy umožňují vytvořit, přiřadit a odebrat ILPIPs z virtuálních počítačů:

### <a name="how-to-request-an-ilpip-during-vm-creation-using-powershell"></a>Postup žádosti splnění během vytváření virtuálních počítačů pomocí prostředí PowerShell
Následující skript prostředí PowerShell vytvoří cloudové služby s názvem *FTPService*, načte bitovou kopii z Azure, vytvoří virtuální počítač s názvem *FTPInstance* pomocí načtené bitové kopie, nastaví virtuálního počítače používat splnění a přidá virtuální počítač do nové služby:

```powershell
New-AzureService -ServiceName FTPService -Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"} `
New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"
```

### <a name="how-to-retrieve-ilpip-information-for-a-vm"></a>Jak načíst informace o splnění pro virtuální počítač
Chcete-li zobrazit informace o splnění pro virtuální počítač vytvořený s předchozí skriptu, spusťte následující příkaz prostředí PowerShell a sledovat hodnoty *PublicIPAddress* a *PublicIPName*:

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

### <a name="how-to-remove-an-ilpip-from-a-vm"></a>Postup odebrání splnění z virtuálního počítače
Chcete-li odebrat splnění přidat do virtuálního počítače v předchozí skriptu, spusťte následující příkaz prostředí PowerShell:

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Remove-AzurePublicIP | Update-AzureVM
```

### <a name="how-to-add-an-ilpip-to-an-existing-vm"></a>Postup přidání splnění do stávajícího virtuálního počítače
Pokud chcete přidat splnění na virtuální počítač vytvořený skript předchozí, spusťte následující příkaz:

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Set-AzurePublicIP -PublicIPName ftpip2 | Update-AzureVM
```

## <a name="manage-an-ilpip-for-a-cloud-services-role-instance"></a>Správa splnění pro instanci role cloudové služby

Pokud chcete přidat splnění instanci role cloudové služby, proveďte následující kroky:

1. Stáhnout pomocí kroků v souboru .cscfg pro cloudové služby [postup konfigurace cloudové služby](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg) článku.
2. Aktualizovat přidáním souboru .cscfg `InstanceAddress` elementu. Následující příklad přidá splnění s názvem *MyPublicIP* role instance s názvem *WebRole1*: 

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
3. Nahrát pomocí kroků v souboru .cscfg pro cloudové služby [postup konfigurace cloudové služby](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg) článku.

## <a name="next-steps"></a>Další postup
* Pochopit, jak [IP adresování](virtual-network-ip-addresses-overview-classic.md) funguje v modelu nasazení classic.
* Další informace o [vyhrazené IP adresy](virtual-networks-reserved-public-ip.md).
