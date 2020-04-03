---
title: Vzdálená práce pomocí síťových služeb Azure
description: Tato stránka popisuje, jak můžete používat síťové služby Azure, které jsou k dispozici k povolení vzdálené práce a jak zmírnit problémy s provozem vyplývající ze zvýšeného počtu lidí pracujících na dálku.
services: networking
author: rambk
ms.service: virtual-network
ms.topic: article
ms.date: 03/26/2020
ms.author: rambala
ms.openlocfilehash: bcdd938365e50da1f5ae0e830e86692f1b915123
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618918"
---
# <a name="working-remotely-using-azure-networking-services"></a>Vzdálená práce pomocí síťových služeb Azure

>[!NOTE]
> Tento článek popisuje, jak můžete využít síťové služby Azure, síť Microsoftu a ekosystém partnerů Azure k vzdálené práci a zmírnění problémů se sítí, kterým můžete čelit kvůli krizi COVID-19.

Tento článek popisuje možnosti, které jsou k dispozici organizacím nastavit vzdálený přístup pro své uživatele nebo doplnit jejich stávající řešení s další kapacitou během období využití ve špičce. Síťoví architekti čelí následujícím výzvám:

- Řešení zvýšení využití sítě.
- Poskytněte spolehlivé bezpečné připojení více zaměstnancům jejich společnosti a zákazníkům.
- Poskytněte připojení ke vzdáleným místům po celém světě.

Ne všechny sítě (například privátní sítě WAN a podnikové základní sítě) dochází k přetížení z zatížení vzdáleného pracovního vytížení ve špičce. Kritická místa se běžně hlásí pouze v domácích širokopásmových sítích a privátních branách místních sítí společností.

Plánovači sítě mohou pomoci zmírnit úzká místa a zmírnit přetížení sítě tím, že mají na paměti, že různé typy přenosů vyžadují různé priority zpracování sítě a některé inteligentní přesměrování/distribuce zatížení. Například, real-time tele-medecine provoz lékaře-pacienta interakce je velmi důležité a zpoždění / nervozita citlivé. Vzhledem k tomu, replikace stejného provozu mezi úložišti není citlivé zpoždění. Dřívější provoz musí být směrován po nejoptimálnější síťové cestě s vyšší kvalitou služeb; že je přijatelné směrovat pozdější provoz po suboptimální trase.



## <a name="sharing-our-best-practices---azure-network-is-designed-for-elasticity-and-high-availability"></a>Sdílení našich osvědčených postupů – síť Azure je navržena pro pružnost a vysokou dostupnost

Azure je navržený tak, aby odolávat náhlým změnám ve využití prostředků a může výrazně pomoci během období využití ve špičce. Společnost Microsoft také udržuje a provozuje jednu z největších sítí na světě. Síť společnosti Microsoft byla navržena pro vysokou dostupnost, která vydrží různé typy selhání: od selhání jednoho síťového prvku až po selhání celé oblasti.

Síť Microsoftu je navržena tak, aby splňovala požadavky a poskytovala optimální výkon pro různé typy síťových přenosů, včetně multimediálního provozu citlivého na zpoždění pro Skype a teams, CDN, analýzu velkých objemů dat v reálném čase, úložiště Azure, Bing a Xbox. Chcete-li zajistit optimální výkon pro různé typy provozu, síť Společnosti Microsoft přitahuje veškerý provoz, který je určen k přenosu nebo chtějí tranzitu prostřednictvím svých prostředků co nejblíže k původu provozu.

>[!NOTE] 
>Použití síťových funkcí Azure popsaných níže využívá chování dopravní přitažlivosti globální sítě Microsoftu k zajištění lepšího prostředí pro vytváření sítí zákazníků. Dopravní přitažlivost chování sítě Společnosti Microsoft pomáhá off načítání provozu co nejdříve z první / poslední míle sítí, které mohou dojít k přetížení během období využití ve špičce.
>

## <a name="enable-employees-to-work-remotely"></a>Umožnit zaměstnancům pracovat na dálku

Brána Azure VPN podporuje připojení VPN z bodu na místo (P2S) i sítě k webu (S2S). Pomocí brány Azure VPN můžete škálovat připojení zaměstnanců, abyste měli zabezpečený přístup k nasazeným prostředkům Azure i místním prostředkům. Další informace naleznete v tématu [Jak povolit uživatelům vzdáleně pracovat](../vpn-gateway/work-remotely-support.md). 

Pokud používáte protokol SSTP (SSTP) se zabezpečeným soketem, počet souběžných připojení je omezen na 128. Chcete-li získat vyšší počet připojení, doporučujeme přechod na OpenVPN nebo IKEv2. Další informace naleznete v [tématu Přechod na protokol OpenVPN nebo IKEv2 z SSTP](../vpn-gateway/ikev2-openvpn-from-sstp.md
).

Pro přístup k prostředkům nasazeným v Azure mohou vzdálení vývojáři místo připojení VPN používat řešení Azure Bastion k získání zabezpečeného přístupu prostředí (RDP nebo SSH), aniž by bylo nutné přistupovat k veřejným IP adresy na virtuálních počítačích. Další informace najdete [v tématu Práce na dálku pomocí Azure Bastion](../bastion/work-remotely-support.md).

Pro agregaci rozsáhlého připojení VPN, pro podporu připojení libovolného k libovolnému připojení mezi prostředky v různých místních globálních umístěních, v různých místních virtuálních sítích rozbočovače a paprsky a pro optimalizaci využití více domácích širokopásmových sítí můžete použít Azure Virtual WAN. Další informace naleznete [v tématu Boj o řešení práce z domova potřebuje? Tady může azure virtuální wan pomoct](../virtual-wan/work-remotely-support.md).

Dalším způsobem, jak podporovat vzdálenou pracovní sílu, je nasazení infrastruktury virtuálních ploch (VDI) hostované ve vaší virtuální síti Azure, zabezpečené pomocí brány Azure Firewall. Například Windows Virtual Desktop (WVD) je služba virtualizace plochy a aplikací, která běží v Azure. S Windows Virtual Desktop můžete nastavit škálovatelné a flexibilní prostředí ve vašem předplatném Azure bez nutnosti spouštět další servery brány. Jste zodpovědní pouze za virtuální počítače WVD ve vaší virtuální síti. Další informace naleznete v tématu [Podpora vzdálené práce azure firewallu](../firewall/remote-work-support.md). 

Azure má také bohatou sadu partnerů ekologického systému. Naši partneři Síťová virtuální zařízení v Azure můžou taky pomoct škálovat připojení VPN. Další informace naleznete v [tématu network virtual appliance (NVA) aspekty pro práci na dálku](../vpn-gateway/nva-work-remotely-support.md).

## <a name="extend-employees-connection-to-access-globally-distributed-resources"></a>Rozšíření připojení zaměstnanců pro přístup k globálně distribuovaným prostředkům

Následující služby Azure můžou zaměstnancům umožnit přístup k globálně distribuovaným prostředkům. Vaše prostředky mohou být v libovolné oblasti Azure, v místních sítích nebo dokonce v jiných veřejných nebo privátních cloudech. 

- **Partnerský vztah virtuální sítě Azure**: Pokud nasadíte prostředky ve více než jedné oblasti Azure a/nebo pokud agregujete připojení vzdáleně pracujících zaměstnanců pomocí více virtuálních sítí, můžete vytvořit připojení mezi více virtuálními sítěmi Azure pomocí partnerského vztahu virtuální sítě. Další informace naleznete v [tématu Partnerský vztah virtuální sítě][VNet-peer].

- **Řešení založené na AZURE:** Pro vzdálené zaměstnance připojené k Azure přes P2S nebo S2S VPN můžete povolit přístup k místním sítím konfigurací S2S VPN mezi místními sítěmi a bránou Azure VPN. Další informace naleznete [v tématu Vytvoření připojení k webu na web][S2S].

- **ExpressRoute**: Pomocí soukromého partnerského vztahu ExpressRoute můžete povolit privátní připojení mezi nasazením Azure a místní infrastrukturou nebo infrastrukturou v zařízení společného umístění. ExpressRoute prostřednictvím partnerského vztahu Microsoftu také umožňuje přístup k veřejným koncovým bodům v Microsoftu z vaší místní sítě. Připojení ExpressRoute se nepřenášejí prostřednictvím veřejného internetu. Nabízejí zabezpečené připojení, spolehlivost, vyšší propustnost s nižší a konzistentní latencí než typická připojení přes Internet. Další informace najdete v článku [Přehled ExpressRoute][ExR]. Využití stávajícího poskytovatele sítě, který je již součástí našeho [ekosystému partnerů ExpressRoute,][ExR-eco] může pomoci zkrátit dobu potřebnou k získání připojení s velkou šířkou pásma k Microsoftu.  Pomocí [služby ExpressRoute Direct][ExR-D] můžete přímo připojit místní síť k páteřní síti Microsoftu. ExpressRoute Direct nabízí dvě různé možnosti linkové sazby duální 10 Gbps nebo 100 Gbps. 

