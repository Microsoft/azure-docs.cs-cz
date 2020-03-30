---
title: Globální síť Microsoftu – Azure
description: Popisuje, jak společnost Microsoft buduje svou rychlou a spolehlivou globální síť
services: networking
documentationcenter: ''
author: KumudD
manager: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/13/2019
ms.author: kumud
ms.reviewer: ypitsch
ms.openlocfilehash: 10a061163447a60f1c25b386ef28028436284650
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75453077"
---
# <a name="microsoft-global-network"></a>Globální síť Microsoftu

Microsoft vlastní a provozuje jednu z největších páteřních sítí na světě. Tato globální a sofistikovaná architektura, která zahrnuje více než 100 000 mil, spojuje naše datová centra a zákazníky. 
 
Zákazníci po celém světě každý den připojují a předávají biliony požadavků do Microsoft Azure, Bingu, Dynamics 365, Office 365, XBoxu a mnoha dalších. Bez ohledu na typ zákazníci očekávají okamžitou spolehlivost a odezvu z našich služeb. 
 
[Globální síť (WAN) společnosti Microsoft](https://azure.microsoft.com/global-infrastructure/global-network/) je centrální součástí poskytování skvělého cloudového prostředí. Naše globální síť propojuje naše [datová centra](https://azure.microsoft.com/global-infrastructure/) Microsoftu v 54 oblastech Azure a velké sítě hraničních uzlů strategicky umístěných po celém světě a nabízí dostupnost, kapacitu i flexibilitu pro splnění veškerých poptávek.

![Globální síť Microsoftu](./media/microsoft-global-network/microsoft-global-wan.png)
 
## <a name="get-the-premium-cloud-network"></a>Získejte prémiovou cloudovou síť
 
Při používání cloudu Microsoftu je snadné se rozhodnout pro [nejlepší možné prostředí.](https://www.sdxcentral.com/articles/news/azure-tops-aws-gcp-in-cloud-performance-says-thousandeyes/2018/11/) Od okamžiku, kdy zákazník do naší globální sítě vstupuje přes naše strategicky umístěné hraniční uzly, vaše data putují optimalizovanými trasami rychlostí blížící se rychlosti světla. To zajišťuje optimální latenci pro nejlepší výkon. Tyto hraniční uzly, všechny propojené s více než 3500 jedinečnými internetovými partnery (partnery) prostřednictvím tisíců připojení ve více než 145 lokalitách, jsou základem naší strategie propojení. 
 
Ať už se připojujete z Londýna do Tokia nebo z Washingtonu DC do Los Angeles, výkon sítě je kvantifikován a ovlivněn věcmi, jako je latence, nervozita, ztráta paketů a propustnost.  Ve společnosti Microsoft dáváme přednost a používáme přímé propojení na rozdíl od tranzitních odkazů, což udržuje provoz odezvy symetrický a pomáhá udržovat směrování, peeringové strany a cesty co nejkratší a nejjednodušší. 

Pokud se například uživatel v Londýně pokusí o přístup ke službě v Tokiu, internetový provoz vstoupí do jedné z našich okrajů v Londýně, přejde přes microsoft wan přes Francii, naše transarabské cesty mezi Evropou a Indií a pak do Japonska, kde je služba hostována. Provoz odezvy je symetrický. To je někdy označováno jako [směrování za studena-brambor,](https://en.wikipedia.org/wiki/Hot-potato_and_cold-potato_routing) což znamená, že provoz zůstane v síti společnosti Microsoft tak dlouho, jak je to možné, než jsme ji předat.  
  
Takže to znamená, že všechny a veškerý provoz při používání služeb společnosti Microsoft? Ano, veškerý provoz mezi datovými centry, v rámci Microsoft Azure nebo mezi službami Microsoftu, jako jsou virtuální počítače, Office 365, XBox, SQL DB, úložiště a virtuální sítě, se směruje v rámci naší globální sítě a nikdy přes veřejný internet, aby byl zajištěn optimální výkon a integritu.  
 
Masivní investice do kapacity vláken a rozmanitosti napříč metro, pozemní a podmořské cesty jsou pro nás zásadní pro udržení konzistentní a vysoké úrovně služeb a zároveň podněcují extrémní růst našich cloudových a online služeb. Nedávné přírůstky do naší globální sítě jsou naše [MAREA](https://www.submarinecablemap.com/#/submarine-cable/marea) podmořský kabel, průmysl je první Open Line System (OLS) přes podmořské, mezi Bilbao, Španělsko a Virginia Beach, Virginia, USA, stejně jako [AEC](https://www.submarinecablemap.com/#/submarine-cable/aeconnect-1) mezi New York, USA a Dublin, Irsko a [New Cross Pacific (NCP)](https://www.submarinecablemap.com/#/submarine-cable/new-cross-pacific-ncp-cable-system) mezi Tokiem, Japonskem a Portland, Oregon, USA. 
 

## <a name="our-network-is-your-network"></a>Naše síť je vaše síť

Dali jsme dvě desetiletí zkušeností, spolu s masivními investicemi do sítě, abychom zajistili optimální výkon za všech okolností. Firmy mohou plně využít našich síťových aktiv a nahoře vytvářet pokročilé překryvné architektury. 
 
Microsoft Azure nabízí nejbohatší portfolio služeb a možností, které zákazníkům umožňuje rychle a snadno vytvářet, rozšiřovat a plnit požadavky na sítě kdekoli. Naše rodina služeb připojení zahrnuje partnerský vztah virtuální sítě mezi oblastmi, hybridními architekturami typu point-to-site a site v cloudu a také globálními scénáři přenosu IP adres.  Pro podniky, které chtějí připojit své vlastní datové centrum nebo síť k Azure, nebo zákazníkům s masivními potřebami příjmu dat nebo přenosu, [expressroute](../expressroute/expressroute-introduction.md)a [ExpressRoute Direct](../expressroute/expressroute-erdirect-about.md) poskytují možnosti až 100 Gb/s šířky pásma přímo do globální sítě Microsoftu v partnerských lokalitách po celém světě.  
 
[ExpressRoute Global Reach](../expressroute/expressroute-global-reach.md) je navržen tak, aby doplňoval implementaci wan u vašeho poskytovatele služeb a propojil vaše místní weby po celém světě. Pokud spustíte globální operaci, můžete použít ExpressRoute Global Reach ve spojení s upřednostňovanými a místními poskytovateli služeb a připojit všechny globální weby pomocí globální sítě společnosti Microsoft. Rozšíření nové sítě v cloudu (WAN) tak, aby zahrnovalo velké množství poboček, lze provést prostřednictvím virtuální sítě Azure WAN, což přináší možnost bezproblémového připojení vašich poboček ke globální síti Microsoftu pomocí zařízení SDWAN & VPN (tj. zařízení Customer Premises Equipment nebo CPE) s integrovaným snadným používáním a automatizovanou správou připojení a konfigurace. 
 
[Globální partnerský vztah virtuální sítě](../virtual-network/virtual-network-peering-overview.md) umožňuje zákazníkům bezproblémově propojit dvě nebo více virtuálních sítí Azure napříč oblastmi. Po vzájemném vztahu se virtuální sítě zobrazí jako jeden. Provoz mezi virtuálními počítači v partnerských virtuálních sítích je směrován přes páteřní infrastrukturu Microsoftu, podobně jako je provoz směrován mezi virtuálními počítači ve stejné virtuální síti – pouze přes privátní IP adresy. 
 

## <a name="well-managed-using-software-defined-innovation"></a>Dobře řízené pomocí softwarově definovaných inovací

Společnost Microsoft, která provozuje jeden z předních cloudů na světě, získala mnoho poznatků a zkušeností s budováním a správou vysoce výkonné globální infrastruktury.  
 
Dodržujeme robustní sadu provozních principů: 
 
- Používejte nejlepší přepínací hardware napříč různými úrovněmi sítě.  
- Nasazujte nové funkce s nulovým dopadem na koncové uživatele.  
- Co nejrychleji zaveďte aktualizace bezpečně a spolehlivě napříč vozovým parkem. Hodiny místo týdnů.  
- Využijte hlubokou telemetrii v cloudovém měřítku a plně automatizované zmírňování chyb.  
- Používejte jednotnou a softwarově definovanou síťovou technologii k řízení všech hardwarových prvků v síti.  Eliminace duplicit a snížení poruch. 
 
Tyto principy platí pro všechny vrstvy sítě: od hostitelského síťového rozhraní, spínací platformy, síťových funkcí v datovém centru, jako jsou nástroje pro vyrovnávání zatížení, až po WAN s naší platformou dopravního inženýrství a našimi optickými sítěmi.  
 
Exponenciální růst Azure a jeho sítě dosáhl bodu, kdy jsme si nakonec uvědomili, že lidská intuice již nemůže být spoléhala na správu operací globální sítě. Abychom splnili potřebu ověřit dlouhodobé, střednědobé a krátkodobé změny v síti, vyvinuli jsme platformu pro syntetizaci a emulaci naší výrobní sítě. Schopnost vytvářet zrcadlená prostředí a spouštět miliony simulací nám umožňuje testovat změny softwaru a hardwaru a jejich dopad, než je zavážeme k naší výrobní platformě a síti. 

## <a name="next-steps"></a>Další kroky
- [Další informace o síťových službách poskytovaných v Azure](https://azure.microsoft.com/product-categories/networking/)
