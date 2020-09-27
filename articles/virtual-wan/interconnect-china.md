---
title: Propojení s Čínou pomocí Azure Virtual WAN a zabezpečeného centra
description: Naučte se, jak propojit s Čínou pomocí Azure Virtual WAN a zabezpečeného centra.
services: virtual-wan
author: skishen525
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: sukishen
ms.openlocfilehash: 83cc7757f31a631af755155b49c7c26753618426
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2020
ms.locfileid: "91399104"
---
# <a name="interconnect-with-china-using-azure-virtual-wan-and-secure-hub"></a>Propojení s Čínou pomocí Azure Virtual WAN a zabezpečeného centra

Při prohlížení společného automobilu, výroby, logistického odvětví nebo jiných ústavů, jako je velvyslanectví, často existuje otázka, jak vylepšit vzájemné propojení s Čínou. Tato vylepšení jsou většinou relevantní pro použití Cloud Services jako Microsoft 365, globálních služeb Azure nebo větví propojení v Číně s páteřní sítí zákazníka.

Ve většině případů se zákazníci působit potíže s vysokou latencí, nízkou šířkou pásma, nestabilním připojením a s vysokou propustností, které se připojují k mimo Čína (například Evropa nebo USA).

Důvodem pro tyto potýká je "skvělý firewall pro Čínu", který chrání čínskou část internetu a filtruje provoz do Číny. Skoro veškerý provoz běžící z Číny pevniny mimo Čínu, s výjimkou zvláštních zón správy, jako jsou Hongkong a Macao, dává skvělé brány firewall. Provoz běžící přes Hongkong a Macao nevyužívá skvělou bránu firewall v plné síle, je zpracována podmnožinou skvělé brány firewall.

![Propojení zprostředkovatele](./media/interconnect-china/provider.png)

Pomocí virtuální sítě WAN může zákazník vytvořit další výkonné a stabilní připojení k Microsoft Cloud službám a připojení k podnikové síti bez narušení čínského zákonu kyberbezpečnosti.

## <a name="requirements-and-workflow"></a><a name="requirements"></a>Požadavky a pracovní postup

Pokud chcete zachovat předpisy čínského kyberbezpečnosti, musíte splnit určitou sadu podmínek.

Nejprve je třeba spolupracovat se sítí a poskytovatelem internetových služeb, který vlastní licenci ICP (Internet Content Provider) pro Čínu. Ve většině případů skončíte s jedním z následujících zprostředkovatelů:

* Čína Telecom Global Ltd.
* Čína Mobile Ltd.
* Čína UNICOM Ltd.
* PCCW Global Ltd.
* Hongkong Telecom Ltd.

V závislosti na poskytovateli a vašich potřebách teď musíte koupit jednu z následujících služeb připojení k síti, abyste mohli propojit své větve v Číně.

* Síť MPLS/IPVPN 
* Software definovaný v síti WAN (SDWAN)
* Vyhrazený internetový přístup

Dále musíte souhlasit s tímto poskytovatelem, aby užitečných globální síť Microsoftu a její hranici Síťové vstupy Hongkong, ne v Peking nebo Shanghai. V tomto případě je velmi důležité, protože se jedná o fyzické připojení a umístění do Číny.

I když se většina zákazníků domníváme, že použití Singapuru pro Interconnect je nejlepší, protože při prohlížení mapy vypadá blízko – Čína, to není pravdivé. Při sledování síťových Fiber map se téměř všechny sítě připojí přes Peking, Shanghai a Hongkong. V důsledku toho je vhodné zvolit lepší umístění pro propojení s Čínou.

V závislosti na poskytovateli můžete získat různé nabídky služeb. Následující tabulka ukazuje příklad poskytovatelů a služby, které nabízí, na základě informací v době, kdy byl tento článek napsán.

| Služba | Příklady poskytovatelů |
| --- | --- |
| Síť MPLS/IPVPN |PCCW, Čína – celosvětová telekomunikace |
|SDWAN| PCCW, Čína – celosvětová telekomunikace|
| Vyhrazený internetový přístup | PCCW, Hongkong – zvláštní telekomunikace, Čína mobil|

S vaším poskytovatelem můžete souhlasit s tím, která z následujících dvou řešení se mají použít pro přístup k globální páteřní síti Microsoftu:

