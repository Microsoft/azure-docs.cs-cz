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
ms.openlocfilehash: 2457ef2843b0d16359b7e47fc54c58e2ef5e6034
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53429938"
---
> [!NOTE]
> Tyto příklady se nevztahují na S2S a ExpressRoute existovat vedle sebe konfigurace.
> Další informace o práci s bránami v konfiguraci coexist najdete v tématu [konfigurace současně existujících připojení.](../articles/expressroute/expressroute-howto-coexist-classic.md#gw)

## <a name="add-a-gateway"></a>Přidání brány

Po přidání brány do virtuální sítě pomocí modelu klasického prostředků, který upravíte soubor konfigurace sítě přímo před vytvořením brány. Hodnoty v následujících příkladech se musí nacházet v souboru k vytvoření brány. Pokud vaše virtuální síť dříve měli bránu přiřazen, některé z těchto hodnot už bude k dispozici. Upravte soubor tak, aby odrážely níže uvedené hodnoty.

### <a name="download-the-network-configuration-file"></a>Stáhněte si soubor konfigurace sítě

1. Stáhněte si soubor konfigurace sítě pomocí postupu v [soubor konfigurace sítě](../articles/virtual-network/virtual-networks-using-network-configuration-file.md) článku. Otevřete soubor pomocí textového editoru.
2. Přidání místní síťové lokality do souboru. Můžete použít jakoukoli předponu platnou adresu. Můžete přidat libovolná platná IP adresa brány VPN. Hodnoty adres v této části se používá pro operace ExpressRoute, ale jsou požadovány pro ověření souboru. "Pobočka1" v příkladu je název webu. Můžete použít jiný název, ale nezapomeňte použít stejnou hodnotu v bráně část souboru.

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
3. Přejděte VirtualNetworkSites a upravit pole.

  * Ověřte, zda existuje podsíť brány pro vaši virtuální síť. Pokud tomu tak není, můžete přidat jednu v tuto chvíli. Název musí být "GatewaySubnet".
  * Ověřte, zda že existuje brány část souboru. Pokud tomu tak není, přidejte ji. To se vyžaduje pro virtuální síť přidružit k místní síťové lokality (která představuje síť, ke kterému se připojujete).
  * Ověřte, že typ připojení = Dedicated. To je potřeba pro připojení ExpressRoute.

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
4. Soubor uložte a nahrajte ho do Azure.

### <a name="create-the-gateway"></a>Vytvoření brány

Pomocí následujícího příkazu vytvořte bránu. Nahraďte všechny hodnoty vlastními.

```powershell
New-AzureVNetGateway -VNetName "MyAzureVNET" -GatewayType DynamicRouting -GatewaySKU  Standard
```

## <a name="verify-the-gateway-was-created"></a>Ověřte, že vytvoření brány

Pomocí následujícího příkazu Pokud chcete ověřit vytvoření brány. Tento příkaz načte také ID brány, které jsou nutné pro další operace.

```powershell
Get-AzureVNetGateway
```

## <a name="resize-a-gateway"></a>Změňte velikost brány

Několik položek [skladové položky brány](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Následující příkaz můžete kdykoli změnit SKU brány.

> [!IMPORTANT]
> Tento příkaz nefunguje pro brány UltraPerformance. Chcete-li změnit brána brány UltraPerformance, nejprve odeberte existující bránu ExpressRoute a pak vytvořte nové brány UltraPerformance. Přejít na nižší verzi z brány UltraPerformance bránu, nejprve odeberte brány UltraPerformance a pak vytvořit novou bránu.
>
>

```powershell
Resize-AzureVNetGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="remove-a-gateway"></a>Odebrání brány

Pomocí následujícího příkazu k odebrání brány

```powershell
Remove-AzureVnetGateway -GatewayId <Gateway ID>
```