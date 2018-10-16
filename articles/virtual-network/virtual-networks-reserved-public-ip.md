---
title: Správa Azure rezervované IP adresy (klasické) | Dokumentace Microsoftu
description: Vysvětlení rezervované IP adresy (klasické) a jak je spravovat pomocí Azure Powershellu a rozhraní příkazového řádku Azure.
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
ms.date: 10/12/2018
ms.author: genli
ms.openlocfilehash: df48e0dbf5a6c010f659e1019e56b7670c264234
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2018
ms.locfileid: "49319689"
---
# <a name="reserved-ip-addresses-classic-deployment"></a>Rezervované IP adresy (klasické nasazení)

 IP adresy v Azure spadají do dvou kategorií: dynamické a rezervované. Veřejné IP adresy, které spravuje Azure jsou ve výchozím nastavení dynamické. To znamená, že IP adresa použitá pro dané cloudové služby (VIP) nebo pro přístup k virtuálnímu počítači nebo přímo role instance (ILPIP) můžete změnit čas od času, když jsou prostředky vypnout nebo zastaveno (přidělení zrušeno).

Chcete-li zabránit ve změně IP adresy, si můžete rezervovat IP adresu. Rezervované IP adresy lze použít pouze jako virtuální IP adresy, zajištění, že IP adresa pro cloudovou službu zůstává stejné, i když jsou prostředky vypnutý nebo zastaveno (přidělení zrušeno). Kromě toho můžete převést stávající dynamické IP adresy použít jako virtuální IP adresu pro rezervovanou IP adresu.

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi: [Resource Manager a klasický model](../azure-resource-manager/resource-manager-deployment-model.md). Tento článek se věnuje použití klasického modelu nasazení. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Zjistěte, jak rezervovat statické veřejné IP adresy using [modelu nasazení Resource Manager](virtual-network-ip-addresses-overview-arm.md).

Další informace o IP adresách v Azure najdete v článku [IP adresy](virtual-network-ip-addresses-overview-classic.md) článku.

## <a name="when-do-i-need-a-reserved-ip"></a>Kdy je potřeba vyhrazená IP adresa?
* **Chcete mít jistotu, že IP adresa je vyhrazena ve vašem předplatném**. Pokud chcete rezervovat IP adresu, která se neuvolní ze svého předplatného za žádných okolností, měli byste použít vyhrazené veřejné IP adresy.  
* **Chcete, aby vaše IP adresa se cloudové služby i napříč zastavena nebo uvolnit stavu (VM)**. Pokud má vaše služba přístup s použitím IP adresy, který nemění, i když virtuální počítače v cloudové službě se vypnout nebo zastaveno (přidělení zrušeno).
* **Chcete zajistit, aby používal odchozího provozu z Azure předvídatelné IP adresu**. Možná bude vaše místní bráně firewall nakonfigurované tak, aby provoz jenom z konkrétních IP adres. Rezervací integrační balíček, znát IP adresu zdroje a není potřeba aktualizovat pravidla brány firewall kvůli o změnu IP.

## <a name="faqs"></a>Nejčastější dotazy
- Můžete použít vyhrazenou IP adresu pro všechny služby Azure?
    Ne. Rezervované IP adresy jde použít jenom pro virtuální počítače a instance rolí cloudové služby prostřednictvím virtuální IP adresu.
