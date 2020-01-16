---
title: Správa rezervovaných IP adres Azure (Classic) | Microsoft Docs
description: Pochopení rezervovaných IP adres (Classic) a způsobu jejich správy pomocí Azure PowerShell a Azure CLI.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
ms.assetid: 34652a55-3ab8-4c2d-8fb2-43684033b191
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: genli
ms.openlocfilehash: 5bcbb8c6845928cf21b5b6a1c04f609305bd7983
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979528"
---
# <a name="reserved-ip-addresses-classic-deployment"></a>Adresy Vyhrazená IP adresa (nasazení Classic)

 IP adresy v Azure spadají do dvou kategorií: dynamická a rezervovaná. Veřejné IP adresy, které spravuje Azure, jsou ve výchozím nastavení dynamické. To znamená, že IP adresa použitá pro danou cloudovou službu (VIP) nebo přímý přístup k virtuálnímu počítači nebo instanci role (ILPIP) se může kdykoli změnit, když se prostředky vypnou nebo zastaví (přidělení zrušeno).

Pokud chcete zabránit změnám IP adres, můžete si vyhradit IP adresu. Rezervované IP adresy se dají použít jenom jako VIP, což zajistí, že IP adresa pro cloudovou službu zůstane stejná i po vypnutí nebo zastavení prostředků (přidělení zrušeno). Kromě toho můžete převést existující dynamické IP adresy používané jako VIP na vyhrazenou IP adresu.

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi: [Resource Manager a klasický model](../azure-resource-manager/management/deployment-models.md). Tento článek se věnuje použití klasického modelu nasazení. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Přečtěte si, jak vyhradit statickou veřejnou IP adresu pomocí [modelu nasazení Správce prostředků](virtual-network-ip-addresses-overview-arm.md).

Další informace o IP adresách v Azure najdete v článku [IP adresy](virtual-network-ip-addresses-overview-classic.md) .

## <a name="when-do-i-need-a-reserved-ip"></a>Kdy potřebuji rezervovanou IP adresu?
* **Chcete zajistit, aby byla IP adresa vyhrazena v rámci vašeho předplatného**. Pokud chcete rezervovat IP adresu, která se v rámci vašeho předplatného neuvolní za žádných okolností, měli byste použít vyhrazenou veřejnou IP adresu.  
* **Chcete, aby vaše IP adresa zůstala v cloudové službě i ve stavu zastaveno nebo zrušeno přidělení (VM)** . Pokud chcete, aby služba byla dostupná pomocí IP adresy, která se nemění, i když jsou virtuální počítače v cloudové službě vypnuté nebo zastavené (přidělení zrušeno).
* **Chcete zajistit, aby odchozí přenosy z Azure používaly předvídatelné IP adresy**. Je možné, že máte nakonfigurovanou místní bránu firewall, aby povolovala jenom přenosy z konkrétních IP adres. Když zachováte IP adresu, znáte zdrojovou IP adresu a nemusíte kvůli změně IP adresy aktualizovat pravidla brány firewall.

## <a name="faqs"></a>Nejčastější dotazy
- Můžu použít rezervovanou IP adresu pro všechny služby Azure?
    Ne. Rezervované IP adresy se dají používat jenom pro virtuální počítače a role instancí cloudové služby, které jsou vystavené prostřednictvím virtuální IP adresy.
- Kolik rezervovaných IP adres můžu mám?
    Podrobnosti najdete v článku o [omezeních Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) .
