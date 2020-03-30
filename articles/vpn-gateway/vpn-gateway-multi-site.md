---
title: 'Připojení virtuální sítě k více webům pomocí brány VPN: Classic'
description: Připojte více místních místních webů ke klasické virtuální síti pomocí brány VPN.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/11/2020
ms.author: yushwang
ms.openlocfilehash: a95cd6ea85a16b0e0bf5f67f5dfc20d57f11463b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198087"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection-classic"></a>Přidání připojení site-to-site do virtuální sítě s existujícím připojením brány VPN (klasické)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

> [!div class="op_single_selector"]
> * [Portál Azure](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (Classic)](vpn-gateway-multi-site.md)
>
>

Tento článek vás provede pomocí prostředí PowerShell k přidání připojení site-to-site (S2S) k bráně VPN, která má existující připojení. Tento typ připojení se často označuje jako konfigurace "více sítí". Kroky v tomto článku platí pro virtuální sítě vytvořené pomocí klasického modelu nasazení (označované také jako Správa služeb). Tyto kroky se nevztahují na konfigurace koexistujícího připojení ExpressRoute/Site-to-Site.

### <a name="deployment-models-and-methods"></a>Modely a metody nasazení

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Tuto tabulku aktualizujeme, jakmile budou pro tuto konfiguraci k dispozici nové články a další nástroje. Když je k dispozici článek, odkazujeme přímo na něj z této tabulky.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="about-connecting"></a>O připojení

K jedné virtuální síti můžete připojit více místních webů. To je obzvláště atraktivní pro vytváření hybridních cloudových řešení. Vytvoření připojení pro více lokalit k bráně virtuální sítě Azure je podobné vytváření dalších připojení mezi lokalitami. Ve skutečnosti můžete použít existující bránu Azure VPN, pokud je brána dynamická (založená na trase).

Pokud už máte statickou bránu připojenou k virtuální síti, můžete změnit typ brány na dynamický, aniž byste museli virtuální síť znovu obnovovat, aby se přizpůsobila více lokalitám. Před změnou typu směrování se ujistěte, že vaše místní brána VPN podporuje konfigurace VPN založené na trasách.

![diagram pro více webů](./media/vpn-gateway-multi-site/multisite.png "více míst")

## <a name="points-to-consider"></a>Body ke zvážení

**Nebudete moci použít portál k provádění změn v této virtuální síti.** Je třeba provést změny v konfiguračním souboru sítě namísto použití portálu. Pokud na portálu provedete změny, přepíší nastavení odkazů na více webů pro tuto virtuální síť.

V době, kdy dokončíte postup pro více lokalit, byste se měli cítit pohodlně při používání konfiguračního síťového souboru. Pokud však na konfiguraci sítě pracuje více lidí, musíte se ujistit, že o tomto omezení ví všichni uživatelé. To neznamená, že portál nemůžete vůbec používat. Můžete ji použít pro všechno ostatní, s výjimkou provádění změn konfigurace této konkrétní virtuální sítě.

## <a name="before-you-begin"></a>Než začnete

Než začnete s konfigurací, ověřte, zda máte následující:

* Kompatibilní hardware VPN pro každé místní umístění. Zkontrolujte, zda zařízení [VPN pro připojení virtuální sítě](vpn-gateway-about-vpn-devices.md) ověřte, zda je zařízení, které chcete použít, něco, o čem je známo, že je kompatibilní.
* Externě směřující veřejná IP adresa IPv4 pro každé zařízení VPN. IP adresu nelze nalézt za adresou NAT. To je požadavek.
* Někdo, kdo je zběhlý v konfiguraci hardwaru VPN. Budete muset mít silné znalosti o tom, jak nakonfigurovat zařízení VPN nebo pracovat s někým, kdo to dělá.
* Rozsahy IP adres, které chcete použít pro virtuální síť (pokud jste ji ještě nevytvořili).
* Rozsahy IP adres pro všechny lokality místní sítě, ke kterým se budete připojovat. Budete se muset ujistit, že rozsahy IP adres pro každý z lokalit místní sítě, ke kterým se chcete připojit, se nepřekrývají. V opačném případě portál nebo rozhraní REST API odmítne konfiguraci, která se nahrává.<br>Například pokud máte dvě lokality místní sítě, které oba obsahují rozsah IP adres 10.2.3.0/24 a máte balíček s cílovou adresou 10.2.3.3, Azure nebude vědět, které lokality chcete odeslat balíček, protože rozsahy adres se překrývají. Chcete-li zabránit problémům s směrováním, Azure neumožňuje nahrát konfigurační soubor, který má překrývající se rozsahy.

### <a name="working-with-azure-powershell"></a>Práce s Azure PowerShellem

