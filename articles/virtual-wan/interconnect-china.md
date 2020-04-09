---
title: Propojení s Čínou pomocí virtuální sítě Azure A zabezpečeného centra
description: Seznamte se s automatizovaným škálovatelným připojením mezi větvemi virtuální sítě WAN, dostupnými oblastmi a partnery.
services: virtual-wan
author: skishen525
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: sukishen
ms.openlocfilehash: d086484ece6faf95dccffb1e29da8a0e906b1da4
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985622"
---
# <a name="interconnect-with-china-using-azure-virtual-wan-and-secure-hub"></a>Propojení s Čínou pomocí Virtuální sítě Azure a Secure Hub

Při pohledu na společný automobilový průmysl, výrobu, logistický průmysl nebo jiné instituce, jako jsou velvyslanectví, je často otázka, jak zlepšit propojení s Čínou. Tato vylepšení jsou většinou relevantní pro používání cloudových služeb, jako je Office 365, Azure Global Services nebo propojení poboček uvnitř Číny s páteří zákazníka.

Ve většině případů se zákazníci potýkají s vysokou latencí, nízkou šířkou pásma, nestabilním připojením a vysokými náklady, které se připojují mimo Čínu (například do Evropy nebo Spojených států).

Důvodem těchto bojů je "Velký čínský firewall", který chrání čínskou část internetu a filtruje dopravu do Číny. Téměř veškerý provoz, který probíhá z pevninské Číny mimo Čínu, s výjimkou zvláštních administrativních zón, jako je Hongkong a Macao, prochází Velkým firewallem. Provoz projíždět Hongkongem a Macaem nenarazí na Velký firewall v plné síle, je zpracován podmnožinou Velké brány firewall.

![Propojení zprostředkovatele](./media/interconnect-china/provider.png)

Pomocí virtuální sítě WAN může zákazník navázat výkonnější a stabilnější připojení ke cloudovým službám společnosti Microsoft a připojení k podnikové síti, aniž by porušil čínské zákony o kybernetické bezpečnosti.

## <a name="requirements-and-workflow"></a><a name="requirements"></a>Požadavky a pracovní postup

Pokud chcete zůstat v souladu s čínským zákonem o kybernetické bezpečnosti, musíte splnit soubor určitých podmínek.

Nejprve je třeba spolupracovat se sítí a poskytovatelem služeb Internetu, který vlastní licenci ICP (Internet Content Provider) pro Čínu. Ve většině případů skončíte s jedním z následujících poskytovatelů:

* China Telecom Global Ltd.
* Čína Mobile Ltd
* Čína Unicom Ltd.
* PCCW Global s.r.o.
* Hong Kong Telecom Ltd.

V závislosti na poskytovateli a vašich potřebách je nyní třeba zakoupit jednu z následujících služeb připojení k síti, abyste mohli propojit své pobočky v Rámci Číny.

* Síť MPLS/IPVPN 
* Softwarově definovaná wan (SDWAN)
* Vyhrazený přístup k internetu

Dále musíte souhlasit s tímto poskytovatelem, abyste poskytli průlom společnosti Microsoft Global Network a její edge network v Hongkongu, nikoli v Pekingu nebo Šanghaji. V tomto případě je Hongkong velmi důležitý kvůli jeho fyzickému spojení a umístění do Číny.

Zatímco většina zákazníků si myslí, že použití Singapuru pro propojení je nejlepší případ, protože vypadá blíže k Číně při pohledu na mapu, to není pravda. Když sledujete mapy síťových vláken, téměř všechna síťová připojení procházejí Pekingem, Šanghají a Hongkongem. Díky tomu je Hongkong lepší volbou pro propojení s Čínou.

V závislosti na poskytovateli můžete získat různé nabídky služeb. Níže uvedená tabulka ukazuje příklad poskytovatelů a služby, které nabízejí, na základě informací v době, kdy byl tento článek napsán.

| Služba | Příklady zprostředkovatelů |
| --- | --- |
| Síť MPLS/IPVPN |PCCW, China Telecom Globální |
|SDWAN| PCCW, China Telecom Globální|
| Vyhrazený přístup k internetu | PCCW, Hong Kong Telecom, Čína Mobil|

Se svým poskytovatelem se můžete dohodnout na tom, které z následujících dvou řešení použít k dosažení globální páteřní sítě společnosti Microsoft:

