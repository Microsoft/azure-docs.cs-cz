---
title: 'Připojení virtuální sítě k několika webům pomocí VPN Gateway: Classic'
description: Připojení několika místních místních lokalit k klasické virtuální síti pomocí VPN Gateway.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: yushwang
ms.openlocfilehash: 168bb9e06c73ec27ec1304813023889c9549b8e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94660691"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection-classic"></a>Přidání připojení typu Site-to-site k virtuální síti s existujícím připojením brány VPN (Classic)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (Classic)](vpn-gateway-multi-site.md)
>
>

Tento článek vás provede postupem použití PowerShellu k přidání připojení typu Site-to-Site (S2S) k bráně VPN, která má existující připojení. Tento typ připojení se často označuje jako konfigurace s více lokalitami. Kroky v tomto článku se vztahují na virtuální sítě vytvořené pomocí modelu nasazení Classic (označuje se také jako Správa služeb). Tyto kroky se nevztahují na existující konfigurace připojení ExpressRoute/site-to-site.

### <a name="deployment-models-and-methods"></a>Modely a metody nasazení

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Tuto tabulku aktualizujeme, protože nové články a další nástroje budou k dispozici pro tuto konfiguraci. Když je článek k dispozici, odkazujeme přímo na něj z této tabulky.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="about-connecting"></a>O připojení

Můžete propojit několik místních serverů s jednou virtuální sítí. To je obzvláště atraktivní pro vytváření hybridních cloudových řešení. Vytvoření připojení s více lokalitami pro bránu virtuální sítě Azure se podobá vytváření dalších připojení typu Site-to-site. Ve skutečnosti můžete použít existující bránu Azure VPN Gateway, pokud je tato brána dynamická (založená na trasách).

Pokud již máte připojenou statickou bránu k virtuální síti, můžete změnit typ brány na dynamický, aniž by bylo nutné znovu sestavit virtuální síť, aby bylo možné pojmout více lokalit. Než začnete měnit typ směrování, ujistěte se, že místní Brána VPN podporuje konfigurace sítě VPN založené na směrování.

![diagram s více weby](./media/vpn-gateway-multi-site/multisite.png "více lokalit")

## <a name="points-to-consider"></a>Body ke zvážení

**K provádění změn v této virtuální síti nebudete moct používat portál.** Místo používání portálu je nutné provést změny v souboru konfigurace sítě. Pokud provedete změny na portálu, přepíší se nastavení odkazů na více lokalit pro tuto virtuální síť.

V případě, že jste dokončili postup pro více webů, byste měli mít pocit, že budete mít k pohodlné používání konfiguračního souboru sítě. Pokud ale v konfiguraci sítě pracujete s více uživateli, musíte se ujistit, že o tomto omezení ví všichni. To neznamená, že portál nemůžete vůbec použít. Můžete ho použít pro všechno ostatní, s výjimkou změny konfigurace této konkrétní virtuální sítě.

## <a name="before-you-begin"></a>Než začnete

Než začnete s konfigurací, ověřte, že máte následující:

* Kompatibilní hardware sítě VPN pro každé místní umístění. Projděte si [informace o zařízeních VPN pro připojení Virtual Network](vpn-gateway-about-vpn-devices.md) , abyste ověřili, jestli zařízení, které chcete použít, je něco, co je známo kompatibilní.
* Veřejná IP adresa IPv4, která je externě přístupná pro každé zařízení VPN. IP adresa nesmí být umístěná za překladem adres (NAT). Toto je požadavek.
* Někdo, který je zdatní na konfiguraci hardwaru VPN. Budete muset mít silný přehled o tom, jak nakonfigurovat zařízení VPN, nebo pracovat s někým, kdo má.
* Rozsahy IP adres, které chcete použít pro virtuální síť (Pokud jste ji ještě nevytvořili).
* Rozsahy IP adres pro všechny místní síťové lokality, ke kterým se budete připojovat. Bude nutné zajistit, aby se rozsahy IP adres pro všechny místní síťové lokality, ke kterým se chcete připojit, překrývaly. V opačném případě bude portál nebo REST API odmítat nahranou konfiguraci.<br>Například pokud máte dvě místní síťové lokality, které obě obsahují rozsah IP adres 10.2.3.0/24 a máte balíček s cílovou adresou 10.2.3.3, Azure by neznal, na který web chcete balíček odeslat, protože rozsahy adres se překrývají. Aby nedocházelo k problémům s směrováním, Azure vám neumožní nahrát konfigurační soubor, který má překrývající se rozsahy.

### <a name="working-with-azure-powershell"></a>Práce s Azure PowerShell

