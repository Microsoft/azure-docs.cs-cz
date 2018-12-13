---
title: Asymetrické směrování – Azure ExpressRoute | Dokumentace Microsoftu
description: Tento článek vás provede problémy, se kterými může setkat s asymetrickým směrováním v síti, která má víc propojení k cíli.
documentationcenter: na
services: expressroute
author: osamazia
ms.service: expressroute
ms.topic: article
ms.date: 10/10/2016
ms.author: osamam
ms.custom: seodec18
ms.openlocfilehash: 6ece48d892f46a4f8bbeb66d3ebda9f532b621b8
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53076647"
---
# <a name="asymmetric-routing-with-multiple-network-paths"></a>Asymetrické směrování s několika síťovými cestami
Tento článek vysvětluje, jak může dopředný a zpětný síťový provoz využívat různé trasy, pokud je mezi zdrojem a cílem v síti k dispozici více cest.

Pro lepší pochopení asymetrického směrování je důležité porozumět dvěma základním konceptům. Prvním je důsledek existence několika síťových cest. Druhým je způsob, jakým si zařízení, například brána firewall, zachovávají stav. Pro tyto typy zařízení se používá označení stavová zařízení. Na základě kombinací těchto dvou faktorů vznikají scénáře, v nichž stavové zařízení zruší síťový provoz, protože toto stavové zařízení nezjistilo, že provoz pochází ze samotného zařízení.

## <a name="multiple-network-paths"></a>Několik síťových cest
Pokud má podniková síť jenom jedno propojení k internetu prostřednictvím poskytovatele internetových služeb, veškerý provoz směrem do internetu a z něj prochází po stejné cestě. Často si ale společnosti kupují víc okruhů, které tvoří redundantní cesty a zlepšují dostupnost sítě. V takových situacích je možné, že provoz směřující ze sítě do internetu využívá jedno propojení a zpětný provoz prochází jiným propojením. To se běžně označuje jako asymetrické směrování. Zpětný síťový provoz při asymetrickém směrování prochází po jiné cestě než původní tok.

![Síť s více cestami](./media/expressroute-asymmetric-routing/AsymmetricRouting3.png)

I když k asymetrickému směrování dochází především v síti Internet, vyskytuje se i u jiných kombinací více cest. Uplatňuje se například v případě, že do stejného cíle vede cesta přes internet i soukromá cesta nebo že do stejného cíle vede několik soukromých cest.

Každý směrovač na cestě ze zdrojového do cílového umístění vypočítá nejlepší cestu pro dosažení cíle. Stanovení nejlepší možné cesty ve směrovači je založené na dvou hlavních faktorech:

* Směrování mezi externími sítěmi je založené na směrovacím protokolu BGP (Border Gateway Protocol). Protokol BGP přijímá inzerování z okolí a provádí s nimi řadu kroků, podle kterých určí nejlepší cestu do požadovaného cíle. Nejlepší cestu uloží do své směrovací tabulky.
* Na cesty směrování má vliv délka masky podsítě přidružené k trase. Pokud směrovači obdrží víc inzerování pro stejnou adresu, ale s různými maskami podsítě, preferuje inzerování s delší maskou podsítě, protože se považuje za konkrétnější trasu.

## <a name="stateful-devices"></a>Stavová zařízení
Směrovače si pro účely směrování čtou hlavičku protokolu IP paketu. Některá zařízení zkoumají paket i hlouběji. Tato zařízení zpravidla zkoumají hlavičky vrstvy 4 (protokol TCP nebo UDP), nebo dokonce vrstvy 7 (aplikační vrstva). Tyto typy zařízení patří buď mezi zařízení zabezpečení, nebo mezi zařízení optimalizace šířky pásma. 

Brána firewall je obvyklým příkladem stavového zařízení. Brána firewall povoluje nebo zakazuje průchod paketu svými rozhraními na základě různých polí, jako je protokol, port TCP/UDP či hlavička adresy URL. Tato úroveň kontroly paketu znamená značné zatížení zařízení. Aby se zvýšil výkon, brána firewall kontroluje první paket toku. Pokud paketu povolí pokračovat v cestě, uchová si informace o toku ve své stavové tabulce. Všechny následné pakety týkající se tohoto toku jsou na základě počátečního vyhodnocení povoleny. Brána firewall může obdržet paket, který je součástí existujícího toku. Pokud o paketu brána firewall nemá žádné předchozí informace o jeho stavu, zahodí ho.

## <a name="asymmetric-routing-with-expressroute"></a>Asymetrické směrování s ExpressRoute
Když se připojíte k Microsoftu prostřednictvím Azure ExpressRoute, vaše síť se změní takto:

* Máte k Microsoftu víc propojení. Jedno propojení je vaše stávající připojení k internetu a druhé je prostřednictvím ExpressRoute. Část provozu do Microsoftu může procházet přes internet, ale vracet se prostřednictvím ExpressRoute, nebo naopak.
* Prostřednictvím ExpressRoute dostáváte mnohem konkrétnější IP adresy. To znamená, že v případě provozu z vaší sítě do Microsoftu pro služby nabízené prostřednictvím ExpressRoute směrovače vždy preferují ExpressRoute.

