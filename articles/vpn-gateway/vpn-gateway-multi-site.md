---
title: 'Připojit virtuální síť k více lokalitám pomocí brány sítě VPN a prostředí PowerShell: Classic | Microsoft Docs'
description: Připojte víc lokalit místní klasické virtuální sítě pomocí brány VPN.
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
ms.openlocfilehash: c0af4271df0e88354edb717b8d6f4c99ab29e573
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2018
ms.locfileid: "29399368"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection-classic"></a>Přidat připojení Site-to-Site k virtuální síti s existující připojení brány sítě VPN (klasické)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (Classic)](vpn-gateway-multi-site.md)
>
>

Tento článek vás provede pomocí prostředí PowerShell pro přidání připojení Site-to-Site (S2S) do brány VPN, který má existující připojení. Tento typ připojení se často označuje jako "s více servery" konfigurace. Postup v tomto článku se vztahuje na virtuální sítě vytvořené pomocí modelu nasazení classic (označovaný taky jako Service Management). Tyto kroky se nevztahují na konfigurace koexistujících připojení ExpressRoute nebo Site-to-Site.

### <a name="deployment-models-and-methods"></a>Modely a metody nasazení

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Tuto tabulku aktualizujeme jako nové články a další nástroje je k dispozici pro tuto konfiguraci. Když je článek k dispozici, jsme přímý odkaz na něj z této tabulky.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="about-connecting"></a>O připojení

Více místními servery můžete připojit k jedné virtuální sítě. To je zvláště atraktivní pro vytváření hybridní cloudové řešení. Vytvoření připojení více lokalit pro bránu virtuální sítě Azure je podobná vytváření jiná připojení Site-to-Site. Ve skutečnosti můžete použít existující bránu Azure VPN, tak dlouho, dokud brány je dynamický (trasové).

Pokud již máte statické brány připojené k virtuální síti, můžete změnit typ brány na dynamické, aniž by museli znovu sestavte virtuální sítě, aby mohla pojmout více lokalit. Před změnou typ směrování, ujistěte se, že vaše místní brána podporuje konfigurace sítě VPN založené na trasách.

![diagram Multi-Site](./media/vpn-gateway-multi-site/multisite.png "Multi-Site")

## <a name="points-to-consider"></a>Body, které je třeba zvážit

**Nebudete moci provádět změny do této virtuální sítě pomocí portálu.** Budete muset provést změny konfiguračního souboru sítě místo pomocí portálu. Pokud provedete změny v portálu, že budete přepsat nastavení odkaz na více lokalit pro tuto virtuální síť.

Má vaše znalosti pomocí konfiguračního souboru sítě podle času, po dokončení procesu více lokalit. Pokud máte více lidí pracujících na konfiguraci sítě, budete ale muset zajistěte, aby všichni ví o toto omezení. To neznamená, že nelze použít na portálu vůbec. Můžete ji všem ostatním, kromě provádění změn konfigurace do této konkrétní virtuální sítě.

## <a name="before-you-begin"></a>Než začnete

Před zahájením konfigurace, ověřte, zda máte následující:

* Kompatibilní hardware sítě VPN pro jednotlivé místní umístění. Zkontrolujte [o zařízeních VPN pro připojení k virtuální síti](vpn-gateway-about-vpn-devices.md) Chcete-li ověřit, zda je zařízení, které chcete použít něco, co se označuje jako kompatibilní.
* Zvenčí veřejnou IPv4 adresu IP pro každé zařízení VPN. IP adresa nesmí být umístěné za adres (NAT) Toto je požadavek.
* Budete potřebovat nainstalovat nejnovější verzi rutin Azure PowerShellu. Ujistěte se, že instalujete službu správy (SM) verze kromě verze Resource Manager. V tématu [postup instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview) Další informace.
* Někoho, kdo je znalosti v konfiguraci hardwaru sítě VPN. Budete muset silné znalosti o tom, jak nakonfigurovat zařízení VPN nebo pracovat s uživatelem, který nemá.
* Rozsahy IP adres, které chcete použít pro virtuální síť (Pokud jste již žádný nevytvořili).
* Rozsahy IP adres pro každou z místní sítě, které budete připojovat k. Budete potřebovat, abyste měli jistotu, že rozsahy IP adres pro každou z místní sítě, které se chcete připojit k nepřekrývají. Konfigurace odesílání bude odmítnout, jinak hodnota portálu nebo REST API.<br>Například pokud máte dvě místní sítě, že oba obsahují 10.2.3.0/24 rozsah adres IP a balíčků s cílovou adresou 10.2.3.3, Azure nebude vědět lokality, která chcete odeslat balíček, protože jsou překrývající se rozsahy adres. Aby se zabránilo problémům směrování, Azure vám neumožňuje nahrát konfiguračního souboru, který má překrývající se rozsahy.

