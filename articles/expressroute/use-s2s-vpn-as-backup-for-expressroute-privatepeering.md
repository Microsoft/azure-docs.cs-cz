---
title: Použití S2S VPN jako zálohy pro privátní partnerský vztah Azure ExpressRoute | Dokumenty společnosti Microsoft
description: Tato stránka obsahuje doporučení architektury pro zálohování privátního partnerského vztahu Azure ExpressRoute pomocí S2S VPN.
services: networking
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 02/05/2020
ms.author: rambala
ms.openlocfilehash: a6a22b667bc66d6ee69bfbd7ad1db88f72d8df0e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687837"
---
# <a name="using-s2s-vpn-as-a-backup-for-expressroute-private-peering"></a>Použití S2S VPN jako zálohy pro soukromý partnerský vztah ExpressRoute

V článku s názvem [Projektování pro zotavení po havárii s ExpressRoute soukromé partnerský vztah][DR-PP], jsme diskutovali o potřebě řešení připojení zálohování pro expressroute privátní partnerský vztah připojení a jak používat geograficky redundantní ExpressRoute obvody pro tento účel. V tomto článku zvažme, jak využít a udržovat síť VPN typu site-to-site (S2S) jako záda pro soukromý partnerský vztah ExpressRoute. 

Na rozdíl od georedundantních obvodů ExpressRoute můžete kombinaci zotavení po havárii ExpressRoute-VPN používat pouze v režimu aktivní ho pasivního režimu. Hlavní výzvou použití libovolného připojení k síti zálohování v pasivním režimu je, že pasivní připojení by často selhat vedle primární připojení. Častým důvodem selhání pasivního připojení je nedostatečná aktivní údržba. Proto v tomto článku se zaměříme na to, jak ověřit a aktivně udržovat připojení S2S VPN, které zálohuje soukromý partnerský vztah ExpressRoute.

>[!NOTE] 
>Když je daná trasa inzerována prostřednictvím ExpressRoute i VPN, Azure by dal přednost směrování přes ExpressRoute.  
>

V tomto článku se podíváme, jak ověřit připojení z hlediska Azure a z hlediska okrajové sítě na straně zákazníka. Možnost ověření z obou kont ů pomůže bez ohledu na to, zda spravujete síťová zařízení na straně zákazníka, která jsou partnerem síťových entit společnosti Microsoft. 

## <a name="example-topology"></a>Příklad topologie

V našem nastavení máme místní síť připojenou k virtuální síti Azure hub uprostřed prostřednictvím okruhu ExpressRoute i připojení S2S VPN. Virtuální síť azure rozbočovače se zase přeřazuje do virtuální sítě s paprsky, jak je znázorněno na obrázku níže:

![1][1]

V nastavení je okruh ExpressRoute ukončen na dvojici směrovačů "Customer Edge" (CE) v místním prostředí. Místní síť LAN je připojena k CE routerům prostřednictvím dvojice firewallů, které pracují v režimu leader-follower. S2S VPN je přímo ukončena na firewallech.

V následující tabulce jsou uvedeny klíčové předpony IP topologie:

| **Entita** | **Předpona** |
| --- | --- |
| Místní síť LAN | 10.1.11.0/25 |
| Virtuální síť Azure Hub | 10.17.11.0/25 |
| Virtuální síť s paprsky Azure | 10.17.11.128/26 |
| Místní testovací server | 10.1.11.10 |
| Virtuální virtuální virtuální síť pro paprsky | 10.17.11.132 |
| Primární podsíť podsítě ExpressRoute p2p | 192.168.11.16/30 |
| Sekundární připojení ExpressRoute podsíť p2p | 192.168.11.20/30 |
| Primární IP adresa partnera BGP brány VPN | 10.17.11.76 |
| Sekundární IP adresa partnera protokolu BGP brány VPN | 10.17.11.77 |
| Místní ip adresa partnera VPN BGP sítě VPN | 192.168.11.88 |
| Primární CE router i/f směrem k firewallu IP | 192.168.11.0/31 |
| Firewall i/f směrem k primárnímu CE routeru IP | 192.168.11.1/31 |
| Sekundární CE router i/f směrem k firewallu IP | 192.168.11.2/31 |
| Firewall i/f směrem k sekundárnímu CE routeru IP | 192.168.11.3/31 |


