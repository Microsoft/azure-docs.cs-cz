---
title: 'Odstranit bránu virtuální sítě: prostředí PowerShell: portál Azure classic | Microsoft Docs'
description: Odstraňte bránu virtuální sítě pomocí prostředí PowerShell v modelu nasazení classic.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: cherylmc
ms.openlocfilehash: b1bc18307227a728e2bc8fd95e30fdc1cbdb8c59
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2017
ms.locfileid: "24994657"
---
# <a name="delete-a-virtual-network-gateway-using-powershell-classic"></a>Odstranit bránu virtuální sítě pomocí prostředí PowerShell (klasické)
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [Classic – prostředí PowerShell](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

Tento článek vám umožňuje odstranit bránu sítě VPN v klasickém modelu nasazení pomocí prostředí PowerShell. Po odstranění brány virtuální sítě, upravte konfigurační soubor sítě. Chcete-li odebrat prvky, které už používáte.

##<a name="connect"></a>Krok 1: Připojení k Azure

### <a name="1-install-the-latest-powershell-cmdlets"></a>1. Nainstalujte nejnovější rutiny prostředí PowerShell.

Stáhněte a nainstalujte nejnovější verzi rutin prostředí PowerShell Azure Service Management (SM). Další informace najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview).

### <a name="2-connect-to-your-azure-account"></a>2. Připojte k účtu Azure. 

Otevřete konzolu PowerShellu se zvýšenými oprávněními a připojte se ke svému účtu. Připojení vám usnadní následující ukázka:

```powershell
Add-AzureAccount
```

## <a name="export"></a>Krok 2: Export a v souboru konfigurace sítě

Vytvořte ve svém počítači adresář a potom do něj exportujte soubor konfigurace sítě. Můžete použít tento soubor do obou zobrazení aktuální informace o konfiguraci a také ke změně konfigurace sítě.

V tomto příkladu se soubor konfigurace sítě exportuje do adresáře C:\AzureNet.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

Otevřete soubor v textovém editoru a zobrazit název pro klasické virtuální síti. Když vytvoříte virtuální síť na portálu Azure, úplný název, který používá Azure není zobrazená v portálu. Například virtuální sítě, který se zobrazí s názvem 'ClassicVNet1' na portálu Azure může mít mnoho delší název v souboru konfigurace sítě. Název může vypadat podobně jako: 'Skupiny ClassicRG1 ClassicVNet1'. Názvy virtuální sítě jsou uvedeny jako **' VirtualNetworkSite name ='**. Použije názvy v konfiguračním souboru sítě při spuštění vaše rutiny prostředí PowerShell.

## <a name="delete"></a>Krok 3: Odstranit bránu virtuální sítě

Při odstranění brány virtuální sítě jsou odpojené všech připojení k virtuální síti prostřednictvím brány. Pokud máte P2S klientů připojených k virtuální síti, budou odpojeny bez upozornění.

Tento příklad odstraní bránu virtuální sítě. Nezapomeňte použít úplný název virtuální sítě z konfiguračního souboru sítě.

```powershell
Remove-AzureVNetGateway -VNetName "Group ClassicRG1 ClassicVNet1"
```

V případě úspěšného návratový uvádí:

```
Status : Successful
```

## <a name="modify"></a>Krok 4: Upravte konfigurační soubor sítě.

Pokud odstraníte bránu virtuální sítě, rutina neupravuje konfiguračního souboru sítě. Budete muset upravit soubor odebrat prvky, které se již nepoužívají. V následujících částech můžete upravit konfiguračního souboru sítě, který jste si stáhli.

### <a name="lnsref"></a>Odkazů na web místní sítě

Odebrat informace o referenční lokality, provést změny konfigurace **ConnectionsToLocalNetwork/LocalNetworkSiteRef**. Odebrání místní lokality odkaz aktivačních událostí Azure a odstranit přes tunelové propojení. V závislosti na konfiguraci, kterou jste vytvořili, nemusí mít **LocalNetworkSiteRef** uvedené.

```
<Gateway>
   <ConnectionsToLocalNetwork>
     <LocalNetworkSiteRef name="D1BFC9CB_Site2">
       <Connection type="IPsec" />
     </LocalNetworkSiteRef>
   </ConnectionsToLocalNetwork>
 </Gateway>
```

Příklad:

```
<Gateway>
   <ConnectionsToLocalNetwork>
   </ConnectionsToLocalNetwork>
 </Gateway>
```

###<a name="lns"></a>Místní sítě

Odeberte všechny místní servery, které jsou již nepoužívá. V závislosti na konfiguraci, které jste vytvořili, je možné, že nemáte **LocalNetworkSite** uvedené.

```
<LocalNetworkSites>
  <LocalNetworkSite name="Site1">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>5.4.3.2</VPNGatewayAddress>
  </LocalNetworkSite>
  <LocalNetworkSite name="Site3">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>57.179.18.164</VPNGatewayAddress>
  </LocalNetworkSite>
 </LocalNetworkSites>
```

V tomto příkladu jsme odebrali pouze Site3.

```
<LocalNetworkSites>
  <LocalNetworkSite name="Site1">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>5.4.3.2</VPNGatewayAddress>
  </LocalNetworkSite>
 </LocalNetworkSites>
```

### <a name="clientaddresss"></a>Fond adres klienta

Pokud jste měli P2S připojení k virtuální síti, budete mít **VPNClientAddressPool**. Odeberte fondy adres klienta, které odpovídají bránu virtuální sítě, kterou jste odstranili.

```
<Gateway>
    <VPNClientAddressPool>
      <AddressPrefix>10.1.0.0/24</AddressPrefix>
    </VPNClientAddressPool>
  <ConnectionsToLocalNetwork />
 </Gateway>
```

Příklad:

```
<Gateway>
  <ConnectionsToLocalNetwork />
 </Gateway>
```

### <a name="gwsub"></a>GatewaySubnet

Odstranit **GatewaySubnet** odpovídající virtuální sítě.

```
<Subnets>
   <Subnet name="FrontEnd">
     <AddressPrefix>10.11.0.0/24</AddressPrefix>
   </Subnet>
   <Subnet name="GatewaySubnet">
     <AddressPrefix>10.11.1.0/29</AddressPrefix>
   </Subnet>
 </Subnets>
```

Příklad:

```
<Subnets>
   <Subnet name="FrontEnd">
     <AddressPrefix>10.11.0.0/24</AddressPrefix>
   </Subnet>
 </Subnets>
```

## <a name="upload"></a>Krok 5: Nahrát soubor konfigurace sítě

Změny uložte a nahrajte konfiguračního souboru sítě do Azure. Ujistěte se, že změníte cestu k souboru v případě potřeby pro vaše prostředí.

```powershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

V případě úspěšného návratový ukazuje něco podobně jako tento příklad:

```
OperationDescription        OperationId                      OperationStatus                                                
--------------------        -----------                      ---------------                                           
Set-AzureVNetConfig         e0ee6e66-9167-cfa7-a746-7casb9   Succeeded