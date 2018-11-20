---
title: 'Připojit virtuální síť k více webům pomocí VPN Gateway a Powershellu: Classic | Dokumentace Microsoftu'
description: Připojení více lokalit místní k klasickou virtuální síť používat bránu VPN.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-service-management
ms.assetid: b043df6e-f1e8-4a4d-8467-c06079e2c093
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: yushwang
ms.openlocfilehash: 768f06c9d007e716f89ca61ccd9f8a2ccd575efd
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2018
ms.locfileid: "52160864"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection-classic"></a>Přidat připojení Site-to-Site k virtuální síti se existující připojení brány VPN (classic)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (Classic)](vpn-gateway-multi-site.md)
>
>

Tento článek vás provede přidáním připojení Site-to-Site (S2S), který má existující připojení brány VPN pomocí Powershellu. Tento typ připojení se často označuje jako "s více lokalitami" konfigurace. Postupy v tomto článku se týkají do virtuální sítě vytvořené pomocí modelu nasazení classic (také označované jako správa služeb). Tyto kroky nevztahují na konfigurace současně existujících připojení ExpressRoute a Site-to-Site.

### <a name="deployment-models-and-methods"></a>Modely a metody nasazení

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Tuto tabulku aktualizujeme přidáváním nových článků a dalších nástrojů bude k dispozici pro tuto konfiguraci. Když je článek k dispozici, odkaz na něj přímo z této tabulky.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="about-connecting"></a>O připojení

Připojení několika místních lokalit k jedné virtuální sítě. To je zvláště atraktivní pro vytváření hybridních cloudových řešení. Vytvoření připojení více lokalit pro vaši bránu virtuální sítě Azure je podobné jako vytvoření další připojení Site-to-Site. Ve skutečnosti můžete použít existující bráně Azure VPN, jako je dynamická brána (trasové).

Pokud už máte statické brány připojené k vaší virtuální sítě, můžete změnit typ brány na dynamické aniž byste museli znovu vytvořit virtuální síť obsáhnout více lokalit. Před změnou typu směrování, ujistěte se, že vaše místní brána podporuje konfigurace sítě VPN založené na směrování.

![diagram Multi-Site](./media/vpn-gateway-multi-site/multisite.png "Multi-Site")

## <a name="points-to-consider"></a>Body ke zvážení

**Nebude moct provést změny do této virtuální sítě pomocí portálu.** Budete muset změnit soubor konfigurace sítě, místo použití na portálu. Pokud provedete změny na portálu, budete se přepsat nastavení Multi-Site odkaz pro tuto virtuální síť.

Musí se klidem, je používán konfigurační soubor sítě podle času jste dokončili postup Multi-Site. Pokud máte více lidí pracujících na konfiguraci sítě, budete však muset Ujistěte se, že všichni ví o toto omezení. To neznamená, že nelze použít na portálu ve všech. Můžete ho použít pro všechno ostatní, s výjimkou změn konfigurace do této konkrétní virtuální sítě.

## <a name="before-you-begin"></a>Než začnete

Než začnete s konfigurací, ověřte, že máte následující:

* Kompatibilní hardware sítě VPN pro jednotlivé v místním umístění. Zkontrolujte [o zařízeních VPN pro připojení k virtuální síti](vpn-gateway-about-vpn-devices.md) ověřit, jestli je zařízení, který chcete použít něco, co je znám jako kompatibilní.
* Veřejnou IP adresa protokolu IPv4 adresu pro každé zařízení VPN. IP adresa nesmí být umístěná za službou NAT. Toto je požadavek.
* Budete potřebovat nainstalovat nejnovější verzi rutin Azure PowerShellu. Ujistěte se, že instalace verze služby správy (SM) kromě verze Resource Manageru. Zobrazit [instalace a konfigurace Azure Powershellu](/powershell/azure/overview) Další informace.
* Někoho, kdo zdatní při konfiguraci hardwarem sítě VPN. Budete muset mít silné znalosti o tom, jak nakonfigurovat zařízení VPN, nebo pracovat s někým, kdo dělá.
* Rozsahy IP adres, které chcete použít pro vaši virtuální síť (pokud už jste ještě nevytvořili jeden).
* Rozsahy IP adres pro každou z místních síťových lokalit, které budete připojovat k. Musíte zajistit, že rozsahy IP adres pro každou z místních síťových lokalit, které chcete připojit k nemusí být stejné. Na portálu nebo rozhraní REST API v opačném případě bude taková konfigurace v průběhu nahrávání.<br>Například pokud máte dvě místní síťové lokality, že oba obsahují 10.2.3.0/24 rozsah IP adres a máte balíček s cílovou adresou 10.2.3.3 Azure vlastně nevěděli o které lokalitě, kterou chcete odeslat balíček, protože jsou překrývající se rozsahy adres. Aby se zabránilo problémům směrování, Azure neumožňuje nahrát konfigurační soubor, který obsahuje překrývající se rozsahy.

