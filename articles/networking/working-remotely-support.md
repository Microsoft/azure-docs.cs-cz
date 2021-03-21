---
title: Vzdálená práce pomocí Azure Networking Services
description: Tato stránka popisuje, jak můžete pomocí síťových služeb Azure, které jsou k dispozici pro vzdálenou práci, a jak zmírnit problémy s přenosem dat, které vyplývají z většího počtu lidí pracujících vzdáleně.
services: networking
author: rambk
ms.service: virtual-network
ms.topic: article
ms.date: 03/26/2020
ms.author: rambala
ms.openlocfilehash: 835be5b867826d75732c9482743ad6a4ca2900e7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98231838"
---
# <a name="working-remotely-using-azure-networking-services"></a>Vzdálená práce pomocí Azure Networking Services

>[!NOTE]
> Tento článek popisuje, jak můžete využít Azure Networking Services, síť Microsoftu a partnerský ekosystém Azure k tomu, aby fungovaly vzdáleně a zmírnit problémy se sítí, na které se můžete zajímat z důvodu krize v COVID-19.

Tento článek popisuje možnosti, které jsou k dispozici organizacím k nastavení vzdáleného přístupu pro své uživatele nebo k doplnění stávajících řešení s dodatečnou kapacitou během období špičky využití. Architektům sítě se čelí následujícím problémům:

- Vyřešte zvýšení využití sítě.
- Poskytněte spolehlivé zabezpečené připojení k více zaměstnancům společnosti a zákazníkům.
- Poskytněte připojení ke vzdáleným lokalitám po celém světě.

Nemusíte využívat všechny sítě (například privátní sítě WAN a podnikové základní sítě) při zahlcení zatížení vzdáleného pracovního procesu ve špičce. Kritické body jsou běžně hlášeny jenom v domácích sítích a branách VPN v místních sítích společnosti.

Plánovače sítě mohou zvýšit kritická místa a zmírnit zahlcení sítě tím, že si myslíte, že různé typy přenosů potřebují různé priority v oblasti zpracování sítě a některé inteligentní přesměrování nebo distribuce inteligentního zatížení. Například provoz v reálném čase tele-medecine s přenosem lékařů proti pacientům má vysokou důležitost a citlivou dobu zpoždění a kolísání. Vzhledem k tomu, že replikace stejného provozu mezi úložišti nezáleží na zpoždění. Původní provoz se musí směrovat přes optimální síťovou cestu s vyšší kvalitou služeb. vzhledem k tomu, že je přijatelné směrovat pozdější přenos prostřednictvím dílčí optimální trasy.



## <a name="sharing-our-best-practices---azure-network-is-designed-for-elasticity-and-high-availability"></a>Sdílení našich osvědčených postupů – síť Azure je navržená pro pružnost a vysokou dostupnost.

Azure je navržený tak, aby vydržel náhlé změny v využívání prostředků a může značně pomáhat během období špičky. Společnost Microsoft také udržuje a provozuje jednu z největších sítí v světů. Síť Microsoftu je navržená pro vysokou dostupnost, která může odolat různým typům selhání: z jedné síťové prvku selže selhání celé oblasti.

Síť Microsoftu je navržená tak, aby splňovala požadavky a poskytovala optimální výkon pro různé typy síťových přenosů, včetně zpoždění citlivého multimediálního provozu pro Skype a týmy, CDN, analýzu velkých objemů dat v reálném čase, služby Azure Storage, Bing a Xbox. Pro zajištění optimálního výkonu pro různé typy provozu síť Microsoftu přilákat veškerý provoz, který je určený k přenosu prostředků, a jeho prostředky, co je potřeba k tomu, aby se nacházel co nejblíže původu provozu.

>[!NOTE] 
>Používání síťových funkcí Azure popsaných níže využívá chování přitažlivosti provozu globální sítě Microsoftu k poskytování lepších síťových prostředí pro zákazníky. Chování provozu sítě Microsoftu, které přitažlivosti provoz, pomáhá co nejdříve načítat provoz z prvních nebo posledních sítí v mílích, které můžou vyskytnout přetížení během období špičky využití.
>

## <a name="enable-employees-to-work-remotely"></a>Umožnění vzdálené práce zaměstnanců

Azure VPN Gateway podporuje připojení typu Point-to-Site (P2S) a VPN typu Site-to-Site (S2S). Pomocí Azure VPN Gateway můžete škálovat připojení zaměstnanců a bezpečně přistupovat k prostředkům nasazeným v Azure i k místním prostředkům. Další informace najdete v tématu [Jak povolit uživatelům vzdálenou práci](../vpn-gateway/work-remotely-support.md). 