- **Virtuální síť Azure:** Azure Virtual WAN umožňuje bezproblémovou interoperabilitu mezi připojeními VPN a okruhy ExpressRoute. Jak již bylo zmíněno dříve, Azure Virtual WAN také podporovat any-to-any připojení mezi prostředky v různých on-prem globální umístění, v různých regionálních rozbočovače a virtuální sítě pro paprsky

## <a name="scale-customer-connectivity-to-frontend-resources"></a>Škálování připojení zákazníků na front-endové prostředky

V době, kdy více lidí jít on-line, mnoho firemních webových stránek zkušenosti zvýšený provoz zákazníků. Azure Application Gateway může pomoci spravovat toto zvýšené front-endové zatížení. Další informace naleznete v [tématu Podpora vysokého provozu aplikace gateway .](../application-gateway/high-traffic-support.md)

## <a name="microsoft-support-for-multi-cloud-traffic"></a>Podpora microsoftu pro provoz ve více cloudech

Pro vaše nasazení v jiných veřejných cloudech může společnost Microsoft poskytovat globální připojení. V tomto ohledu vám můžou pomoct virtuální SÍŤ AZURE, VPN nebo ExpressRoute. Chcete-li rozšířit připojení z Azure do jiných cloudů, můžete nakonfigurovat S2S VPN mezi dvěma cloudy. Pomocí expressroute můžete také navázat připojení z Azure do jiných veřejných cloudů. Oracle cloud je součástí partnerského ekosystému ExpressRoute. Můžete [nastavit přímé propojení mezi Azure a infrastrukturou Oracle Cloud Infrastructure][Az-OCI]. Většina poskytovatelů služeb, kteří jsou součástí ekosystému partnerů ExpressRoute, také nabízí privátní připojení k jiným veřejným cloudům. S využitím těchto poskytovatelů služeb můžete prostřednictvím ExpressRoute vytvořit privátní připojení mezi nasazeními v Azure a dalšími cloudy.

## <a name="next-steps"></a>Další kroky

Následující články popisují, jak lze různé síťové funkce Azure použít k škálování uživatelů tak, aby pracovali na dálku:

| **Článku** | **Poslední aktualizace** |
| --- | --- |
| [Jak povolit uživatelům pracovat na dálku](../vpn-gateway/work-remotely-support.md) | 23. března 2020 |
| [Snaží se obstarávat práci z domova potřeb? Zde je místo, kde azure virtuální WAN může pomoci](../virtual-wan/work-remotely-support.md) | 23. března 2020 |
| [Podpora vysokého provozu služby Application Gateway](../application-gateway/high-traffic-support.md) | 23. března 2020 |
| [Aspekty síťového virtuálního zařízení (NVA) pro práci na dálku](../vpn-gateway/nva-work-remotely-support.md)| 23. března 2020 |
| [Přechod na protokol OpenVPN nebo IKEv2 z SSTP](https://go.microsoft.com/fwlink/?linkid=2124112) | 23. března 2020 |
| [Vzdálená práce s Azure Bastion](../bastion/work-remotely-support.md) | 23. března 2020 |
| [Použití Služby Azure ExpressRoute k vytvoření hybridního připojení pro podporu vzdálených uživatelů](../expressroute/work-remotely-support.md) | 23. března 2020 |
| [Podpora vzdálené práce azure firewallu](../firewall/remote-work-support.md)|25. března 2020|

<!--Link References-->
[VNet-peer]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview
[S2S]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal
[ExR]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[ExR-eco]: https://docs.microsoft.com/azure/expressroute/expressroute-locations
[ExR-D]: https://docs.microsoft.com/azure/expressroute/expressroute-erdirect-about
[Az-OCI]: https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-azure-oci-networking
