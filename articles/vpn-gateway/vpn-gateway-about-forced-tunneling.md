---
title: 'Konfigurace vynuceného tunelování pro připojení k Azure Site-to-Site: classic | Dokumentace Microsoftu'
description: Jak přesměrování nebo "Vynutit" veškerý provoz směřující na Internet zpět do místního umístění.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: 5c0177f1-540c-4474-9b80-f541fa44240b
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/01/2017
ms.author: cherylmc
ms.openlocfilehash: cf566811f1e5fe7fde20d148e68417acf6d42f54
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53073818"
---
# <a name="configure-forced-tunneling-using-the-classic-deployment-model"></a>Konfigurace vynuceného tunelování pomocí modelu nasazení Classic

Vynucené tunelování umožňuje přesměrování nebo "Vynutit" veškerý provoz směřující na Internet zpět do místního umístění prostřednictvím tunelu VPN typu Site-to-Site pro kontrolu a auditování. Toto je důležité zabezpečení požadavek pro většinu podnikových IT zásady. Bez vynucené tunelování, se internetový provoz z virtuálních počítačů v Azure přechod z Azure síťovou infrastrukturu přímo na Internetu, bez možnosti a umožňuje tak kontrolovat nebo auditování provozu vždy. Neoprávněný přístup k Internetu může potenciálně vést k zpřístupnění informací nebo jiných druhů porušení zabezpečení.

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Tento článek vás provede konfigurací vynuceného tunelového propojení virtuální sítě vytvořené pomocí modelu nasazení classic. Vynucené tunelování se dá konfigurovat pomocí prostředí PowerShell, na portálu. Pokud chcete nakonfigurovat vynucené tunelování pro model nasazení Resource Manager, vyberte z rozevíracího seznamu následující klasické článku:

> [!div class="op_single_selector"]
> * [PowerShell – Classic](vpn-gateway-about-forced-tunneling.md)
> * [PowerShell – Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 
> 

## <a name="requirements-and-considerations"></a>Požadavky a předpoklady
Vynucené tunelování v Azure se konfiguruje prostřednictvím virtuální sítě trasy definované uživatelem (UDR). Přesměrování přenosů do místní lokality je vyjádřena jako výchozí trasu pro bránu Azure VPN. V následující části jsou uvedené aktuální omezení směrovací tabulky a trasy pro služby Azure Virtual Network:

* Každé podsíti virtuální sítě má integrované, směrovací tabulky systému. Systémovou tabulku směrování má následující tři skupiny tras:

  * **Místní virtuální sítě trasy:** přímo do cílového umístění virtuálních počítačů ve stejné virtuální síti.
  * **Místní trasy:** pro Azure VPN gateway.
  * **Výchozí trasa:** přímo k Internetu. Pakety směřující na privátní IP adresy není pokrytá předchozí dvě trasy se zahodí.
* S vydáním trasy definované uživatelem můžete vytvořit směrovací tabulku, která chcete přidat výchozí trasa a přidružte směrovací tabulky do vaší virtuální síti tento počet podsítí: Povolit vynucené tunelování na těchto podsítí.
* Budete muset nastavit "výchozí web" mezi místy místní servery připojené k virtuální síti.
* Vynucené tunelování musí být přidružen virtuální síť, která má bránu dynamického směrování VPN (není statická brána).
* ExpressRoute se vynucené tunelování přes tento mechanismus není nakonfigurovaná, ale místo toho zajišťuje inzeruje výchozí trasu prostřednictvím relací vytvoření partnerského vztahu protokolu BGP ExpressRoute. Podrobnosti najdete [dokumentace ke službě ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) Další informace.

## <a name="configuration-overview"></a>Přehled konfigurace
V následujícím příkladu front-endové podsítě není vynucené tunelové propojení. Úlohy ve front-endové podsíti můžete nadále přijímat a reagovat na požadavky zákazníků z Internetu přímo. Střední vrstvě a back-endové podsítě jsou vynuceného tunelového propojení. Odchozí připojení k Internetu, tyto dvě podsítě se vynucené nebo přesměrován zpět do místní lokality přes jeden tunel VPN s2s.

Můžete omezit a kontrolovat přístup k Internetu z vašich virtuálních počítačů nebo cloudových služeb v Azure a přitom dál povolit architektury víceúrovňová služba vyžaduje. Můžete také provést vynucené tunelové propojení pro celý virtuální sítě. Pokud nejsou žádné úlohy přístupem k Internetu ve virtuálních sítích.

![Vynucené tunelování](./media/vpn-gateway-about-forced-tunneling/forced-tunnel.png)

## <a name="before-you-begin"></a>Před zahájením
Před zahájením konfigurace ověřte, zda máte následující.

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).
* Nakonfigurované virtuální sítě. 
* Nejnovější verzi rutin Azure Powershellu. Další informace o instalaci rutin prostředí PowerShell najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview).