Pokud používáte protokol SSTP (Secure Sockets Tunneling Protocol), je počet souběžných připojení omezený na 128. Pokud chcete získat větší počet připojení, doporučujeme, abyste přešli na OpenVPN nebo IKEv2. Další informace najdete v tématu [Přechod na Protokol OpenVPN nebo IKEv2 z protokolu SSTP](../vpn-gateway/ikev2-openvpn-from-sstp.md
).

Pro přístup k prostředkům nasazeným v Azure můžou vzdálení vývojáři použít řešení Azure bastionu namísto připojení VPN k získání zabezpečeného přístupu do prostředí (RDP nebo SSH) bez vyžadování veřejných IP adres na virtuálních počítačích, ke kterým se přistupuje. Další informace najdete v tématu [práce vzdáleně pomocí Azure bastionu](../bastion/work-remotely-support.md).

Pro agregaci vysoce škálovatelného připojení k síti VPN, aby bylo možné podporovat jakékoli připojení mezi prostředky v různých místních lokalitách, v různých místních virtuálních sítích centrálních hub a paprsků a k optimalizaci využití více domácích širokopásmových sítí můžete použít Azure Virtual WAN. Další informace najdete v tématu [působit potíže to stravování pro práci s požadavky na domácnosti? Tady je, kde může pomáhat Azure Virtual WAN](../virtual-wan/work-remotely-support.md).

Další možností, jak zajistit podporu vzdálených zaměstnanců, je nasazení infrastruktury virtuálních klientských počítačů (VDI), která je hostovaná ve službě Azure Virtual Network, zabezpečená pomocí Azure Firewall. Například virtuální počítač s Windows (WVD) je služba virtualizace plochy a aplikací, která běží v Azure. S virtuálním počítačem s Windows můžete nastavit škálovatelné a flexibilní prostředí v předplatném Azure, aniž byste museli spouštět žádné další servery brány. Zodpovídáte jenom za virtuální počítače s WVD ve vaší virtuální síti. Další informace najdete v tématu [Podpora vzdálené práce Azure firewall](../firewall/remote-work-support.md). 

Azure má také bohatou sadu partnerů pro ekosystém systému. Naši partneři sítě virtuální zařízení v Azure můžou také lépe škálovat připojení VPN. Další informace najdete v tématu [požadavky na síťové virtuální zařízení (síťové virtuální zařízení) pro vzdálenou práci](../vpn-gateway/nva-work-remotely-support.md).

## <a name="extend-employees-connection-to-access-globally-distributed-resources"></a>Rozšiřování připojení zaměstnanců pro přístup k globálně distribuovaným prostředkům

Tyto služby Azure můžou pracovníkům umožnit přístup k globálním distribuovaným prostředkům. Vaše prostředky můžou být v některé oblasti Azure, v místních sítích nebo dokonce i v jiných veřejných nebo privátních cloudech. 

- **Partnerský vztah virtuálních sítí Azure**: Pokud nasazujete prostředky ve více oblastech Azure a/nebo pokud agregujete připojení vzdálených pracovních zaměstnanců pomocí více virtuálních sítí, můžete vytvořit připojení mezi několika virtuálními sítěmi Azure pomocí partnerského vztahu virtuálních sítí. Další informace najdete v tématu [partnerský vztah virtuálních sítí][VNet-peer].

- **Řešení založené na Azure VPN**: pro vaše vzdálené zaměstnance připojené k Azure prostřednictvím P2S nebo S2S VPN můžete povolit přístup k místním SÍTÍM konfigurací S2S VPN mezi místními sítěmi a Azure VPN Gateway. Další informace najdete v tématu [vytvoření připojení typu Site-to-site][S2S].

- **ExpressRoute**: použití privátního partnerského vztahu ExpressRoute můžete povolit privátní připojení mezi nasazeními Azure a místní infrastrukturou nebo vaší infrastrukturou v zařízení společného umístění. ExpressRoute prostřednictvím partnerského vztahu Microsoftu také umožňuje přístup k veřejným koncovým bodům v Microsoftu z vaší místní sítě. Připojení ExpressRoute se nepřenášejí prostřednictvím veřejného internetu. Nabízí zabezpečené připojení, spolehlivost a vyšší propustnost s nižší a konzistentní latencí než typická připojení přes Internet. Další informace najdete v článku [Přehled ExpressRoute][ExR]. Využití stávajícího poskytovatele sítě, který už je součástí našeho [ekosystému partnerských ExpressRoute][ExR-eco] , může zkrátit dobu, po kterou se získá velká šířka připojení k Microsoftu.  Pomocí [ExpressRoute Direct][ExR-D] můžete svoji místní síť připojit přímo k páteřní síti Microsoftu. ExpressRoute Direct nabízí dvě různé možnosti přenosové rychlosti Dual 10 GB/s nebo 100 GB/s. 

- **Azure Virtual WAN**: Azure Virtual WAN umožňuje bezproblémovou spolupráci mezi připojeními VPN a okruhy ExpressRoute. Jak už bylo zmíněno dříve, Azure Virtual WAN podporuje taky jakékoli propojení mezi prostředky v různých globálních umístěních Prem v různých místních a koncových virtuálních sítích.

## <a name="scale-customer-connectivity-to-frontend-resources"></a>Škálování připojení zákazníků k prostředkům front-endu

V době, kdy se více lidí přejdou online, mnoho podnikových webů zvyšuje provoz zákazníků. Azure Application Gateway může pomáhat spravovat tuto zvýšené zatížení front-endu. Další informace najdete v tématu [Podpora vysokého provozu v Application Gateway](../application-gateway/high-traffic-support.md).

## <a name="microsoft-support-for-multi-cloud-traffic"></a>Podpora pro provoz ve více cloudech od Microsoftu

Pro vaše nasazení v jiných veřejných cloudech může společnost Microsoft poskytovat globální připojení. V tomto ohledu vám může pomáhat Azure Virtual WAN, VPN nebo ExpressRoute. Pokud chcete připojit připojení z Azure k jiným cloudům, můžete nakonfigurovat S2S VPN mezi těmito dvěma cloudy. Připojení z Azure můžete také navázat na jiné veřejné cloudy pomocí ExpressRoute. Oracle Cloud je součástí ekosystému partnerů ExpressRoute. Můžete [nastavit přímé propojení mezi cloudovou infrastrukturou Azure a Oracle][Az-OCI]. Většina poskytovatelů služeb, kteří jsou součástí ekosystému partnerů ExpressRoute, taky nabízí privátní připojení k ostatním veřejným cloudům. S využitím těchto poskytovatelů služeb můžete vytvořit privátní připojení mezi nasazeními v Azure a dalšími cloudy prostřednictvím ExpressRoute.

## <a name="next-steps"></a>Další kroky

Následující články popisují, jak lze pomocí různých síťových funkcí Azure škálovat uživatele, aby fungovaly vzdáleně:

| **Článek** | **Popis** |
| --- | --- |
| [Jak povolit vzdálenou práci uživatelů](../vpn-gateway/work-remotely-support.md) | Projděte si dostupné možnosti pro nastavení vzdáleného přístupu pro uživatele nebo doplnění stávajících řešení o další kapacitu vaší organizace.|
| [Působit potíže se do služby stravování, abyste mohli pracovat s požadavky na domácnosti? Tady je místo, kde může Azure Virtual WAN pomáhat.](../virtual-wan/work-remotely-support.md) | Využijte Azure Virtual WAN k řešení potřeb vaší organizace pro vzdálené připojení.|
| [Podpora vysokého provozu služby Application Gateway](../application-gateway/high-traffic-support.md) | Pomocí Application Gateway s firewallem webových aplikací (WAF) můžete škálovatelný a zabezpečený způsob správy provozu do webových aplikací. |
| [Požadavky na síťové virtuální zařízení (síťové virtuální zařízení) pro vzdálenou práci](../vpn-gateway/nva-work-remotely-support.md)|Přečtěte si pokyny k využití síťová virtuální zařízení v Azure k poskytování řešení vzdáleného přístupu. |
| [Přechod na Protokol OpenVPN nebo IKEv2 z SSTP](../vpn-gateway/ikev2-openvpn-from-sstp.md) | Přechodem na Protokol OpenVPN nebo IKEv2 překonání limitu souběžného počtu připojení (SSTP) 128.|
| [Vzdálená práce pomocí Azure bastionu](../bastion/work-remotely-support.md) | Poskytněte zabezpečené a bezproblémové připojení RDP/SSH k virtuálním počítačům v rámci virtuální sítě Azure přímo v Azure Portal bez použití veřejné IP adresy. |
| [Použití Azure ExpressRoute k vytvoření hybridního připojení pro podporu vzdálených uživatelů](../expressroute/work-remotely-support.md) | Použijte ExpressRoute pro hybridní připojení, aby uživatelé ve vaší organizaci mohli pracovat vzdáleně.|
| [Podpora vzdálené práce Azure Firewall](../firewall/remote-work-support.md)|Chraňte své prostředky virtuální sítě Azure pomocí Azure Firewall. |

<!--Link References-->
[VNet-peer]: ../virtual-network/virtual-network-peering-overview.md
[S2S]: ../vpn-gateway/tutorial-site-to-site-portal.md
[ExR]: ../expressroute/expressroute-introduction.md
[ExR-eco]: ../expressroute/expressroute-locations.md
[ExR-D]: ../expressroute/expressroute-erdirect-about.md
[Az-OCI]: ../virtual-machines/workloads/oracle/configure-azure-oci-networking.md