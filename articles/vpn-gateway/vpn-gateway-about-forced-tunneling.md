---
title: 'Nakonfigurujte vynucené tunelování pro připojení Azure Site-to-Site: classic | Microsoft Docs'
description: Postup přesměrování nebo "Vynutit" veškerý provoz vázaný na Internet na vaše místní umístění.
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
ms.openlocfilehash: 79bf6892c823da282c3e763921e830f986419854
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2017
ms.locfileid: "23883520"
---
# <a name="configure-forced-tunneling-using-the-classic-deployment-model"></a>Konfigurace vynuceného tunelování pomocí modelu nasazení Classic

Vynucené tunelování vám umožní přesměrování nebo "Vynutit" veškerý provoz vázaný na Internet zpět na místní umístění prostřednictvím tunelu Site-to-Site VPN pro kontrolu a auditování. Požadavek kritické zabezpečení pro většinu organizace IT zásad. Bez vynucené tunelování, se internetový provoz z virtuálních počítačů v Azure procházení od Azure síťové infrastruktury přímo se k Internetu, bez možnosti a umožní vám na svoji provoz vždy. Neoprávněný přístup k Internetu může potenciálně vést k informacím nebo jiné typy narušení zabezpečení.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Tento článek vás provede procesem konfigurace vynucené tunelování pro virtuální sítě vytvořené pomocí modelu nasazení classic. Vynucené tunelování se dá konfigurovat pomocí prostředí PowerShell, ne prostřednictvím portálu. Pokud chcete konfigurovat vynucené tunelování pro model nasazení Resource Manager, vyberte z rozevíracího seznamu následující klasické článku:

> [!div class="op_single_selector"]
> * [PowerShell – Classic](vpn-gateway-about-forced-tunneling.md)
> * [PowerShell – Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 
> 

## <a name="requirements-and-considerations"></a>Požadavky a důležité informace
Vynucené tunelování v Azure je nakonfigurován pomocí virtuální síti trasy definované uživatelem (UDR). Přesměrování přenosů na místní web je vyjádřen jako výchozí směrování k bráně Azure VPN. V následující části jsou uvedené aktuální omezení směrovací tabulku a trasy pro virtuální síť Azure:

* Každá podsíť virtuální sítě má integrovanou, směrovací tabulky systému. Systémovou tabulku směrování má tyto tři skupiny tras:

  * **Místní virtuální síť trasy:** přímo do cílového umístění virtuálních počítačů ve stejné virtuální síti.
  * **Místní trasy:** k Azure VPN gateway.
  * **Výchozí trasu:** přímo k Internetu. Dojde ke ztrátě paketů určené na privátní IP adresy, které nejsou pokryty předchozí dva trasy.
* S vydáním trasy definované uživatelem můžete vytvořit směrovací tabulku, která chcete přidat výchozí trasu a pak přidružit do směrovací tabulky pro vaši virtuální síť podsítí povolit vynucené tunelování na těchto podsítí.
* Budete muset nastavit "výchozí web" mezi místní lokality mezi různými místy připojený k virtuální síti.
* Vynucené tunelování musí být přidruženy k virtuální síti, která má dynamické směrování brána sítě VPN (není statická brána).
* ExpressRoute vynuceného tunelování přes tento mechanismus není nakonfigurovaná, ale místo toho je ve inzeruje výchozí trasu prostřednictvím relace partnerského vztahu ExpressRoute BGP povolen. Podrobnosti najdete [dokumentace ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) Další informace.

## <a name="configuration-overview"></a>Přehled konfigurace
V následujícím příkladu tunelovým propojením front-endu podsíť není vynutit. Úlohy v podsíť Frontend můžete nadále přijímat a reagovat na požadavky zákazníků z Internetu přímo. Střední vrstvě a back-end podsítě, vynuceně přesunuty tunelového propojení. Odchozí připojení k Internetu tyto dvě podsítě bude vynutit nebo přesměrován zpět na místní web prostřednictvím jednoho tunelu S2S VPN.

To vám umožňuje omezit a kontrolovat přístup k Internetu z virtuálních počítačů nebo cloudových služeb v Azure, můžete nadále povolit vaší architektury víceúrovňová služba vyžaduje. Také můžete použít vynucené tunelování na celý virtuální sítě Pokud nejsou žádná internetového zatížení ve virtuálních sítích.

![Vynucené tunelování](./media/vpn-gateway-about-forced-tunneling/forced-tunnel.png)

## <a name="before-you-begin"></a>Než začnete
Před zahájením konfigurace ověřte, zda máte následující.

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).
* Nakonfigurované virtuální sítě. 
* Nejnovější verzi rutin prostředí Azure PowerShell. Další informace o instalaci rutin prostředí PowerShell najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview).