V následující tabulce jsou uvedeny seznamy ASN topologie:

| **Autonomní systém** | **Asn** |
| --- | --- |
| Lokálně | 65020 |
| Microsoft Enterprise Edge | 12076 |
| Virtuální síť GW (ExR) | 65515 |
| Virtuální síť GW (VPN) | 65515 |

## <a name="high-availability-without-asymmetricity"></a>Vysoká dostupnost bez asymetrie

### <a name="configuring-for-high-availability"></a>Konfigurace pro vysokou dostupnost

[Konfigurace koexistujících připojení ExpressRoute a Site-to-Site][Conf-CoExist] popisuje, jak nakonfigurovat koexistující okruh ExpressRoute a připojení VPN S2S. Jak jsme diskutovali v [navrhování pro vysokou dostupnost s ExpressRoute][HA], ke zlepšení ExpressRoute vysokou dostupnost naše nastavení udržuje redundanci sítě (zabraňuje jeden bod selhání) celou cestu až do koncových bodů. Také primární a sekundární připojení obvodů ExpressRoute jsou konfigurovány tak, aby fungovaly v aktivním aktivním režimu inzeruje místní předpony stejným způsobem prostřednictvím obou připojení. 

Místní inzerování tras primárního ce routeru prostřednictvím primárního připojení okruhu ExpressRoute je uvedeno níže (junos příkazy):

    user@SEA-MX03-01> show route advertising-protocol bgp 192.168.11.18 

    Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

Místní inzerování tras sekundárního CE routeru prostřednictvím sekundárního připojení okruhu ExpressRoute je uvedeno níže (Junos příkazy):

    user@SEA-MX03-02> show route advertising-protocol bgp 192.168.11.22 

    Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

Chcete-li zlepšit vysokou dostupnost připojení zálohování, s2S VPN je také nakonfigurován v aktivním aktivním režimu. Konfigurace brány Azure VPN je uvedená níže. Poznámka: Jako součást konfigurace VPN VPN jsou také uvedeny IP adresy Druhé strany Protokolu BGP brány--10.17.11.76 a 10.17.11.77--.

![2][2]

Místní trasa je inzerována bránami firewall pro primární a sekundární partnery Protokolu BGP brány VPN. Inzerování tras je uvedeno níže (Junos):

    user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.76 

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

    {primary:node0}
    user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.77    

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

>[!NOTE] 
>Konfigurace sítě S2S VPN v aktivním aktivním režimu poskytuje nejen vysokou dostupnost pro připojení k síti zálohování zotavení po havárii, ale také poskytuje vyšší propustnost pro připojení zálohy. Jinými slovy, konfigurace S2S VPN v aktivním aktivním režimu se doporučuje, protože vynutí vytvoření více podkladových tunelů.
>

### <a name="configuring-for-symmetric-traffic-flow"></a>Konfigurace pro symetrický tok provozu

Všimli jsme si, že když je daná místní trasa inzerována prostřednictvím ExpressRoute i S2S VPN, Azure by dal přednost cestě ExpressRoute. Chcete-li vynutit Azure preferovat cestu S2S VPN přes koexistující ExpressRoute, je třeba inzerovat konkrétnější trasy (delší předponu s větší maskou podsítě) prostřednictvím připojení VPN. Naším cílem je použít připojení VPN pouze jako záda. Výchozí chování výběru cesty Azure je tedy v souladu s naším cílem. 

