---
title: 'Odstranění brány virtuální sítě: Azure Classic'
description: Odstraňte bránu virtuální sítě pomocí prostředí PowerShell v modelu nasazení Classic.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 01/09/2020
ms.author: cherylmc
ms.openlocfilehash: ded1887248e7313c2a284388e8338af96ad7614c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84987425"
---
# <a name="delete-a-virtual-network-gateway-using-powershell-classic"></a>Odstranění brány virtuální sítě pomocí PowerShellu (Classic)

> [!div class="op_single_selector"]
> * [Správce prostředků – Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [Klasický – PowerShell](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>

Tento článek vám pomůže odstranit bránu VPN v modelu nasazení Classic pomocí prostředí PowerShell. Po odstranění brány virtuální sítě upravte soubor konfigurace sítě tak, aby se odebraly elementy, které už nepoužíváte.

## <a name="step-1-connect-to-azure"></a><a name="connect"></a>Krok 1: připojení k Azure

### <a name="1-install-the-latest-powershell-cmdlets"></a>1. Nainstalujte nejnovější rutiny PowerShellu.

[!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

### <a name="2-connect-to-your-azure-account"></a>2. Připojte se k účtu Azure.

Otevřete konzolu PowerShellu se zvýšenými oprávněními a připojte se ke svému účtu. Připojení vám usnadní následující ukázka:

1. Otevřete konzolu PowerShellu se zvýšenými právy. Chcete-li přepnout na správu služeb, použijte tento příkaz:

   ```powershell
   azure config mode asm
   ```
2. Připojte se ke svému účtu. Připojení vám usnadní následující ukázka:

   ```powershell
   Add-AzureAccount
   ```

## <a name="step-2-export-and-view-the-network-configuration-file"></a><a name="export"></a>Krok 2: Export a zobrazení konfiguračního souboru sítě

Vytvořte ve svém počítači adresář a potom do něj exportujte soubor konfigurace sítě. Tento soubor použijete pro zobrazení aktuálních informací o konfiguraci a také pro úpravu konfigurace sítě.

V tomto příkladu se soubor konfigurace sítě exportuje do adresáře C:\AzureNet.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

Otevřete soubor pomocí textového editoru a zobrazte název své klasické virtuální sítě. Když v Azure Portal vytvoříte virtuální síť, úplný název, který Azure používá, není na portálu viditelný. Například virtuální síť, která se zdá mít název "ClassicVNet1" v Azure Portal, může mít v souboru konfigurace sítě mnohem delší název. Název může vypadat nějak takto: "Group ClassicRG1 ClassicVNet1". Názvy virtuálních sítí jsou uvedené jako **VirtualNetworkSite název =**. Při spouštění rutin PowerShellu použijte názvy v souboru konfigurace sítě.

## <a name="step-3-delete-the-virtual-network-gateway"></a><a name="delete"></a>Krok 3: odstranění brány virtuální sítě

Když odstraníte bránu virtuální sítě, odpojí se všechna připojení k virtuální síti přes bránu. Pokud máte P2S klienty připojené k virtuální síti, budou odpojeni bez upozornění.

Tento příklad odstraní bránu virtuální sítě. Ujistěte se, že používáte celý název virtuální sítě ze souboru konfigurace sítě.

```powershell
Remove-AzureVNetGateway -VNetName "Group ClassicRG1 ClassicVNet1"
```

V případě úspěchu zobrazí návrat:

```
Status : Successful
```

## <a name="step-4-modify-the-network-configuration-file"></a><a name="modify"></a>Krok 4: Úprava konfiguračního souboru sítě

Když odstraníte bránu virtuální sítě, rutina neupraví soubor konfigurace sítě. Chcete-li odebrat prvky, které již nejsou používány, je třeba upravit soubor. Následující části vám pomůžou upravit soubor konfigurace sítě, který jste stáhli.

### <a name="local-network-site-references"></a><a name="lnsref"></a>Odkazy na místní síťové lokality

Chcete-li odebrat informace o odkazu na lokalitu, proveďte změny konfigurace v **ConnectionsToLocalNetwork/LocalNetworkSiteRef**. Odebrání odkazu na místní lokalitu aktivuje službu Azure za účelem odstranění tunelu. V závislosti na konfiguraci, kterou jste vytvořili, možná nebudete mít **LocalNetworkSiteRef** v seznamu.

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

### <a name="local-network-sites"></a><a name="lns"></a>Místní síťové lokality

Odeberte všechny místní weby, které již nepoužíváte. V závislosti na konfiguraci, kterou jste vytvořili, je možné, že **LocalNetworkSite** není uvedený.

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

V tomto příkladu jsme odebrali jenom Site3.

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

### <a name="client-addresspool"></a><a name="clientaddresss"></a>Fondu adres klienta

Pokud jste P2S připojení k virtuální síti, budete mít **VPNClientAddressPool**. Odeberte fondy adres klientů, které odpovídají bráně virtuální sítě, kterou jste odstranili.

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

Odstraňte **GatewaySubnet** , který odpovídá virtuální síti.

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

## <a name="step-5-upload-the-network-configuration-file"></a><a name="upload"></a>Krok 5: nahrání konfiguračního souboru sítě

Uložte změny a nahrajte soubor konfigurace sítě do Azure. Ujistěte se, že jste změnili cestu k souboru, jak je to nutné pro vaše prostředí.

```powershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

V případě úspěchu vrátí návrat něco podobného jako v tomto příkladu:

```
OperationDescription        OperationId                      OperationStatus                                                
--------------------        -----------                      ---------------                                           
Set-AzureVNetConfig         e0ee6e66-9167-cfa7-a746-7casb9   Succeeded
```