[!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

## <a name="1-create-a-site-to-site-vpn"></a>1. Vytvořte VPN site-to-site
Pokud již máte SÍŤ VPN site-to-site s dynamickou směrovací bránou, skvělé! Můžete přejít k [exportu nastavení konfigurace virtuální sítě](#export). Pokud ne, postupujte takto:

### <a name="if-you-already-have-a-site-to-site-virtual-network-but-it-has-a-static-policy-based-routing-gateway"></a>Pokud již máte virtuální síť Site-to-Site, ale má statickou (založenou na zásadách) směrovací bránu:
1. Změňte typ brány na dynamické směrování. Síť VPN s více lokalitami vyžaduje dynamickou (označovanou také jako směrovací) bránu směrování. Chcete-li změnit typ brány, musíte nejprve odstranit existující bránu a pak vytvořit novou.
2. Nakonfigurujte novou bránu a vytvořte tunel VPN. Pokyny naleznete v [tématu Pokyny naleznete v tématu Specify the SKU and VPN type](vpn-gateway-howto-site-to-site-classic-portal.md#sku). Ujistěte se, že jste zadali typ směrování jako dynamický.

### <a name="if-you-dont-have-a-site-to-site-virtual-network"></a>Pokud virtuální síť Site-to-Site nemáte:
1. Vytvořte virtuální síť Site-to-Site podle těchto pokynů: [Vytvořte virtuální síť s připojením VPN site-to-site](vpn-gateway-site-to-site-create.md).  
2. Konfigurace dynamické brány směrování podle těchto pokynů: [Konfigurace brány VPN](vpn-gateway-configure-vpn-gateway-mp.md). Nezapomeňte vybrat **dynamické směrování** pro typ brány.

## <a name="2-export-the-network-configuration-file"></a><a name="export"></a>2. Export síťového konfiguračního souboru

Otevřete konzolu PowerShell se zvýšenými právy. Chcete-li přepnout na správu služeb, použijte tento příkaz:

```powershell
azure config mode asm
```

Připojte se ke svému účtu. Připojení vám usnadní následující ukázka:

```powershell
Add-AzureAccount
```

Exportujte konfigurační soubor sítě Azure spuštěním následujícího příkazu. V případě potřeby můžete změnit umístění souboru, které chcete exportovat do jiného umístění.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

## <a name="3-open-the-network-configuration-file"></a>3. Otevření síťového konfiguračního souboru
Otevřete konfigurační soubor sítě, který jste stáhli v posledním kroku. Použijte libovolný editor XML, který se vám líbí. Soubor by měl vypadat podobně jako následující:

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

## <a name="4-add-multiple-site-references"></a>4. Přidání více odkazů na web
Když přidáte nebo odeberete referenční informace o webu, provedete změny konfigurace connectionsToLocalNetwork/LocalNetworkSiteRef. Přidání nového odkazu na místní lokalitu spustí Azure k vytvoření nového tunelu. V níže uvedeném příkladu je konfigurace sítě pro připojení jedné lokality. Po dokončení změn soubor uložte.

```xml
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

Chcete-li přidat další odkazy na lokalitu (vytvořit konfiguraci s více lokalitami), jednoduše přidejte další řádky "LocalNetworkSiteRef", jak je znázorněno v příkladu níže:

```xml
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
      <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

## <a name="5-import-the-network-configuration-file"></a>5. Import konfiguračního souboru sítě
Importujte konfigurační síťový soubor. Při importu tohoto souboru se změnami budou přidány nové tunelové propojení. Tunelové propojení budou používat dynamickou bránu, kterou jste vytvořili dříve. K importu souboru můžete použít prostředí PowerShell.

## <a name="6-download-keys"></a>6. Klávesy ke stažení
Po přidání nových tunelových propojení použijte rutinu prostředí PowerShell Get-AzureVNetGatewayKey k získání předsdílených klíčů IPsec/IKE pro každý tunelový propojení.

Například:

```powershell
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"
```

Pokud chcete, můžete také použít *get Virtual Network Gateway sdíleného klíče* REST API získat předsdílené klíče.

## <a name="7-verify-your-connections"></a>7. Ověření připojení
Zkontrolujte stav tunelového propojení ve více lokalitě. Po stažení klíčů pro každý tunel budete chtít ověřit připojení. Pomocí příkazu Get-AzureVnetConnection získáte seznam tunelových propojení virtuálnísítě, jak je znázorněno v příkladu níže. VNet1 je název virtuální sítě.

```powershell
Get-AzureVnetConnection -VNetName VNET1
```

Příklad vrácení:

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

Další informace o privátní bráně najdete v [tématu O privátní brány](vpn-gateway-about-vpngateways.md).