Je naší odpovědností zajistit, aby provoz určený do Azure z místního prostředí také upřednostňoval cestu ExpressRoute před S2S VPN. Výchozí místní preference ce směrovačů a firewallů v našem místním nastavení je 100. Takže konfigurací místní předvolby tras přijatých prostřednictvím expressroute soukromé partnerské společnosti větší než 100 (řekněme 150), můžeme způsobit, že provoz určený pro Azure preferuje okruh ExpressRoute v ustáleném stavu.

Konfigurace Protokolu BGP primárního směrovače CE, který ukončí primární připojení okruhu ExpressRoute, je uvedena níže. Všimněte si, že hodnota místní předvolby tras inzerovaných v průběhu relace iBGP je nakonfigurována jako 150. Podobně musíme zajistit, aby místní preference sekundárního CE routeru, který ukončí sekundární připojení okruhu ExpressRoute, byla také nakonfigurována na 150.

    user@SEA-MX03-01> show configuration routing-instances Cust11 
    description "Customer 11 VRF";
    instance-type virtual-router;
    interface xe-0/0/0:0.110;
    interface ae0.11;
    protocols {
      bgp {
        group ibgp {
            type internal;
            local-preference 150;
            neighbor 192.168.11.1;
        }
        group ebgp {
            peer-as 12076;
            bfd-liveness-detection {
                minimum-interval 300;
                multiplier 3;
            }
            neighbor 192.168.11.18;
        }
      }
    }

Směrovací tabulka místních bran firewall potvrzuje (viz níže), že pro místní provoz, který je určen pro Azure upřednostňované cesty je přes ExpressRoute v ustáleném stavu.

    user@SEA-SRX42-01> show route table Cust11.inet.0 10.17.11.0/24    

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
    + = Active Route, - = Last Active, * = Both

    10.17.11.0/25      *[BGP/170] 2d 00:34:04, localpref 150
                          AS path: 12076 I, validation-state: unverified
                        > to 192.168.11.0 via reth1.11
                          to 192.168.11.2 via reth2.11
                        [BGP/170] 2d 00:34:01, localpref 150
                          AS path: 12076 I, validation-state: unverified
                        > to 192.168.11.2 via reth2.11
                        [BGP/170] 2d 21:12:13, localpref 100, from 10.17.11.76
                          AS path: 65515 I, validation-state: unverified
                        > via st0.118
                        [BGP/170] 2d 00:41:51, localpref 100, from 10.17.11.77
                          AS path: 65515 I, validation-state: unverified
                        > via st0.119
    10.17.11.76/32     *[Static/5] 2d 21:12:16
                        > via st0.118
    10.17.11.77/32     *[Static/5] 2d 00:41:56
                        > via st0.119
    10.17.11.128/26    *[BGP/170] 2d 00:34:04, localpref 150
                          AS path: 12076 I, validation-state: unverified
                        > to 192.168.11.0 via reth1.11
                          to 192.168.11.2 via reth2.11
                        [BGP/170] 2d 00:34:01, localpref 150
                          AS path: 12076 I, validation-state: unverified
                        > to 192.168.11.2 via reth2.11
                        [BGP/170] 2d 21:12:13, localpref 100, from 10.17.11.76
                          AS path: 65515 I, validation-state: unverified
                        > via st0.118
                        [BGP/170] 2d 00:41:51, localpref 100, from 10.17.11.77
                          AS path: 65515 I, validation-state: unverified
                        > via st0.119

Ve výše uvedené tabulce tras pro trasy virtuální sítě rozbočovače a paprsku --10.17.11.0/25 a 10.17.11.128/26 -- vidíme, že okruh ExpressRoute je upřednostňován před připojením VPN. Jedničky 192.168.11.0 a 192.168.11.2 jsou IP adresy na rozhraní brány firewall směrem k ce routerům.

## <a name="validation-of-route-exchange-over-s2s-vpn"></a>Ověření výměny tras přes S2S VPN

