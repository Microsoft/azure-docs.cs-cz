---
title: 'Ověření připojení – Průvodce odstraňováním potíží s ExpressRoute: Azure | Dokumentace Microsoftu'
description: Tato stránka obsahuje pokyny k odstraňování potíží a ověření koncového připojení okruhu ExpressRoute.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 09/26/2017
ms.author: rambala
ms.custom: seodec18
ms.openlocfilehash: a64aa59b205e8986b80a575c50041f826606e16f
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53272803"
---
# <a name="verifying-expressroute-connectivity"></a>Ověření možností připojení ExpressRoute
Tento článek vám pomůže ověřit a řešení potíží s připojením ExpressRoute. ExpressRoute, které rozšiřuje místní síť do cloudu Microsoftu přes soukromé připojení zajišťované poskytovatelem připojení, zahrnuje následující tři odlišné sítě zóny:

-   Síť zákazníka
-   Síť poskytovatele
-   Datového centra Microsoftu

Účelem tohoto dokumentu je pomoct uživatelům určit, kam (nebo i v případě) existuje problém s připojením a v rámci které zónu, a tím a vyhledejte nápovědu z odpovídající týmu k vyřešení daného problému. Pokud podpory společnosti Microsoft, je potřeba vyřešit problém, otevřete lístek podpory s [Microsoft Support][Support].

> [!IMPORTANT]
> Účelem tohoto dokumentu je pomoct Diagnostika a řešení problémů jednoduché. Není určen jako náhrada za podporu Microsoftu. Vytvořit lístek podpory s [Microsoft Support] [ Support] Pokud nemůžete vyřešit problém podle pokynů.
>
>

## <a name="overview"></a>Přehled
Následující diagram znázorňuje připojení logické sítě do sítě Microsoftu pomocí ExpressRoute.
[![1]][1]

Na předchozím obrázku čísla udávají body klíčové sítě. Body sítě jsou odkazovány často prostřednictvím tohoto článku podle jejich přidružených čísla.

V závislosti na modelu připojení ExpressRoute (Cloud Exchange společné umístění, připojení k síti Ethernet typu Point-to-Point nebo Any-to-any (IPVPN)) může být body sítě 3 a 4 přepínače (vrstva 2 zařízení). Body klíčové sítě znázorněno jsou následující:

1.  Zákazník výpočetní zařízení (třeba server nebo počítač)
2.  Webovou službu zápis certifikátů: Hraniční směrovače zákazníka 
3.  Odkaz pEs (CE přístupem): Zprostředkovatel hraniční směrovače/přepínače, které se potýkáte hraniční směrovače zákazníka. Říká PE CEs v tomto dokumentu.
4.  Odkaz pEs (MSEE přístupem): Zprostředkovatel hraniční směrovače/přepínače, které se potýkáte Msee. Říká Msee PE v tomto dokumentu.
5.  Msee: Microsoft Enterprise Edge (MSEE) ExpressRoute směrovače
6.  Brána virtuální sítě (VNet)
7.  Zařízení ve virtuální síti Azure COMPUTE

Pokud se používají modely připojení cloudu Exchange společné umístění nebo připojení k síti Ethernet typu Point-to-Point, hraniční směrovač zákazníka (2) by navázat s Msee (5) partnerského vztahu protokolu BGP. Body sítě 3 a 4 by stále existují ale poněkud transparentnost jako zařízení vrstvy 2.

Pokud se používá model připojení Any-to-any (IPVPN), odkaz PEs (MSEE přístupem) (4) by navázat partnerský vztah s Msee (5) protokolu BGP. Trasy by šířeny zpět do sítě zákazníka prostřednictvím IPVPN síti poskytovatele služeb.

>[!NOTE]
>Pro zajištění vysoké dostupnosti ExpressRoute Microsoft vyžaduje redundantní dvojici relací protokolu BGP mezi směrovači Msee (5) a PE-Msee (4). Redundantní dvojici síťových cest je také podporována mezi sítí zákazníka a PE CEs. Model připojení Any-to-any (IPVPN), může být připojené jedno zařízení CE (2) na jeden nebo více PEs (3).
>
>

