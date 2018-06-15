---
title: Správa Azure vyhrazené IP adresy (klasické) – prostředí PowerShell | Microsoft Docs
description: Pochopit vyhrazené IP adresy (klasické) a jak spravovat pomocí prostředí PowerShell.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
ms.assetid: 34652a55-3ab8-4c2d-8fb2-43684033b191
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/10/2016
ms.author: genli
ms.openlocfilehash: 25fe3c5361ff58f8d62d5d083b7a69f517d2a267
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
ms.locfileid: "31798950"
---
# <a name="reserved-ip-addresses-classic"></a>Vyhrazené IP adresy (klasické)

> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [Azure CLI](virtual-network-deploy-static-pip-arm-cli.md)
> * [PowerShell (Classic)](virtual-networks-reserved-public-ip.md)

IP adresy v Azure lze rozdělit do dvou kategorií: dynamické a vyhrazené. Veřejné IP adresy, které spravuje Azure je dynamická ve výchozím nastavení. To znamená, že IP adresa použít pro dané cloudové služby (VIP) nebo pro přístup k virtuálnímu počítači nebo přímo instance role (splnění) můžete změnit čas od času, když jsou prostředky vypnout nebo zastavena (deallocated).

Abyste zabránili změna IP adresy, je možné rezervovat IP adresu. Vyhrazené IP adresy lze použít pouze jako virtuální IP adresu, zajistíte, že je IP adresa pro cloudové služby zůstane stejný, to i v případě prostředky jsou vypnout nebo zastavená (deallocated). Kromě toho můžete převést existující dynamické IP adresy, které jsou použity jako virtuální IP adresu pro rezervovanou IP adresu.

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi: [Resource Manager a klasický model](../azure-resource-manager/resource-manager-deployment-model.md). Tento článek se věnuje použití klasického modelu nasazení. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Zjistěte, jak můžete vyhradit statické veřejné IP adresy pomocí [modelu nasazení Resource Manager](virtual-network-ip-addresses-overview-arm.md).

Další informace o IP adresách v Azure, najdete [IP adresy](virtual-network-ip-addresses-overview-classic.md) článku.

## <a name="when-do-i-need-a-reserved-ip"></a>Co je potřeba vyhrazená IP adresa?
* **Chcete zajistit, že IP adresa je vyhrazena ve vašem předplatném**. Pokud chcete rezervovat IP adresu, která se neuvolní ze svého předplatného za žádných okolností, používejte vyhrazené veřejné IP adresy.  
* **Chcete, aby vaše IP adresa zůstat u cloudové služby i napříč zastavena nebo navrácena stavu (VM)**. Pokud chcete, aby služby, ke kterým přistupují pomocí IP adresy, nemění, i když virtuální počítače v rámci cloudové služby jsou vypnut, nebo zastavte (deallocated).
* **Chcete zajistit, aby používal odchozí provoz z Azure předvídatelný IP adresu**. Můžete mít místní brány firewall nakonfigurovat tak, aby pouze provoz z konkrétní IP adresy. Vyhrazením IP adresy, které znáte zdrojovou IP adresu a není nutné aktualizovat vašich pravidlech brány firewall kvůli změně IP.

## <a name="faq"></a>Nejčastější dotazy
1. Můžete použít vyhrazenou IP adresu pro všechny služby Azure? <br>
    Ne. Vyhrazené IP adresy lze použít pouze pro virtuální počítače a instance role cloudové služby vystavenou přes virtuální IP adresu.