Dříve v tomto článku jsme ověřili místní inzerování tras brány firewall primárním a sekundárním partnerům protokolu BGP brány VPN. Kromě toho potvrdíme trasy Azure přijaté branami firewall od primárních a sekundárních partnerů Protokolu BGP brány VPN.

    user@SEA-SRX42-01> show route receive-protocol bgp 10.17.11.76 table Cust11.inet.0 

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
      10.17.11.0/25           10.17.11.76                             65515 I
      10.17.11.128/26         10.17.11.76                             65515 I

    {primary:node0}
    user@SEA-SRX42-01> show route receive-protocol bgp 10.17.11.77 table Cust11.inet.0    

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
      10.17.11.0/25           10.17.11.77                             65515 I
      10.17.11.128/26         10.17.11.77                             65515 I

Podobně pojďme ověřit místní předpony síťových tras přijatých bránou Azure VPN. 

    PS C:\Users\user> Get-AzVirtualNetworkGatewayLearnedRoute -ResourceGroupName SEA-Cust11 -VirtualNetworkGatewayName SEA-Cust11-VNet01-gw-vpn | where {$_.Network -eq "10.1.11.0/25"} | select Network, NextHop, AsPath, Weight

    Network      NextHop       AsPath      Weight
    -------      -------       ------      ------
    10.1.11.0/25 192.168.11.88 65020        32768
    10.1.11.0/25 10.17.11.76   65020        32768
    10.1.11.0/25 10.17.11.69   12076-65020  32769
    10.1.11.0/25 10.17.11.69   12076-65020  32769
    10.1.11.0/25 192.168.11.88 65020        32768
    10.1.11.0/25 10.17.11.77   65020        32768
    10.1.11.0/25 10.17.11.69   12076-65020  32769
    10.1.11.0/25 10.17.11.69   12076-65020  32769

Jak je vidět výše, brána VPN má trasy přijaté primárními i sekundárními partnery Protokolu BGP brány VPN. Má také viditelnost přes trasy přijaté prostřednictvím primárních a sekundárních expressroute připojení (ty s AS-cesta před12076). Chcete-li potvrdit trasy přijaté prostřednictvím připojení VPN, potřebujeme znát místní IP adresy partnera Protokolu BGP připojení. V našem nastavení v úvahu, je to 192.168.11.88 a vidíme trasy přijaté z něj.

Dále ověřme trasy inzerované bránou Azure VPN do místního partnera Protokolu BGP brány firewall (192.168.11.88).

    PS C:\Users\user> Get-AzVirtualNetworkGatewayAdvertisedRoute -Peer 192.168.11.88 -ResourceGroupName SEA-Cust11 -VirtualNetworkGatewayName SEA-Cust11-VNet01-gw-vpn |  select Network, NextHop, AsPath, Weight

    Network         NextHop     AsPath Weight
    -------         -------     ------ ------
    10.17.11.0/25   10.17.11.76 65515       0
    10.17.11.128/26 10.17.11.76 65515       0
    10.17.11.0/25   10.17.11.77 65515       0
    10.17.11.128/26 10.17.11.77 65515       0


Nezobrazení výměny tras indikuje selhání připojení. Viz [Řešení potíží: Připojení VPN azure site-to-site se nemůže připojit a přestane pracovat][VPN Troubleshoot] s nápovědou k řešení potíží s připojením VPN.

## <a name="testing-failover"></a>Testování převzetí služeb při selhání

Nyní, když jsme potvrdili úspěšné výměny tras přes připojení VPN (řídicí rovina), jsme připraveni přepnout provoz (rovinu dat) z připojení ExpressRoute na připojení VPN. 

>[!NOTE] 
>V produkčních prostředích je nutné provést testování převzetí služeb při selhání během plánovaného pracovního okna údržby sítě, protože může být rušivé pro služby.
>

Před spuštěním přepínače provozu trasujme aktuální cestu v našem nastavení z místního testovacího serveru do testovacího virtuálního počítače ve virtuální síti s paprskem.

    C:\Users\PathLabUser>tracert 10.17.11.132

    Tracing route to 10.17.11.132 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.11.1
      2    <1 ms    <1 ms    11 ms  192.168.11.0
      3    <1 ms    <1 ms    <1 ms  192.168.11.18
      4     *        *        *     Request timed out.
      5     6 ms     6 ms     5 ms  10.17.11.132

    Trace complete.

Primární a sekundární podsítě připojení ExpressRoute point-to-point našeho nastavení jsou 192.168.11.16/30 a 192.168.11.20/30. Ve výše uvedené trasovací trase, v kroku 3 vidíme, že jsme bít 192.168.11.18, což je rozhraní IP primární MSEE. Přítomnost rozhraní MSEE potvrzuje, že podle očekávání je naše aktuální cesta přes ExpressRoute.

Jak je uvedeno v [partnerských linkách okruhu Reset ExpressRoute][RST], použijte následující příkazy prostředí powershell uzakázat primární i sekundární partnerský vztah okruhu ExpressRoute.

    $ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
    $ckt.Peerings[0].State = "Disabled"
    Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt

Doba přepnutí převzetí služeb při selhání závisí na době konvergence protokolu BGP. V našem nastavení přepínač převzetí služeb při selhání trvá několik sekund (méně než 10). Po přepínači opakování traceroute ukazuje následující cestu:

    C:\Users\PathLabUser>tracert 10.17.11.132

    Tracing route to 10.17.11.132 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.11.1
      2     *        *        *     Request timed out.
      3     6 ms     7 ms     9 ms  10.17.11.132

    Trace complete.

Výsledek traceroute potvrzuje, že záložní připojení přes S2S VPN je aktivní a může poskytnout kontinuitu služby, pokud primární i sekundární ExpressRoute připojení nezdaří. Chcete-li dokončit testování převzetí služeb při selhání, povolte připojení ExpressRoute zpět a normalizujte tok provozu pomocí následující sady příkazů.

    $ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
    $ckt.Peerings[0].State = "Enabled"
    Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt

Chcete-li potvrdit, že provoz je přepnut zpět na ExpressRoute, opakujte traceroute a ujistěte se, že prochází ExpressRoute soukromé peering.

## <a name="next-steps"></a>Další kroky

ExpressRoute je určen pro vysokou dostupnost bez jediného bodu selhání v rámci sítě společnosti Microsoft. Okruh ExpressRoute je stále omezen na jednu zeměpisnou oblast a na poskytovatele služeb. S2S VPN může být dobrým řešením pasivního zálohování zotavení po havárii pro okruh ExpressRoute. Pro spolehlivé řešení pasivního zálohování připojení je důležitá pravidelná údržba pasivní konfigurace a pravidelné ověřování připojení. Je nezbytné, aby konfigurace VPN stala zastaralou a pravidelně (řekněme každé čtvrtletí) opakovat kroky ověření a převzetí služeb při selhání popsané v tomto článku během okna údržby.

Informace o povolení monitorování a výstrah založených na metrikách brány VPN najdete v [tématu Nastavení výstrah na metriky brány VPN][VPN-alerts].

Chcete-li urychlit konvergenci protokolu BGP po selhání expressroute, [nakonfigurujte BFD přes ExpressRoute][BFD].

<!--Image References-->
[1]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/topology.png "topologie v úvahu"
[2]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/vpn-gw-config.png "Konfigurace VPN GW"

<!--Link References-->
[DR-PP]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering
[Conf-CoExist]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[VPN Troubleshoot]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-site-to-site-cannot-connect
[VPN-alerts]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric
[BFD]: https://docs.microsoft.com/azure/expressroute/expressroute-bfd
[RST]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-reset-peering



