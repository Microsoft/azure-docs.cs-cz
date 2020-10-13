---
title: 'Azure VPN Gateway: Konfigurace vynuceného tunelování – připojení typu Site-to-site: Classic'
description: Naučte se konfigurovat vynucené tunelování pro virtuální sítě vytvořené pomocí modelu nasazení Classic.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 10/08/2020
ms.author: cherylmc
ms.openlocfilehash: 94a5459ade634f6a1de029808aa6bad4d16b9a5d
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91874625"
---
# <a name="configure-forced-tunneling-using-the-classic-deployment-model"></a>Konfigurace vynuceného tunelování pomocí modelu nasazení Classic

Vynucené tunelování umožňuje přesměrování nebo „vynucení“ směrování veškerého provozu vázaného na internet zpět do místního umístění prostřednictvím tunelu VPN typu site-to-site pro kontrolu a auditování. Toto je zásadní požadavek na zabezpečení pro většinu podnikových zásad IT. Bez vynuceného tunelování se internetový provoz z vašich virtuálních počítačů v Azure vždycky přesměruje z síťové infrastruktury Azure přímo na Internet bez možnosti, která vám umožní kontrolovat nebo auditovat provoz. Neoprávněný přístup k Internetu může potenciálně vést k odhalení informací nebo jiným typům porušení zabezpečení.

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Tento článek vás provede konfigurací vynuceného tunelování pro virtuální sítě vytvořené pomocí modelu nasazení Classic. Vynucené tunelování se dá nakonfigurovat pomocí PowerShellu, ne přes portál. Pokud chcete pro model nasazení Správce prostředků nakonfigurovat vynucené tunelování, vyberte Správce prostředků článek z následujícího rozevíracího seznamu:

