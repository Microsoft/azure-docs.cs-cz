---
title: 'Ověřování připojení: Průvodce odstraňováním potíží s Azure ExpressRoute | Microsoft Docs'
description: Tato stránka obsahuje pokyny na řešení problémů a ověření připojení koncová okruhu ExpressRoute.
documentationcenter: na
services: expressroute
author: rambk
manager: tracsman
editor: ''
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2017
ms.author: cherylmc
ms.openlocfilehash: 10d4779d05d95822ffd487db1ce8992d199c495f
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36753437"
---
# <a name="verifying-expressroute-connectivity"></a>Ověření možností připojení ExpressRoute
ExpressRoute, které zasahuje do místní sítě do cloudu Microsoftu přes privátní připojení, které usnadňují poskytovatele připojení, zahrnuje následující tři odlišné sítě zóny:

-   Své sítě.
-   Síti poskytovatele
-   Microsoft Datacenter

Účelem tohoto dokumentu je pomoct uživatelům určete, kam (nebo i v případě) existuje problém s připojením a v rámci které zónu, a tím k vyhledání pomoc od týmu vhodné k vyřešení problému. Pokud vyřešíte problém, je potřeba podporu společnosti Microsoft, otevřete lístek podpory s [Microsoft Support][Support].

> [!IMPORTANT]
> Tento dokument je určený k diagnostikování a opravě jednoduché problémy. Rozhraní není určeno k jako náhrada za podporu společnosti Microsoft. Otevřete lístek podpory s [Microsoft Support] [ Support] nejde pomocí pokynů uvedených problém vyřešit.
>
>

## <a name="overview"></a>Přehled
Následující diagram znázorňuje připojení logické sítě zákazníka k síti Microsoftu pomocí ExpressRoute.
[![1]][1]

Na předchozím obrázku označují čísla bodů klíče sítě. Body sítě jsou odkazovány často prostřednictvím tohoto článku podle jejich přidružené čísla.

V závislosti na modelu připojení ExpressRoute (cloudu Exchange společné umístění, připojení Ethernet typu Point-to-Point nebo Any-to-any (IPVPN)) může být body sítě 3 a 4 přepínače (vrstvy 2 zařízení). Ilustrovaný body klíče sítě jsou následující:

1.  Zákazník výpočetní zařízení (například server nebo počítače)
2.  Webovou službu zápis certifikátů: Hraniční směrovače zákazníka 
3.  PEs (CE přístupem): zprostředkovatele hraniční směrovače nebo přepínače, které stojí hraniční směrovače zákazníka. Označuje jako PE CEs v tomto dokumentu.
4.  PEs (MSEE přístupem): zprostředkovatele hraniční směrovače nebo přepínače, které stojí Msee. Označuje jako PE Msee v tomto dokumentu.
5.  Msee: Microsoft Edge Enterprise (MSEE) ExpressRoute směrovači
6.  Brána virtuální sítě (VNet)
7.  Výpočetní zařízení na virtuální síť Azure

Pokud používáte společné umístění Exchange cloudu nebo připojení k síti Ethernet typu Point-to-Point modelů připojení, by vytvořit hraniční směrovač zákazníka (2) s Msee (5) partnerského vztahu protokolu BGP. Bodů sítě 3 a 4 by stále existují, ale poněkud transparentní jako zařízení vrstvy 2.

Pokud se používá model připojení Any-to-any (IPVPN), odkaz PEs (MSEE přístupem) (4) by navázat s Msee (5) partnerského vztahu protokolu BGP. Zpátky k síti zákazníka prostřednictvím síti poskytovatele služeb IPVPN by pak rozšíří trasy.

>[!NOTE]
>Pro zajištění vysoké dostupnosti ExpressRoute vyžaduje Microsoft redundantní dvojici relací protokolu BGP mezi Msee (5) a PE-Msee (4). Redundantní dvojici síťových cest je také podporována mezi síti zákazníka a PE CEs. Model připojení Any-to-any (IPVPN), může být připojena jedno zařízení CE (2) na jeden nebo více PEs (3).
>
>

Ověření okruhu ExpressRoute, následující kroky jsou popsané (s bodu sítě indikován přidružená čísla):
1. [Ověření zřizování okruhů a stavu (5)](#validate-circuit-provisioning-and-state)
2. [Ověření alespoň jeden ExpressRoute partnerského vztahu je nakonfigurovaný (5)](#validate-peering-configuration)
3. [Ověření protokolu ARP mezi společností Microsoft a služby zprostředkovatele (propojení mezi 4 a 5)](#validate-arp-between-microsoft-and-the-service-provider)
4. [Ověření protokolu BGP a trasy na MSEE (protokol BGP až 4 až 5, 5 až 6, pokud je připojený virtuální sítě)](#validate-bgp-and-routes-on-the-msee)
5. [Zkontrolujte statistiku provozu (provoz procházející 5)](#check-the-traffic-statistics)

Další ověření a kontroly bude přidána v budoucí, vraťte měsíčně!

## <a name="validate-circuit-provisioning-and-state"></a>Zřizování okruhů a stavu ověření
Bez ohledu na modelu připojení je potřeba vytvořit okruh ExpressRoute a proto vygenerované zřizování okruhů klíč služby. Zřizování okruh ExpressRoute vytváří redundantní připojení vrstvy 2 mezi PE-Msee (4) a Msee (5). Další informace o tom, jak vytvořit, upravit, poskytnout a ověřit okruh ExpressRoute najdete v článku [vytvoření a úprava okruhu ExpressRoute][CreateCircuit].

>[!TIP]
>Klíč služby jednoznačně identifikuje okruhu ExpressRoute. Tento klíč je požadován pro většinu příkazů prostředí powershell uvedených v tomto dokumentu. Taky byste potřebovali pomoc od Microsoftu nebo od partnera chcete vyřešit nějaký problém ExpressRoute, zadejte klíč služby snadno identifikovat okruhu ExpressRoute.
>
>

### <a name="verification-via-the-azure-portal"></a>Ověření prostřednictvím portálu Azure
Na portálu Azure stav okruhu ExpressRoute můžete zkontrolovat výběrem ![2][2] v nabídce vlevo. straně panelu a potom vyberete okruh ExpressRoute. Výběr ExpressRoute okruhu uvedené v části "Všechny prostředky" otevře okno okruhu ExpressRoute. V ![3][3] části okna, ExpressRoute essentials jsou uvedeny, jak je znázorněno na následujícím snímku obrazovky:

![4][4]    

V ExpressRoute Essentials *okruhu stav* označuje stav okruhu na straně společnosti Microsoft. *Stav zprostředkovatele* označuje, zda došlo ke okruhu *zajištěno nebo není zřízený* na straně poskytovatele služeb. 

Pro okruh ExpressRoute do provozu *okruhu stav* musí být *povoleno* a *stav zprostředkovatele* musí být *zajištěno*.

>[!NOTE]
>Pokud *okruhu stav* není povoleno, obraťte se na [Microsoft Support][Support]. Pokud *stav zprostředkovatele* není zajišťováno, obraťte se na svého poskytovatele služeb.
>
>

### <a name="verification-via-powershell"></a>Ověření pomocí prostředí PowerShell
K zobrazení seznamu všech okruhy ExpressRoute ve skupině prostředků, použijte následující příkaz:

    Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG"

>[!TIP]
>Název skupiny prostředků můžete získat prostřednictvím Azure. Viz předchozí část tohoto dokumentu a Všimněte si, že je název skupiny prostředků uvedené v na snímku obrazovky příklad.
>
>

Pokud chcete vybrat konkrétní okruh ExpressRoute ve skupině prostředků, použijte následující příkaz:

    Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"

Ukázková odpověď je:

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

Pokud chcete potvrdit, pokud okruh ExpressRoute je funkční, věnujte zvláštní pozornost následující pole:

    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned

>[!NOTE]
>Pokud *CircuitProvisioningState* není povoleno, obraťte se na [Microsoft Support][Support]. Pokud *ServiceProviderProvisioningState* není zajišťováno, obraťte se na svého poskytovatele služeb.
>
>

### <a name="verification-via-powershell-classic"></a>Ověření pomocí prostředí PowerShell (klasické)
K zobrazení seznamu všech okruhy ExpressRoute v rámci předplatného, použijte následující příkaz:

    Get-AzureDedicatedCircuit

Pokud chcete vybrat konkrétní okruh ExpressRoute, použijte následující příkaz:

    Get-AzureDedicatedCircuit -ServiceKey **************************************

Ukázková odpověď je:

    andwidth                         : 100
    BillingType                      : UnlimitedData
    CircuitName                      : Test-ER-Ckt
    Location                         : westus2
    ServiceKey                       : **************************************
    ServiceProviderName              : ****
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

Abyste si potvrdili, pokud je provozní okruh ExpressRoute, věnovat zvláštní pozornost následující pole: ServiceProviderProvisioningState: zřízený stav: povoleno

>[!NOTE]
>Pokud *stav* není povoleno, obraťte se na [Microsoft Support][Support]. Pokud *ServiceProviderProvisioningState* není zajišťováno, obraťte se na svého poskytovatele služeb.
>
>

## <a name="validate-peering-configuration"></a>Ověření konfigurace partnerského vztahu
Po dokončení zřizování okruh ExpressRoute poskytovatele služeb konfigurace směrování lze vytvořit nad rámec okruhu ExpressRoute mezi MSEE-PRs (4) a Msee (5). Každý okruh ExpressRoute může mít jednu, dvě nebo tři směrování kontexty povoleno: soukromý partnerský vztah Azure (provoz v Azure virtuální privátní sítě), veřejný partnerský vztah Azure (provoz na veřejné IP adresy v Azure) a partnerský vztah Microsoftu (provoz do služeb Office 365 a Dynamics 365). Další informace o tom, jak vytvořit a upravit konfigurace směrování, najdete v článku [vytvoření a úprava směrování pro okruh ExpressRoute][CreatePeering].

### <a name="verification-via-the-azure-portal"></a>Ověření prostřednictvím portálu Azure

>[!NOTE]
>Pokud vrstvy 3 je poskytované poskytovatelem služby a partnerských vztahů jsou prázdné na portálu, aktualizujte konfiguraci okruh pomocí tlačítka Aktualizovat na portálu. Tato operace platí správné konfigurace směrování na váš okruh. 
>
>

Na portálu Azure můžete zkontrolovat stav okruhu ExpressRoute výběrem ![2][2] v nabídce vlevo. straně panelu a potom vyberete okruh ExpressRoute. Výběr ExpressRoute okruhu uvedené v části "Všechny prostředky" se otevřou v okně okruhu ExpressRoute. V ![3][3] části okna, ExpressRoute essentials by byly uvedeny, jak je znázorněno na následujícím snímku obrazovky:

![5][5]

V předchozím příkladu jako uvedené Azure soukromého partnerského vztahu směrování kontextu je povoleno, zatímco veřejný Azure a kontexty směrování partnerského vztahu Microsoftu nejsou povolené. Úspěšně povolilo partnerského vztahu kontextu by měla mít také podsítě primární a sekundární point-to-point (povinné pro protokol BGP) uvedené. / 30 podsítě se používají pro IP adresu rozhraní Msee a PE Msee. 

>[!NOTE]
>Pokud partnerský vztah není povolena, zkontrolujte, pokud primární a sekundární podsítě, které jsou přiřazené odpovídat konfiguraci PE Msee. Pokud není, chcete-li změnit konfiguraci na směrovači MSEE, podívejte se na [vytvoření a úprava směrování pro okruh ExpressRoute][CreatePeering]
>
>

### <a name="verification-via-powershell"></a>Ověření pomocí prostředí PowerShell
Chcete-li podrobností konfigurace partnerského vztahu Azure privátní, použijte následující příkazy:

    $ckt = Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Ukázková odpověď, úspěšně nakonfigurovaný soukromého partnerského vztahu, je:

    Name                       : AzurePrivatePeering
    Id                         : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt/peerings/AzurePrivatePeering
    Etag                       : W/"################################"
    PeeringType                : AzurePrivatePeering
    AzureASN                   : 12076
    PeerASN                    : ####
    PrimaryPeerAddressPrefix   : 172.16.0.0/30
    SecondaryPeerAddressPrefix : 172.16.0.4/30
    PrimaryAzurePort           : 
    SecondaryAzurePort         : 
    SharedKey                  : 
    VlanId                     : 300
    MicrosoftPeeringConfig     : null
    ProvisioningState          : Succeeded

 Úspěšně povolilo partnerského vztahu kontextu by měla mít uvedené primární a sekundární předpony. / 30 podsítě se používají pro IP adresu rozhraní Msee a PE Msee.

Podrobnosti konfigurace partnerského vztahu Azure veřejné získáte pomocí následujících příkazů:

    $ckt = Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt

Konfigurace podrobností partnerského vztahu Microsoftu získáte pomocí následujících příkazů:

    $ckt = Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
     Get-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Pokud není nakonfigurováno partnerský vztah, by chybovou zprávu. Ukázka odpověď, když stanovené partnerského vztahu (Azure veřejný partnerský vztah v tomto příkladu) není nakonfigurovaný v rámci okruhu:

    Get-AzureRmExpressRouteCircuitPeeringConfig : Sequence contains no matching element
    At line:1 char:1
        + Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering ...
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
            + CategoryInfo          : CloseError: (:) [Get-AzureRmExpr...itPeeringConfig], InvalidOperationException
            + FullyQualifiedErrorId : Microsoft.Azure.Commands.Network.GetAzureExpressRouteCircuitPeeringConfigCommand


<p/>
>[!NOTE]
>Pokud partnerský vztah není povolena, zkontrolujte, pokud primární a sekundární podsítě, které jsou přiřazené odpovídat konfiguraci propojené PE-MSEE. Také zkontrolujte, zda správný *VlanId*, *AzureASN*, a *PeerASN* se používají na Msee a pokud tyto hodnoty se mapuje na ty, které slouží na propojené PE-MSEE. Pokud zvolíte použití algoritmu hash MD5, by měla být stejná na dvojice MSEE a PE MSEE sdílený klíč. Změnit konfiguraci na směrovači MSEE, najdete v tématu [vytvoření a úprava směrování pro okruh ExpressRoute] [CreatePeering].  
>
>

### <a name="verification-via-powershell-classic"></a>Ověření pomocí prostředí PowerShell (klasické)
K získání podrobností konfigurace partnerského vztahu Azure privátní, použijte následující příkaz:

    Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"

Ukázková odpověď, úspěšně nakonfigurovaný soukromého partnerského vztahu je:

    AdvertisedPublicPrefixes       : 
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 
    PeerAsn                        : ####
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 10.0.0.0/30
    RoutingRegistryName            : 
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 10.0.0.4/30
    State                          : Enabled
    VlanId                         : 100

Úspěšně, povolená A partnerského vztahu kontextu by měla mít podsítě primárního a sekundárního partnera uvedené. / 30 podsítě se používají pro IP adresu rozhraní Msee a PE Msee.

Podrobnosti konfigurace partnerského vztahu Azure veřejné získáte pomocí následujících příkazů:

    Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

Konfigurace podrobností partnerského vztahu Microsoftu získáte pomocí následujících příkazů:

    Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

>[!IMPORTANT]
>Pokud vrstvy 3 partnerských vztahů byly nastavené zásadami poskytovatele služeb, nastavení partnerských vztahů ExpressRoute prostřednictvím portálu nebo prostředí PowerShell přepíše nastavení poskytovatele služeb. Resetování nastavení partnerského vztahu straně zprostředkovatele vyžaduje podporu poskytovatele služeb. Pokud je jisté, že poskytovatele služeb poskytuje pouze služby vrstvy 2, změnit pouze partnerských vztahů ExpressRoute!
>
>

<p/>
>[!NOTE]
>Pokud partnerský vztah není povolena, zkontrolujte, pokud primární a sekundární sdílené podsítě přiřadit odpovídat konfiguraci propojené PE-MSEE. Také zkontrolujte, zda správný *VlanId*, *AzureAsn*, a *PeerAsn* se používají na Msee a pokud tyto hodnoty se mapuje na ty, které slouží na propojené PE-MSEE. Změnit konfiguraci na směrovači MSEE, najdete v tématu [vytvoření a úprava směrování pro okruh ExpressRoute] [CreatePeering].
>
>

## <a name="validate-arp-between-microsoft-and-the-service-provider"></a>Ověření protokolu ARP mezi společností Microsoft a poskytovatele služeb
Tato část používá příkazy prostředí PowerShell (klasické). Pokud používáte příkazy prostředí PowerShell Azure Resource Manager, ujistěte se, zda máte přístup správce nebo spolusprávce k předplatnému. Řešení potíží s pomocí Azure Resource Manager příkazy naleznete [získávání ARP tabulky v modelu nasazení Resource Manager] [ ARP] dokumentu.

>[!NOTE]
>Chcete-li získat protokolu ARP, lze použít portál Azure a příkazy prostředí PowerShell Azure Resource Manager. Pokud k chybám pomocí příkazů prostředí PowerShell Azure Resource Manager, classic příkazy prostředí PowerShell by měly fungovat jako Classic PowerShell příkazy spolupracovat taky s nástrojem okruhy ExpressRoute Azure Resource Manager.
>
>

Základě tabulky ARP z primární směrovači MSEE pro soukromý partnerský vztah, použijte následující příkaz:

    Get-AzureDedicatedCircuitPeeringArpInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

Odpověď příklad pro příkaz, v tomto scénáři úspěšná:

    ARP Info:

                 Age           Interface           IpAddress          MacAddress
                 113             On-Prem       10.0.0.1           e8ed.f335.4ca9
                   0           Microsoft       10.0.0.2           7c0e.ce85.4fc9

Podobně můžete zkontrolovat tabulky ARP z MSEE v *primární*/*sekundární* cestu, pro *privátní*/*veřejné*  / *Microsoft* partnerských vztahů.

Následující příklad ukazuje, že odpověď příkazu pro partnerský vztah neexistuje.

    ARP Info:
       
>[!NOTE]
>Pokud základě tabulky ARP nemá IP adresy rozhraní namapované na adresy MAC, projděte si následující informace:
>1. Pokud první IP adresa/30 podsítě přiřazené pro propojení mezi MSEE PR a MSEE se používá na rozhraní MSEE PR. Azure vždy používá druhou IP adresu pro Msee.
>2. Ověřte, pokud zákazník (C-Tag) a značky VLAN služby (S-Tag) odpovídají na dvojice MSEE PR a MSEE.
>
>

## <a name="validate-bgp-and-routes-on-the-msee"></a>Ověření protokolu BGP a tras MSEE
Tato část používá příkazy prostředí PowerShell (klasické). Pokud používáte příkazy prostředí PowerShell Azure Resource Manager, ujistěte se, zda máte přístup správce nebo spolusprávce k předplatnému.

>[!NOTE]
>Získat informace o protokolu BGP, můžete použít portál Azure a příkazy prostředí PowerShell Azure Resource Manager. Pokud k chybám pomocí příkazů prostředí PowerShell Azure Resource Manager, classic příkazy prostředí PowerShell by měly fungovat jako classic PowerShell příkazy spolupracovat taky s nástrojem okruhy ExpressRoute Azure Resource Manager.
>
>

Postup získání shrnutí tabulce směrování (BGP sousedním) pro konkrétní směrování kontext, použijte následující příkaz:

    Get-AzureDedicatedCircuitPeeringRouteTableSummary -AccessType Private -Path Primary -ServiceKey "*********************************"

Odpověď příklad je:

    Route Table Summary:

            Neighbor                   V                  AS              UpDown         StatePfxRcd
            10.0.0.1                   4                ####                8w4d                  50

Jak je znázorněno v předchozím příkladu, příkaz je užitečné k určení, jak dlouho směrování kontextu bylo úspěšně navázáno. Označuje také počet trasy předpon inzerovaných směrovači partnerského vztahu.

>[!NOTE]
>Pokud stav není v aktivní a nečinnosti, zkontrolujte, pokud primární a sekundární sdílené podsítě přiřadit odpovídat konfiguraci propojené PE-MSEE. Také zkontrolujte, zda správný *VlanId*, *AzureAsn*, a *PeerAsn* se používají na Msee a pokud tyto hodnoty se mapuje na ty, které slouží na propojené PE-MSEE. Pokud zvolíte použití algoritmu hash MD5, by měla být stejná na dvojice MSEE a PE MSEE sdílený klíč. Chcete-li změnit konfiguraci na směrovači MSEE, přečtěte [vytvoření a úprava směrování pro okruh ExpressRoute][CreatePeering].
>
>

<p/>
>[!NOTE]
>Pokud některá místa určení není dostupný přes konkrétní partnerský vztah, zkontrolujte tabulky trasy Msee patřící do určité partnerského vztahu kontextu. Pokud odpovídající předpony (můžou být NATed IP) se nachází ve směrovací tabulce, potom zkontrolujte, zda existují brány firewall nebo nebo seznamy ACL skupiny NSG na cestě a v případě, že povolují provoz.
>
>

Chcete-li získat úplné směrovací tabulky z MSEE *primární* cestu pro konkrétní *privátní* směrování kontextu, použijte následující příkaz:

    Get-AzureDedicatedCircuitPeeringRouteTableInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

Je úspěšnému výsledku příklad pro příkaz:

    Route Table Info:

             Network             NextHop              LocPrf              Weight                Path
         10.1.0.0/16            10.0.0.1                                       0    #### ##### #####     
         10.2.0.0/16            10.0.0.1                                       0    #### ##### #####
    ...

Podobně můžete zkontrolovat z MSEE ve směrovací tabulce *primární*/*sekundární* cestu, pro *privátní* /  *Veřejné*/*Microsoft* partnerského vztahu kontextu.

Následující příklad ukazuje, že odpověď příkazu pro partnerský vztah neexistuje:

    Route Table Info:

## <a name="check-the-traffic-statistics"></a>Zkontrolujte statistiky provozu
Chcete-li získat statistiku provozu kombinované primární a sekundární cesta – bajtů a odhlašování – partnerského vztahu kontextu, použijte následující příkaz:

    Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf683b3a6e5c -AccessType Private

Ukázkový výstup příkazu je:

    PrimaryBytesIn PrimaryBytesOut SecondaryBytesIn SecondaryBytesOut
    -------------- --------------- ---------------- -----------------
         240780020       239863857        240565035         239628474

Ukázkový výstup příkazu pro neexistující partnerský vztah je:

    Get-AzureDedicatedCircuitStats : ResourceNotFound: Can not find any subinterface for peering type 'Public' for circuit '97f85950-01dd-4d30-a73c-bf683b3a6e5c' .
    At line:1 char:1
    + Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Get-AzureDedicatedCircuitStats], CloudException
        + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.GetAzureDedicatedCircuitPeeringStatsCommand

## <a name="next-steps"></a>Další kroky
Další informace a nápovědu najdete na následujících odkazech:

- [Podporu společnosti Microsoft][Support]
- [Vytvoření a úprava okruhu ExpressRoute][CreateCircuit]
- [Vytvoření a úprava směrování pro okruh ExpressRoute][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-expressroute-overview/expressroute-logical-diagram.png "logické Express Route připojení"
[2]: ./media/expressroute-troubleshooting-expressroute-overview/portal-all-resources.png "Ikona všechny prostředky"
[3]: ./media/expressroute-troubleshooting-expressroute-overview/portal-overview.png "Ikona – přehled"
[4]: ./media/expressroute-troubleshooting-expressroute-overview/portal-circuit-status.png "Snímek obrazovky ukázkové ExpressRoute Essentials"
[5]: ./media/expressroute-troubleshooting-expressroute-overview/portal-private-peering.png "Snímek obrazovky ukázkové ExpressRoute Essentials"

<!--Link References-->
[Support]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ARP]: https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager






