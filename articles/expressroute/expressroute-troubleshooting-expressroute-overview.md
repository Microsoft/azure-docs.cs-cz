---
title: 'Azure ExpressRoute: Ověření připojení – průvodce odstraňováním potíží'
description: Tato stránka obsahuje pokyny pro řešení potíží a ověření připojení okruhu ExpressRoute od konce do konce.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 10/31/2019
ms.author: rambala
ms.custom: seodec18
ms.openlocfilehash: 58ae39e8dfdf918ae14ca9bb8dac28405828999e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78330953"
---
# <a name="verifying-expressroute-connectivity"></a>Ověření možností připojení ExpressRoute
Tento článek vám pomůže ověřit a odstranit potíže s připojením ExpressRoute. ExpressRoute rozšiřuje místní síť do cloudu Microsoftu přes privátní připojení, které je běžně usnadněno poskytovatelem připojení. Připojení ExpressRoute tradičně zahrnuje tři odlišné síťové zóny, a to následovně:

-   Síť zákazníka
-   Síť zprostředkovatelů
-   Datové centrum Společnosti Microsoft

> [!NOTE]
> V modelu přímého připojení ExpressRoute (nabízeném s šířkou pásma 10/100 Gb/s) se mohou zákazníci přímo připojit k portu směrovačů Microsoft Enterprise Edge (MSEE). Proto v modelu přímé připojení existují pouze zóny sítě zákazníka a microsoft.
>


Účelem tohoto dokumentu je pomoci uživateli určit, zda a kde existuje problém s připojením. Tím, aby pomohla hledat podporu od příslušného týmu k vyřešení problému. Pokud je k vyřešení problému potřeba podpora společnosti Microsoft, otevřete lístek podpory s [podporou společnosti Microsoft][Support].

> [!IMPORTANT]
> Tento dokument je určen k diagnostice a opravě jednoduchých problémů. Není určena jako náhrada za podporu společnosti Microsoft. Pokud se vám nedaří problém vyřešit pomocí uvedených pokynů, otevřete lístek podpory s [podporou společnosti Microsoft.][Support]
>
>

## <a name="overview"></a>Přehled
Následující diagram znázorňuje logické připojení sítě zákazníka k síti společnosti Microsoft pomocí expressroute.
[![1]][1]

V předchozím diagramu čísla označují klíčové síťové body. Tyto síťové body jsou odkazovány v tomto článku občas jejich přidružené číslo. V závislosti na modelu připojení ExpressRoute -- cloudové exchange společné umístění, připojení Ethernet typu point-to-point nebo libovolné (IPVPN)-- síťové body 3 a 4 mohou být přepínače (zařízení vrstvy 2) nebo směrovače (zařízení vrstvy 3). V modelu přímého připojení nejsou k dispozici žádné síťové body 3 a 4; místo toho jsou CE (2) přímo spojeny s MSEEs prostřednictvím tmavých vláken. Klíčové body sítě jsou následující:

1.  Výpočetní zařízení zákazníka (například server nebo počítač)
2.  Ce: Hraniční směrovače zákazníka 
3.  PEs (CE směřující): Hraniční směrovače/přepínače poskytovatele, které čelí směrovačům hraničního propojení zákazníků. V tomto dokumentu se označuje jako PE-CE.
4.  PEs (MSEE směřující): Hraniční směrovače/přepínače poskytovatele, které směřují k msees. V tomto dokumentu se označuje jako PE-MSEEs.
5.  MSEEs: Směrovače Microsoft Enterprise Edge (MSEE) ExpressRoute
6.  Brána virtuální sítě (VNet)
7.  Výpočetní zařízení na virtuální síti Azure

Pokud se používá společné umístění cloudové burzy, ethernet ový bod nebo přímé připojení, cees (2) vytvoří partnerský vztah Protokolu BGP s msees (5). 

Pokud se používá model připojení Any-to-any (IPVPN), PE-MSEEs (4) vytvořit partnerský vztah Protokolu BGP s MSEEs (5). Pe-MSEEs šíří trasy přijaté od společnosti Microsoft zpět do sítě zákazníka prostřednictvím sítě poskytovatele služeb IPVPN.

> [!NOTE]
>Pro vysokou dostupnost společnost Microsoft zavádí plně redundantní paralelní připojení mezi dvojicemi MSEEs (5) a PE-MSEEs (4). Mezi zákaznickou sítí a dvojicí PE-CE je také podporována plně redundantní paralelní síťová cesta. Další informace týkající se vysoké dostupnosti naleznete v článku [Návrh vysoké dostupnosti pomocí technologie ExpressRoute][HA]
>
>

Následující jsou logické kroky při odstraňování potíží expressroute okruhu:

* [Ověření zřizování a stavu okruhu](#verify-circuit-provisioning-and-state)
  
* [Ověřit konfiguraci partnerského vztahu](#validate-peering-configuration)
  
* [Ověřit ARP](#validate-arp)
  
* [Ověření protokolu BGP a tras na msee](#validate-bgp-and-routes-on-the-msee)
  
* [Potvrzení dopravního toku](#confirm-the-traffic-flow)


## <a name="verify-circuit-provisioning-and-state"></a>Ověření zřizování a stavu okruhu
Zřizování okruhu ExpressRoute vytváří redundantní připojení vrstvy 2 mezi CE/PE-MSEEs (2)/(4) a MSEEs (5). Další informace o tom, jak vytvořit, upravit, zřídit a ověřit okruh ExpressRoute, naleznete v článku [Vytvoření a úprava okruhu ExpressRoute][CreateCircuit].

>[!TIP]
>Klíč služby jednoznačně identifikuje okruh ExpressRoute. Pokud potřebujete pomoc od společnosti Microsoft nebo od partnera ExpressRoute k řešení problému ExpressRoute, zadejte klíč služby pro snadnou identifikaci okruhu.
>
>

### <a name="verification-via-the-azure-portal"></a>Ověření prostřednictvím portálu Azure
Na webu Azure Portal otevřete okno okruhu ExpressRoute. V části ![3][3] okna jsou základy ExpressRoute uvedeny tak, jak je znázorněno na následujícím snímku obrazovky:

![4][4]    

Ve stavu ExpressRoute Essentials *označuje stav okruhu* na straně Microsoftu. *Stav zprostředkovatele* označuje, pokud byl okruh *zřízen nebo není zřízen* na straně poskytovatele služeb. 

Aby byl okruh ExpressRoute funkční, musí být *povolen* *stav Okruhu* a musí být *zřízen* *stav Zprostředkovatel* .

> [!NOTE]
> Po konfiguraci okruhu ExpressRoute, pokud je *stav okruhu* zasažen ve stavu není povolen, obraťte se na [podporu společnosti Microsoft][Support]. Na druhou stranu, pokud *je stav poskytovatele* zasažen v nezřízeném stavu, obraťte se na svého poskytovatele služeb.
>
>

### <a name="verification-via-powershell"></a>Ověření přes PowerShell
Chcete-li vypsat všechny okruhy ExpressRoute ve skupině prostředků, použijte následující příkaz:

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG"

>[!TIP]
>Pokud hledáte název skupiny prostředků, můžete ji získat tak, že vyhovíte všechny skupiny prostředků ve vašem předplatném pomocí příkazu *Get-AzResourceGroup*
>


Chcete-li vybrat určitý okruh ExpressRoute ve skupině prostředků, použijte následující příkaz:

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"

Odpověď vzorku je:

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

Chcete-li potvrdit, zda je okruh ExpressRoute funkční, věnujte zvláštní pozornost následujícím polím:

    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned

> [!NOTE]
> Po konfiguraci okruhu ExpressRoute, pokud je *stav okruhu* zasažen ve stavu není povolen, obraťte se na [podporu společnosti Microsoft][Support]. Na druhou stranu, pokud *je stav poskytovatele* zasažen v nezřízeném stavu, obraťte se na svého poskytovatele služeb.
>
>

## <a name="validate-peering-configuration"></a>Ověřit konfiguraci partnerského vztahu
Po dokončení zřizování okruhu ExpressRoute lze vytvořit více konfigurací směrování založených na eBGP prostředkem okruhu ExpressRoute mezi cees/MSEE-PEs (2)/(4) a MSEEs (5). Každý okruh ExpressRoute může mít: Privátní partnerský vztah Azure (provoz do privátních virtuálních sítí v Azure) nebo partnerský vztah Microsoftu (přenos do veřejných koncových bodů PaaS a SaaS). Další informace o vytváření a úpravě konfigurace směrování naleznete v článku [Vytvoření a úprava směrování okruhu ExpressRoute][CreatePeering].

### <a name="verification-via-the-azure-portal"></a>Ověření prostřednictvím portálu Azure

> [!NOTE]
> V modelu připojení IPVPN zvládnou poskytovatelé služeb odpovědnost za konfiguraci partnerských uživatelů (služby vrstvy 3). V takovém modelu poté, co poskytovatel služeb nakonfiguroval partnerský vztah a pokud je partnerský vztah na portálu prázdný, zkuste aktualizovat konfiguraci okruhu pomocí tlačítka aktualizace na portálu. Tato operace bude vyžádat aktuální konfiguraci směrování z okruhu. 
>

Na portálu Azure lze zkontrolovat stav partnerského vztahu okruhu ExpressRoute pod unožem okruhu ExpressRoute. V části ![3][3] okna by byly peeringy ExpressRoute uvedeny tak, jak je znázorněno na následujícím snímku obrazovky:

![5][5]

V předchozím příkladu, jak je uvedeno Azure privátní partnerský vztah je zřízena, vzhledem k tomu, že Azure veřejné a Partnerských společností Microsoft nejsou zřízené. Úspěšně zřízený kontext partnerského vztahu by měl také uvedené primární a sekundární podsítě typu point-to-point. Podsítě /30 se používají pro IP adresu rozhraní MSEEs a CE/PE-MSEEs. Pro partnerské partnerské partnerské partnery, které jsou zřízeny výpis také označuje, kdo naposledy upravil konfiguraci. 

> [!NOTE]
> Pokud povolení partnerského vztahu selže, zkontrolujte, zda přiřazené primární a sekundární podsítě odpovídají konfiguraci propojeného CE/PE-MSEE. Zkontrolujte také, zda správné *VlanId*, *AzureASN*a *PeerASN* se používají na MSEEs a pokud tyto hodnoty se mapuje na ty, které se používají na propojené CE/PE-MSEE. Pokud je vybráno hashování MD5, sdílený klíč by měl být stejný na MSEE a PE-MSEE/CE pár. Dříve nakonfigurovaný sdílený klíč nebude z bezpečnostních důvodů zobrazen. Pokud potřebujete změnit některou z těchto konfigurací na směrovači MSEE, přečtěte si informace [o vytvoření a úpravě směrování okruhu ExpressRoute][CreatePeering].  
>

> [!NOTE]
> V podsíti /30 přiřazené k rozhraní vybere společnost Microsoft druhou použitelnou adresu IP podsítě pro rozhraní MSEE. Proto zajistěte, aby první použitelná IP adresa podsítě byla přiřazena na partnerský CE/PE-MSEE.
>


### <a name="verification-via-powershell"></a>Ověření přes PowerShell
Chcete-li získat podrobnosti o konfiguraci privátního partnerského vztahu Azure, použijte následující příkazy:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Ukázková odpověď pro úspěšně nakonfigurovaný soukromý partnerský vztah je:

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

 Úspěšně povolený kontext partnerského vztahu by měl uvedené předpony primární a sekundární adresy. Podsítě /30 se používají pro IP adresu rozhraní MSEEs a CE/PE-MSEEs.

Chcete-li získat podrobnosti o konfiguraci veřejného partnerského vztahu Azure, použijte následující příkazy:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt

Chcete-li získat podrobnosti o konfiguraci partnerského vztahu microsoftu, použijte následující příkazy:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
     Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Pokud partnerský vztah není nakonfigurován, bude chybová zpráva. Ukázková odpověď, když uvedený partnerský vztah (veřejný partnerský vztah Azure v tomto příkladu) není nakonfigurován v rámci okruhu:

    Get-AzExpressRouteCircuitPeeringConfig : Sequence contains no matching element
    At line:1 char:1
        + Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering ...
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
            + CategoryInfo          : CloseError: (:) [Get-AzExpr...itPeeringConfig], InvalidOperationException
            + FullyQualifiedErrorId : Microsoft.Azure.Commands.Network.GetAzureExpressRouteCircuitPeeringConfigCommand


> [!NOTE]
> Pokud povolení partnerského vztahu selže, zkontrolujte, zda přiřazené primární a sekundární podsítě odpovídají konfiguraci propojeného CE/PE-MSEE. Zkontrolujte také, zda správné *VlanId*, *AzureASN*a *PeerASN* se používají na MSEEs a pokud tyto hodnoty se mapuje na ty, které se používají na propojené CE/PE-MSEE. Pokud je vybráno hashování MD5, sdílený klíč by měl být stejný na MSEE a PE-MSEE/CE pár. Dříve nakonfigurovaný sdílený klíč nebude z bezpečnostních důvodů zobrazen. Pokud potřebujete změnit některou z těchto konfigurací na směrovači MSEE, přečtěte si informace [o vytvoření a úpravě směrování okruhu ExpressRoute][CreatePeering].  
>
>

> [!NOTE]
> V podsíti /30 přiřazené k rozhraní vybere společnost Microsoft druhou použitelnou adresu IP podsítě pro rozhraní MSEE. Proto zajistěte, aby první použitelná IP adresa podsítě byla přiřazena na partnerský CE/PE-MSEE.
>

## <a name="validate-arp"></a>Ověřit ARP

Tabulka ARP obsahuje mapování IP adresy a mac adresy pro konkrétní partnerský vztah. Tabulka ARP pro partnerský vztah okruhu ExpressRoute poskytuje následující informace pro každé rozhraní (primární a sekundární):
* Mapování ip adresy rozhraní místního routeru na MAC adresu
* Mapování IP adresy rozhraní směrovače ExpressRoute na adresu MAC
* Stáří mapovacích tabulek ARP může pomoci ověřit konfiguraci vrstvy 2 a řešení potíží se základní mise 2.


Informace o tom, jak zobrazit tabulku ARP partnerského vztahu ExpressRoute a jak tyto informace použít k řešení problému s připojením vrstvy 2, naleznete [v tématu Získání tabulek ARP v][ARP] dokumentu modelu nasazení Správce prostředků.


## <a name="validate-bgp-and-routes-on-the-msee"></a>Ověření protokolu BGP a tras na msee

Chcete-li získat směrovací tabulku z MSEE na *primární* cestě pro kontext *soukromésměrování,* použijte následující příkaz:

    Get-AzExpressRouteCircuitRouteTable -DevicePath Primary -ExpressRouteCircuitName ******* -PeeringType AzurePrivatePeering -ResourceGroupName ****

Příklad odpovědi je:

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


> [!NOTE]
> Pokud je stav partnerského vztahu eBGP mezi MSEE a CE/PE-MSEE v aktivní nebo nečinné, zkontrolujte, zda primární a sekundární partnerské podsítě přiřazené odpovídají konfiguraci na propojené CE/PE-MSEE. Zkontrolujte také, zda správné *VlanId*, *AzureAsn*a *PeerAsn* se používají na MSEEs a pokud tyto hodnoty se mapuje na ty, které se používají na propojené PE-MSEE/CE. Pokud je vybráno hashování MD5, sdílený klíč by měl být stejný na MSEE a CE/PE-MSEE pár. Pokud potřebujete změnit některou z těchto konfigurací na směrovači MSEE, přečtěte si informace [o vytvoření a úpravě směrování okruhu ExpressRoute][CreatePeering].
>


> [!NOTE]
> Pokud některé cíle nejsou dosažitelné přes partnerský vztah, zkontrolujte trasovou tabulku MSEEs pro odpovídající kontext partnerského vztahu. Pokud je ve směrovací tabulce k dispozici odpovídající předpona (může se jedná o ADRESU IP), zkontrolujte, zda na cestě, která blokuje provoz, nejsou brány firewall/Seznamy a řízení sítě NSG/ACL.
>


Následující příklad ukazuje odpověď příkazu pro partnerský vztah, který neexistuje:

    Get-AzExpressRouteCircuitRouteTable : The BGP Peering AzurePublicPeering with Service Key ********************* is not found.
    StatusCode: 400

## <a name="confirm-the-traffic-flow"></a>Potvrzení dopravního toku
Chcete-li získat kombinovanou statistiku provozu primární a sekundární cesty – bajty v kontextu partnerského vztahu a ven), použijte následující příkaz:

    Get-AzExpressRouteCircuitStats -ResourceGroupName $RG -ExpressRouteCircuitName $CircuitName -PeeringType 'AzurePrivatePeering'

Ukázkový výstup příkazu je:

    PrimaryBytesIn PrimaryBytesOut SecondaryBytesIn SecondaryBytesOut
    -------------- --------------- ---------------- -----------------
         240780020       239863857        240565035         239628474

Ukázkový výstup příkazu pro neexistující partnerský vztah je:

    Get-AzExpressRouteCircuitRouteTable : The BGP Peering AzurePublicPeering with Service Key ********************* is not found.
    StatusCode: 400

## <a name="next-steps"></a>Další kroky
Další informace nebo nápovědu najdete v následujících odkazech:

- [Podpora společnosti Microsoft][Support]
- [Vytvoření a úprava okruhu ExpressRoute][CreateCircuit]
- [Vytvoření a úprava směrování pro okruh ExpressRoute][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-expressroute-overview/expressroute-logical-diagram.png "Připojení logické expresní trasy"
[2]: ./media/expressroute-troubleshooting-expressroute-overview/portal-all-resources.png "Ikona Všechny zdroje"
[3]: ./media/expressroute-troubleshooting-expressroute-overview/portal-overview.png "Ikona Přehled"
[4]: ./media/expressroute-troubleshooting-expressroute-overview/portal-circuit-status.png "Ukázkový snímek obrazovky ExpressRoute Essentials"
[5]: ./media/expressroute-troubleshooting-expressroute-overview/portal-private-peering.png "Ukázkový snímek obrazovky ExpressRoute Essentials"

<!--Link References-->
[Support]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ARP]: https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[DR-Pvt]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering





