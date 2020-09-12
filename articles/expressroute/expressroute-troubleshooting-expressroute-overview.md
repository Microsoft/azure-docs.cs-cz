---
title: 'Azure ExpressRoute: ověření připojení – Průvodce odstraňováním potíží'
description: Tato stránka poskytuje pokyny pro řešení potíží a ověření koncového připojení okruhu ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: troubleshooting
ms.date: 10/31/2019
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 5689bf60144cf3d66335eb4d77a96d29d8cdcc96
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89401737"
---
# <a name="verifying-expressroute-connectivity"></a>Ověření možností připojení ExpressRoute
Tento článek vám pomůže ověřit ExpressRoute konektivitu a řešit potíže. ExpressRoute rozšiřuje místní síť do cloudu Microsoftu přes soukromé připojení, které běžně usnadňuje poskytovatel připojení. Připojení ExpressRoute tradičně zahrnuje tři odlišné síťové zóny, a to takto:

-   Síť zákazníka
-   Síť poskytovatele
-   Datacenter Microsoftu

> [!NOTE]
> V modelu přímého připojení ExpressRoute (nabízený při 10/100 GB/s) se zákazníci můžou přímo připojit k portu směrovačů Microsoft Enterprise Edge (MSEE). V modelu přímého připojení proto existují jenom zóny zákazníka a sítě Microsoftu.
>


Účelem tohoto dokumentu je pomáhat uživateli zjistit, jestli a kde existuje problém s připojením. Kvůli vyřešení problému tak, aby bylo možné vyhodnotit podporu od příslušného týmu. Pokud je pro vyřešení problému nutná podpora Microsoftu, otevřete lístek podpory s [Podpora Microsoftu][Support].

> [!IMPORTANT]
> Tento dokument je určený k tomu, aby vám pomohla diagnostikovat a opravit jednoduché problémy. Není určena jako náhrada za podporu Microsoftu. Pokud problém nemůžete vyřešit pomocí poskytnutých pokynů, otevřete lístek podpory s [Podpora Microsoftu][Support] .
>
>

## <a name="overview"></a>Přehled
Následující diagram znázorňuje logické připojení sítě zákazníka k síti Microsoftu pomocí ExpressRoute.
[![1]][1]

V předchozím diagramu čísla označují klíčové síťové body. Na tyto síťové body se v tomto článku odkazuje v době podle jejich přidruženého čísla. V závislosti na modelu připojení ExpressRoute – ve společném umístění v cloudu Exchange, připojení k síti Ethernet typu Point-to-Point nebo Any-to-Any (IPVPN) – síťové body 3 a 4 můžou být přepínače (zařízení vrstvy 2) nebo směrovače (zařízení vrstvy 3). V modelu přímého připojení neexistují žádné síťové body 3 a 4; místo toho se službu zápis certifikátů (2) přímo připojí k směrovači msee prostřednictvím tmavého vlákna. Klíčové body sítě jsou znázorněny takto:

1.  Výpočetní zařízení zákazníka (například server nebo počítač)
2.  Zápis certifikátů: hraniční směrovače zákazníka 
3.  PEs (s přístupem CE): hraniční směrovače poskytovatele/přepínače, které jsou na hraničních směrovačích zákazníka. Označuje se jako PE-zápis certifikátů v tomto dokumentu.
4.  PEs (MSEE s přístupem): hraniční směrovače a přepínače poskytovatele, které jsou na směrovači msee. V tomto dokumentu se říká směrovači msee PE.
5.  Směrovači msee: směrovače ExpressRoute Microsoft Enterprise Edge (MSEE)
6.  Brána Virtual Network (VNet)
7.  Výpočetní zařízení ve virtuální síti Azure

Pokud se používá společné umístění cloudového systému Exchange, sítě Ethernet Point-to-Point nebo přímé připojení, vystavování partnerského vztahu protokolu BGP (2) pomocí směrovači msee (5). 

Pokud se používá model připojení any-to-Any (IPVPN), PE-směrovači msee (4) vytvoří partnerský vztah protokolu BGP s směrovači msee (5). PE – směrovači msee šíří trasy obdržené od Microsoftu zpátky k síti zákazníka prostřednictvím sítě poskytovatele služeb IPVPN.