## <a name="configure-forced-tunneling"></a>Konfigurace vynuceného tunelování
Následující postup vám pomůže určit vynucené tunelování pro virtuální síť. Postup konfigurace odpovídají konfiguračního souboru sítě VNet.

```
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

V tomto příkladu virtuální sítě "MultiTier-VNet, má tři podsítě: 'Front-endu', 'Midtier' a 'Back-end' podsítě s připojeními čtyři mezi více místy: 'DefaultSiteHQ' a tři větve. 

Kroky DefaultSiteHQ nastavit jako výchozí připojení lokality pro vynuceného tunelování a nakonfigurovat Midtier a back-end podsítě používat vynucené tunelování.

1. Umožňuje vytvořte směrovací tabulku. Chcete-li vytvořit směrovací tabulku použijte následující rutinu.

  ```powershell
  New-AzureRouteTable –Name "MyRouteTable" –Label "Routing Table for Forced Tunneling" –Location "North Europe"
  ```
2. Přidejte výchozí trasy do směrovací tabulky. 

  Následující příklad přidá výchozí trasy do směrovací tabulky vytvořili v kroku 1. Všimněte si, že je podporována pouze trasy, která je předpona cílové "0.0.0.0/0" na "Brána VPN" dalšího segmentu.

  ```powershell
  Get-AzureRouteTable -Name "MyRouteTable" | Set-AzureRoute –RouteTable "MyRouteTable" –RouteName "DefaultRoute" –AddressPrefix "0.0.0.0/0" –NextHopType VPNGateway
  ```
3. Přidružte do směrovací tabulky k podsítím. 

  Po vytvoření směrovací tabulku a přidat trasu, použijte následující příklad pro přidání nebo přidružení tabulku směrování pro podsíť virtuální sítě. V příkladu přidá směrovací tabulka "MyRouteTable" do podsítě virtuální sítě MultiTier-VNet Midtier a back-end.

  ```powershell
  Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Midtier" -RouteTableName "MyRouteTable"
  Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Backend" -RouteTableName "MyRouteTable"
  ```
4. Přiřadíte výchozí web pro vynucené tunelování. 

  V předchozím kroku ukázkové skripty rutiny vytvořit směrovací tabulky a související směrovací tabulka ke dvěma z podsítí virtuální sítě. Zbývající krokem je vybrat jako výchozí web nebo tunelové propojení místní lokality mezi více lokalit připojení virtuální sítě.

  ```powershell
  $DefaultSite = @("DefaultSiteHQ")
  Set-AzureVNetGatewayDefaultSite –VNetName "MultiTier-VNet" –DefaultSite "DefaultSiteHQ"
  ```

## <a name="additional-powershell-cmdlets"></a>Další rutiny prostředí PowerShell
### <a name="to-delete-a-route-table"></a>Chcete-li odstranit tabulku směrování

```powershell
Remove-AzureRouteTable -Name <routeTableName>
```
  
### <a name="to-list-a-route-table"></a>Do seznamu směrovací tabulku

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

### <a name="to-list-the-route-table-associated-with-a-subnet"></a>Seznam směrovací tabulka spojené s podsítí

```powershell
Get-AzureSubnetRouteTable -VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-remove-a-default-site-from-a-vnet-vpn-gateway"></a>Odebrání výchozí web brány virtuální sítě VPN

```powershell
Remove-AzureVnetGatewayDefaultSite -VNetName <virtualNetworkName>
```