Pro lepší pochopení účinku těchto dvou změn v síti se podívejme na několik scénářů. Předpokládejme například, že máte jenom jeden okruh do internetu a že využíváte všechny služby Microsoftu prostřednictvím internetu. Provoz z vaší sítě do Microsoftu a zpátky prochází stejným propojením k internetu a přes bránu firewall. Brána firewall zaznamená tok, jakmile obdrží první paket, a jsou povoleny i zpětné pakety, protože je tok uveden ve stavové tabulce.

![Asymetrické směrování s ExpressRoute](./media/expressroute-asymmetric-routing/AsymmetricRouting1.png)

Pak zapnete ExpressRoute a začnete využívat služby nabízené Microsoftem prostřednictvím ExpressRoute. Všechny ostatní služby z Microsoftu se využívají přes internet. Na hranici připojení k ExpressRoute nasadíte samostatnou bránu firewall. Microsoft ve vaší síti přes ExpressRoute inzeruje pro určité služby konkrétnější předpony. Vaše infrastruktura směrování zvolí jako upřednostňovanou cestu pro tyto předpony ExpressRoute. Pokud neinzerujete své veřejné IP adresy Microsoftu přes ExpressRoute, Microsoft komunikuje s vašimi veřejnými IP adresami přes internet. Provoz z vaší sítě do Microsoftu používá ExpressRoute a zpětný provoz z Microsoftu používá internet. Když brána firewall na hranici obdrží paket odezvy pro tok, který nenajde ve stavové tabulce, zpětný provoz zruší.

Pokud zvolíte použití stejného fondu překládání adres (NAT) pro ExpressRoute a pro internet, bude docházet k obdobným problémům ve vaší síti i s klienty na privátních IP adresách. Žádosti o služby, jako je například Windows Update, procházejí přes internet, protože IP adresy pro tyto služby se neinzerují prostřednictvím ExpressRoute. Zpětný přenos se ale vrací přes ExpressRoute. Pokud Microsoft obdrží IP adresu se stejnou maskou podsítě z internetu i ExpressRoute, upřednostňuje ExpressRoute před internetem. Pokud brána firewall nebo jiné stavové zařízení na hranici vaší sítě, které je připojené k ExpressRoute, nemá žádné předchozí informace o toku, zahodí pakety, které do tohoto toku patří.

## <a name="asymmetric-routing-solutions"></a>Řešení asymetrického směrování
Máte dvě hlavní možnosti, jak vyřešit problém asymetrického směrování. Jedna je prostřednictvím směrování a druhá prostřednictvím překladu adres na základě zdroje (SNAT).

### <a name="routing"></a>Směrování
Zajistěte, aby vaše veřejné IP adresy byly inzerované v odpovídajících propojeních sítě WAN. Pokud chcete například používat internet pro ověřovací provoz a ExpressRoute pro e-mailový provoz, neměli byste inzerovat veřejné IP adresy služby AD FS (Active Directory Federation Services) přes ExpressRoute. Podobně nezpřístupňujte místní server služby AD FS pro IP adresy, které směrovač obdrží přes ExpressRoute. Trasy přijímané přes ExpressRoute jsou konkrétnější, takže se ExpressRoute stane upřednostňovanou cestou pro ověřovací provoz do Microsoftu. Tím je způsobeno asymetrické směrování.

Pokud chcete použít ExpressRoute pro ověřování, ujistěte se, že inzerujete veřejné IP adresy služby AD FS prostřednictvím ExpressRoute bez překladu adres (NAT). Provoz, který pochází z Microsoftu a míří na místní server služby AD FS, pak prochází přes ExpressRoute. Zpětný provoz od zákazníka do Microsoftu používá ExpressRoute, protože se jedná o trasu upřednostňovanou před internetem.

### <a name="source-based-nat"></a>Překlad adres na základě zdroje
Jiný způsob řešení problémů asymetrického směrování je prostřednictvím překladu adres na základě zdroje (SNAT). Předpokládejme, že jste neinzerovali veřejnou IP adresu místního serveru SMTP (Simple Mail Transfer Protocol) přes ExpressRoute, protože máte v úmyslu používat pro tento typ komunikace internet. Požadavek pocházející z Microsoftu a pokračující na váš místní server SMTP prochází internetem. U příchozího požadavku provedete překlad adresy zdroje na vnitřní IP adresu. Zpětný provoz ze serveru SMTP je směrován na hraniční bránu firewall (který používáte pro překlad adres), a ne přes ExpressRoute. Zpětný provoz se vrací přes internet.

![Konfigurace sítě s překladem adres na základě zdroje](./media/expressroute-asymmetric-routing/AsymmetricRouting2.png)

## <a name="asymmetric-routing-detection"></a>Detekce asymetrického směrování
Nejlepším způsobem, jak zajistit, že provoz z vaší sítě prochází očekávanou cestou, je traceroute. Pokud budete chtít, aby provoz z vašeho místního serveru SMTP do Microsoftu procházel internetem, očekávané použití příkazu traceroute je ze serveru SMTP na Office 365. Výsledek ověří, že provoz skutečně opouští vaši síť směrem do internetu, a ne směrem do ExpressRoute.