- Počet vyhrazených IP adres může mít?
    Podrobnosti najdete v tématu [omezeních Azure](../azure-subscription-service-limits.md#networking-limits) článku.
- Platí se za pro rezervované IP adresy?
    V některých případech. Podrobnosti o cenách najdete v článku [rezervované IP adresy – podrobnosti o cenách](http://go.microsoft.com/fwlink/?LinkID=398482) stránky.
- Jak můžu rezervovat IP adresu?
    Můžete použít PowerShell, [REST API pro správu Azure](https://msdn.microsoft.com/library/azure/dn722420.aspx), nebo [webu Azure portal](https://portal.azure.com) rezervovat IP adresu v oblasti Azure. Vyhrazená IP adresa je přidružená k vašemu předplatnému.
- Můžete použít vyhrazenou IP adresu pomocí virtuální sítě založené na skupinu vztahů?
    Ne. Rezervované IP adresy se podporují jenom v regionální virtuální sítě. Rezervované IP adresy nejsou podporovány pro virtuální sítě, které jsou přidružené skupiny vztahů. Další informace o přidružování oblast nebo skupina vztahů virtuální sítě, najdete v článku [o regionální virtuální sítě a skupin vztahů](virtual-networks-migrate-to-regional-vnet.md) článku.

## <a name="manage-reserved-vips"></a>Spravovat vyhrazené virtuální IP adresy

### <a name="using-azure-powershell-classic"></a>Pomocí prostředí Azure PowerShell (classic)

Před použitím vyhrazené IP adresy, je nutné přidat do vašeho předplatného. Vytvořit vyhrazenou IP adresu z fondu veřejných IP adres k dispozici v *USA (střed)* umístění následujícím způsobem:

> [!NOTE]
> Pro model nasazení classic musíte nainstalovat verzi správy služeb prostředí Azure PowerShell. Další informace najdete v tématu popisujícím [instalaci modulu správy služeb Azure PowerShellu](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0). 

  ```powershell
    New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"
  ```
Všimněte si však, že nelze zadat, co se ještě IP vyhrazena. Chcete-li zobrazit, jaké IP adresy jsou vyhrazené ve vašem předplatném, spusťte následující příkaz prostředí PowerShell a Všimněte si hodnot pro *ReservedIPName* a *adresu*:

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
>Když vytvoříte vyhrazené IP adresy pomocí prostředí PowerShell, nelze zadat skupinu prostředků vytvořit vyhrazenou IP adresu v. Azure umístí do skupiny prostředků s názvem *výchozí sítě* automaticky. Pokud vytvoříte rezervované IP používat [webu Azure portal](http://portal.azure.com), můžete zadat všechny skupiny prostředků, které zvolíte. Pokud vytvoříte vyhrazenou IP adresu ve skupině prostředků než *výchozí sítě* však vždy, když odkazujete vyhrazenou IP adresu s příkazy, jako `Get-AzureReservedIP` a `Remove-AzureReservedIP`, musí odkazovat na název  *Vyhrazené resource-group-name-ip název skupiny*.  Například můžete vytvořit vyhrazenou IP adresu s názvem *myReservedIP* ve skupině prostředků s názvem *myResourceGroup*, musí odkazovat na název vyhrazené IP adresy jako *myResourceGroup skupiny myReservedIP*.   


Jakmile je vyhrazené IP adresy, zůstane přidružený k vašemu předplatnému, dokud je neodstraníte. Odstraňte vyhrazenou IP adresu následujícím způsobem:

```powershell
Remove-AzureReservedIP -ReservedIPName "MyReservedIP"
```

### <a name="using-azure-cli-classic"></a>Pomocí rozhraní příkazového řádku Azure (classic)
Vytvořit vyhrazenou IP adresu z fondu veřejných IP adres k dispozici v *USA (střed)* umístění jako Azure pomocí rozhraní příkazového řádku classic řídí:

> [!NOTE]
> Pro nasazení classic je nutné použít Azure classic CLI. Informace o instalaci Azure classic CLI najdete v tématu [instalace Azure classic CLI](https://docs.microsoft.com/cli/azure/install-classic-cli?view=azure-cli-latest)
  
 Příkaz:
 
```azurecli
azure network reserved-ip create <name> <location>
```
Příklad:
 ```azurecli
 azure network reserved-ip create MyReservedIP centralus
 ```

Můžete zobrazit, jaké IP adresy jsou vyhrazené ve vašem předplatném pomocí rozhraní příkazového řádku Azure následujícím způsobem: 

Příkaz:
```azurecli
azure network reserved-ip list
```
Jakmile je vyhrazené IP adresy, zůstane přidružený k vašemu předplatnému, dokud je neodstraníte. Odstraňte vyhrazenou IP adresu následujícím způsobem:

Příkaz:

 ```azurecli
 azure network reserved-ip delete <name>
 ```
  Příklad:  
 ```azurecli
 azure network reserved-ip delete MyReservedIP
 ```
## <a name="reserve-the-ip-address-of-an-existing-cloud-service"></a>Vyhrazení IP adresy existující cloudové služby
Můžete vyhrazení IP adresy existující cloudové služby tak, že přidáte `-ServiceName` parametru. Vyhrazení IP adresy cloudové služby *TestService* v *USA (střed)* umístění následujícím způsobem:

- Pomocí prostředí Azure PowerShell (classic):

  ```powershell
  New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US" -ServiceName TestService
  ```

- Pomocí rozhraní příkazového řádku Azure (classic):
  
    Příkaz:

    ```azurecli
     azure network reserved-ip create <name> <location> -r <service-name> -d <deployment-name>
    ```
    Příklad:

    ```azurecli
      azure network reserved-ip create MyReservedIP centralus -r TestService -d asmtest8942
    ```

## <a name="associate-a-reserved-ip-to-a-new-cloud-service"></a>Přidružení vyhrazené IP adresy k nové cloudové služby
Následující skript vytvoří nové vyhrazené IP adresy a přidruží ji k nové cloudové službě s názvem *TestService*.

### <a name="using-azure-powershell-classic"></a>Pomocí prostředí Azure PowerShell (classic)
```powershell
New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService -ReservedIPName MyReservedIP -Location "Central US"
```
> [!NOTE]
> Při vytváření vyhrazená IP adresa pro použití s cloudovou službu, můžete stále odkazují k virtuálnímu počítači pomocí *virtuálních IP adres:&lt;číslo portu >* pro příchozí komunikaci. Vyhrazení IP neznamená, že se že přímo připojit k virtuálnímu počítači. Rezervovaná IP adresa se přiřadí ke cloudové službě, která byla nasazena do virtuálního počítače. Pokud chcete připojit přímo k virtuálnímu počítači pomocí protokolu IP, budete muset nakonfigurovat veřejnou IP adresu na úrovni instance. Veřejná IP adresa na úrovni instance je typu veřejné IP adresy (označované jako ILPIP), který je přiřazen přímo k virtuálnímu počítači. Nelze rezervovat. Další informace najdete v článku [veřejné IP na úrovni Instance (ILPIP)](virtual-networks-instance-level-public-ip.md) článku.
> 

## <a name="remove-a-reserved-ip-from-a-running-deployment"></a>Odstranit vyhrazenou IP adresu v běžícím nasazení

Odstraňte vyhrazenou IP adresu přidat do nové cloudové službě následujícím způsobem: 
### <a name="using-azure-powershell-classic"></a>Pomocí prostředí Azure PowerShell (classic)

```powershell
Remove-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService
```

### <a name="using-azure-cli-classic"></a>Pomocí rozhraní příkazového řádku Azure (classic)
Příkaz:

```azurecli
azure network reserved-ip disassociate <name> <service-name> <deployment-name>
```

Příklad:

```azurecli
azure network reserved-ip disassociate MyReservedIP TestService asmtest8942
```

> [!NOTE]
> Odebrání vyhrazená IP adresa v běžícím nasazení neodebere rezervace z vašeho předplatného. Jednoduše uvolnění IP má být použit jiným prostředkem ve vašem předplatném.
> 

Úplné odebrání vyhrazenou IP adresu z předplatného, spusťte následující příkaz:

Příkaz:

```azurecli
azure network reserved-ip delete <name>
```
Příklad:

```azurecli
azure network reserved-ip delete MyReservedIP
```

## <a name="associate-a-reserved-ip-to-a-running-deployment"></a>Přidružení vyhrazené IP adresy na běžícím nasazení

### <a name="using-azure-powershell-classic"></a>Pomocí prostředí Azure PowerShell (classic)

Následující příkazy vytvoří cloudovou službu s názvem *TestService2* pomocí nového virtuálního počítače s názvem *TestVM2*. Existující vyhrazené IP s názvem *MyReservedIP* je pak přidružené ke cloudové službě.

```powershell
$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM2 -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService2 -Location "Central US"

Set-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService2
```

### <a name="using-azure-cli-classic"></a>Pomocí rozhraní příkazového řádku Azure (classic)
Novou vyhrazenou IP adresu můžete přidružit jen k vaší spuštěné nasazení cloudové služby pomocí rozhraní příkazového řádku Azure následujícím způsobem:

Příkaz:
```azurecli
azure network reserved-ip associate <name> <service-name> <deployment-name>
```
Příklad:
```azurecli
azure network reserved-ip associate MyReservedIP TestService asmtest8942
```
## <a name="associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file"></a>Přidružení vyhrazené IP adresy pro cloudové služby pomocí konfiguračního souboru služby
Vyhrazená IP adresa cloudové služby můžete přiřadit také pomocí souboru konfigurace (CSCFG) služby. Následující ukázkový kód xml ukazuje, jak nakonfigurovat cloudovou službu používat vyhrazené virtuální IP adresy s názvem *MyReservedIP*:
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
## <a name="next-steps"></a>Další postup
* Pochopit, jak [přidělování IP adres](virtual-network-ip-addresses-overview-classic.md) funguje v modelu nasazení classic.
* Další informace o [vyhrazené privátní IP adresy](virtual-networks-reserved-private-ip.md).
* Další informace o [Instance úroveň veřejné IP (ILPIP) řeší](virtual-networks-instance-level-public-ip.md).