2. Kolik vyhrazené IP adresy, které může mít <br>
    Podrobnosti najdete v tématu [Azure omezuje](../azure-subscription-service-limits.md#networking-limits) článku.
3. Je pro vyhrazené IP adresy zdarma? <br>
    V některých případech. Podrobnosti o cenách najdete v článku [vyhrazené IP adresy podrobnosti o cenách](http://go.microsoft.com/fwlink/?LinkID=398482) stránky.
4. Jak rezervovat IP adresu? <br>
    Můžete použít PowerShell, [REST API pro správu Azure](https://msdn.microsoft.com/library/azure/dn722420.aspx), nebo [portál Azure](https://portal.azure.com) rezervovat IP adresu v oblasti Azure. Vyhrazená IP adresa je přidružená k vašemu předplatnému.
5. Můžete použít vyhrazenou IP adresu na základě skupiny vztahů sítě vnet? <br>
    Ne. Vyhrazené IP adresy jsou podporovány pouze v regionální virtuální sítě. Vyhrazené IP adresy nejsou podporovány pro virtuální sítě, které jsou přidruženy skupiny vztahů. Další informace o přiřazení virtuální síť s oblast nebo skupinu vztahů, najdete v článku [o virtuální místní sítě a skupiny vztahů](virtual-networks-migrate-to-regional-vnet.md) článku.

## <a name="manage-reserved-vips"></a>Spravovat vyhrazené virtuální IP adresy

Ujistěte se, je nainstalován a nakonfigurován prostředí PowerShell pomocí kroků v [instalace a konfigurace prostředí PowerShell](/powershell/azure/overview) článku. 

Před použitím vyhrazené IP adresy, je třeba přidat ji do vašeho předplatného. Chcete-li vytvořit vyhrazenou IP adresu z fondu veřejných IP adres k dispozici v *střed USA* umístění, spusťte následující příkaz:

```powershell
New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"
```

Všimněte si, ale není možné zadat co IP je vyhrazena. Pokud chcete zobrazit, jaké IP adres rezervovaných v rámci vašeho předplatného, spusťte následující příkaz prostředí PowerShell a Všimněte si hodnot pro *ReservedIPName* a *adresu*:

```powershell
Get-AzureReservedIP
```

Očekávaný výstup:

    ReservedIPName       : MyReservedIP
    Address              : 23.101.114.211
    Id                   : d73be9dd-db12-4b5e-98c8-bc62e7c42041
    Label                :
    Location             : Central US
    State                : Created
    InUse                : False
    ServiceName          :
    DeploymentName       :
    OperationDescription : Get-AzureReservedIP
    OperationId          : 55e4f245-82e4-9c66-9bd8-273e815ce30a
    OperationStatus      : Succeeded

>[!NOTE]
>Když vytvoříte pomocí prostředí PowerShell vyhrazenou IP adresu, nemůžete zadat skupinu prostředků vytvořit vyhrazenou IP adresu v. Azure místech do skupiny prostředků s názvem *výchozí sítě* automaticky. Pokud vytvoříte vyhrazené IP pomocí [portál Azure](http://portal.azure.com), můžete zadat všechny skupiny prostředků, které zvolíte. Pokud vytvoříte vyhrazenou IP adresu ve skupině prostředků než *výchozí sítě* však vždy, když odkazujete vyhrazenou IP adresu s příkazy, jako `Get-AzureReservedIP` a `Remove-AzureReservedIP`, musí odkazovat na název *vyhrazený název skupiny prostředků ip název skupiny*.  Například pokud vytvoříte vyhrazená IP adresa s názvem *myReservedIP* ve skupině prostředků s názvem *myResourceGroup*, musí odkazovat na název vyhrazenou IP adresu jako *skupiny myResourceGroup myReservedIP*.   

Jakmile je vyhrazené IP adresy, zůstane přidružený k vašemu předplatnému, dokud ho neodstraníte. Pokud chcete odstranit vyhrazená IP adresa, spusťte následující příkaz prostředí PowerShell:

```powershell
Remove-AzureReservedIP -ReservedIPName "MyReservedIP"
```

## <a name="reserve-the-ip-address-of-an-existing-cloud-service"></a>Rezervovat IP adresu existující cloudové služby
Je možné rezervovat IP adresu existující cloudové služby tak, že přidáte `-ServiceName` parametr. Rezervovat IP adresu cloudové služby *TestService* v *střed USA* umístění, spusťte následující příkaz prostředí PowerShell:

```powershell
New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US" -ServiceName TestService
```

## <a name="associate-a-reserved-ip-to-a-new-cloud-service"></a>Přidružení vyhrazené IP adresy na novou cloudovou službu
Následující skript vytvoří nové vyhrazená IP adresa a přidruží ji k novou cloudovou službu s názvem *TestService*.

```powershell
New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService -ReservedIPName MyReservedIP -Location "Central US"
```

> [!NOTE]
> Když vytvoříte vyhrazená IP adresa pro použití s cloudovou službu, je stále odkazovat na virtuální počítač pomocí *VIP:&lt;číslo portu >* pro příchozí komunikaci. Rezervace IP neznamená, že přímo připojit k virtuálnímu počítači. Vyhrazená IP adresa je přiřazený k cloudovou službu, která byla nasazena do virtuálního počítače. Pokud se chcete připojit k virtuálnímu počítači pomocí IP přímo, budete muset nakonfigurovat úrovni instance veřejnou IP adresu. Veřejná IP adresa úrovni instance je typ veřejné IP (nazývané splnění), který je přiřazen přímo k virtuálnímu počítači. Nelze rezervovat. Další informace najdete v tématu [úrovni Instance veřejné IP splnění](virtual-networks-instance-level-public-ip.md) článku.
> 

## <a name="remove-a-reserved-ip-from-a-running-deployment"></a>Odebrání spuštěného nasazení vyhrazená IP adresa
Chcete-li odebrat vyhrazenou IP adresu přidat do novou cloudovou službu, spusťte následující příkaz prostředí PowerShell:

```powershell
Remove-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService
```

> [!NOTE]
> Odebrání vyhrazenou IP adresu z spuštěného nasazení neodebere rezervace ze svého předplatného. Uvolní jednoduše IP, který bude používán jiným prostředkem v rámci vašeho předplatného.
> 

## <a name="associate-a-reserved-ip-to-a-running-deployment"></a>Přidružení vyhrazené IP adresy na spuštěného nasazení
Následující příkazy vytvoření cloudové služby s názvem *TestService2* s nový virtuální počítač s názvem *TestVM2*. Existující vyhrazené IP adresy s názvem *MyReservedIP* je pak přidružený ke cloudové službě.

```powershell
$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM2 -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService2 -Location "Central US"

Set-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService2
```

## <a name="associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file"></a>Pomocí konfigurační soubor služby přidružit vyhrazenou IP adresu do cloudové služby
Vyhrazená IP adresa v cloudové službě lze přiřadit pomocí souboru konfigurace (CSCFG) služby. Následující ukázkový kód xml ukazuje, jak nakonfigurovat cloudovou službu používat vyhrazené virtuální IP adresy s názvem *MyReservedIP*:

    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
        </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <AddressAssignments>
          <ReservedIPs>
           <ReservedIP name="MyReservedIP"/>
          </ReservedIPs>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>

## <a name="next-steps"></a>Další postup
* Pochopit, jak [IP adresování](virtual-network-ip-addresses-overview-classic.md) funguje v modelu nasazení classic.
* Další informace o [vyhrazené soukromé IP adresy](virtual-networks-reserved-private-ip.md).
* Další informace o [Instance úroveň veřejné IP splnění adresy](virtual-networks-instance-level-public-ip.md).

