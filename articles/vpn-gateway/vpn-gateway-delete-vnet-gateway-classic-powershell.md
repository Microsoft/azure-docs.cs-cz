---
title: 'Odstranit bránu virtuální sítě: prostředí PowerShell: Azure classic | Dokumentace Microsoftu'
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
ms.openlocfilehash: 0c5d045cb949f5393bb2d9da54715b2b84229c36
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39036284"
---
# <a name="delete-a-virtual-network-gateway-using-powershell-classic"></a>Odstranit bránu virtuální sítě pomocí prostředí PowerShell (classic)
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [Classic – PowerShell](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

Tento článek pomáhá odstranění brány VPN v modelu nasazení classic pomocí prostředí PowerShell. Po odstranění brány virtuální sítě, upravte konfigurační soubor sítě k odebrání prvků, které už nepoužíváte.

##<a name="connect"></a>Krok 1: Připojení k Azure

### <a name="1-install-the-latest-powershell-cmdlets"></a>1. Nainstalujte nejnovější rutiny prostředí PowerShell.

Stáhněte si a nainstalujte nejnovější verzi rutin Powershellu pro Azure Service Management (SM). Další informace najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview).

### <a name="2-connect-to-your-azure-account"></a>2. Připojte se ke svému účtu Azure. 

Otevřete konzolu PowerShellu se zvýšenými oprávněními a připojte se ke svému účtu. Připojení vám usnadní následující ukázka:

```powershell
Add-AzureAccount
```

## <a name="export"></a>Krok 2: Export a zobrazit soubor konfigurace sítě

Vytvořte ve svém počítači adresář a potom do něj exportujte soubor konfigurace sítě. Tento soubor do obou zobrazení aktuální informace o konfiguraci a také upravit konfiguraci sítě používáte.

V tomto příkladu se soubor konfigurace sítě exportuje do adresáře C:\AzureNet.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

V textovém editoru otevřete soubor a podívejte se na název pro klasické virtuální sítě. Při vytváření virtuální sítě na webu Azure Portal, není úplný název, který používá Azure nezobrazuje na portálu. Virtuální síť, která se zobrazí s názvem "ClassicVNet1" na webu Azure Portal, například může mít mnohem déle, název v konfiguračním souboru sítě. Název může vypadat nějak takto: "Skupina ClassicRG1 ClassicVNet1". Názvy virtuální sítě jsou uvedené jako **"VirtualNetworkSite name ="**. Při spuštění rutiny Powershellu, použijte názvy v konfiguračním souboru sítě.

## <a name="delete"></a>Krok 3: Odstranění brány virtuální sítě

Při odstranění brány virtuální sítě, všechna připojení k virtuální síti prostřednictvím brány jsou odpojené. Pokud máte klienty P2S, kteří jsou připojení k virtuální síti, se odpojí bez předchozího upozornění.

Tento příklad odstraní bránu virtuální sítě. Nezapomeňte použít celý název virtuální sítě z konfiguračního souboru sítě.

```powershell
Remove-AzureVNetGateway -VNetName "Group ClassicRG1 ClassicVNet1"
```

V případě úspěchu se zobrazí vrácení:

```
Status : Successful
```

## <a name="modify"></a>Krok 4: Upravte konfigurační soubor sítě

Při odstranění brány virtuální sítě, rutina neupravuje soubor konfigurace sítě. Budete muset upravit soubor odebrat prvky, které jsou již nejsou déle používány. V následujících částech můžete upravit soubor konfigurace sítě, který jste stáhli.

### <a name="lnsref"></a>Odkazů místních síťových lokalit

Chcete-li odebrat odkaz na informace o lokalitě, provést změny konfigurace **ConnectionsToLocalNetwork/LocalNetworkSiteRef**. Odebrat místní lokality odkaz aktivační události Azure odstranit tunelové propojení. V závislosti na konfiguraci, kterou jste vytvořili, nemusíte mít **LocalNetworkSiteRef** uvedené.

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

###<a name="lns"></a>Místní síťové lokality

Odeberte všechny místní servery, které už nepoužíváte. V závislosti na konfiguraci, které jste vytvořili, je možné, že není nutné **LocalNetworkSite** uvedené.

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

### <a name="clientaddresss"></a>Fondu adres klienta

Pokud máte připojení typu P2S k virtuální síti, budete mít **VPNClientAddressPool**. Odeberte fondy adres klienta, které odpovídají bránu virtuální sítě, který jste odstranili.

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

### <a name="gwsub"></a>Podsíť brány

Odstranit **GatewaySubnet** , který odpovídá k virtuální síti.

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

Uložte změny a nahrajte soubor konfigurace sítě do Azure. Ujistěte se, že změníte cestu k souboru jako nezbytné pro vaše prostředí.

```powershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

V případě úspěchu se zobrazí návratový podobně jako v tomto příkladu:

```
OperationDescription        OperationId                      OperationStatus                                                
--------------------        -----------                      ---------------                                           
Set-AzureVNetConfig         e0ee6e66-9167-cfa7-a746-7casb9   Succeeded
```
