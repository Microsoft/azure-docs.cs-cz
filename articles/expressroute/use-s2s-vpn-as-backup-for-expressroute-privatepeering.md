---
title: Použití S2S VPN jako zálohy pro privátní partnerské vztahy Azure ExpressRoute | Microsoft Docs
description: Tato stránka poskytuje doporučení pro architekturu pro zálohování privátního partnerského vztahu Azure ExpressRoute pomocí S2S VPN.
services: networking
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/05/2020
ms.author: duau
ms.openlocfilehash: 752edea8078cf55fc3965bdc7aa9e1b4269dee34
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92207916"
---
# <a name="using-s2s-vpn-as-a-backup-for-expressroute-private-peering"></a>Použití S2S VPN jako zálohy privátního partnerského vztahu ExpressRoute

V článku s názvem [Návrh pro zotavení po havárii s privátním partnerským vztahem ExpressRoute][DR-PP]jsme si pobývali potřebu řešení pro připojení k zálohování pro připojení privátního partnerského vztahu ExpressRoute a použití geograficky redundantních okruhů ExpressRoute pro účely. V tomto článku můžeme zvážit, jak využít a spravovat síť Site-to-Site (S2S) VPN jako zálohu pro privátní partnerské vztahy ExpressRoute. 

Na rozdíl od geograficky redundantních okruhů ExpressRoute můžete použít kombinaci ExpressRoute-VPN Recovery v režimu aktivní-pasivní. Hlavní výzvou k použití libovolného záložního připojení k síti v pasivním režimu je to, že pasivní připojení často selže vedle primárního připojení. Běžným důvodem pro selhání pasivního připojení je neexistence aktivní údržby. Proto se v tomto článku zaměřte na to, jak ověřit a aktivně udržovat připojení S2S VPN, které provádí zálohování privátního partnerského vztahu ExpressRoute.

>[!NOTE] 
>V případě inzerce dané trasy prostřednictvím ExpressRoute i VPN by Azure chtěl upřednostnit směrování přes ExpressRoute.  
>

V tomto článku se podíváme, jak ověřit konektivitu z perspektivy Azure a perspektivy hraniční sítě pro zákazníky. Možnost ověření z obou konců vám pomůže bez ohledu na to, jestli spravujete zařízení se sítí na straně zákazníka, která jsou rovnocenná entitám sítě Microsoftu. 

## <a name="example-topology"></a>Ukázková topologie

V naší instalaci máme místní síť připojenou k virtuální síti centra Azure přes okruh ExpressRoute a připojení VPN S2S. Virtuální síť centra Azure je v partnerském vztahu k virtuální síti paprsků, jak je znázorněno na následujícím obrázku:

![1][1]

V nastavení se okruh ExpressRoute ukončí na páru směrovačů "zákazník Edge" (CE) v místním prostředí. Místní síť LAN je připojená k směrovačům CE prostřednictvím dvojice bran firewall, které fungují v režimu vedoucího procesu následného řízení. S2S VPN se v bránách firewall přímo ukončí.

V následující tabulce jsou uvedeny klíčové předpony IP pro topologii:

| **Entita** | **Předpona** |
| --- | --- |
| Místní síť LAN | 10.1.11.0/25 |
| Virtuální síť centra Azure | 10.17.11.0/25 |
| Virtuální síť Azure s paprsky | 10.17.11.128/26 |
| Místní testovací server | 10.1.11.10 |
| Virtuální počítač testu virtuální sítě | 10.17.11.132 |
| Podsíť P2P pro primární připojení ExpressRoute | 192.168.11.16/30 |
| Podsíť P2P pro sekundární připojení ExpressRoute | 192.168.11.20/30 |
| IP adresa primárního partnerského uzlu BGP brány VPN | 10.17.11.76 |
| Sekundární IP adresa partnerského uzlu protokolu BGP brány VPN | 10.17.11.77 |
| Místní IP adresa partnerského uzlu BGP brány firewall sítě VPN | 192.168.11.88 |
| Primární směrovač CE i/f směrem k IP adrese brány firewall | 192.168.11.0/31 |
| Brána firewall i/f směrem k IP adrese primárního směrovače CE | 192.168.11.1/31 |
| Sekundární směrovač CE i/f směrem k IP adrese brány firewall | 192.168.11.2/31 |
| I/f brány firewall k sekundární IP adrese směrovače CE | 192.168.11.3/31 |


V následující tabulce je uveden seznam čísla ASN topologie:

| **Autonomní systém** | **ASN** |
| --- | --- |
| Místní | 65020 |
| Microsoft Edge | 12076 |
| Virtual Network GS (ExR) | 65515 |
| Virtual Network GS (VPN) | 65515 |

## <a name="high-availability-without-asymmetricity"></a>Vysoká dostupnost bez asymetrického použití

### <a name="configuring-for-high-availability"></a>Konfigurace pro vysokou dostupnost

[Konfigurace ExpressRoute a současně existujících připojení typu Site-to-site][Conf-CoExist] popisuje, jak nakonfigurovat existující okruh ExpressRoute a připojení S2S VPN. Jak jsme probrali v tématu [navrhování pro zajištění vysoké dostupnosti s ExpressRoute][HA], aby se zlepšila vysoká dostupnost ExpressRoute, naše nastavení udržuje redundanci sítě (nezpůsobuje selhání jednoho bodu) až do koncových bodů. I primární i sekundární připojení okruhu ExpressRoute jsou taky nakonfigurovaná tak, aby fungovala v režimu aktivní-aktivní díky inzerování místních předpon stejným způsobem prostřednictvím obou připojení. 

Místní oznámení o trasách primárního směrovače CE prostřednictvím primárního připojení okruhu ExpressRoute se zobrazuje níže (příkazy Junos):

```console
user@SEA-MX03-01> show route advertising-protocol bgp 192.168.11.18 

Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I
```

Místní oznámení o trasách sekundárního směrovače CE prostřednictvím sekundárního připojení okruhu ExpressRoute se zobrazuje níže (příkazy Junos):

```console
user@SEA-MX03-02> show route advertising-protocol bgp 192.168.11.22 

Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I
```

Pro zvýšení vysoké dostupnosti záložního připojení je síť VPN S2S taky nakonfigurovaná v režimu aktivní-aktivní. Níže je uvedená konfigurace Azure VPN Gateway. Poznámka: v rámci sítě VPN konfigurace sítě VPN jsou uvedeny i IP adresy partnerského uzlu protokolu BGP brány--10.17.11.76 a 10.17.11.77--.

![2][2]

Místní trasu inzerují brány firewall k primárním a sekundárním partnerským partnerům protokolu BGP brány VPN. Inzerce tras se zobrazuje níže (Junos):

```console
user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.76 

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I

{primary:node0}
user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.77    

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I
```

>[!NOTE] 
>Konfigurace S2S VPN v režimu aktivní-aktivní neposkytuje vysokou dostupnost připojení k síti zálohování pro zotavení po havárii, ale také poskytuje vyšší propustnost připojení k zálohování. Jinými slovy konfigurace S2S VPN v režimu aktivní-aktivní se doporučuje, protože vynutí vytvoření několika podkladových tunelů.
>

### <a name="configuring-for-symmetric-traffic-flow"></a>Konfigurace pro tok symetrického provozu

Poznamenali jsme, že když je daná místní trasa inzerována prostřednictvím sítě VPN ExpressRoute i S2S, Azure by preferovat cestu ExpressRoute. Pokud chcete vynutit, aby Azure preferovat S2S VPN cestou přes kostávající ExpressRoute, je potřeba prostřednictvím připojení VPN inzerovat konkrétnější trasy (delší předponu s větší maskou podsítě). Naším cílem je, aby se připojení VPN používala jenom jako záloha. Proto je výchozí chování při výběru cesty v Azure v souladu s naším cílem. 

Je naše zodpovědnost za to, že přenos, který je určený pro Azure z místního prostředí, taky upřednostňuje ExpressRoute cestu přes S2S VPN. Výchozí místní preference směrovačů CE a bran firewall v naší místní instalaci jsou 100. Pokud tedy nakonfigurujete místní preference tras přijatých prostřednictvím privátních partnerských vztahů ExpressRoute větší než 100 (řekněme 150), můžeme provoz určený pro Azure preferovat ExpressRoute okruh ve stabilním stavu.

Konfigurace protokolu BGP primárního směrovače CE, který ukončuje primární připojení okruhu ExpressRoute, je uvedená níže. Všimněte si, že hodnota místní předvolby tras inzerovaných v relaci iBGP je nakonfigurovaná tak, aby byla 150. Podobně je potřeba zajistit, aby byla místní priorita sekundárního směrovače CE, která ukončuje sekundární připojení okruhu ExpressRoute, taky nakonfigurovaná tak, aby byla 150.

```console
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
```

Směrovací tabulka místních bran firewall potvrzuje (viz níže), které jsou pro místní provoz určené pro Azure upřednostňovanou cestou nad ExpressRoute ve stabilním stavu.

```console
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
```

Ve výše uvedené tabulce směrování pro trasy virtuální sítě rozbočovače a paprsků – 10.17.11.0/25 a 10.17.11.128/26 – uvidíme, že u připojení VPN se upřednostňuje okruh ExpressRoute. 192.168.11.0 a 192.168.11.2 jsou IP adresy na rozhraní brány firewall směrem k ES.

## <a name="validation-of-route-exchange-over-s2s-vpn"></a>Ověření metody Route Exchange přes S2S VPN

Dříve v tomto článku jsme ověřili místní oznámení o trasách bran firewall k primárním a sekundárním partnerským partnerům protokolu BGP služby VPN Gateway. Pojďme taky potvrdit trasy Azure přijaté branami firewall od primárních a sekundárních partnerských uzlů protokolu BGP služby VPN Gateway.

```console
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
```

Podobně je možné ověřit předpony tras místní sítě přijaté bránou Azure VPN. 

```powershell
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
```

Jak vidíte výše, brána sítě VPN obsahuje trasy přijaté primárním i sekundárním partnerským uzlem protokolu BGP brány VPN. Má také přehled o trasách přijatých prostřednictvím primárních a sekundárních připojení ExpressRoute (ta se stejnou cestou představila 12076). Abychom potvrdili, že trasy byly přijaty prostřednictvím připojení VPN, musíme znát místní IP adresu partnerského uzlu BGP připojení. V našem nastavení je 192.168.11.88 a my se zobrazuje z něj přijaté trasy.

V dalším kroku ověříte trasy inzerované bránou Azure VPN na místní partnerský uzel BGP brány firewall (192.168.11.88).

```powershell
PS C:\Users\user> Get-AzVirtualNetworkGatewayAdvertisedRoute -Peer 192.168.11.88 -ResourceGroupName SEA-Cust11 -VirtualNetworkGatewayName SEA-Cust11-VNet01-gw-vpn |  select Network, NextHop, AsPath, Weight

Network         NextHop     AsPath Weight
-------         -------     ------ ------
10.17.11.0/25   10.17.11.76 65515       0
10.17.11.128/26 10.17.11.76 65515       0
10.17.11.0/25   10.17.11.77 65515       0
10.17.11.128/26 10.17.11.77 65515       0
```

Nepovedlo se zobrazit výměny tras, což znamená selhání připojení. Další informace najdete v tématu [řešení potíží: připojení VPN typu Site-to-Site VPN se nemůže připojit a přestane fungovat,][VPN Troubleshoot] Pokud potřebujete pomoc s řešením potíží s připojením VPN.

## <a name="testing-failover"></a>Testování převzetí služeb při selhání

Teď, když jsme potvrdili úspěšné pokusy o vystavování tras přes připojení VPN (řídicí plocha), jsme nastavili přepínání přenosů (roviny dat) od připojení ExpressRoute k připojení VPN. 

>[!NOTE] 
>V produkčních prostředích se testování převzetí služeb při selhání musí provést během plánovaného časového období údržby sítě, protože může být služba přerušená.
>

Předtím, než provedete přepínač provozu, sledujeme trasu aktuální cesty v naší instalaci z místního testovacího serveru do testovacího virtuálního počítače ve virtuální síti rozbočovače.

```console
C:\Users\PathLabUser>tracert 10.17.11.132

Tracing route to 10.17.11.132 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.1.11.1
  2    <1 ms    <1 ms    11 ms  192.168.11.0
  3    <1 ms    <1 ms    <1 ms  192.168.11.18
  4     *        *        *     Request timed out.
  5     6 ms     6 ms     5 ms  10.17.11.132

Trace complete.
```

Primární a sekundární podsítě ExpressRoute připojení typu Point-to-Point našeho nastavení jsou v uvedeném pořadí 192.168.11.16/30 a 192.168.11.20/30. V výše uvedeném postupu trasování se v kroku 3 zobrazuje, že jsme rádi 192.168.11.18, což je IP adresa rozhraní primárního MSEE. Přítomnost rozhraní MSEE potvrdí, že aktuální cesta je nad ExpressRoute.

Jak je uvedeno v části [resetování partnerských okruhů ExpressRoute][RST], pomocí následujících příkazů PowerShellu zakažte primární i sekundární partnerský vztah okruhu ExpressRoute.

```powershell
$ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
$ckt.Peerings[0].State = "Disabled"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Doba přepínání převzetí služeb při selhání závisí na době konvergence protokolu BGP. V naší instalaci trvá přepínač převzetí služeb při selhání několik sekund (méně než 10). Po přepínači opakuje traceroute zobrazení následující cesty:

```console
C:\Users\PathLabUser>tracert 10.17.11.132

Tracing route to 10.17.11.132 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.1.11.1
  2     *        *        *     Request timed out.
  3     6 ms     7 ms     9 ms  10.17.11.132

Trace complete.
```

Výsledkem traceroute je potvrzení, že záložní připojení prostřednictvím S2S VPN je aktivní a může zajistit kontinuitu služeb, pokud dojde k selhání primárních i sekundárních připojení ExpressRoute. Aby bylo možné provést testování převzetí služeb při selhání, umožněte ExpressRoute připojení zpět a Normalizujte tok přenosů pomocí následující sady příkazů.

```powershell
$ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
$ckt.Peerings[0].State = "Enabled"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Pokud chcete potvrdit, že se provoz přepne zpátky na ExpressRoute, opakujte traceroute a ujistěte se, že prochází přes soukromý partnerský vztah ExpressRoute.

## <a name="next-steps"></a>Další kroky

ExpressRoute je navržená pro vysokou dostupnost bez jednoho bodu selhání v síti Microsoftu. Okruh ExpressRoute je stále omezen na jednu geografickou oblast a na poskytovatele služeb. S2S VPN může být dobrým řešením pro zotavení po havárii do okruhu ExpressRoute. Pro závislé řešení pasivního zálohovacího připojení je důležité pravidelnou údržbu pasivní konfigurace a pravidelného ověřování připojení. Není nutné, aby konfigurace sítě VPN byla zastaralá a pravidelně (například každé čtvrtletí) opakovala kroky pro ověření a převzetí služeb při selhání, které jsou popsané v tomto článku, během časového období údržby.

Pokud chcete povolit monitorování a výstrahy na základě metrik brány VPN Gateway, přečtěte si téma [Nastavení výstrah pro VPN Gateway metriky][VPN-alerts].

Pro urychlení konvergence BGP po ExpressRoute selhání [NAKONFIGURUJTE BFD přes ExpressRoute][BFD].

<!--Image References-->
[1]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/topology.png "uvažované topologie"
[2]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/vpn-gw-config.png "Konfigurace GS sítě VPN"

<!--Link References-->
[DR-PP]: ./designing-for-disaster-recovery-with-expressroute-privatepeering.md
[Conf-CoExist]: ./expressroute-howto-coexist-resource-manager.md
[HA]: ./designing-for-high-availability-with-expressroute.md
[VPN Troubleshoot]: ../vpn-gateway/vpn-gateway-troubleshoot-site-to-site-cannot-connect.md
[VPN-alerts]: ../vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md
[BFD]: ./expressroute-bfd.md
[RST]: ./expressroute-howto-reset-peering.md