> [!div class="op_single_selector"]
> * [PowerShell – Classic](vpn-gateway-about-forced-tunneling.md)
> * [PowerShell – Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 
> 

## <a name="requirements-and-considerations"></a>Požadavky a předpoklady
Vynucené tunelování v Azure se konfiguruje prostřednictvím uživatelsky definovaných tras (UDR) virtuální sítě. Přesměrování provozu do místní lokality se vyjádří jako výchozí trasa k bráně Azure VPN. Následující část uvádí aktuální omezení směrovací tabulky a tras pro Azure Virtual Network:

* Každá podsíť virtuální sítě má vestavěnou systémovou směrovací tabulku. Tabulka směrování systému má následující tři skupiny tras:

  * **Trasy místní virtuální sítě:** Přímo k cílovým virtuálním počítačům ve stejné virtuální síti.
  * **Místní trasy:** K bráně Azure VPN Gateway.
  * **Výchozí trasa:** Přímo na Internet. Pakety určené k privátním IP adresám, které nejsou pokryté předchozími dvěma trasami, budou vyřazeny.
* S vydáním uživatelsky definovaných tras můžete vytvořit směrovací tabulku pro přidání výchozí trasy a potom přidružit směrovací tabulku k vašim virtuálním podsítím, aby bylo možné v těchto podsítích povolit vynucené tunelové propojení.
* Musíte nastavit výchozí lokalitu mezi místními lokalitami, které jsou připojené k virtuální síti.
* Vynucené tunelování musí být přidruženo k virtuální síti s bránou VPN s dynamickým směrováním (ne se statickou bránou).
* Vynucené tunelování ExpressRoute není nakonfigurované prostřednictvím tohoto mechanismu, ale místo toho je povolené inzerováním výchozí trasy prostřednictvím relací partnerských vztahů protokolu BGP ExpressRoute. Další informace najdete v [dokumentaci k ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) .

## <a name="configuration-overview"></a>Přehled konfigurace
V následujícím příkladu není podsíť front-endu vynucená tunelem. Úlohy v podsíti front-endu mohou nadále přijímat a reagovat na žádosti zákazníků přímo z Internetu. Podsítě střední úrovně a back-end jsou vynucené tunelování. Všechna odchozí připojení z těchto dvou podsítí na Internet se vynutí nebo přesměrují zpátky na místní lokalitu prostřednictvím jednoho z tunelových propojení VPN S2S.

Díky tomu můžete omezit a prozkoumat přístup k Internetu z virtuálních počítačů nebo cloudových služeb v Azure a zároveň pokračovat v povolení vaší vícevrstvé architektury služeb. Vynucené tunelování můžete také použít pro celé virtuální sítě, pokud ve svých virtuálních sítích neexistují žádné úlohy s přístupem k Internetu.

![Vynucené tunelování](./media/vpn-gateway-about-forced-tunneling/forced-tunnel.png)

## <a name="before-you-begin"></a>Než začnete
Před zahájením konfigurace ověřte, zda máte následující:

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).
* Nakonfigurovaná virtuální síť. 
* [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

### <a name="to-sign-in"></a>Pro přihlášení

1. Otevřete konzolu PowerShellu se zvýšenými právy. Připojte se k účtu pomocí následujícího příkladu:

   ```powershell
   Add-AzureAccount
   ```

## <a name="configure-forced-tunneling"></a>Konfigurace vynuceného tunelování
Následující postup vám pomůže určit vynucené tunelování pro virtuální síť. Konfigurační kroky odpovídají konfiguračnímu souboru sítě virtuální sítě.

```xml
<VirtualNetworkSite name="MultiTier-VNet" Location="North Europe">
     <AddressSpace>
      <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Frontend">
            <AddressPrefix>10.1.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Midtier">
            <AddressPrefix>10.1.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Backend">
            <AddressPrefix>10.1.2.0/23</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.1.200.0/28</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="DefaultSiteHQ">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch1">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch2">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch3">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSite>
```

V tomto příkladu má virtuální síť s více vrstvami tři podsítě: front-end, Midtier a podsítě back-end se čtyřmi připojeními mezi různými místy: DefaultSiteHQ a tři větve. 

Tento postup nastaví jako výchozí připojení k vynucenému tunelování "DefaultSiteHQ" a nakonfiguruje podsítě Midtier a back-endu tak, aby používaly vynucené tunelování.

1. Vytvořte směrovací tabulku. Pomocí následující rutiny vytvořte tabulku směrování.

   ```powershell
   New-AzureRouteTable –Name "MyRouteTable" –Label "Routing Table for Forced Tunneling" –Location "North Europe"
   ```

2. Přidejte výchozí trasu do směrovací tabulky. 

   Následující příklad přidá výchozí trasu do směrovací tabulky vytvořené v kroku 1. Všimněte si, že jedinou podporovanou trasou je cílová předpona "0.0.0.0/0" na přesměrování "VPNGateway".

   ```powershell
   Get-AzureRouteTable -Name "MyRouteTable" | Set-AzureRoute –RouteTable "MyRouteTable" –RouteName "DefaultRoute" –AddressPrefix "0.0.0.0/0" –NextHopType VPNGateway
   ```

3. Přidružte směrovací tabulku k podsítím. 

   Po vytvoření směrovací tabulky a přidání trasy použijte následující příklad k přidání nebo přidružení směrovací tabulky k podsíti virtuální sítě. V příkladu se přidá směrovací tabulka "MyRouteTable" do Midtier a back-endu podsítí virtuální sítě VNet s více vrstvami.

   ```powershell
   Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Midtier" -RouteTableName "MyRouteTable"
   Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Backend" -RouteTableName "MyRouteTable"
   ```

4. Přiřaďte výchozí web pro vynucené tunelování. 

   V předchozím kroku ukázkové skripty rutiny vytvořily směrovací tabulku a přidružil tabulku směrování do dvou z podsítí virtuální sítě. Zbývajícím krokem je výběr místní lokality mezi připojeními k více lokalitám virtuální sítě jako výchozí lokalitou nebo tunelem.

   ```powershell
   $DefaultSite = @("DefaultSiteHQ")
   Set-AzureVNetGatewayDefaultSite –VNetName "MultiTier-VNet" –DefaultSite "DefaultSiteHQ"
   ```

## <a name="additional-powershell-cmdlets"></a>Další rutiny PowerShellu
### <a name="to-delete-a-route-table"></a>Odstranění směrovací tabulky

```powershell
Remove-AzureRouteTable -Name <routeTableName>
```
  
### <a name="to-list-a-route-table"></a>Vypsání směrovací tabulky

```powershell
Get-AzureRouteTable [-Name <routeTableName> [-DetailLevel <detailLevel>]]
```

### <a name="to-delete-a-route-from-a-route-table"></a>Postup odstranění trasy z směrovací tabulky

```powershell
Remove-AzureRouteTable –Name <routeTableName>
```

### <a name="to-remove-a-route-from-a-subnet"></a>Postup odebrání trasy z podsítě

```powershell
Remove-AzureSubnetRouteTable –VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-list-the-route-table-associated-with-a-subnet"></a>Výpis směrovací tabulky přidružené k podsíti

```powershell
Get-AzureSubnetRouteTable -VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-remove-a-default-site-from-a-vnet-vpn-gateway"></a>Postup odebrání výchozí lokality z virtuální sítě VPN Gateway

```powershell
Remove-AzureVnetGatewayDefaultSite -VNetName <virtualNetworkName>
```
