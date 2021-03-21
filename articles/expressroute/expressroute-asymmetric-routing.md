---
title: 'Azure ExpressRoute: asymetrické směrování'
description: Tento článek vás provede problémy, se kterými se můžete setkat s asymetrickým směrováním v síti s více odkazy na cíl.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 12/14/2020
ms.author: duau
ms.openlocfilehash: 0713c52c7f07db93d9ae9084062ef2c3b25a9074
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97560504"
---
# <a name="asymmetric-routing-with-multiple-network-paths"></a>Asymetrické směrování s několika síťovými cestami
Tento článek vysvětluje, jak může síťový provoz přihlédnout k různým cestám, pokud je mezi zdrojem a cílem sítě k dispozici více tras.

Existují dvě koncepce, které potřebujete vědět, abyste porozuměli asymetrickému směrování. První je účinek více síťových cest. Druhá je způsob, jakým zařízení, jako je brána firewall, udržuje stav. Pro tyto typy zařízení se používá označení stavová zařízení. Pokud jsou tyto dva faktory kombinovány, mohou vytvořit scénář, ve kterém bude stavový síťový provoz vyřazen stavovým zařízením.  Provoz se zahodil, protože nezjistil, že provoz vznikl sám sebou.

## <a name="multiple-network-paths"></a>Několik síťových cest
Pokud má podniková síť jenom jedno propojení k Internetu prostřednictvím poskytovatele internetových služeb, veškerý provoz do Internetu a z něj bude směrovat stejnou cestu. Je běžné, že společnosti nakupují více okruhů za účelem vytvoření redundantních cest, které zlepšují dobu provozu sítě. U tohoto typu konfigurace je možné, že provoz přechází z jednoho propojení na Internet a vrátí se přes jiný odkaz. Tento scénář se běžně označuje jako asymetrické směrování. V případě asymetrického směrování má návratový síťový provoz jinou cestu než původní tok.

![Síť s více cestami](./media/expressroute-asymmetric-routing/AsymmetricRouting3.png)

Přestože se při přechodu na Internet obvykle objevuje asymetrické směrování. K tomu dochází také v případě, že je zavedena kombinace více cest. Prvním příkladem je, že máte internetovou cestu a soukromou cestu, která odpovídá stejnému cíli. Druhý příklad je v případě, že máte více privátních cest, které se také nacházejí do stejného cíle.

Každý směrovač na cestě mezi zdrojem a cílem vypočítá nejlepší cestu, která se má provést pro dosažení cíle. Směrovač určí nejlepší možnou cestu na základě dvou hlavních faktorů:

* Směrování mezi externími sítěmi je založené na směrovacím protokolu BGP (Border Gateway Protocol). Protokol BGP přijímá inzerování z okolí a provádí s nimi řadu kroků, podle kterých určí nejlepší cestu do požadovaného cíle. Nejlepší cestu uloží do své směrovací tabulky.
* Na cesty směrování má vliv délka masky podsítě přidružené k trase. Pokud směrovač obdrží pro stejnou IP adresu více oznámení, směrovač vybere cestu s delší maskou podsítě, protože se považuje za konkrétnější trasu.

## <a name="stateful-devices"></a>Stavová zařízení
Směrovače si pro účely směrování čtou hlavičku protokolu IP paketu. Některá zařízení zkoumají paket i hlouběji. Obvykle se tato zařízení hledají v hlavičkách protokolu TCP (Transmission Control Protocol) nebo protokolu UDP (User Datagram Protocol), nebo dokonce i v záhlavích vrstvy 7 (aplikační vrstva). Tyto typy zařízení patří buď mezi zařízení zabezpečení, nebo mezi zařízení optimalizace šířky pásma. 

Brána firewall je obvyklým příkladem stavového zařízení. Brána firewall povoluje nebo odmítá pakety, aby prošly svými rozhraními na základě různých kritérií. Tato kritéria zahrnují, ale nejsou omezená na protokol, port TCP/UDP a hlavičky adres URL. Tato úroveň kontroly paketů může na zařízení předávat vysoké zatížení při zpracování. 

Aby se zvýšil výkon, brána firewall kontroluje první paket toku. Pokud může paket projít svými rozhraními, udržuje informace o toku ve své tabulce stavů. V závislosti na počátečním určení se pak povolí všechny přijaté pakety týkající se tohoto toku. Paket, který je součástí existujícího toku, může přijít do brány firewall, ze které nepochází. Vzhledem k tomu, že nemá žádné předchozí stavové informace o počátečním toku, brána firewall paket neuvolní.

## <a name="asymmetric-routing-with-expressroute"></a>Asymetrické směrování s ExpressRoute
Když se připojíte k Microsoftu prostřednictvím Azure ExpressRoute, vaše síť se změní takto:

* Máte k Microsoftu víc propojení. Jedno propojení je vaše stávající připojení k Internetu a druhé je prostřednictvím připojení ExpressRoute. Určitý provoz určený pro Microsoft může projít připojením k Internetu, ale vracet se přes připojení ExpressRoute. K tomu může dojít také v případě, že provoz přechází přes ExpressRoute, ale vrátí se přes internetovou cestu.
* Z okruhu ExpressRoute jste dostali konkrétnější IP adresy. Takže když provoz z vaší sítě přechází do Microsoftu pro služby nabízené prostřednictvím ExpressRoute, budou směrovače vždy preferovat připojení ExpressRoute.

