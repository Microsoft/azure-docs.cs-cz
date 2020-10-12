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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "67174741"
---
> [!NOTE]
> Tyto příklady neplatí pro konfigurace S2S/ExpressRoute.
> Další informace o práci s branami v konfiguraci s jednou službou najdete v tématu [Konfigurace současně existujících připojení.](../articles/expressroute/expressroute-howto-coexist-classic.md#gw)

## <a name="add-a-gateway"></a>Přidání brány

Když přidáte bránu do virtuální sítě pomocí modelu klasických prostředků, upravíte soubor konfigurace sítě přímo před vytvořením brány. Aby bylo možné vytvořit bránu, musí být v souboru přítomny hodnoty v níže uvedených příkladech. Pokud jste k virtuální síti dříve měli bránu, která je k ní přidružená, některé z těchto hodnot už budou k dispozici. Upravte soubor tak, aby odrážel následující hodnoty.

### <a name="download-the-network-configuration-file"></a>Stažení konfiguračního souboru sítě

1. Stáhněte si konfigurační soubor sítě pomocí postupu v článku [konfigurační soubor sítě](../articles/virtual-network/virtual-networks-using-network-configuration-file.md) . Otevřete soubor pomocí textového editoru.
2. Přidejte do souboru místní síťovou lokalitu. Můžete použít libovolnou platnou předponu adresy. Pro bránu VPN můžete přidat libovolnou platnou IP adresu. Hodnoty adresy v této části se nepoužívají pro operace ExpressRoute, ale vyžadují se pro ověření souboru. V tomto příkladu je "pobočka1" název lokality. Můžete použít jiný název, ale nezapomeňte použít stejnou hodnotu v části Gateway souboru.

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
3. Přejděte do VirtualNetworkSites a upravte pole.

   * Ověřte, že podsíť brány pro vaši virtuální síť existuje. Pokud tomu tak není, můžete ho v tuto chvíli přidat. Název musí být "GatewaySubnet".
   * Ověřte, že oddíl Gateway souboru existuje. Pokud tomu tak není, přidejte ho. Tento postup je nutný k přidružení virtuální sítě k místní síťové lokalitě (která představuje síť, ke které se připojujete).
   * Ověřte, zda typ připojení = vyhrazeno. To je vyžadováno pro připojení ExpressRoute.

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

Bránu vytvoříte pomocí následujícího příkazu. Nahraďte libovolné hodnoty vlastními.

```powershell
New-AzureVNetGateway -VNetName "MyAzureVNET" -GatewayType DynamicRouting -GatewaySKU  Standard
```

## <a name="verify-the-gateway-was-created"></a>Ověření, že se brána vytvořila

Pomocí níže uvedeného příkazu ověřte, že se brána vytvořila. Tento příkaz také načte ID brány, které potřebujete pro jiné operace.

```powershell
Get-AzureVNetGateway
```

## <a name="resize-a-gateway"></a>Změna velikosti brány

Existuje několik [SKU brány](../articles/expressroute/expressroute-about-virtual-network-gateways.md). K změně SKU brány můžete použít následující příkaz.

> [!IMPORTANT]
> Tento příkaz nefunguje pro bránu UltraPerformance. Pokud chcete bránu změnit na bránu UltraPerformance, odeberte nejdřív existující bránu ExpressRoute a pak vytvořte novou bránu UltraPerformance. Pokud chcete bránu downgradovat z brány UltraPerformance, nejdřív odeberte bránu UltraPerformance a pak vytvořte novou bránu.
>
>

```powershell
Resize-AzureVNetGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="remove-a-gateway"></a>Odebrání brány

Pomocí následujícího příkazu odeberte bránu.

```powershell
Remove-AzureVnetGateway -GatewayId <Gateway ID>
```