## <a name="configure-forced-tunneling"></a>Konfigurace vynuceného tunelování
Následující postup vám pomůže určit vynucené tunelové propojení pro virtuální síť. Postup konfigurace odpovídají soubor konfigurace sítě VNet.

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

Virtuální síť "MultiTier virtuální sítě" v tomto příkladu má tři podsítě: "Frontend", "Midtier" a "Backend" podsítě s připojeními čtyři místy: "DefaultSiteHQ" a tři větve. 

Postup "DefaultSiteHQ" nastavit jako výchozí web připojení pro vynucené tunelování a nakonfigurovat Midtier a back-endové podsítě používat vynucené tunelování.

1. Vytvoření směrovací tabulky. Vytvoření směrovací tabulky pomocí následující rutiny.

   ```powershell
   New-AzureRouteTable –Name "MyRouteTable" –Label "Routing Table for Forced Tunneling" –Location "North Europe"
   ```

2. Přidání výchozí trasy do směrovací tabulky. 

   Následující příklad přidá výchozí trasy do směrovací tabulky vytvořené v kroku 1. Všimněte si, že podporovány pouze trasy je předpona cílové "0.0.0.0/0" k "VPNGateway" dalšího segmentu.

   ```powershell
   Get-AzureRouteTable -Name "MyRouteTable" | Set-AzureRoute –RouteTable "MyRouteTable" –RouteName "DefaultRoute" –AddressPrefix "0.0.0.0/0" –NextHopType VPNGateway
   ```

3. Přidružení směrovací tabulky podsítě. 

   Po vytvoření směrovací tabulky a přidá trasu, použijte následující příklad pro přidání nebo přidružení směrovací tabulky k podsíti virtuální sítě. Tento příklad přidá směrovací tabulka "MyRouteTable" Midtier a back-endové podsítě z MultiTier virtuálními sítěmi.

   ```powershell
   Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Midtier" -RouteTableName "MyRouteTable"
   Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Backend" -RouteTableName "MyRouteTable"
   ```

4. Přiřadíte výchozí lokalitu pro vynucené tunelování. 

   Ukázky skriptů pro rutinu v předchozím kroku, vytvořili směrovací tabulku a přidruženou směrovací tabulku do dvou podsítí virtuální sítě. Zbývající krokem je výběr místní sítě mezi připojení více webů ve virtuální síti jako výchozí web nebo tunelové propojení.

   ```powershell
   $DefaultSite = @("DefaultSiteHQ")
   Set-AzureVNetGatewayDefaultSite –VNetName "MultiTier-VNet" –DefaultSite "DefaultSiteHQ"
   ```

## <a name="additional-powershell-cmdlets"></a>Další rutiny prostředí PowerShell
### <a name="to-delete-a-route-table"></a>Chcete-li odstranit tabulku směrování

```powershell
Remove-AzureRouteTable -Name <routeTableName>
```
  
### <a name="to-list-a-route-table"></a>Do seznamu tabulku směrování

```powershell
Get-AzureRouteTable [-Name <routeTableName> [-DetailLevel <detailLevel>]]
```

### <a name="to-delete-a-route-from-a-route-table"></a>Chcete-li odstranit trasy z tabulky trasy

```powershell
Remove-AzureRouteTable –Name <routeTableName>
```

### <a name="to-remove-a-route-from-a-subnet"></a>Chcete-li odebrat trasu z podsítě

```powershell
Remove-AzureSubnetRouteTable –VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-list-the-route-table-associated-with-a-subnet"></a>Chcete-li vypsat směrovací tabulky přidružené k podsíti

```powershell
Get-AzureSubnetRouteTable -VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-remove-a-default-site-from-a-vnet-vpn-gateway"></a>Chcete-li odebrat výchozí web ze Brána virtuální sítě VPN

```powershell
Remove-AzureVnetGatewayDefaultSite -VNetName <virtualNetworkName>
```