Abychom porozuměli vlivu těchto dvou změn na síť, zvažte několik scénářů. Například můžete mít okruh na Internet a využívat všechny služby Microsoftu prostřednictvím Internetu. Provoz z vaší sítě do a ze společnosti Microsoft projde stejným odkazem na Internet a projde bránou firewall. Brána firewall záznamuje tok, když uvidí první paket. Všechny pakety z dané konverzace jsou povoleny, protože tok existuje v tabulce stavů.

![Asymetrické směrování s ExpressRoute](./media/expressroute-asymmetric-routing/AsymmetricRouting1.png)

Pak se přinese okruh ExpressRoute, ve kterém se budou využívat služby nabízené společností Microsoft prostřednictvím ExpressRoute. Všechny ostatní služby od Microsoftu se spotřebovávají přes Internet. Nasadíte samostatnou bránu firewall na hranici, která je připojená k připojení ExpressRoute. Společnost Microsoft inzeruje do vaší sítě pro určité služby konkrétnější předpony přes ExpressRoute. Vaše infrastruktura směrování zvolí jako upřednostňovanou cestu pro tyto předpony ExpressRoute. 

Pokud neinzerujete veřejné IP adresy Microsoftu přes ExpressRoute. Microsoft bude komunikovat s vašimi veřejnými IP adresami přes Internet. Provoz odeslaný z vaší sítě do Microsoftu používá připojení ExpressRoute, ale návratový provoz od Microsoftu pak bude používat internetovou cestu. Když brána firewall na vaší hraniční bráně zobrazuje paket odezvy pro tok, na kterém neví, že tyto pakety přeruší.

Pokud se rozhodnete inzerovat stejný fond adres NAT (Network Address Translation) pro ExpressRoute a pro Internet. V případě klientů ve vaší síti na privátních IP adresách se zobrazí podobné problémy. Žádosti o služby, jako je web Windows Update, procházejí přes Internet, protože IP adresy pro tyto služby se Neinzerují prostřednictvím ExpressRoute. Návratový provoz se ale vrátí zpět přes ExpressRoute. Vzhledem k tomu, že společnost Microsoft obdržela IP adresu se stejnou maskou podsítě z Internetu a ExpressRoute, je upřednostňovaná cesta vždy ExpressRoute. Pokud brána firewall nebo jiné stavové zařízení v hraniční síti s připojením ExpressRoute nemá žádné předchozí informace o toku, vynechá tyto pakety.

## <a name="asymmetric-routing-solutions"></a>Řešení asymetrického směrování
Máte dvě dostupné možnosti, jak vyřešit problém asymetrického směrování. První je prostřednictvím směrování a druhý je pomocí překladu adres (SNAT) na základě zdroje.

### <a name="routing"></a>Směrování
Zajistěte, aby vaše veřejné IP adresy byly inzerované pro příslušné odkazy sítě WAN (Wide Area Network). Například pokud chcete používat Internet k ověřování provozu a ExpressRoute pro přenosy pošty. Neinzerovat veřejné IP adresy Active Directory Federation Services (AD FS) (AD FS) přes ExpressRoute. Také se ujistěte, že nezveřejňujete místní AD FS Server s IP adresami, které směrovač přijímá přes ExpressRoute. Trasy přijímané přes ExpressRoute jsou konkrétnější, takže se ExpressRoute stane upřednostňovanou cestou pro ověřovací provoz do Microsoftu. Pokud nebudete věnovat pozornost tomu, jak se směrování provádí v síti, může dojít k problémům se síťovým asymetrickým směrováním.

Pokud chcete použít ExpressRoute pro ověřování, ujistěte se, že jste provedli inzerci AD FS veřejných IP adres přes ExpressRoute bez překladu adres (NAT). Pokud tento způsob nakonfigurujete, přenosy pocházející z Microsoftu budou přecházet do vašeho místního AD FSho serveru přes ExpressRoute. Návratová data ze sítě, která přecházejí společnosti Microsoft, budou používat ExpressRoute, protože se jedná o upřednostňovanou trasu přes Internet.

### <a name="source-based-nat"></a>Překlad adres na základě zdroje
Dalším způsobem, jak vyřešit problém asymetrického směrování, je použití SNAT. Například se rozhodnete, že neoznamujete veřejnou IP adresu místního serveru SMTP (Simple Mail Transfer Protocol) přes ExpressRoute. Místo toho máte v úmyslu používat Internet pro tento typ komunikace. Požadavek pocházející od Microsoftu, který směřuje k vašemu místnímu serveru SMTP, prochází internetem. U příchozího požadavku provedete překlad adresy zdroje na vnitřní IP adresu. Návratový provoz ze serveru SMTP přejde do hraniční brány firewall (kterou používáte pro překlad adres (NAT)) místo přes ExpressRoute. Výsledkem je, že návratový přenos bude mít internetovou cestu.

![Konfigurace sítě s překladem adres na základě zdroje](./media/expressroute-asymmetric-routing/AsymmetricRouting2.png)

## <a name="asymmetric-routing-detection"></a>Detekce asymetrického směrování
Nejlepším způsobem, jak zajistit, že provoz z vaší sítě prochází očekávanou cestou, je traceroute. Pokud očekáváte, že provoz z místního serveru SMTP do Microsoftu bude pořizovat internetovou cestu, očekává se, že se traceroute ze serveru SMTP na Microsoft 365. Výsledek ověří, že provoz skutečně opouští vaši síť směrem k Internetu, a ne směrem k ExpressRoute.