* Probíhá ukončení Microsoft Azure ExpressRoute ukončená v Hongkongu. To by mělo být případ použití MPLS/IPVPN. V současné době je v současnosti jenom jediný poskytovatel licence ICP, který má ExpressRoute na Hongkong – Telecom celosvětově. Nicméně můžou komunikovat s ostatními poskytovateli, pokud využívají poskytovatele cloudových výměn, jako je Megaport nebo Cloud. Další informace najdete v tématu [poskytovatelé připojení ExpressRoute](../expressroute/expressroute-locations-providers.md#partners).

* Použití vyhrazeného internetového přístupu přímo v jednom z následujících internetových bodů Exchange nebo pomocí propojení privátní sítě.

V následujících seznamech jsou uvedeny možné internetové výměny v Hongkongu:

* AMS – IX – Hongkong
* BBIX Hongkong
* Equinix Hongkong
* HKIX

Při použití tohoto připojení musí být vaším dalším segmentem směrování protokolu BGP pro služby Microsoftu číslo autonomního systému Microsoft (#) 8075. Pokud používáte jedno umístění nebo řešení SDWAN, může to být volba připojení.

V obou případech stále doporučujeme, abyste měli k dispozici druhý a běžný internetový užitečných do kontinentální části čínského. Slouží k rozdělení provozu mezi podnikovým provozem na cloudové služby, jako jsou Microsoft 365 a Azure, a podle zákona řízený internetový provoz.

Kompatibilní architektura sítě v rámci Číny může vypadat jako v následujícím příkladu:

![Více větví](./media/interconnect-china/multi-branch.png)

V tomto příkladu, který má propojení s globálním Síťové vstupy Microsoftu, teď můžete začít využívat [Azure Virtual WAN pro globální přenosovou architekturu](virtual-wan-global-transit-network-architecture.md) a další služby, jako je Azure Secure Virtual WAN, aby se daly využívat služby a propojení s vašimi pobočkami a datacentrem mimo Čína.

## <a name="hub-to-hub-communication"></a><a name="hub-to-hub"></a>Komunikace mezi rozbočovači

V této části používáme komunikaci mezi rozbočovači a připojením typu Virtual WAN k propojení. V tomto scénáři vytvoříte nový virtuální prostředek sítě WAN pro připojení k virtuálnímu rozbočovači WAN v Hongkongu, dalších oblastech, které dáváte přednost, oblasti, kde už máte prostředky Azure, nebo pokud se chcete připojit.

Ukázková architektura by mohla vypadat jako v následujícím příkladu:

![Ukázka sítě WAN](./media/interconnect-china/sample.png)

V tomto příkladu se čínské větve připojí ke cloudové Číně Azure a navzájem pomocí připojení VPN nebo MPLS. Větve, které je potřeba připojit ke globálním službám, používají MPLS nebo internetové služby, které jsou připojené přímo k Hongkongu. Pokud chcete používat ExpressRoute v Hongkongu i v jiné oblasti, je nutné nakonfigurovat [ExpressRoute Global REACH](../expressroute/expressroute-global-reach.md) pro propojení obou okruhů ExpressRoute.

ExpressRoute Global Reach není v některých oblastech k dispozici. Pokud potřebujete propojit s Brazílií nebo Indie, například potřebujete využít [poskytovatele cloudového Exchange](../expressroute/expressroute-locations.md#connectivity-through-exchange-providers) k poskytování služeb směrování.

Následující obrázek ukazuje oba příklady pro tento scénář.

![Global Reach](./media/interconnect-china/global.png)

## <a name="secure-internet-breakout-for-microsoft-365"></a><a name="secure"></a>Zabezpečená internetová užitečných pro Microsoft 365

Dalším aspektem je zabezpečení sítě a protokolování pro vstupní bod mezi Čínou a virtuální sítí WAN vytvořená páteřní součást a páteřní síť zákazníka. Ve většině případů je třeba užitečných na Internet v Hongkongu, aby přímo dosáhly sítě Microsoft Edge a s tím se servery front-dveří Azure používané pro Microsoft 365 služby.

U obou scénářů s virtuální sítí WAN byste využili [zabezpečené centrum Azure Virtual WAN](../firewall-manager/secured-virtual-hub.md). Pomocí správce Azure Firewall můžete změnit normální virtuální síť WAN na zabezpečený rozbočovač a potom nasadit a spravovat Azure Firewall v rámci tohoto centra.

Příklad tohoto scénáře vidíte na následujícím obrázku:

![Internet užitečných pro webové služby a provoz služeb Microsoftu](./media/interconnect-china/internet.png)

## <a name="architecture-and-traffic-flows"></a><a name="traffic"></a>Architektura architektury a provozu

V závislosti na vaší volbě, která se týká připojení k Hongkongu, se celková architektura může mírně měnit. V této části se zobrazují tři dostupné architektury v různých kombinacích s VPN nebo SDWAN a/nebo ExpressRoute.

Všechny tyto možnosti využívají zabezpečené centrum Azure Virtual WAN pro přímé připojení Microsoft 365 v Hongkongu. Tyto architektury také podporují požadavky na dodržování předpisů pro [Microsoft 365 s více geografickými](/microsoft-365/enterprise/microsoft-365-multi-geo) oblastmi a udržují provoz v blízkosti dalšího umístění front-dveří Azure. V důsledku toho je také lepší využití Microsoft 365 mimo Čína.

Při použití Azure Virtual WAN společně s připojením k Internetu může každé připojení těžit z dalších služeb, jako je [Microsoft Azure služby partnerského vztahu (Maps)](https://docs.microsoft.com/azure/peering-service/about). Služba MAPS byla navržena pro optimalizaci provozu přicházejícího do globální sítě Microsoft od poskytovatelů internetových služeb třetích stran.

### <a name="option-1-sdwan-or-vpn"></a><a name="option-1"></a>Možnost 1: SDWAN nebo VPN

Tato část popisuje návrh, který využívá SDWAN nebo VPN k Hongkongu a k ostatním větvím. Tato možnost zobrazuje tok použití a přenosu při použití čistého připojení k Internetu na obou lokalitách páteře virtuální sítě WAN. V takovém případě se připojení zavede na nekonkrétní použití vyhrazeného internetového přístupu nebo řešení SDWAN poskytovatele ICP. Jiné větve využívají také čistě Internet nebo SDWAN řešení.

![Čína pro Hongkong provozu](./media/interconnect-china/china-traffic.png)

V této architektuře je každá lokalita připojená k globální síti Microsoft pomocí sítě VPN a Azure Virtual WAN. Přenos dat mezi lokalitami a Hongkong se přenáší do sítě Microsoft a používá se k poslednímu kilometru jenom běžné připojení k Internetu.

### <a name="option-2-expressroute-and-sdwan-or-vpn"></a><a name="option-2"></a>Možnost 2: ExpressRoute a SDWAN nebo VPN

Tato část popisuje návrh, který používá ExpressRoute v Hongkongu a dalších větvích s větvemi VPN/SDWAN. Tato možnost zobrazuje použití a ExpressRoute ukončené v Hongkongu a dalších větvích připojených prostřednictvím SDWAN nebo VPN. ExpressRoute v Hongkongu je aktuálně omezený na krátký seznam zprostředkovatelů, které najdete v seznamu [partnerů Express Route](../expressroute/expressroute-locations-providers.md#global-commercial-azure).

![Čína pro Hongkong ExpressRoute provozu](./media/interconnect-china/expressroute.png)

K dispozici jsou také možnosti pro ukončení ExpressRoute z Číny, například v Jižní Koreje nebo v Japonsku. Ale dodržování předpisů, regulace a latence je aktuálně nejlepší volbou.

### <a name="option-3-expressroute-only"></a><a name="option-3"></a>Možnost 3: jenom ExpressRoute

Tato část popisuje návrh, ve kterém se ExpressRoute používá pro Hongkong a další větve. Tato možnost zobrazí propojení pomocí ExpressRoute na obou koncích. V tomto případě máte jiný tok provozu než druhý. Přenos Microsoft 365 se bude přesměrovat do zabezpečeného centra Azure Virtual WAN a odtud do sítě Microsoft Edge a do Internetu.

Provoz, který směřuje do propojených větví nebo z nich do umístění v Číně, bude v rámci této architektury splňovat jiný přístup. Virtuální síť WAN v současné době nepodporuje ExpressRoute k přenosu ExpressRoute. Provoz bude využívat ExpressRoute Global Reach nebo propojení třetích stran bez předávání virtuálního centra WAN. Přímo se přesměruje z jednoho Microsoft Enterprise Edge (MSEE) do jiného.

![ExpressRoute Global Reach](./media/interconnect-china/expressroute-virtual.png)

Aktuálně ExpressRoute Global Reach není k dispozici v každé zemi nebo oblasti, ale můžete nakonfigurovat řešení pomocí Azure Virtual WAN.

Můžete například nakonfigurovat ExpressRoute s partnerským vztahem Microsoftu a připojit tunel VPN prostřednictvím tohoto partnerského vztahu k Azure Virtual WAN. Nyní jste povolili a znovu provedete přenos mezi sítěmi VPN a ExpressRoute bez Global Reach a poskytovatele a služby třetí strany, jako je Megaport Cloud.

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích:

* [Globální přenosová architektura sítě s využitím Azure Virtual WAN](virtual-wan-global-transit-network-architecture.md)

* [Vytvoření virtuálního centra sítě WAN](virtual-wan-site-to-site-portal.md)

* [Konfigurace virtuálního zabezpečeného centra WAN](../firewall-manager/secure-cloud-network.md)

* [Přehled služby Azure peering ve verzi Preview](https://docs.microsoft.com/azure/peering-service/about)