## <a name="1-create-a-site-to-site-vpn"></a>1. Vytvoření S2S (Site-to-site) VPN
Pokud už máte sítě Site-to-Site VPN pomocí brány dynamického směrování, skvěle! Můžete přejít k [exportovat nastavení konfigurace virtuální sítě](#export). Pokud ne, postupujte takto:

### <a name="if-you-already-have-a-site-to-site-virtual-network-but-it-has-a-static-policy-based-routing-gateway"></a>Pokud už máte virtuální síť Site-to-Site, ale obsahuje statické směrování brány (zásadové):
1. Změňte typ brány na dynamické směrování. Multi-Site VPN vyžaduje (označované také jako založené na směrování) brány dynamického směrování. Chcete-li změnit váš typ brány, budete muset nejdřív odstraňte existující bránu a potom vytvořte novou.
2. Konfigurace nové brány a vytvoření tunelu VPN. Pokyny, pokyny najdete v tématu [zadání SKU a síť VPN typu](vpn-gateway-howto-site-to-site-classic-portal.md#sku). Ujistěte se, že zadáte typ směrování jako 'Dynamic'.

### <a name="if-you-dont-have-a-site-to-site-virtual-network"></a>Pokud nemáte virtuální síť Site-to-Site:
1. Vytvoření virtuální sítě Site-to-Site pomocí těchto pokynů: [vytvoření virtuální sítě s připojením VPN typu Site-to-Site](vpn-gateway-site-to-site-create.md).  
2. Konfigurace brány dynamického směrování podle těchto pokynů: [konfigurovat bránu VPN](vpn-gateway-configure-vpn-gateway-mp.md). Je potřeba vybrat možnost **s dynamickým směrováním** pro váš typ brány.

## <a name="export"></a>2. Exportovat soubor konfigurace sítě
Spuštěním následujícího příkazu exportujte konfigurační soubor sítě Azure. Můžete změnit umístění souboru pro export do jiného umístění v případě potřeby.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

## <a name="3-open-the-network-configuration-file"></a>3. Otevřete soubor konfigurace sítě
Otevřete soubor konfigurace sítě, který jste stáhli v předchozím kroku. Pomocí editoru xml, který vás zajímá. Soubor by měl vypadat nějak takto:

        <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
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

## <a name="4-add-multiple-site-references"></a>4. Přidání více odkazů
Při přidání nebo odebrání referenční informace o lokalitě, budete provádět ConnectionsToLocalNetwork/LocalNetworkSiteRef změny konfigurace. Přidání nových triggerů odkaz na místní lokality Azure za účelem vytvoření nové tunelové propojení. V následujícím příkladu je konfigurace sítě pro připojení k jedné lokalitě. Po provedení změn uložte soubor.

```xml
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

Chcete-li přidat odkazy na další lokality (vytvořit konfiguraci více lokalit), jednoduše přidat další řádky "LocalNetworkSiteRef", jak je znázorněno v následujícím příkladu:

```xml
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
      <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

## <a name="5-import-the-network-configuration-file"></a>5. Importujte soubor konfigurace sítě
Importujte soubor konfigurace sítě. Při importování tohoto souboru se změnami se přidá nový tunelů. Tunely budou používat dynamické brány, kterou jste vytvořili dříve. Prostředí PowerShell můžete použít k importu souboru.

## <a name="6-download-keys"></a>6. Stáhnout klíče
Po přidání nové tunely použijte rutinu Powershellu: Get-AzureVNetGatewayKey"zobrazíte předsdílené klíče protokolu IPsec/IKE pro každé tunelové propojení.

Příklad:

```powershell
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"
```

Pokud dáváte přednost, můžete také použít *získat virtuální sítě brány sdíleného klíče* rozhraní REST API k získání předsdílené klíče.

## <a name="7-verify-your-connections"></a>7. Zkontrolujte svá připojení
Zkontrolujte stav tunelového propojení Multi-Site. Po stažení klíče pro každé tunelové propojení, budete chtít ověřit připojení. Pomocí "Get-AzureVnetConnection" můžete získat seznam tunelových propojení virtuální sítě, jak je znázorněno v následujícím příkladu. Síť VNet1 je název sítě VNet.

```powershell
Get-AzureVnetConnection -VNetName VNET1
```

Příklad vrácené:

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

## <a name="next-steps"></a>Další postup

Další informace o branách VPN najdete v tématu [informace o branách VPN](vpn-gateway-about-vpngateways.md).