> [!NOTE]
>Pro zajištění vysoké dostupnosti společnost Microsoft vytvoří plně redundantní paralelní připojení mezi páry směrovači msee (5) a PE-směrovači msee (4). Mezi zákaznickou sítí a párm zápisů na straně typu PE se taky doporučuje plně redundantní cesta k paralelní síti. Další informace o vysoké dostupnosti najdete v článku [navrhování pro zajištění vysoké dostupnosti pomocí ExpressRoute][HA] .
>
>

Následující postup popisuje logické kroky při řešení potíží s okruhem ExpressRoute:

* [Ověřit zřizování a stav okruhu](#verify-circuit-provisioning-and-state)
  
* [Ověřit konfiguraci partnerského vztahu](#validate-peering-configuration)
  
* [Ověřit protokol ARP](#validate-arp)
  
* [Ověření protokolu BGP a tras na MSEE](#validate-bgp-and-routes-on-the-msee)
  
* [Potvrzení toku přenosů](#confirm-the-traffic-flow)


## <a name="verify-circuit-provisioning-and-state"></a>Ověřit zřizování a stav okruhu
Zřízení okruhu ExpressRoute vytvoří redundantní připojení vrstvy 2 mezi zápisem a zápisem do PE – směrovači msee (2)/(4) a směrovači msee (5). Další informace o tom, jak vytvořit, upravit, zřídit a ověřit okruh ExpressRoute, najdete v článku [Vytvoření a úprava okruhu ExpressRoute][CreateCircuit].

>[!TIP]
>Klíč služby jednoznačně identifikuje okruh ExpressRoute. Pokud potřebujete pomoc od Microsoftu nebo od partnera ExpressRoute, abyste mohli řešit problémy s ExpressRoute, poskytněte klíč služby, který tento okruh snadno identifikuje.
>
>

### <a name="verification-via-the-azure-portal"></a>Ověření prostřednictvím Azure Portal
V Azure Portal otevřete okno okruh ExpressRoute. V části ![3][3] v okně jsou uvedeny ExpressRoute základy, jak je znázorněno na následujícím snímku obrazovky:

![4][4]    

Ve ExpressRoute Essentials indikuje *stav okruhu* stav okruhu na straně Microsoftu. *Stav poskytovatele* označuje, zda byl okruh *zřízen/není zřízen* na straně poskytovatele služby. 

Aby okruh ExpressRoute fungoval, musí být *povolen* *stav okruhu* a musí být *zřízen* *stav poskytovatele* .

> [!NOTE]
> Pokud je *stav okruhu* po konfiguraci okruhu ExpressRoute v nepovoleném stavu, kontaktujte [Podpora Microsoftu][Support]. Na druhé straně, pokud je *stav poskytovatele* v nezřízeném stavu, obraťte se na svého poskytovatele služeb.
>
>

### <a name="verification-via-powershell"></a>Ověření prostřednictvím PowerShellu
Pokud chcete zobrazit seznam všech okruhů ExpressRoute ve skupině prostředků, použijte následující příkaz:

```azurepowershell
Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG"
```

>[!TIP]
>Pokud hledáte název skupiny prostředků, můžete k ní získat výpis všech skupin prostředků ve vašem předplatném pomocí příkazu *Get-AzResourceGroup* .
>


Pokud chcete vybrat konkrétní okruh ExpressRoute ve skupině prostředků, použijte tento příkaz:

```azurepowershell
Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
```

Ukázková odpověď:

```output
Name                             : Test-ER-Ckt
ResourceGroupName                : Test-ER-RG
Location                         : westus2
Id                               : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt
Etag                             : W/"################################"
ProvisioningState                : Succeeded
Sku                              : {
                                    "Name": "Standard_UnlimitedData",
                                    "Tier": "Standard",
                                    "Family": "UnlimitedData"
                                    }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : Provisioned
ServiceProviderNotes             : 
ServiceProviderProperties        : {
                                    "ServiceProviderName": "****",
                                    "PeeringLocation": "******",
                                    "BandwidthInMbps": 100
                                    }
ServiceKey                       : **************************************
Peerings                         : []
Authorizations                   : []
```

Pokud si chcete ověřit, jestli je okruh ExpressRoute funkční, věnujte zvláštní pozornost následujícím polím:

```output
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : Provisioned
```

> [!NOTE]
> Pokud je *stav okruhu* po konfiguraci okruhu ExpressRoute v nepovoleném stavu, kontaktujte [Podpora Microsoftu][Support]. Na druhé straně, pokud je *stav poskytovatele* v nezřízeném stavu, obraťte se na svého poskytovatele služeb.
>
>

## <a name="validate-peering-configuration"></a>Ověřit konfiguraci partnerského vztahu
Poté, co poskytovatel služeb dokončí zřizování okruhu ExpressRoute, je možné vytvořit více konfigurací směrování na základě eBGP pro okruh ExpressRoute mezi zápisem a MSEE-PEs (2)/(4) a směrovači msee (5). Každý okruh ExpressRoute může mít: soukromý partnerský vztah Azure (provoz do privátních virtuálních sítí v Azure) nebo partnerský vztah Microsoftu (provoz do veřejných koncových bodů PaaS a SaaS). Další informace o tom, jak vytvořit a upravit konfiguraci směrování, najdete v článku [Vytvoření a úprava směrování pro okruh ExpressRoute][CreatePeering].

### <a name="verification-via-the-azure-portal"></a>Ověření prostřednictvím Azure Portal

> [!NOTE]
> V modelu připojení IPVPN zajišťují poskytovatelé služeb zodpovědnost za konfiguraci partnerských vztahů (služby vrstvy 3). V takovém modelu poté, co poskytovatel služby nakonfiguruje partnerský vztah a pokud je partnerský vztah na portálu prázdný, zkuste aktualizovat konfiguraci okruhu pomocí tlačítka Aktualizovat na portálu. Tato operace načte aktuální konfiguraci směrování z vašeho okruhu. 
>

V Azure Portal můžete v okně okruhu ExpressRoute zkontrolovat stav partnerského vztahu okruhu ExpressRoute. V části ![3][3] v okně by partnerské vztahy ExpressRoute byly uvedeny, jak je znázorněno na následujícím snímku obrazovky:

![5][5]

Jak je uvedeno v předchozím příkladu, jak je uvedeno u soukromého partnerského vztahu Azure, zatímco veřejné partnerské vztahy Azure a partnerské vztahy Microsoftu se zřízené nejsou. Úspěšně zřízený kontext partnerského vztahu by měl také obsahovat primární a sekundární podsítě typu Point-to-Point. Podsítě/30 se používají pro IP adresu rozhraní směrovači msee a zápis certifikátů/PE-směrovači msee. U partnerských vztahů, které jsou zřízeny, se v seznamu zobrazí také datum poslední změny konfigurace. 

> [!NOTE]
> Pokud se povolení partnerského vztahu nepovede, ověřte, jestli se přiřazené primární a sekundární podsítě shodují s konfigurací u propojeného CE/PE – MSEE. Také ověřte, zda jsou v směrovači msee použity správné *VlanId*, *AzureASN*a *PeerASN* a zda jsou tyto hodnoty mapovány na ty, které jsou použity v propojeném CE/PE-MSEE. Pokud je zvolena hodnota hash MD5, sdílený klíč by měl být stejný jako u páru MSEE a PE-MSEE/CE. Dříve nakonfigurovaný sdílený klíč se z bezpečnostních důvodů nezobrazuje. Pokud potřebujete změnit některou z těchto konfigurací na směrovači MSEE, přečtěte si téma [Vytvoření a úprava směrování pro okruh ExpressRoute][CreatePeering].  
>

> [!NOTE]
> V podsíti/30 přiřazené k rozhraní vybere Microsoft druhou použitelnou IP adresu podsítě pro rozhraní MSEE. Proto zajistěte, aby byla první použitelná IP adresa podsítě přiřazena k partnerským uzlům CE/PE-MSEE.
>


### <a name="verification-via-powershell"></a>Ověření prostřednictvím PowerShellu
Podrobnosti o konfiguraci privátního partnerského vztahu Azure získáte pomocí následujících příkazů:

```azurepowershell
$ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
```

Ukázková odpověď pro úspěšnou konfiguraci privátního partnerského vztahu je:

```output
Name                       : AzurePrivatePeering
Id                         : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt/peerings/AzurePrivatePeering
Etag                       : W/"################################"
PeeringType                : AzurePrivatePeering
AzureASN                   : 12076
PeerASN                    : 123##
PrimaryPeerAddressPrefix   : 172.16.0.0/30
SecondaryPeerAddressPrefix : 172.16.0.4/30
PrimaryAzurePort           : 
SecondaryAzurePort         : 
SharedKey                  : 
VlanId                     : 200
MicrosoftPeeringConfig     : null
ProvisioningState          : Succeeded
```

 Úspěšně povolený kontext partnerského vztahu by měl mít uvedené předpony primární a sekundární adresy. Podsítě/30 se používají pro IP adresu rozhraní směrovači msee a zápis certifikátů/PE-směrovači msee.

Podrobnosti o konfiguraci veřejného partnerského vztahu Azure získáte pomocí následujících příkazů:

```azurepowershell
$ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
```

Podrobnosti o konfiguraci partnerského vztahu Microsoftu získáte pomocí následujících příkazů:

```azurepowershell
$ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt
```

Pokud partnerský vztah není nakonfigurovaný, bude se jednat o chybovou zprávu. Ukázková odpověď, když je v tomto příkladu uvedený partnerský vztah (veřejný partnerský vztah Azure v tomto příkladu), není v okruhu nakonfigurován:

```azurepowershell
Get-AzExpressRouteCircuitPeeringConfig : Sequence contains no matching element
At line:1 char:1
    + Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Get-AzExpr...itPeeringConfig], InvalidOperationException
        + FullyQualifiedErrorId : Microsoft.Azure.Commands.Network.GetAzureExpressRouteCircuitPeeringConfigCommand
```

> [!NOTE]
> Pokud se povolení partnerského vztahu nepovede, ověřte, jestli se přiřazené primární a sekundární podsítě shodují s konfigurací u propojeného CE/PE – MSEE. Také ověřte, zda jsou v směrovači msee použity správné *VlanId*, *AzureASN*a *PeerASN* a zda jsou tyto hodnoty mapovány na ty, které jsou použity v propojeném CE/PE-MSEE. Pokud je zvolena hodnota hash MD5, sdílený klíč by měl být stejný jako u páru MSEE a PE-MSEE/CE. Dříve nakonfigurovaný sdílený klíč se z bezpečnostních důvodů nezobrazuje. Pokud potřebujete změnit některou z těchto konfigurací na směrovači MSEE, přečtěte si téma [Vytvoření a úprava směrování pro okruh ExpressRoute][CreatePeering].  
>
>

> [!NOTE]
> V podsíti/30 přiřazené k rozhraní vybere Microsoft druhou použitelnou IP adresu podsítě pro rozhraní MSEE. Proto zajistěte, aby byla první použitelná IP adresa podsítě přiřazena k partnerským uzlům CE/PE-MSEE.
>

## <a name="validate-arp"></a>Ověřit protokol ARP

Tabulka ARP poskytuje mapování IP adresy a adresy MAC pro konkrétní partnerský vztah. Tabulka ARP pro partnerský vztah okruhu ExpressRoute poskytuje pro každé rozhraní (primární a sekundární) následující informace:
* Mapování IP adresy místního rozhraní směrovače na adresu MAC
* Mapování IP adresy rozhraní směrovače ExpressRoute na adresu MAC
* Stáří tabulek mapování protokolu ARP může pomáhat ověřit konfiguraci vrstvy 2 a řešit potíže s problémy s připojením základní vrstvy 2.


Viz téma [získání tabulek ARP v dokumentu modelu nasazení Správce prostředků][ARP] , informace o tom, jak zobrazit tabulku ARP partnerského vztahu ExpressRoute a jak používat informace k řešení potíží s připojením vrstvy 2.


## <a name="validate-bgp-and-routes-on-the-msee"></a>Ověření protokolu BGP a tras na MSEE

Chcete-li získat směrovací tabulku z MSEE na *primární* cestě pro kontext *privátního* směrování, použijte následující příkaz:

```azurepowershell
Get-AzExpressRouteCircuitRouteTable -DevicePath Primary -ExpressRouteCircuitName ******* -PeeringType AzurePrivatePeering -ResourceGroupName ****
```

Příklad odpovědi:

```output
Network : 10.1.0.0/16
NextHop : 10.17.17.141
LocPrf  : 
Weight  : 0
Path    : 65515

Network : 10.1.0.0/16
NextHop : 10.17.17.140*
LocPrf  : 
Weight  : 0
Path    : 65515

Network : 10.2.20.0/25
NextHop : 172.16.0.1
LocPrf  : 
Weight  : 0
Path    : 123##
```

> [!NOTE]
> Pokud je stav partnerského vztahu eBGP mezi MSEE a a MSEE/PE-MSEE aktivní nebo nečinný, ověřte, jestli se přiřazené primární a sekundární podsítě partnerského vztahu shodují s konfigurací u propojeného CE/PE-. Také ověřte, zda jsou v směrovači msee použity správné *VlanId*, *AzureAsn*a *PeerAsn* a zda jsou tyto hodnoty mapovány na ty, které se používají v propojeném prostředí PE-MSEE/CE. Pokud je zvolena hodnota hash MD5, sdílený klíč by měl být stejný v páru MSEE a CE/PE-MSEE. Pokud potřebujete změnit některou z těchto konfigurací na směrovači MSEE, přečtěte si téma [Vytvoření a úprava směrování pro okruh ExpressRoute][CreatePeering].
>


> [!NOTE]
> Pokud nejsou některá cílová umístění dostupná přes partnerský vztah, podívejte se do směrovací tabulky směrovači msee pro odpovídající kontext partnerského vztahu. Pokud je v směrovací tabulce k dispozici odpovídající předpona (NATed IP), zkontrolujte, zda jsou brány firewall/NSG/seznamy ACL na cestě, která blokuje provoz.
>


Následující příklad ukazuje odpověď příkazu pro partnerský vztah, který neexistuje:

```azurepowershell
Get-AzExpressRouteCircuitRouteTable : The BGP Peering AzurePublicPeering with Service Key ********************* is not found.
StatusCode: 400
```

## <a name="confirm-the-traffic-flow"></a>Potvrzení toku přenosů
Pokud chcete získat kombinovanou statistiku přenosu primárních a sekundárních cest – bajty v a mimo kontext partnerského vztahu, použijte následující příkaz:

```azurepowershell
Get-AzExpressRouteCircuitStats -ResourceGroupName $RG -ExpressRouteCircuitName $CircuitName -PeeringType 'AzurePrivatePeering'
```

Vzorový výstup příkazu je:

```output
PrimaryBytesIn PrimaryBytesOut SecondaryBytesIn SecondaryBytesOut
-------------- --------------- ---------------- -----------------
     240780020       239863857        240565035         239628474
```

Ukázkový výstup příkazu pro neexistující partnerský vztah:

```azurepowershell
Get-AzExpressRouteCircuitRouteTable : The BGP Peering AzurePublicPeering with Service Key ********************* is not found.
StatusCode: 400
```

## <a name="next-steps"></a>Další kroky
Další informace nebo nápovědu najdete na následujících odkazech:

- [Podpora společnosti Microsoft][Support]
- [Vytvoření a úprava okruhu ExpressRoute][CreateCircuit]
- [Vytvoření a úprava směrování pro okruh ExpressRoute][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-expressroute-overview/expressroute-logical-diagram.png "připojení logické expressové trasy"
[2]: ./media/expressroute-troubleshooting-expressroute-overview/portal-all-resources.png "Ikona všech prostředků"
[3]: ./media/expressroute-troubleshooting-expressroute-overview/portal-overview.png "Ikona přehledu"
[4]: ./media/expressroute-troubleshooting-expressroute-overview/portal-circuit-status.png "Ukázkový snímek obrazovky ExpressRoute Essentials"
[5]: ./media/expressroute-troubleshooting-expressroute-overview/portal-private-peering.png "Ukázkový snímek obrazovky ExpressRoute Essentials"

<!--Link References-->
[Support]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ARP]: https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[DR-Pvt]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering





