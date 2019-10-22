---
title: 'Ověření připojení – Průvodce odstraňováním potíží s ExpressRoute: Azure | Microsoft Docs'
description: Tato stránka poskytuje pokyny pro řešení potíží a ověření koncového připojení okruhu ExpressRoute.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 09/26/2017
ms.author: rambala
ms.custom: seodec18
ms.openlocfilehash: 026900e3dcbf7c20750bb8e17e44ba64897c9a30
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "71123442"
---
# <a name="verifying-expressroute-connectivity"></a>Ověření možností připojení ExpressRoute
Tento článek vám pomůže ověřit ExpressRoute konektivitu a řešit potíže. ExpressRoute, který rozšiřuje místní síť do cloudu Microsoftu přes soukromé připojení, které usnadňuje poskytovatel připojení, zahrnuje následující tři odlišné síťové zóny:

-   Síť zákazníka
-   Síť poskytovatele
-   Datacenter Microsoftu

Účelem tohoto dokumentu je pomáhat uživateli určit, kde (nebo i když) existuje problém s připojením a v jaké zóně, a v jakém pásmu si vyžádá, aby při řešení tohoto problému pomohly příslušné týmy. Pokud je pro vyřešení problému nutná podpora Microsoftu, otevřete lístek podpory s [Podpora Microsoftu][Support].

> [!IMPORTANT]
> Tento dokument je určený k tomu, aby vám pomohla diagnostikovat a opravit jednoduché problémy. Není určena jako náhrada za podporu Microsoftu. Pokud problém nemůžete vyřešit pomocí poskytnutých pokynů, otevřete lístek podpory s [Podpora Microsoftu][Support] .
>
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Přehled
Následující diagram znázorňuje logické připojení sítě zákazníka k síti Microsoftu pomocí ExpressRoute.
[![1]][1]

V předchozím diagramu čísla označují klíčové síťové body. Na síťové body se často odkazuje prostřednictvím tohoto článku podle jejich přidruženého čísla.

V závislosti na modelu připojení ExpressRoute (společné umístění cloudového systému Exchange, připojení k síti Ethernet typu Point-to-Point nebo IPVPN) můžou být síťové body 3 a 4 přepínači (zařízení vrstvy 2). Klíčové body sítě jsou znázorněny takto:

1.  Výpočetní zařízení zákazníka (například server nebo počítač)
2.  Zápis certifikátů: hraniční směrovače zákazníka 
3.  PEs (s přístupem CE): hraniční směrovače poskytovatele/přepínače, které jsou na hraničních směrovačích zákazníka. Označuje se jako PE-zápis certifikátů v tomto dokumentu.
4.  PEs (MSEE s přístupem): hraniční směrovače a přepínače poskytovatele, které jsou na směrovači msee. V tomto dokumentu se říká směrovači msee PE.
5.  Směrovači msee: směrovače ExpressRoute Microsoft Enterprise Edge (MSEE)
6.  Brána Virtual Network (VNet)
7.  Výpočetní zařízení ve virtuální síti Azure

Pokud se používají cloudové služby pro připojení k síti Ethernet ve společném umístění nebo sítích typu Point-to-Point, vytvoří se partnerský vztah protokolu BGP (2) s směrovači msee (5). Síťové body 3 a 4 by pořád existovaly, ale jsou pro zařízení vrstvy 2 trochu transparentní.

Pokud se používá model připojení typu any-to-Any (IPVPN), vytvoří se partnerský vztah protokolu BGP (MSEE) (4) s směrovači msee (5). Trasy by se pak rozšířily zpátky k síti zákazníka prostřednictvím sítě poskytovatele služeb IPVPN.

> [!NOTE]
>Pro zajištění vysoké dostupnosti ExpressRoute vyžaduje Microsoft redundantní dvojici relací protokolu BGP mezi směrovači msee (5) a PE-směrovači msee (4). Mezi zákaznickou sítí a prostředím pro zápis do PE se taky doporučuje redundantní dvojice síťových cest. V modelu připojení typu any-to-Any (IPVPN) ale může být jedno nebo více zařízení CE (2) připojeno k jednomu nebo více PEs (3).
>
>

K ověření okruhu ExpressRoute jsou popsány následující kroky (se síťovým bodem, který je označen přidruženým číslem):
1. [Ověřit zřizování a stav okruhu (5)](#validate-circuit-provisioning-and-state)
2. [Ověřte, že je nakonfigurovaný aspoň jeden partnerský vztah ExpressRoute (5).](#validate-peering-configuration)
3. [Ověření protokolu ARP mezi společností Microsoft a poskytovatelem služeb (propojení mezi 4 a 5)](#validate-arp-between-microsoft-and-the-service-provider)
4. [Ověří protokol BGP a trasy v MSEE (BGP mezi 4 a 5 a 5 až 6, pokud je virtuální síť připojená).](#validate-bgp-and-routes-on-the-msee)
5. [Projděte si statistiku provozu (provoz procházející až 5)](#check-the-traffic-statistics)

Další ověřování a kontroly budou v budoucnu přidány, vraťte se měsíčně!

## <a name="validate-circuit-provisioning-and-state"></a>Ověřit zřizování a stav okruhu
Bez ohledu na model připojení je potřeba vytvořit okruh ExpressRoute, a proto se vygeneroval klíč služby pro zřizování okruhů. Zřizování okruhu ExpressRoute vytvoří redundantní připojení vrstvy 2 mezi PE-směrovači msee (4) a směrovači msee (5). Další informace o tom, jak vytvořit, upravit, zřídit a ověřit okruh ExpressRoute, najdete v článku [Vytvoření a úprava okruhu ExpressRoute][CreateCircuit].

>[!TIP]
>Klíč služby jednoznačně identifikuje okruh ExpressRoute. Tento klíč se vyžaduje pro většinu příkazů PowerShellu, které jsou uvedené v tomto dokumentu. Měli byste taky potřebovat pomoc od Microsoftu nebo od partnera ExpressRouteu, abyste mohli řešit problémy s ExpressRoute, a to tak, že zadáte klíč služby pro okamžité identifikaci okruhu.
>
>

### <a name="verification-via-the-azure-portal"></a>Ověření prostřednictvím Azure Portal
V Azure Portal lze zkontrolovat stav okruhu ExpressRoute výběrem ![2][2] v nabídce na levé straně a následným výběrem okruhu ExpressRoute. Když vyberete okruh ExpressRoute, který je uvedený v části všechny prostředky, otevře se okno okruh ExpressRoute. V části ![3][3] v okně jsou uvedeny ExpressRoute základy, jak je znázorněno na následujícím snímku obrazovky:

![4][4]    

Ve ExpressRoute Essentials indikuje *stav okruhu* stav okruhu na straně Microsoftu. *Stav poskytovatele* označuje, zda byl okruh *zřízen/není zřízen* na straně poskytovatele služby. 

Aby okruh ExpressRoute fungoval, musí být *povolen* *stav okruhu* a musí být *zřízen* *stav poskytovatele* .

> [!NOTE]
> Pokud *stav okruhu* není povolený, kontaktujte [Podpora Microsoftu][Support]. Pokud není *stav poskytovatele* zřízený, obraťte se na svého poskytovatele služeb.
>
>

### <a name="verification-via-powershell"></a>Ověření prostřednictvím PowerShellu
Pokud chcete zobrazit seznam všech okruhů ExpressRoute ve skupině prostředků, použijte následující příkaz:

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG"

>[!TIP]
>Název skupiny prostředků můžete získat prostřednictvím Azure. Podívejte se na předchozí část tohoto dokumentu a Všimněte si, že název skupiny prostředků je uvedený na ukázkovém snímku obrazovky.
>
>

Pokud chcete vybrat konkrétní okruh ExpressRoute ve skupině prostředků, použijte tento příkaz:

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"

Ukázková odpověď:

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

Pokud si chcete ověřit, jestli je okruh ExpressRoute funkční, věnujte zvláštní pozornost následujícím polím:

    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned

> [!NOTE]
> Pokud není povolená *CircuitProvisioningState* , kontaktujte [Podpora Microsoftu][Support]. Pokud není *ServiceProviderProvisioningState* zřízené, obraťte se na svého poskytovatele služeb.
>
>

### <a name="verification-via-powershell-classic"></a>Ověření prostřednictvím PowerShellu (Classic)
Pokud chcete zobrazit seznam všech okruhů ExpressRoute v rámci předplatného, použijte následující příkaz:

    Get-AzureDedicatedCircuit

Chcete-li vybrat konkrétní okruh ExpressRoute, použijte následující příkaz:

    Get-AzureDedicatedCircuit -ServiceKey **************************************

Ukázková odpověď:

    andwidth                         : 100
    BillingType                      : UnlimitedData
    CircuitName                      : Test-ER-Ckt
    Location                         : westus2
    ServiceKey                       : **************************************
    ServiceProviderName              : ****
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

Pokud si chcete ověřit, jestli je okruh ExpressRoute funkční, věnujte zvláštní pozornost následujícím polím: ServiceProviderProvisioningState: stav zřizování: povoleno

> [!NOTE]
> Pokud není *stav* povolený, kontaktujte [Podpora Microsoftu][Support]. Pokud není *ServiceProviderProvisioningState* zřízené, obraťte se na svého poskytovatele služeb.
>
>

## <a name="validate-peering-configuration"></a>Ověřit konfiguraci partnerského vztahu
Jakmile poskytovatel služeb dokončí zřizování okruhu ExpressRoute, je možné vytvořit konfiguraci směrování přes okruh ExpressRoute mezi MSEE-PR (4) a směrovači msee (5). Každý okruh ExpressRoute může mít povolený jeden, dva nebo tři kontexty směrování: privátní partnerské vztahy Azure (provoz do privátních virtuálních sítí v Azure), veřejný partnerský vztah Azure (provoz na veřejné IP adresy v Azure) a partnerský vztah Microsoftu (provoz do Office 365). Další informace o tom, jak vytvořit a upravit konfiguraci směrování, najdete v článku [Vytvoření a úprava směrování pro okruh ExpressRoute][CreatePeering].

### <a name="verification-via-the-azure-portal"></a>Ověření prostřednictvím Azure Portal

> [!NOTE]
> Pokud poskytovatel služby poskytuje vrstvu 3 a partnerské vztahy jsou na portálu prázdné, aktualizujte konfiguraci okruhu pomocí tlačítka Aktualizovat na portálu. Tato operace použije na okruhu správnou konfiguraci směrování. 
>
>

V Azure Portal lze zkontrolovat stav okruhu ExpressRoute výběrem ![2][2] v nabídce na levé straně a následným výběrem okruhu ExpressRoute. Když vyberete okruh ExpressRoute, který je uvedený v části všechny prostředky, otevře se okno okruh ExpressRoute. V části ![3][3] tohoto okna by se ExpressRoute Essentials zobrazila tak, jak je znázorněno na následujícím snímku obrazovky:

![5][5]

V předchozím příkladu je to, že je povolený kontext směrování privátního partnerského vztahu Azure, zatímco kontexty směrování Azure Public a Microsoft peering nejsou povolené. Úspěšně povolený kontext partnerského vztahu by měl mít také primární a sekundární podsíť (vyžadované pro BGP), které jsou uvedené v seznamu. Podsítě/30 se používají pro IP adresu rozhraní směrovači msee a PE-směrovači msee. 

> [!NOTE]
> Pokud partnerský vztah není povolený, ověřte, jestli jsou primární a sekundární podsítě přiřazené ke konfiguraci v PE-směrovači msee. Pokud ne, chcete-li změnit konfiguraci směrovačů MSEE, přečtěte si téma [Vytvoření a úprava směrování pro okruh ExpressRoute][CreatePeering] .
>
>

### <a name="verification-via-powershell"></a>Ověření prostřednictvím PowerShellu
Podrobnosti o konfiguraci privátního partnerského vztahu Azure získáte pomocí následujících příkazů:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Ukázková odpověď pro úspěšnou konfiguraci privátního partnerského vztahu je:

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

 Úspěšně povolený kontext partnerského vztahu by měl mít uvedené předpony primární a sekundární adresy. Podsítě/30 se používají pro IP adresu rozhraní směrovači msee a PE-směrovači msee.

Podrobnosti o konfiguraci veřejného partnerského vztahu Azure získáte pomocí následujících příkazů:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt

Podrobnosti o konfiguraci partnerského vztahu Microsoftu získáte pomocí následujících příkazů:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
     Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Pokud partnerský vztah není nakonfigurovaný, bude se jednat o chybovou zprávu. Ukázková odpověď, když je v tomto příkladu uvedený partnerský vztah (veřejný partnerský vztah Azure v tomto příkladu), není v okruhu nakonfigurován:

    Get-AzExpressRouteCircuitPeeringConfig : Sequence contains no matching element
    At line:1 char:1
        + Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering ...
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
            + CategoryInfo          : CloseError: (:) [Get-AzExpr...itPeeringConfig], InvalidOperationException
            + FullyQualifiedErrorId : Microsoft.Azure.Commands.Network.GetAzureExpressRouteCircuitPeeringConfigCommand


> [!NOTE]
> Pokud partnerský vztah není povolený, ověřte, jestli jsou primární a sekundární podsítě přiřazené konfiguraci v propojeném prostředí PE – MSEE. Také ověřte, zda jsou v směrovači msee použity správné *VlanId*, *AzureASN*a *PeerASN* a zda jsou tyto hodnoty mapovány na ty, které se používají v propojeném prostředí PE-MSEE. Pokud je zvolena hodnota hash MD5, sdílený klíč by měl být stejný v páru MSEE a PE-MSEE. Pokud chcete změnit konfiguraci směrovačů MSEE, přečtěte si téma [Vytvoření a úprava směrování pro okruh ExpressRoute][CreatePeering].  
>
>

### <a name="verification-via-powershell-classic"></a>Ověření prostřednictvím PowerShellu (Classic)
Pokud chcete získat podrobnosti o konfiguraci privátního partnerského vztahu Azure, použijte následující příkaz:

    Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"

Ukázková odpověď pro úspěšnou konfiguraci privátního partnerského vztahu je:

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

Úspěšně povolený kontext partnerských vztahů by měl mít uvedené primární a sekundární podsítě partnerského vztahu. Podsítě/30 se používají pro IP adresu rozhraní směrovači msee a PE-směrovači msee.

Podrobnosti o konfiguraci veřejného partnerského vztahu Azure získáte pomocí následujících příkazů:

    Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

Podrobnosti o konfiguraci partnerského vztahu Microsoftu získáte pomocí následujících příkazů:

    Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

> [!IMPORTANT]
> Pokud poskytovatel služby nastavil partnerské vztahy vrstvy 3, nastavení partnerských vztahů ExpressRoute prostřednictvím portálu nebo prostředí PowerShell přepíše nastavení poskytovatele služeb. Resetování nastavení partnerského vztahu na straně poskytovatele vyžaduje podporu poskytovatele služeb. Pokud je jisté, že poskytovatel služeb poskytuje jenom služby vrstvy 2, upravte pouze partnerské vztahy ExpressRoute.
>
>

> [!NOTE]
> Pokud partnerský vztah není povolený, ověřte, jestli se přiřazené primární a sekundární podsítě partnerského vztahu shodují s konfigurací v propojeném prostředí PE – MSEE. Také ověřte, zda jsou v směrovači msee použity správné *VlanId*, *AzureAsn*a *PeerAsn* a zda jsou tyto hodnoty mapovány na ty, které se používají v propojeném prostředí PE-MSEE. Pokud chcete změnit konfiguraci směrovačů MSEE, přečtěte si téma [Vytvoření a úprava směrování pro okruh ExpressRoute][CreatePeering].
>
>

## <a name="validate-arp-between-microsoft-and-the-service-provider"></a>Ověření protokolu ARP mezi společností Microsoft a poskytovatelem služeb
V této části se používají příkazy PowerShellu (Classic). Pokud jste používali příkazy Azure Resource Manager PowerShellu, ujistěte se, že máte oprávnění správce nebo spolusprávce k předplatnému. Řešení potíží pomocí Azure Resource Managerch příkazů najdete v dokumentu věnovaném [získání tabulek ARP v dokumentu modelu nasazení Správce prostředků][ARP] .

> [!NOTE]
>K získání protokolu ARP lze použít příkazy prostředí PowerShell Azure Portal i Azure Resource Manager. Pokud se při použití příkazů prostředí PowerShell vyskytnou chyby, měly by klasické příkazy PowerShellu fungovat i v případě, Azure Resource Manager že se Azure Resource Manager ExpressRoute okruhy fungují jako klasické příkazy PowerShellu.
>
>

Chcete-li získat tabulku ARP z primárního směrovače MSEE pro privátní partnerské vztahy, použijte následující příkaz:

    Get-AzureDedicatedCircuitPeeringArpInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

Ukázková odpověď pro příkaz v úspěšném scénáři:

    ARP Info:

                 Age           Interface           IpAddress          MacAddress
                 113             On-Prem       10.0.0.1           e8ed.f335.4ca9
                   0           Microsoft       10.0.0.2           7c0e.ce85.4fc9

Podobně můžete zaškrtnout tabulku ARP z MSEE v*sekundární* cestě *primárního* / pro *privátní* /*veřejné* / partnerské vztahy*Microsoftu* .

Následující příklad ukazuje odpověď příkazu pro partnerský vztah neexistuje.

    ARP Info:
       
> [!NOTE]
> Pokud tabulka protokolu ARP nemá IP adresy rozhraní namapovaných na adresy MAC, přečtěte si následující informace:
>1. Pokud se první IP adresa podsítě/30 přiřazené k propojení mezi MSEE-PR a MSEE používá v rozhraní MSEE-PR. Azure vždycky používá druhou IP adresu pro směrovači msee.
>2. Ověřte, zda značky VLAN zákazníka (značka C) a Service (S-tag) odpovídají obou dvojicím MSEE-PR a MSEE.
>
>

## <a name="validate-bgp-and-routes-on-the-msee"></a>Ověření protokolu BGP a tras na MSEE
V této části se používají příkazy PowerShellu (Classic). Pokud jste používali příkazy Azure Resource Manager PowerShellu, ujistěte se, že máte oprávnění správce nebo spolusprávce k předplatnému.

> [!NOTE]
>Chcete-li získat informace o protokolu BGP, lze použít příkazy prostředí PowerShell Azure Portal i Azure Resource Manager. Pokud se při použití příkazů prostředí PowerShell vyskytnou chyby, měly by klasické příkazy PowerShellu fungovat i v případě, Azure Resource Manager že se Azure Resource Manager ExpressRoute okruhy fungují jako klasické příkazy PowerShellu.
>
>

Chcete-li získat souhrn směrovací tabulky (sousední soused protokolu BGP) pro konkrétní kontext směrování, použijte následující příkaz:

    Get-AzureDedicatedCircuitPeeringRouteTableSummary -AccessType Private -Path Primary -ServiceKey "*********************************"

Příklad odpovědi:

    Route Table Summary:

            Neighbor                   V                  AS              UpDown         StatePfxRcd
            10.0.0.1                   4                ####                8w4d                  50

Jak je znázorněno v předchozím příkladu, příkaz je užitečný k určení, jak dlouho byl kontext směrování vytvořen. Označuje také počet předpon trasy inzerovaných směrovačem partnerského vztahu.

> [!NOTE]
> Pokud je stav aktivní nebo nečinný, ověřte, zda jsou primární a sekundární podsítě partnerského vztahu přiřazeny ke konfiguraci v propojeném prostředí PE-MSEE. Také ověřte, zda jsou v směrovači msee použity správné *VlanId*, *AzureAsn*a *PeerAsn* a zda jsou tyto hodnoty mapovány na ty, které se používají v propojeném prostředí PE-MSEE. Pokud je zvolena hodnota hash MD5, sdílený klíč by měl být stejný v páru MSEE a PE-MSEE. Pokud chcete změnit konfiguraci směrovačů MSEE, přečtěte si téma [Vytvoření a úprava směrování pro okruh ExpressRoute][CreatePeering].
>
>

> [!NOTE]
> Pokud nejsou určitá umístění dostupná přes konkrétní partnerský vztah, podívejte se do směrovací tabulky směrovači msee patřící do konkrétního kontextu partnerského vztahu. Pokud je v směrovací tabulce k dispozici rozlišovací předpona (NATed IP), zkontrolujte, jestli jsou v cestě brány firewall/NSG/seznamy řízení přístupu (ACL) a jestli povolují přenosy.
>
>

Pokud chcete získat úplnou směrovací tabulku z MSEE na *primární* cestě pro konkrétní kontext *privátního* směrování, použijte následující příkaz:

    Get-AzureDedicatedCircuitPeeringRouteTableInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

Příklad úspěšného výsledku pro příkaz:

    Route Table Info:

             Network             NextHop              LocPrf              Weight                Path
         10.1.0.0/16            10.0.0.1                                       0    #### ##### #####     
         10.2.0.0/16            10.0.0.1                                       0    #### ##### #####
    ...

Podobně můžete zaškrtnout směrovací tabulku z MSEE v *primární* /*sekundární* cestu pro *privátní* /*Public* / kontextu partnerského vztahu*Microsoftu* .

Následující příklad ukazuje odpověď příkazu pro partnerský vztah neexistuje:

    Route Table Info:

## <a name="check-the-traffic-statistics"></a>Zkontroluje statistiku provozu.
Pokud chcete získat kombinovanou statistiku přenosu primárních a sekundárních cest – bajty v a mimo kontext partnerského vztahu, použijte následující příkaz:

    Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf683b3a6e5c -AccessType Private

Vzorový výstup příkazu je:

    PrimaryBytesIn PrimaryBytesOut SecondaryBytesIn SecondaryBytesOut
    -------------- --------------- ---------------- -----------------
         240780020       239863857        240565035         239628474

Ukázkový výstup příkazu pro neexistující partnerský vztah:

    Get-AzureDedicatedCircuitStats : ResourceNotFound: Can not find any subinterface for peering type 'Public' for circuit '97f85950-01dd-4d30-a73c-bf683b3a6e5c' .
    At line:1 char:1
    + Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Get-AzureDedicatedCircuitStats], CloudException
        + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.GetAzureDedicatedCircuitPeeringStatsCommand

## <a name="next-steps"></a>Další kroky
Další informace nebo nápovědu najdete na následujících odkazech:

- [podpora Microsoftu][Support]
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