Ověření okruhu ExpressRoute, následující postup se vztahuje (bodu sítě indikován přidružená čísla):
1. [Ověření zřizování okruhů a stavu (5)](#validate-circuit-provisioning-and-state)
2. [Ověření ExpressRoute alespoň jeden partnerský vztah, je nakonfigurovaný (5)](#validate-peering-configuration)
3. [Ověření protokolu ARP mezi společnostmi Microsoft a služby poskytovatele (propojení mezi 4 a 5)](#validate-arp-between-microsoft-and-the-service-provider)
4. [Ověření protokolu BGP a trasy na směrovači MSEE (protokolu BGP mezi 4 až 5 a 5 až 6, pokud je připojený virtuální sítě)](#validate-bgp-and-routes-on-the-msee)
5. [Zkontrolujte statistiky provozu (přenosů HTTP procházejících 5)](#check-the-traffic-statistics)

Další kontroly a ověření bude přidána v budoucí, vrácení zpět každý měsíc!

## <a name="validate-circuit-provisioning-and-state"></a>Zřizování okruhů a stavu ověření
Bez ohledu na model připojení je potřeba vytvořit okruh ExpressRoute a tak vygenerovat klíč služby pro zřizování. Zřizování okruhu ExpressRoute vytváří redundantního připojení vrstvy 2 mezi PE-směrovači Msee (4) a Msee (5). Další informace o tom, jak vytvořit, upravit, zřídit a ověření okruhu ExpressRoute najdete v článku [vytvoření a úprava okruhu ExpressRoute][CreateCircuit].

>[!TIP]
>Klíč služby jednoznačně identifikuje okruhu ExpressRoute. Tento klíč se požaduje pro většinu příkazů powershellu, které jsou uvedené v tomto dokumentu. Také byste potřebovali pomoc od Microsoftu nebo od partnera pro řešení potíží s ExpressRoute problém, zadejte klíč služby snadno identifikovat okruh ExpressRoute.
>
>

### <a name="verification-via-the-azure-portal"></a>Ověření přes Azure portal
Na webu Azure Portal, stav okruhu ExpressRoute můžete zkontrolovat tak, že vyberete ![2][2] v nabídce vlevo. bočním panelu a pak vyberete okruh ExpressRoute. Výběr ExpressRoute okruh uvedené v části "Všechny prostředky" se otevře okno okruhu ExpressRoute. V ![3][3] okna ExpressRoute essentials jsou uvedeny, jak je znázorněno na následujícím snímku obrazovky:

![4][4]    

V základní ExpressRoute *stav okruhu* označuje stav okruhu na straně Microsoftu. *Stav poskytovatele* naznačuje, pokud byla okruh *zřízená nebo není zřízený* na straně poskytovatele služeb. 

Pro okruh ExpressRoute do provozu *stav okruhu* musí být *povoleno* a *stav poskytovatele* musí být *zřízená*.

>[!NOTE]
>Pokud *stav okruhu* není nastavení povoleno, obraťte se na [Microsoft Support][Support]. Pokud *stav poskytovatele* není zřízený, obraťte se na svého poskytovatele služeb.
>
>

### <a name="verification-via-powershell"></a>Ověřování pomocí Powershellu
Chcete-li vypsat všechny okruhy ExpressRoute ve skupině prostředků, použijte následující příkaz:

    Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG"

>[!TIP]
>Název skupiny prostředků můžete získat pomocí Azure. Viz předchozí část tohoto dokumentu a Všimněte si, že je název skupiny prostředků uvedené na příkladu snímku obrazovky.
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

Pokud chcete potvrdit, pokud je funkční okruh ExpressRoute, věnujte zvláštní pozornost následující pole:

    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned

>[!NOTE]
>Pokud *CircuitProvisioningState* není nastavení povoleno, obraťte se na [Microsoft Support][Support]. Pokud *serviceproviderprovisioningstate vzájemného propojení* není zřízený, obraťte se na svého poskytovatele služeb.
>
>

### <a name="verification-via-powershell-classic"></a>Ověřování přes PowerShell (Classic)
Chcete-li vypsat všechny okruhy ExpressRoute v rámci předplatného, použijte následující příkaz:

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

Pokud chcete potvrdit, pokud je funkční okruh ExpressRoute, věnujte zvláštní pozornost následující pole: Serviceproviderprovisioningstate vzájemného propojení: Zřízené stav: Povoleno

>[!NOTE]
>Pokud *stav* není nastavení povoleno, obraťte se na [Microsoft Support][Support]. Pokud *serviceproviderprovisioningstate vzájemného propojení* není zřízený, obraťte se na svého poskytovatele služeb.
>
>

## <a name="validate-peering-configuration"></a>Ověřit konfiguraci partnerského vztahu
Po dokončení zřizování okruhu ExpressRoute poskytovatele služeb se dají vytvořit konfigurace směrování přes okruh ExpressRoute mezi směrovači MSEE – žádosti o přijetí změn (4) a Msee (5). Každý okruh ExpressRoute může mít jeden, dva nebo tři směrování kontexty povoleno: Azure soukromého partnerského vztahu (provoz do privátní virtuální sítě v Azure), Azure veřejného partnerského vztahu (provoz na veřejné IP adresy v Azure) a Microsoft partnerského vztahu (provoz do služeb Office 365 a Dynamics 365). Další informace o tom, jak vytvářet a upravovat konfigurace směrování, najdete v článku [vytvoření a úprava směrování pro okruh ExpressRoute][CreatePeering].

### <a name="verification-via-the-azure-portal"></a>Ověření přes Azure portal

>[!NOTE]
>Pokud je vrstvy 3 poskytované poskytovatelem služby a partnerských vztahů jsou prázdné na portálu, aktualizujte konfiguraci okruh pomocí tlačítka Aktualizovat na portálu. Tato operace použije správné konfigurace směrování na váš okruh. 
>
>

Na webu Azure Portal, lze zkontrolovat stav okruhu ExpressRoute tak, že vyberete ![2][2] v nabídce vlevo. bočním panelu a pak vyberete okruh ExpressRoute. Výběr ExpressRoute okruh uvedené v části "Všechny prostředky" by otevřete okno okruhu ExpressRoute. V ![3][3] okna ExpressRoute essentials by byly uvedeny, jak je znázorněno na následujícím snímku obrazovky:

![5][5]

V předchozím příkladu jako uvedené Azure soukromého partnerského vztahu směrování kontextu je povoleno, zatímco veřejný partnerský vztah Azure a kontexty směrování partnerského vztahu Microsoftu nejsou povolené. Úspěšně povoleno vytvoření partnerského vztahu kontextu také třeba podsítě typu point-to-point primární a sekundární (vyžadováno pro protokol BGP) uvedené. / 30 podsítě se používají pro IP adresu rozhraní směrovači Msee a PE Msee. 

>[!NOTE]
>Pokud partnerského vztahu není povoleno, zkontrolujte, pokud primární a sekundární podsítě, které jsou přiřazené odpovídat konfiguraci PE Msee. Pokud ne, chcete-li změnit konfiguraci na směrovači MSEE najdete [vytvoření a úprava směrování pro okruh ExpressRoute][CreatePeering]
>
>

### <a name="verification-via-powershell"></a>Ověřování pomocí Powershellu
Podrobnosti konfigurace partnerského vztahu Azure privátní získáte pomocí následujících příkazů:

    $ckt = Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Ukázková odpověď pro úspěšně nakonfiguroval privátní partnerský vztah, je:

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

 Úspěšně povoleno vytvoření partnerského vztahu kontextu by měla mít uvedené primární a sekundární předpony. / 30 podsítě se používají pro IP adresu rozhraní směrovači Msee a PE Msee.

Podrobnosti konfigurace partnerského vztahu Azure veřejné získáte pomocí následujících příkazů:

    $ckt = Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt

Konfigurace podrobností partnerského vztahu Microsoftu získáte pomocí následujících příkazů:

    $ckt = Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
     Get-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Pokud není nakonfigurován partnerský vztah, by se chybová zpráva. Ukázkové odpovědi, pokud uvedená partnerského vztahu (Azure veřejného partnerského vztahu v tomto příkladu) není nakonfigurovaná v rámci okruhu:

    Get-AzureRmExpressRouteCircuitPeeringConfig : Sequence contains no matching element
    At line:1 char:1
        + Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering ...
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
            + CategoryInfo          : CloseError: (:) [Get-AzureRmExpr...itPeeringConfig], InvalidOperationException
            + FullyQualifiedErrorId : Microsoft.Azure.Commands.Network.GetAzureExpressRouteCircuitPeeringConfigCommand


<p/>
>[!NOTE]
>Pokud partnerského vztahu není povoleno, zaškrtněte, pokud primární a sekundární podsítě, které jsou přiřazené odpovídat konfiguraci propojené PE směrovači MSEE. Také zkontrolujte, zda správné *VlanId*, *AzureASN*, a *PeerASN* se používají na Msee a pokud tyto hodnoty se mapuje na ty, které slouží na propojené PE směrovači MSEE. Pokud je zvolená výpočtu hodnoty hash MD5, sdílený klíč by měl být stejný na pár směrovači MSEE a PE směrovači MSEE. Změňte konfiguraci na směrovači MSEE, najdete v tématu [vytvoření a úprava směrování pro okruh ExpressRoute] [CreatePeering].  
>
>

### <a name="verification-via-powershell-classic"></a>Ověřování přes PowerShell (Classic)
K získání podrobností konfigurace partnerského vztahu Azure privátní, použijte následující příkaz:

    Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"

Ukázková odpověď pro úspěšně nakonfiguroval soukromého partnerského vztahu je:

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

Úspěšně povolena A kontext vytvoření partnerského vztahu by měla podsítě primárního a sekundárního partnera uvedené. / 30 podsítě se používají pro IP adresu rozhraní směrovači Msee a PE Msee.

Podrobnosti konfigurace partnerského vztahu Azure veřejné získáte pomocí následujících příkazů:

    Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

Konfigurace podrobností partnerského vztahu Microsoftu získáte pomocí následujících příkazů:

    Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

>[!IMPORTANT]
>Pokud vrstvy 3 partnerských vztahů byly nastavené zásadami poskytovatele služeb, nastavení partnerské vztahy ExpressRoute pomocí portálu nebo Powershellu přepíše nastavení poskytovatele služeb. Resetování nastavení zprostředkovatele na straně partnerského vztahu vyžaduje podporu poskytovatele služeb. Partnerské vztahy ExpressRoute změňte, jenom Pokud je jisté, že poskytovatel služeb je poskytuje pouze vrstvy 2 služby!
>
>

<p/>
>[!NOTE]
>Pokud partnerského vztahu není povoleno, zkontrolujte, pokud primární a sekundární peer podsítě přiřazené odpovídat konfiguraci propojené PE směrovači MSEE. Také zkontrolujte, zda správné *VlanId*, *AzureAsn*, a *PeerAsn* se používají na Msee a pokud tyto hodnoty se mapuje na ty, které slouží na propojené PE směrovači MSEE. Změňte konfiguraci na směrovači MSEE, najdete v tématu [vytvoření a úprava směrování pro okruh ExpressRoute] [CreatePeering].
>
>

## <a name="validate-arp-between-microsoft-and-the-service-provider"></a>Ověření protokolu ARP mezi společnostmi Microsoft a poskytovatel služeb
Tato část používá příkazy prostředí PowerShell (Classic). Pokud používáte příkazy prostředí PowerShell Azure Resource Manageru, ujistěte se, že máte přístup správce nebo spolusprávce k předplatnému. Řešení potíží s Azure Resource Manageru pomocí příkazů najdete [tabulek získávání protokolu ARP v modelu nasazení Resource Manager] [ ARP] dokumentu.

>[!NOTE]
>K získání protokolu ARP, je možné webu Azure portal a příkazy Powershellu pro Azure Resource Manager. Pokud byly zjištěny chyby v příkazy Powershellu pro Azure Resource Manager, klasické příkazy prostředí PowerShell by měly fungovat jako PowerShell Classic příkazy fungovat i pro okruhy ExpressRoute Azure Resource Manageru.
>
>

Chcete-li získat tabulky ARP primární MSEE směrovače pro soukromý partnerský vztah, použijte následující příkaz:

    Get-AzureDedicatedCircuitPeeringArpInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

Příklad odpovědi pro příkaz, v případě úspěšné:

    ARP Info:

                 Age           Interface           IpAddress          MacAddress
                 113             On-Prem       10.0.0.1           e8ed.f335.4ca9
                   0           Microsoft       10.0.0.2           7c0e.ce85.4fc9

Podobně můžete zkontrolovat základě tabulky ARP z směrovači MSEE v *primární*/*sekundární* cesty, pro *privátní*/*veřejné*  / *Microsoft* partnerské vztahy.

Následující příklad ukazuje, že odpovědi na příkaz pro partnerský vztah neexistuje.

    ARP Info:
       
>[!NOTE]
>Pokud základě tabulky ARP nemá žádné IP adresy rozhraní namapované na adresy MAC, projděte si následující informace:
>1. Pokud první IP adresa/30 podsítě přiřazené pro propojení mezi směrovači MSEE – žádost o přijetí změn a směrovači MSEE se používá v rozhraní směrovači MSEE – žádosti o přijetí změn Azure vždy používá druhou IP adresu pro Msee.
>2. Ověřte, pokud zákazník (C-Tag) a sítě VLAN značky služeb (S-Tag) odpovídají na pár směrovači MSEE – žádost o přijetí změn a směrovači MSEE.
>
>

## <a name="validate-bgp-and-routes-on-the-msee"></a>Ověření protokolu BGP a trasy směrovači MSEE
Tato část používá příkazy prostředí PowerShell (Classic). Pokud používáte příkazy prostředí PowerShell Azure Resource Manageru, ujistěte se, že máte přístup správce nebo spolusprávce k předplatnému.

>[!NOTE]
>Pokud chcete získat informace o protokolu BGP, je možné webu Azure portal a příkazy Powershellu pro Azure Resource Manager. Pokud byly zjištěny chyby v příkazy Powershellu pro Azure Resource Manager, klasické příkazy prostředí PowerShell by měly fungovat jako PowerShell classic příkazy fungovat i pro okruhy ExpressRoute Azure Resource Manageru.
>
>

Získejte přehled směrovací tabulky (okolní sítě BGP) pro konkrétní kontext směrování, následujícím příkazem:

    Get-AzureDedicatedCircuitPeeringRouteTableSummary -AccessType Private -Path Primary -ServiceKey "*********************************"

Příklad odpovědi je:

    Route Table Summary:

            Neighbor                   V                  AS              UpDown         StatePfxRcd
            10.0.0.1                   4                ####                8w4d                  50

Jak je znázorněno v předchozím příkladu, příkaz je užitečná k určení, jak dlouho směrování kontextu se vytvořilo. Označuje se také počet předpon trasy, inzerované partnerským vztahem směrovačem.

>[!NOTE]
>Pokud stav není v aktivní nebo neaktivní, zaškrtněte, pokud primární a sekundární peer podsítě přiřazené odpovídat konfiguraci propojené PE směrovači MSEE. Také zkontrolujte, zda správné *VlanId*, *AzureAsn*, a *PeerAsn* se používají na Msee a pokud tyto hodnoty se mapuje na ty, které slouží na propojené PE směrovači MSEE. Pokud je zvolená výpočtu hodnoty hash MD5, sdílený klíč by měl být stejný na pár směrovači MSEE a PE směrovači MSEE. Chcete-li změnit konfiguraci na směrovači MSEE, přečtěte si [vytvoření a úprava směrování pro okruh ExpressRoute][CreatePeering].
>
>

<p/>
>[!NOTE]
>Pokud některá místa určení počítačů nejsou dostupní přes konkrétní partnerský vztah, zkontrolujte směrovací tabulky Msee patřící do konkrétní kontext partnerského vztahu. Pokud odpovídající předpony (může být NATed IP) je k dispozici ve směrovací tabulce, zkontrolujte, jestli jsou brány firewall nebo NSG a seznamy ACL na cestě a jestli umožňují provoz.
>
>

Chcete-li získat úplné směrovací tabulky z směrovači MSEE *primární* cestu pro konkrétní *privátní* směrování kontext, použijte následující příkaz:

    Get-AzureDedicatedCircuitPeeringRouteTableInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

Příklad úspěšný výsledek příkazu je:

    Route Table Info:

             Network             NextHop              LocPrf              Weight                Path
         10.1.0.0/16            10.0.0.1                                       0    #### ##### #####     
         10.2.0.0/16            10.0.0.1                                       0    #### ##### #####
    ...

Podobně můžete zkontrolovat z směrovači MSEE ve směrovací tabulce *primární*/*sekundární* cesty, pro *privátní* /  *Veřejné*/*Microsoft* partnerského vztahu kontextu.

Následující příklad ukazuje, že odpovědi na příkaz pro partnerský vztah neexistuje:

    Route Table Info:

## <a name="check-the-traffic-statistics"></a>Zkontrolujte statistiky provozu
Chcete-li získat statistiky provozu kombinované primární a sekundární cesty – bajtů dovnitř a ven – vytvoření partnerského vztahu kontextu, použijte následující příkaz:

    Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf683b3a6e5c -AccessType Private

Ukázkový výstup příkazu je:

    PrimaryBytesIn PrimaryBytesOut SecondaryBytesIn SecondaryBytesOut
    -------------- --------------- ---------------- -----------------
         240780020       239863857        240565035         239628474

Ukázkový výstup příkazu pro neexistující partnerského vztahu je:

    Get-AzureDedicatedCircuitStats : ResourceNotFound: Can not find any subinterface for peering type 'Public' for circuit '97f85950-01dd-4d30-a73c-bf683b3a6e5c' .
    At line:1 char:1
    + Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Get-AzureDedicatedCircuitStats], CloudException
        + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.GetAzureDedicatedCircuitPeeringStatsCommand

## <a name="next-steps"></a>Další kroky
Další informace a nápovědu najdete na následujících odkazech:

- [Podpora Microsoftu][Support]
- [Vytvoření a úprava okruhu ExpressRoute][CreateCircuit]
- [Vytvoření a úprava směrování pro okruh ExpressRoute][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-expressroute-overview/expressroute-logical-diagram.png "připojení logické Express Route"
[2]: ./media/expressroute-troubleshooting-expressroute-overview/portal-all-resources.png "Ikona všechny prostředky"
[3]: ./media/expressroute-troubleshooting-expressroute-overview/portal-overview.png "Přehled ikonu"
[4]: ./media/expressroute-troubleshooting-expressroute-overview/portal-circuit-status.png "Snímek obrazovky ukázkové ExpressRoute Essentials"
[5]: ./media/expressroute-troubleshooting-expressroute-overview/portal-private-peering.png "Snímek obrazovky ukázkové ExpressRoute Essentials"

<!--Link References-->
[Support]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ARP]: https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager






