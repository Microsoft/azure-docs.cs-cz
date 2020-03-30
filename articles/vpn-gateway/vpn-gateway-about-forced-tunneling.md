---
title: 'Brána Azure VPN: Konfigurace vynuceného tunelového propojení – připojení site-to-site: klasické'
description: Jak přesměrovat nebo vynutit veškerý internetový provoz zpět do místního umístění.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/01/2017
ms.author: cherylmc
ms.openlocfilehash: fe06257127ff352f68fb27d3507cee0229e31498
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201573"
---
# <a name="configure-forced-tunneling-using-the-classic-deployment-model"></a>Konfigurace vynuceného tunelování pomocí modelu nasazení Classic

Vynucené tunelování umožňuje přesměrování nebo „vynucení“ směrování veškerého provozu vázaného na internet zpět do místního umístění prostřednictvím tunelu VPN typu site-to-site pro kontrolu a auditování. Jedná se o kritický požadavek zabezpečení pro většinu podnikových zásad IT. Bez vynuceného tunelového propojení bude internetový provoz z vašich virtuálních aplikací v Azure vždy přecházet ze síťové infrastruktury Azure přímo do Internetu, aniž by vám umožnil kontrolovat nebo auditovat provoz. Neoprávněný přístup k Internetu může potenciálně vést ke zveřejnění informací nebo k jiným typům narušení zabezpečení.

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Tento článek vás provede konfigurací vynuceného tunelového propojení pro virtuální sítě vytvořené pomocí klasického modelu nasazení. Vynucené tunelové propojení lze nakonfigurovat pomocí prostředí PowerShell, nikoli prostřednictvím portálu. Pokud chcete nakonfigurovat vynucené tunelové propojení pro model nasazení Správce prostředků, vyberte článek Správce prostředků z následujícího rozevíracího seznamu:

> [!div class="op_single_selector"]
> * [PowerShell – Classic](vpn-gateway-about-forced-tunneling.md)
> * [PowerShell – Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 
> 

## <a name="requirements-and-considerations"></a>Požadavky a úvahy
Vynucené tunelové propojení v Azure je nakonfigurované prostřednictvím uživatelem definovaných tras virtuální sítě (UDR). Přesměrování provozu na místní web se vyjadřuje jako výchozí trasa do brány Azure VPN. V následující části je uvedeno aktuální omezení směrovací tabulky a tras pro virtuální síť Azure:

* Každá podsíť virtuální sítě má integrovanou systémovou směrovací tabulku. Systémová směrovací tabulka obsahuje následující tři skupiny tras:

  * **Místní trasy virtuální sítě:** Přímo do cílových virtuálních počítačů ve stejné virtuální síti.
  * **Místní trasy:** K bráně Azure VPN.
  * **Výchozí trasa:** Přímo na internet. Pakety určené na soukromé IP adresy, které nejsou zahrnuty v předchozích dvou trasách, budou zrušeny.
* S vydáním uživatelem definovaných tras můžete vytvořit směrovací tabulku pro přidání výchozí trasy a pak přidružit směrovací tabulku k podsíti virtuální sítě, abyste povolili vynucené tunelování v těchto podsítích.
* Je třeba nastavit "výchozí lokalitu" mezi místními weby mezi místními prostory připojenými k virtuální síti.
* Vynucené tunelové propojení musí být přidruženo k virtuální síti, která má dynamickou bránu VPN směrování (ne statickou bránu).
* Vynucené tunelové propojení ExpressRoute není nakonfigurováno prostřednictvím tohoto mechanismu, ale místo toho je povoleno inzerováním výchozí trasy prostřednictvím relací partnerského vztahu ExpressRoute BGP. Další informace naleznete v [dokumentaci expressroute.](https://azure.microsoft.com/documentation/services/expressroute/)

## <a name="configuration-overview"></a>Přehled konfigurace
V následujícím příkladu není podsíť front-endu vynuceně tunelována. Úlohy v podsíti front-endu mohou nadále přijímat a odpovídat na požadavky zákazníků z Internetu přímo. Podsítě Střední vrstvy a Back-end jsou vynuceně tunelovány. Všechna odchozí připojení z těchto dvou podsítí do Internetu budou vynucena nebo přesměrována zpět do místního webu prostřednictvím jednoho z tunelových propojení S2S VPN.

To vám umožní omezit a kontrolovat přístup k Internetu z vašich virtuálních počítačů nebo cloudových služeb v Azure a zároveň pokračovat v povolení požadované architektury vícevrstvých služeb. Vynucené tunelové propojení můžete také použít pro celé virtuální sítě, pokud ve virtuálních sítích nejsou žádné úlohy pro připojení k Internetu.

![Vynucené tunelování](./media/vpn-gateway-about-forced-tunneling/forced-tunnel.png)

## <a name="before-you-begin"></a>Než začnete
Před zahájením konfigurace ověřte, zda máte následující:

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).
* Nakonfigurovaná virtuální síť. 
* [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

### <a name="to-sign-in"></a>Přihlášení

1. Otevřete konzolu PowerShell se zvýšenými právy. Chcete-li přepnout na správu služeb, použijte tento příkaz:

   ```powershell
   azure config mode asm
   ```
2. Připojte se ke svému účtu. Připojení vám usnadní následující ukázka:

   ```powershell
   Add-AzureAccount
   ```

## <a name="configure-forced-tunneling"></a>Konfigurace vynuceného tunelování
Následující postup vám pomůže určit vynucené tunelové propojení pro virtuální síť. Kroky konfigurace odpovídají konfiguračnímu souboru sítě virtuální sítě.

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

V tomto příkladu virtuální síť "MultiTier-VNet" má tři podsítě: "Frontend", "Midtier" a "Backend" podsítě, se čtyřmi připojeními mezi prostory: "DefaultSiteHQ" a tři větve. 

Kroky nastaví "DefaultSiteHQ" jako výchozí připojení lokality pro vynucené tunelové propojení a nakonfigurují podsítě Midtier a Backend tak, aby používaly vynucené tunelové propojení.

1. Vytvořte směrovací tabulku. K vytvoření směrovací tabulky použijte následující rutinu.

   ```powershell
   New-AzureRouteTable –Name "MyRouteTable" –Label "Routing Table for Forced Tunneling" –Location "North Europe"
   ```

2. Přidejte výchozí trasu do směrovací tabulky. 

   Následující příklad přidá výchozí trasu do směrovací tabulky vytvořené v kroku 1. Všimněte si, že pouze podporovaná trasa je cílová předpona "0.0.0.0/0" na "VPNGateway" NextHop.

   ```powershell
   Get-AzureRouteTable -Name "MyRouteTable" | Set-AzureRoute –RouteTable "MyRouteTable" –RouteName "DefaultRoute" –AddressPrefix "0.0.0.0/0" –NextHopType VPNGateway
   ```

3. Přidružte směrovací tabulku k podsítím. 

   Po vytvoření směrovací tabulky a přidání trasy přidejte nebo přidružte směrovací tabulku k podsíti virtuální sítě. Příklad přidá směrovací tabulku "MyRouteTable" do podsítí Midtier a Back-end v síti VNet MultiTier-VNet.

   ```powershell
   Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Midtier" -RouteTableName "MyRouteTable"
   Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Backend" -RouteTableName "MyRouteTable"
   ```

4. Přiřaďte výchozí lokalitu pro vynucené tunelové propojení. 

   V předchozím kroku ukázkové skripty rutiny vytvořily směrovací tabulku a přidružišovaly směrovací tabulku ke dvěma podsítím virtuální sítě. Zbývajícím krokem je výběr místní lokality mezi více lokalitami připojení virtuální sítě jako výchozí lokality nebo tunelového propojení.

   ```powershell
   $DefaultSite = @("DefaultSiteHQ")
   Set-AzureVNetGatewayDefaultSite –VNetName "MultiTier-VNet" –DefaultSite "DefaultSiteHQ"
   ```

## <a name="additional-powershell-cmdlets"></a>Další rutiny prostředí PowerShell
### <a name="to-delete-a-route-table"></a>Odstranění směrovací tabulky

```powershell
Remove-AzureRouteTable -Name <routeTableName>
```
  
### <a name="to-list-a-route-table"></a>Zobrazení seznamu směrovací tabulky

```powershell
Get-AzureRouteTable [-Name <routeTableName> [-DetailLevel <detailLevel>]]
```

### <a name="to-delete-a-route-from-a-route-table"></a>Odstranění trasy z tabulky postupu

```powershell
Remove-AzureRouteTable –Name <routeTableName>
```

### <a name="to-remove-a-route-from-a-subnet"></a>Odebrání trasy z podsítě

```powershell
Remove-AzureSubnetRouteTable –VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-list-the-route-table-associated-with-a-subnet"></a>Seznam směrovací tabulky přidružené k podsíti

```powershell
Get-AzureSubnetRouteTable -VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-remove-a-default-site-from-a-vnet-vpn-gateway"></a>Odebrání výchozího webu z brány VPN virtuální sítě

```powershell
Remove-AzureVnetGatewayDefaultSite -VNetName <virtualNetworkName>
```
