---
title: zahrnout soubor
description: zahrnout soubor
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 12/13/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 70ac106995324c758bde942d12191a01e3457e6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67174741"
---
> [!NOTE]
> Tyto příklady se nevztahují na konfigurace S2S/ExpressRoute koexistující.
> Další informace o práci s bránami v koexistující konfiguraci naleznete v [tématu Konfigurace koexistujících připojení.](../articles/expressroute/expressroute-howto-coexist-classic.md#gw)

## <a name="add-a-gateway"></a>Přidání brány

Když přidáte bránu do virtuální sítě pomocí klasického modelu prostředků, upravíte konfigurační soubor sítě přímo před vytvořením brány. Hodnoty v níže uvedených příkladech musí být k dispozici v souboru k vytvoření brány. Pokud vaše virtuální síť dříve měla bránu k ní spojené, některé z těchto hodnot již budou k dispozici. Upravte soubor tak, aby odrážel níže uvedené hodnoty.

### <a name="download-the-network-configuration-file"></a>Stažení konfiguračního souboru sítě

1. Stáhněte si konfigurační soubor sítě pomocí kroků v článku [konfiguračního souboru sítě.](../articles/virtual-network/virtual-networks-using-network-configuration-file.md) Otevřete soubor pomocí textového editoru.
2. Přidejte do souboru lokalitu místní sítě. Můžete použít libovolnou platnou předponu adresy. Můžete přidat libovolnou platnou IP adresu pro bránu VPN. Hodnoty adres v této části se nepoužívají pro operace ExpressRoute, ale jsou vyžadovány pro ověření souboru. V příkladu "branch1" je název webu. Můžete použít jiný název, ale nezapomeňte použít stejnou hodnotu v části Brána souboru.

   ```
   <VirtualNetworkConfiguration>
    <Dns />
    <LocalNetworkSites>
      <LocalNetworkSite name="branch1">
        <AddressSpace>
          <AddressPrefix>165.3.1.0/27</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>3.2.1.4</VPNGatewayAddress>
    </LocalNetworkSite>
   ```
3. Přejděte na web virtualnetworkwe a upravte pole.

   * Ověřte, zda podsíť brány existuje pro vaši virtuální síť. Pokud tomu tak není, můžete přidat jeden v tomto okamžiku. Název musí být "GatewaySubnet".
   * Ověřte, zda v části Brána souboru existuje. Pokud ne, přidejte to. To je nutné k přidružení virtuální sítě k lokalitě místní sítě (která představuje síť, ke které se připojujete).
   * Ověřte, zda typ připojení = Vyhrazeno. To je vyžadováno pro připojení ExpressRoute.

   ```
   </LocalNetworkSites>
    <VirtualNetworkSites>
      <VirtualNetworkSite name="myAzureVNET" Location="East US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="default">
            <AddressPrefix>10.0.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.0.1.0/27</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="branch1">
              <Connection type="Dedicated" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
   </VirtualNetworkConfiguration>
   </NetworkConfiguration>
   ```
4. Uložte soubor a nahrajte ho do Azure.

### <a name="create-the-gateway"></a>Vytvoření brány

Pomocí níže uvedeného příkazu vytvořte bránu. Nahraďte všechny hodnoty za své vlastní.

```powershell
New-AzureVNetGateway -VNetName "MyAzureVNET" -GatewayType DynamicRouting -GatewaySKU  Standard
```

## <a name="verify-the-gateway-was-created"></a>Ověření vytvoření brány

Pomocí následujícího příkazu ověřte, zda byla brána vytvořena. Tento příkaz také načte ID brány, které potřebujete pro další operace.

```powershell
Get-AzureVNetGateway
```

## <a name="resize-a-gateway"></a>Změna velikosti brány

Existuje několik [skum brány](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Pomocí následujícího příkazu můžete kdykoli změnit skladovou položku brány.

> [!IMPORTANT]
> Tento příkaz nefunguje pro bránu UltraPerformance. Chcete-li změnit bránu na bránu UltraPerformance, nejprve odeberte existující bránu ExpressRoute a vytvořte novou bránu UltraPerformance. Chcete-li bránu snížit na nižší verzi z brány UltraPerformance, nejprve bránu UltraPerformance odeberte a pak vytvořte novou bránu.
>
>

```powershell
Resize-AzureVNetGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="remove-a-gateway"></a>Odebrání brány

Odebrání brány pomocí následujícího příkazu

```powershell
Remove-AzureVnetGateway -GatewayId <Gateway ID>
```