---
title: 'Odstranění brány virtuální sítě: Azure classic'
description: Odstraňte bránu virtuální sítě pomocí PowerShellu v klasickém modelu nasazení.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: cherylmc
ms.openlocfilehash: e7283f5e28edc6f7beaad3a2743aa155f6ea6e14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198645"
---
# <a name="delete-a-virtual-network-gateway-using-powershell-classic"></a>Odstranění brány virtuální sítě pomocí PowerShellu (klasické)

> [!div class="op_single_selector"]
> * [Správce prostředků – portál Azure](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [Classic - PowerShell](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>

Tento článek vám pomůže odstranit bránu VPN v klasickém modelu nasazení pomocí Prostředí PowerShell. Po odstranění brány virtuální sítě upravte konfigurační soubor sítě a odeberte prvky, které již nepoužíváte.

## <a name="step-1-connect-to-azure"></a><a name="connect"></a>Krok 1: Připojení k Azure

### <a name="1-install-the-latest-powershell-cmdlets"></a>1. Nainstalujte nejnovější rutiny prostředí PowerShell.

[!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

### <a name="2-connect-to-your-azure-account"></a>2. Připojte se ke svému účtu Azure.

Otevřete konzolu PowerShellu se zvýšenými oprávněními a připojte se ke svému účtu. Připojení vám usnadní následující ukázka:

1. Otevřete konzolu PowerShell se zvýšenými právy. Chcete-li přepnout na správu služeb, použijte tento příkaz:

   ```powershell
   azure config mode asm
   ```
2. Připojte se ke svému účtu. Připojení vám usnadní následující ukázka:

   ```powershell
   Add-AzureAccount
   ```

## <a name="step-2-export-and-view-the-network-configuration-file"></a><a name="export"></a>Krok 2: Export a zobrazení konfiguračního síťového souboru

Vytvořte ve svém počítači adresář a potom do něj exportujte soubor konfigurace sítě. Tento soubor slouží k zobrazení aktuálních informací o konfiguraci a také k úpravě konfigurace sítě.

V tomto příkladu se soubor konfigurace sítě exportuje do adresáře C:\AzureNet.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

Otevřete soubor s textovým editorem a zobrazte název klasické virtuální sítě. Když vytvoříte virtuální síť na webu Azure Portal, úplný název, který Azure používá, není na portálu viditelný. Například virtuální síť, která se zobrazí s názvem "ClassicVNet1" na webu Azure Portal, může mít mnohem delší název v konfiguračním souboru sítě. Název může vypadat nějak takto: "Skupina ClassicRG1 ClassicVNet1". Názvy virtuálních sítí jsou uvedeny jako **název VirtualNetworkSite ='**. Při spuštění rutin prostředí PowerShell použijte názvy v konfiguračním síťovém souboru.

## <a name="step-3-delete-the-virtual-network-gateway"></a><a name="delete"></a>Krok 3: Odstranění brány virtuální sítě

Když odstraníte bránu virtuální sítě, všechna připojení k virtuální síti prostřednictvím brány se odpojí. Pokud máte klienty P2S připojené k virtuální síti, budou odpojeni bez upozornění.

Tento příklad odstraní bránu virtuální sítě. Ujistěte se, že používáte úplný název virtuální sítě z konfiguračního souboru sítě.

```powershell
Remove-AzureVNetGateway -VNetName "Group ClassicRG1 ClassicVNet1"
```

V případě úspěchu se na vrácení zobrazí:

```
Status : Successful
```

## <a name="step-4-modify-the-network-configuration-file"></a><a name="modify"></a>Krok 4: Úprava konfiguračního síťového souboru

Když odstraníte bránu virtuální sítě, rutina nezmění konfigurační soubor sítě. Chcete-li odebrat prvky, které se již nepoužívají, je třeba soubor upravit. Následující části vám pomohou upravit stažený konfigurační soubor sítě.

### <a name="local-network-site-references"></a><a name="lnsref"></a>Odkazy na lokality místní sítě

Chcete-li odebrat referenční informace o lokalitě, proveďte změny konfigurace **v programu ConnectionsToLocalNetwork/LocalNetworkSiteRef**. Odebrání odkazu na místní web spustí Azure k odstranění tunelového propojení. V závislosti na konfiguraci, kterou jste vytvořili, pravděpodobně nemáte v seznamu **LocalNetworkSiteRef.**

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

### <a name="local-network-sites"></a><a name="lns"></a>Lokality místní sítě

Odeberte všechny místní weby, které již nepoužíváte. V závislosti na konfiguraci, kterou jste vytvořili, je možné, že nemáte **localnetworksite** uvedeny.

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

### <a name="client-addresspool"></a><a name="clientaddresss"></a>Klientský fond adres

Pokud jste měli připojení P2S k virtuální síti, budete mít **VPNClientAddressPool**. Odeberte fondy adres klientů, které odpovídají bráně virtuální sítě, kterou jste odstranili.

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

### <a name="gatewaysubnet"></a><a name="gwsub"></a>GatewaySubnet

Odstraňte **GatewaySubnet,** který odpovídá virtuální síti.

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

## <a name="step-5-upload-the-network-configuration-file"></a><a name="upload"></a>Krok 5: Nahrání konfiguračního souboru sítě

Uložte změny a nahrajte konfigurační soubor sítě do Azure. Ujistěte se, že změníte cestu k souboru podle potřeby pro vaše prostředí.

```powershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

Pokud je úspěšná, návrat ukazuje něco podobného tomuto příkladu:

```
OperationDescription        OperationId                      OperationStatus                                                
--------------------        -----------                      ---------------                                           
Set-AzureVNetConfig         e0ee6e66-9167-cfa7-a746-7casb9   Succeeded
```