[!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

## <a name="1-create-a-site-to-site-vpn"></a>1. vytvoření sítě VPN typu Site-to-site
Pokud už máte síť VPN typu Site-to-site s bránou dynamického směrování, Skvělé! Můžete pokračovat a [Exportovat nastavení konfigurace virtuální sítě](#export). Pokud ne, udělejte toto:

### <a name="if-you-already-have-a-site-to-site-virtual-network-but-it-has-a-static-policy-based-routing-gateway"></a>Pokud již máte virtuální síť typu Site-to-site, ale má statickou bránu směrování (založenou na zásadách):
1. Změňte typ brány na dynamické směrování. SÍŤ VPN s více lokalitami vyžaduje dynamickou (také známou jako směrovací bránu založenou na trasách). Pokud chcete změnit typ brány, musíte nejdřív odstranit existující bránu a pak vytvořit novou.
2. Nakonfigurujte novou bránu a vytvořte tunel VPN. Pokyny najdete v tématu [určení typu SKU a sítě VPN](vpn-gateway-howto-site-to-site-classic-portal.md#sku). Ujistěte se, že jste zadali typ směrování jako dynamický.

### <a name="if-you-dont-have-a-site-to-site-virtual-network"></a>Pokud nemáte virtuální síť typu Site-to-site:
1. Pomocí těchto pokynů vytvořte virtuální síť Site-to-site: [vytvořte Virtual Network s připojením VPN typu Site-to-site](./vpn-gateway-howto-site-to-site-classic-portal.md).  
2. Pomocí těchto pokynů nakonfigurujte bránu dynamického směrování: [nakonfigurujte VPN Gateway](./vpn-gateway-howto-site-to-site-classic-portal.md). Ujistěte se, že jste vybrali možnost **dynamické směrování** pro typ brány.

## <a name="2-export-the-network-configuration-file"></a><a name="export"></a>2. exportujte konfigurační soubor sítě.

Otevřete konzolu PowerShellu se zvýšenými právy. Chcete-li přepnout na správu služeb, použijte tento příkaz:

```powershell
azure config mode asm
```

Připojte se ke svému účtu. Připojení vám usnadní následující ukázka:

```powershell
Add-AzureAccount
```

Exportujte soubor konfigurace sítě Azure spuštěním následujícího příkazu. V případě potřeby můžete změnit umístění souboru, aby se v případě potřeby exportovali do jiného umístění.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

## <a name="3-open-the-network-configuration-file"></a>3. Otevřete soubor konfigurace sítě.
Otevřete soubor konfigurace sítě, který jste stáhli v posledním kroku. Použijte libovolný editor XML, který chcete. Soubor by měl vypadat nějak takto:

```xml
<NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
  <VirtualNetworkConfiguration>
    <LocalNetworkSites>
      <LocalNetworkSite name="Site1">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
          <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>131.2.3.4</VPNGatewayAddress>
      </LocalNetworkSite>
      <LocalNetworkSite name="Site2">
        <AddressSpace>
          <AddressPrefix>10.2.0.0/16</AddressPrefix>
          <AddressPrefix>10.3.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>131.4.5.6</VPNGatewayAddress>
      </LocalNetworkSite>
    </LocalNetworkSites>
    <VirtualNetworkSites>
      <VirtualNetworkSite name="VNet1" AffinityGroup="USWest">
        <AddressSpace>
          <AddressPrefix>10.20.0.0/16</AddressPrefix>
          <AddressPrefix>10.21.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="FE">
            <AddressPrefix>10.20.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="BE">
            <AddressPrefix>10.20.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.20.2.0/29</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
  </VirtualNetworkConfiguration>
</NetworkConfiguration>
```

## <a name="4-add-multiple-site-references"></a>4. Přidání více odkazů webu
Když přidáte nebo odeberete informace o odkazech na lokalitu, provedete změny konfigurace ConnectionsToLocalNetwork/LocalNetworkSiteRef. Přidáním nového odkazu na místní lokalitu se spustí Azure a vytvoří se nové tunelové propojení. V následujícím příkladu je konfigurace sítě určena pro připojení s jednou lokalitou. Až změny dokončíte, soubor uložte.

```xml
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

Chcete-li přidat další odkazy na web (vytvořit konfiguraci s více lokalitami), stačí přidat další řádky "LocalNetworkSiteRef", jak je znázorněno v následujícím příkladu:

```xml
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
      <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

## <a name="5-import-the-network-configuration-file"></a>5. importujte soubor konfigurace sítě.
Importujte soubor konfigurace sítě. Při importu tohoto souboru se změnami budou přidány nové tunely. Tunely budou používat dynamickou bránu, kterou jste vytvořili dříve. K importu tohoto souboru můžete použít PowerShell.

## <a name="6-download-keys"></a>6. stažení klíčů
Až budou vaše nové tunely přidané, pomocí rutiny Get-AzureVNetGatewayKey v PowerShellu Získejte předsdílené klíče IPsec/IKE pro každé tunelové propojení.

Například:

```powershell
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"
```

Pokud chcete, můžete k získání předsdílených klíčů použít taky REST API *sdíleného klíče Virtual Network pro bránu get* .

## <a name="7-verify-your-connections"></a>7. Ověřte připojení.
Ověřte stav tunelového propojení více lokalit. Po stažení klíčů pro každé tunelové propojení budete chtít ověřit připojení. Pomocí příkazu Get-AzureVnetConnection získáte seznam tunelů virtuální sítě, jak je znázorněno v následujícím příkladu. VNet1 je název virtuální sítě.

```powershell
Get-AzureVnetConnection -VNetName VNET1
```

Příklad návratu:

```
    ConnectivityState         : Connected
    EgressBytesTransferred    : 661530
    IngressBytesTransferred   : 519207
    LastConnectionEstablished : 5/2/2014 2:51:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site1' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site1
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7f68a8e6-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded

    ConnectivityState         : Connected
    EgressBytesTransferred    : 789398
    IngressBytesTransferred   : 143908
    LastConnectionEstablished : 5/2/2014 3:20:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site2' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site2
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7893b329-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded
```

## <a name="next-steps"></a>Další kroky

Další informace o branách VPN najdete v tématu [informace o branách VPN](vpn-gateway-about-vpngateways.md).