- Účtuje se za rezervované IP adresy poplatky?
    Označovan. Podrobnosti o cenách najdete na stránce s [podrobnostmi o cenách vyhrazená IP adresa adres](https://go.microsoft.com/fwlink/?LinkID=398482) .
- Návody rezervovat IP adresu?
    K rezervaci IP adresy v oblasti Azure můžete použít PowerShell, [REST API pro správu Azure](https://msdn.microsoft.com/library/azure/dn722420.aspx)nebo [Azure Portal](https://portal.azure.com) . Rezervovaná IP adresa je přidružená k vašemu předplatnému.
- Můžu použít vyhrazenou IP adresu se virtuální sítě založenými na skupině vztahů?
    Ne. Rezervované IP adresy se podporují jenom v regionálních virtuální sítě. Rezervované IP adresy nejsou podporované pro virtuální sítě, které jsou přidružené ke skupinám vztahů. Další informace o tom, jak přidružit virtuální síť k oblasti nebo skupině vztahů, najdete v článku o oblastech region [virtuální sítě a příbuznosti](virtual-networks-migrate-to-regional-vnet.md) .

## <a name="manage-reserved-vips"></a>Správa rezervovaných virtuálních IP adres

### <a name="using-azure-powershell-classic"></a>Použití Azure PowerShell (Classic)

Než budete moct použít rezervované IP adresy, musíte je přidat do svého předplatného. Vytvořte vyhrazenou IP adresu z fondu veřejných IP adres, které jsou k dispozici v umístění *střed USA* následujícím způsobem:

> [!NOTE]
> Pro model nasazení Classic je nutné nainstalovat Azure PowerShell verze správy služby. Další informace najdete v tématu popisujícím [instalaci modulu správy služeb Azure PowerShellu](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0). 

  ```powershell
    New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"
  ```
Všimněte si ale, že nemůžete určit, jaká IP adresa je rezervovaná. Pokud chcete zobrazit, jaké IP adresy jsou rezervované ve vašem předplatném, spusťte následující příkaz PowerShellu a Všimněte si hodnot pro *ReservedIPName* a *Address*:

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
>Při vytváření rezervované IP adresy pomocí PowerShellu nemůžete zadat skupinu prostředků, ve které se vytvoří vyhrazená IP adresa. Azure ho umístí do skupiny prostředků s názvem *výchozí-síť* automaticky. Pokud vytvoříte vyhrazenou IP adresu pomocí [Azure Portal](https://portal.azure.com), můžete zadat jakoukoli skupinu prostředků, kterou zvolíte. Pokud vytvoříte vyhrazenou IP adresu ve skupině prostředků, která je jiná než *výchozí-síť* , kdykoli ale na vyhrazenou IP adresu odkazujete pomocí příkazů, jako jsou `Get-AzureReservedIP` a `Remove-AzureReservedIP`, musíte odkazovat na název *skupiny Resource-Group-Name rezervovaný-IP-Name*.  Pokud například vytvoříte rezervovanou IP adresu s názvem *myReservedIP* ve skupině prostředků s názvem *myResourceGroup*, musíte odkazovat na název vyhrazené IP adresy jako *skupiny myResourceGroup myReservedIP*.   


Jakmile je IP adresa rezervovaná, zůstane přidružená k vašemu předplatnému, dokud ji neodstraníte. Vyhrazenou IP adresu odstraníte následujícím způsobem:

```powershell
Remove-AzureReservedIP -ReservedIPName "MyReservedIP"
```

### <a name="using-azure-cli-classic"></a>Použití rozhraní příkazového řádku Azure (Classic)
Vytvořte vyhrazenou IP adresu z fondu veřejných IP adres, které jsou k dispozici v umístění *střed USA* pomocí Azure Classic CLI:

> [!NOTE]
> Pro nasazení v klasickém prostředí musíte použít Azure Classic CLI. Informace o instalaci rozhraní příkazového řádku Azure Classic najdete v tématu [instalace rozhraní příkazového řádku Azure Classic](https://docs.microsoft.com/cli/azure/install-classic-cli?view=azure-cli-latest) .
  
 Příkaz:
 
```azurecli
azure network reserved-ip create <name> <location>
```
Příklad:
 ```azurecli
 azure network reserved-ip create MyReservedIP centralus
 ```

Pomocí Azure CLI můžete zobrazit, jaké IP adresy jsou rezervované v předplatném, a to následujícím způsobem: 

Příkaz:
```azurecli
azure network reserved-ip list
```
Jakmile je IP adresa rezervovaná, zůstane přidružená k vašemu předplatnému, dokud ji neodstraníte. Vyhrazenou IP adresu odstraníte následujícím způsobem:

Příkaz:

 ```azurecli
 azure network reserved-ip delete <name>
 ```
  Příklad:  
 ```azurecli
 azure network reserved-ip delete MyReservedIP
 ```
## <a name="reserve-the-ip-address-of-an-existing-cloud-service"></a>Vyhrazení IP adresy existující cloudové služby
IP adresu existující cloudové služby můžete rezervovat přidáním parametru `-ServiceName`. IP adresu *TestService* cloudové služby vyhraďte v umístění *střed USA* následujícím způsobem:

- Použití Azure PowerShell (Classic):

  ```powershell
  New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US" -ServiceName TestService
  ```

- Použití rozhraní příkazového řádku Azure (Classic):
  
    Příkaz:

    ```azurecli
     azure network reserved-ip create <name> <location> -r <service-name> -d <deployment-name>
    ```
    Příklad:

    ```azurecli
      azure network reserved-ip create MyReservedIP centralus -r TestService -d asmtest8942
    ```

## <a name="associate-a-reserved-ip-to-a-new-cloud-service"></a>Přidružení rezervované IP adresy k nové cloudové službě
Následující skript vytvoří novou rezervovanou IP adresu a přidruží ji k nové cloudové službě s názvem *TestService*.

### <a name="using-azure-powershell-classic"></a>Použití Azure PowerShell (Classic)
```powershell
New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService -ReservedIPName MyReservedIP -Location "Central US"
```
> [!NOTE]
> Při vytváření rezervované IP adresy pro použití s cloudovou službou se i nadále odkazujete na virtuální počítač pomocí *VIP:&lt;číslo portu >* pro příchozí komunikaci. Vyhrazení IP adres neznamená, že se k virtuálnímu počítači můžete připojit přímo. Rezervovaná IP adresa se přiřadí ke cloudové službě, do které byl virtuální počítač nasazen. Pokud se chcete připojit k virtuálnímu počítači přímo pomocí protokolu IP, musíte nakonfigurovat veřejnou IP adresu na úrovni instance. Veřejná IP adresa na úrovni instance je typ veřejné IP adresy (označované jako ILPIP), který je přiřazený přímo k vašemu VIRTUÁLNÍmu počítači. Nedá se rezervovat. Další informace najdete v článku o [Veřejná IP adresa na úrovni instance (ILPIP)](virtual-networks-instance-level-public-ip.md) .
> 

## <a name="remove-a-reserved-ip-from-a-running-deployment"></a>Odebrání rezervované IP adresy z běžícího nasazení

Odeberte vyhrazenou IP adresu přidanou do nové cloudové služby následujícím způsobem: 
### <a name="using-azure-powershell-classic"></a>Použití Azure PowerShell (Classic)

```powershell
Remove-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService
```

### <a name="using-azure-cli-classic"></a>Použití rozhraní příkazového řádku Azure (Classic)
Příkaz:

```azurecli
azure network reserved-ip disassociate <name> <service-name> <deployment-name>
```

Příklad:

```azurecli
azure network reserved-ip disassociate MyReservedIP TestService asmtest8942
```

> [!NOTE]
> Odebráním rezervované IP adresy z běžícího nasazení nedojde k odebrání rezervace z předplatného. Tuto IP adresu jenom uvolní, aby ji používal jiný prostředek ve vašem předplatném.
> 

K úplnému odebrání rezervované IP adresy z předplatného spusťte následující příkaz:

Příkaz:

```azurecli
azure network reserved-ip delete <name>
```
Příklad:

```azurecli
azure network reserved-ip delete MyReservedIP
```

## <a name="associate-a-reserved-ip-to-a-running-deployment"></a>Přidružení rezervované IP adresy k běžícímu nasazení

### <a name="using-azure-powershell-classic"></a>Použití Azure PowerShell (Classic)

Následující příkazy vytvoří cloudovou službu s názvem *TestService2* s novým virtuálním počítačem s názvem *TestVM2*. Existující vyhrazená IP adresa s názvem *MyReservedIP* je pak přidružená ke cloudové službě.

```powershell
$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM2 -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService2 -Location "Central US"

Set-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService2
```

### <a name="using-azure-cli-classic"></a>Použití rozhraní příkazového řádku Azure (Classic)
K nasazení cloudové služby můžete přidružit novou rezervovanou IP adresu pomocí Azure CLI následujícím způsobem:

Příkaz:
```azurecli
azure network reserved-ip associate <name> <service-name> <deployment-name>
```
Příklad:
```azurecli
azure network reserved-ip associate MyReservedIP TestService asmtest8942
```
## <a name="associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file"></a>Přidružení rezervované IP adresy ke cloudové službě pomocí konfiguračního souboru služby
Vyhrazenou IP adresu můžete také přidružit ke cloudové službě pomocí souboru konfigurace služby (CSCFG). Následující vzorový kód XML ukazuje, jak nakonfigurovat cloudovou službu pro použití rezervované virtuální IP adresy s názvem *MyReservedIP*:
```
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
```
## <a name="next-steps"></a>Další kroky
* Pochopte, jak [přidělování IP adres](virtual-network-ip-addresses-overview-classic.md) funguje v modelu nasazení Classic.
* Přečtěte si o [vyhrazených privátních IP adresách](virtual-networks-reserved-private-ip.md).
* Přečtěte si informace o [veřejných IP adresách na úrovni instance (ILPIP)](virtual-networks-instance-level-public-ip.md).