* Získání Microsoft Azure ExpressRoute ukončena v Hongkongu. To by byl případ pro použití MPLS / IPVPN. V současné době je jediným poskytovatelem licencí ICP s ExpressRoute do Hongkongu china telecom global. Mohou však také mluvit s ostatními poskytovateli, pokud využívají poskytovatele cloudových exchange, jako je Megaport nebo InterCloud. Další informace naleznete v tématu [ExpressRoute zprostředkovatelé připojení](../expressroute/expressroute-locations-providers.md#partners).

* Použití vyhrazeného přístupu k Internetu přímo v jednom z následujících výměnných bodů Sítě Internet nebo pomocí propojení privátní sítě.

V následujícím seznamu je uvedeno možné internetové výměny v Hongkongu:

* AMS-IX Hongkong
* BBIX Hongkong
* Equinix Hongkong
* HKIX

Při použití tohoto připojení musí být dalším směrováním protokolu BGP pro služby společnosti Microsoft číslo autonomního systému (AS#) 8075. Pokud používáte jedno umístění nebo řešení SDWAN, to by byla volba připojení.

Ať tak či onak, stále doporučujeme, abyste měli druhý a pravidelný internet Breakout do čínské pevniny. Tím se rozdělí provoz mezi podnikový provoz na cloudové služby, jako je Microsoft 365 a Azure, a podle zákona regulovaný internetový provoz.

Kompatibilní síťová architektura v Číně může vypadat jako následující příklad:

![Více větví](./media/interconnect-china/multi-branch.png)

V tomto příkladu, které mají propojení s Microsoft Global Network v Hongkongu, můžete nyní začít využívat [Azure Virtual WAN Global Transit Architecture](virtual-wan-global-transit-network-architecture.md) a další služby, jako je Azure zabezpečené virtuální WAN hub, s cílem využívat služby a propojit se s pobočkami a datovým centrem mimo Čínu.

## <a name="hub-to-hub-communication"></a><a name="hub-to-hub"></a>Komunikace mezi rozbočovači

V této části používáme virtuální komunikaci WAN rozbočovače k rozbočovači pro propojení. V tomto scénáři vytvoříte nový prostředek centra Virtuální WAN pro připojení k centru Virtuální WAN v Hongkongu, jiné oblasti, které upřednostňujete, oblast, kde už máte prostředky Azure nebo kde se chcete připojit.

Ukázková architektura může vypadat jako následující příklad:

![Ukázka sítě WAN](./media/interconnect-china/sample.png)

V tomto příkladu se pobočky V Číně připojují k Azure Cloud China a navzájem pomocí připojení VPN nebo MPLS. Pobočky, které je třeba připojit ke globálním službám, používají mpls nebo internetové služby, které jsou připojeny přímo do Hongkongu. Pokud chcete použít ExpressRoute v Hongkongu i v jiné oblasti, musíte nakonfigurovat [ExpressRoute Global Reach](../expressroute/expressroute-global-reach.md) pro propojení obou okruhů ExpressRoute.

ExpressRoute Global Reach není v některých oblastech k dispozici. Pokud potřebujete například propojit s Brazílií nebo Indií, musíte k poskytování směrovacích služeb využít [poskytovatele cloudových exchange.](../expressroute/expressroute-locations.md#connectivity-through-exchange-providers)

Na obrázku níže jsou uvedeny oba příklady pro tento scénář.

![Globální dosah](./media/interconnect-china/global.png)

## <a name="secure-internet-breakout-for-office-365"></a><a name="secure"></a>Zabezpečený internet breakout pro Office 365

Dalším aspektem je zabezpečení sítě, stejně jako protokolování pro vstupní bod mezi Čínou a virtuální WAN zavedené páteřní součásti a páteřní sítí zákazníka. Ve většině případů je potřeba proniknout na Internet v Hongkongu, aby se přímo dostal y do sítě Microsoft Edge Network a s tím i na servery Azure Front Door používané pro služby Microsoft 365.

Pro oba scénáře s virtuální WAN byste využít [Azure Virtual WAN zabezpečené rozbočovač](../firewall-manager/secured-virtual-hub.md). Pomocí Správce Azure Firewall můžete změnit běžné centrum Virtual WAN na zabezpečené centrum a pak v rámci tohoto centra nasadit a spravovat bránu Azure Firewall.

Následující obrázek ukazuje příklad tohoto scénáře:

![Internet breakout pro provoz webových služeb a služeb společnosti Microsoft](./media/interconnect-china/internet.png)

## <a name="architecture-and-traffic-flows"></a><a name="traffic"></a>Architektura a dopravní toky

V závislosti na vaší volbě, pokud jde o připojení k Hongkongu, se celková architektura může mírně změnit. Tato část zobrazuje tři dostupné architektury v různých kombinacích s VPN nebo SDWAN a/nebo ExpressRoute.

Všechny tyto možnosti využívají zabezpečené centrum Azure Virtual WAN pro přímé připojení M365 v Hongkongu. Tyto architektury také podporují požadavky na dodržování předpisů pro [Office 365 Multi-Geo](https://docs.microsoft.com/office365/enterprise/office-365-multi-geo) a udržují tento provoz v blízkosti dalšího umístění předních dveří Office 365. V důsledku toho je to také zlepšení pro použití Microsoft 365 z Číny.

Při použití Azure Virtual WAN společně s připojením k Internetu může každé připojení využívat další služby, jako jsou [služby Partnerského vztahu Microsoft Azure (MAPS).](https://docs.microsoft.com/azure/peering-service/about) Služba MAPS byla vytvořena pro optimalizaci provozu přicházejícího do globální sítě společnosti Microsoft od poskytovatelů internetových služeb třetích stran.

### <a name="option-1-sdwan-or-vpn"></a><a name="option-1"></a>Možnost 1: SDWAN nebo VPN

Tato část popisuje návrh, který používá SDWAN nebo VPN do Hongkongu a do jiných větví. Tato možnost zobrazuje tok použití a provozu při použití čistého připojení k Internetu na obou webech páteřní sítě Virtuální wan. V tomto případě je připojení přeneseno do Hongkongu pomocí vyhrazeného přístupu k Internetu nebo řešení SDWAN poskytovatele ICP. Ostatní pobočky používají čistý Internet nebo SDWAN Solutions stejně.

![Čína do Hongkongu provozu](./media/interconnect-china/china-traffic.png)

V této architektuře je každá lokalita připojena k Microsoft Global Network pomocí vpn a Azure Virtual WAN. Provoz mezi weby a Hongkongem je přenášen korytem sítě Microsoft Network a používá pouze běžné připojení k Internetu na poslední míli.

### <a name="option-2-expressroute-and-sdwan-or-vpn"></a><a name="option-2"></a>Možnost 2: ExpressRoute a SDWAN nebo VPN

Tato část popisuje návrh, který používá ExpressRoute v Hongkongu a další větve s pobočkami VPN/SDWAN. Tato možnost ukazuje použití a ExpressRoute ukončena v Hongkongu a dalších větví připojených přes SDWAN nebo VPN. ExpressRoute v Hongkongu je v současné době omezena na krátký seznam poskytovatelů, které najdete v seznamu [Express Route Partners](../expressroute/expressroute-locations-providers.md#global-commercial-azure).

![Čína do Hongkongu dopravní ExpressRoute](./media/interconnect-china/expressroute.png)

Existují také možnosti ukončení ExpressRoute z Číny, například v Jižní Koreji nebo Japonsku. Vzhledem k dodržování předpisů, regulaci a latenci je však Hongkong v současnosti nejlepší volbou.

### <a name="option-3-expressroute-only"></a><a name="option-3"></a>Možnost 3: Pouze ExpressRoute

Tato část popisuje návrh, který kde ExpressRoute se používá pro Hongkong a další větve. Tato možnost zobrazuje propojení pomocí ExpressRoute na obou koncích. Zde máte jiný dopravní tok než ostatní. Provoz Microsoftu 365 bude přetékat do zabezpečeného centra Azure virtual WAN a odtud do microsoft edge network a na Internet.

Provoz, který jde do vzájemně propojených poboček nebo z nich do míst v Číně bude následovat jiný přístup v rámci této architektury. V současné době virtuální WAN nepodporuje ExpressRoute na ExpressRoute tranzitu. Provoz bude využívat ExpressRoute Global Reach nebo třetí strany propojení bez předání virtuální WAN Hub. Bude přímo tok z jednoho Microsoft Enterprise Edge (MSEE) do jiného.

![ExpressRoute Global Reach](./media/interconnect-china/expressroute-virtual.png)

V současné době ExpressRoute Global Reach není k dispozici ve všech zemích, ale můžete nakonfigurovat řešení pomocí Azure Virtual WAN.

Můžete například nakonfigurovat ExpressRoute s partnerským vztahem Microsoftu a připojit tunel VPN prostřednictvím tohoto partnerského vztahu k virtuální síti Azure. Nyní jste opět povolili přenos mezi VPN a ExpressRoute bez globálního dosahu a poskytovatelem a službami třetích stran, jako je Megaport Cloud.

## <a name="next-steps"></a>Další kroky

Další informace naleznete v následujících článcích:

* [Architektura globální chodnících s virtuální sítí Azure](virtual-wan-global-transit-network-architecture.md)

* [Vytvoření centra virtuální sítě WAN](virtual-wan-site-to-site-portal.md)

* [Konfigurace zabezpečeného rozbočovače Virtual WAN](../firewall-manager/secure-cloud-network.md)

* [Přehled náhledu služby Azure Partnerskývztah](https://docs.microsoft.com/azure/peering-service/about)