## <a name="1-create-a-site-to-site-vpn"></a>1. Vytvoření S2S (Site-to-site) VPN
Pokud už máte sítě Site-to-Site VPN s brány dynamického směrování, skvěle! Můžete přejít k [exportovat nastavení konfigurace virtuální sítě](#export). Pokud ne, postupujte takto:

### <a name="if-you-already-have-a-site-to-site-virtual-network-but-it-has-a-static-policy-based-routing-gateway"></a>Pokud již máte virtuální síť Site-to-Site, ale má statické směrování brány (zásadové):
1. Změňte typ vaší brány na dynamické směrování. Síť VPN více lokalit vyžaduje (také označované jako založené na směrování) brány dynamického směrování. Chcete-li změnit váš typ brány, musíte nejprve odstraňte existující bránu, a poté vytvořit novou.
2. Konfigurace nové brány a vytvořte vaše tunelového připojení sítě VPN. Pokyny, pokyny najdete v tématu [zadejte typ SKU a síť VPN](vpn-gateway-howto-site-to-site-classic-portal.md#sku). Ujistěte se, že zadáte typ směrování jako "Dynamických".

### <a name="if-you-dont-have-a-site-to-site-virtual-network"></a>Pokud nemáte virtuální síť Site-to-Site:
1. Vytvoření svojí virtuální sítě Site-to-Site pomocí těchto pokynů: [vytvoření virtuální sítě pomocí připojení Site-to-Site VPN](vpn-gateway-site-to-site-create.md).  
2. Konfigurace brány dynamického směrování podle těchto pokynů: [konfigurovat bránu VPN](vpn-gateway-configure-vpn-gateway-mp.md). Je nutné vybrat **dynamické směrování** pro váš typ brány.

## <a name="export"></a>2. Exportovat konfigurační soubor sítě
Spuštěním následujícího příkazu exportujte konfiguračního souboru sítě Azure. Můžete změnit umístění souboru pro export do jiného umístění v případě potřeby.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

## <a name="3-open-the-network-configuration-file"></a>3. Otevření konfiguračního souboru sítě
Otevření konfiguračního souboru sítě, který jste stáhli v předchozím kroku. Pomocí editoru xml, který chcete. Soubor by měl vypadat podobně jako následující:

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

## <a name="4-add-multiple-site-references"></a>4. Přidání více odkazů na web
Když přidáváte nebo odebíráte lokality referenční informace, budete provedete změny konfigurace ConnectionsToLocalNetwork/LocalNetworkSiteRef. Přidání nové vyvolá odkaz místního webu Azure k vytvoření nové tunelové propojení. V následujícím příkladu je konfigurace sítě pro připojení k jedné lokalitě. Jakmile dokončíte provádění změny, uložte soubor.

```
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

Chcete-li přidat odkazy na další lokality (vytvořit konfiguraci s více servery), jednoduše přidat další řádky "LocalNetworkSiteRef", jak je znázorněno v následujícím příkladu:

```
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
      <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

## <a name="5-import-the-network-configuration-file"></a>5. Import konfiguračního souboru sítě
Importujte konfiguračního souboru sítě. Při importování tohoto souboru se změnami, bude přidána do nové tunelových propojení. Tunely použije dynamickou bránu, kterou jste vytvořili dříve. Můžete použít PowerShell k importu souboru.

## <a name="6-download-keys"></a>6. Stáhněte si klíče
Po přidání vaší nové tunely, použijte rutinu prostředí PowerShell 'Get-AzureVNetGatewayKey' získat předsdílené klíče protokolu IPsec/IKE pro každé tunelové propojení.

Příklad:

```powershell
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"
```

Pokud dáváte přednost, můžete také použít *získat virtuální sítě sdílený klíč brány* REST API získat předsdílených klíčů.

## <a name="7-verify-your-connections"></a>7. Zkontrolujte svá připojení
Zkontrolujte stav tunelového propojení více lokalit. Po stažení klíče pro každé tunelové propojení, budete chtít ověřit připojení. Použijte 'Get-AzureVnetConnection' k získání seznamu tunelových propojení virtuální sítě, jak je znázorněno v následujícím příkladu. VNet1 je název sítě vnet.

```powershell
Get-AzureVnetConnection -VNetName VNET1
```

Příklad návratový:

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
