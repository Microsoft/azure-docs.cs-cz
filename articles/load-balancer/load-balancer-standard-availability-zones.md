---
title: Standardní vyrovnávání zatížení Azure a zóny dostupnosti
titleSuffix: Azure Load Balancer
description: S tímto studijním programem můžete začít s Azure Standard Load Balancer a zóny dostupnosti.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2019
ms.author: allensu
ms.openlocfilehash: 5a65982c5c13eb4e4273efcfd8d14910b0f35572
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78197143"
---
# <a name="standard-load-balancer-and-availability-zones"></a>Load Balancer úrovně Standard a zóny dostupnosti

Azure Standard Balancer podporuje scénáře [zón dostupnosti.](../availability-zones/az-overview.md) Standardní nástroj pro vyrovnávání zatížení můžete použít k optimalizaci dostupnosti ve scénáři konce na konec zarovnáním prostředků se zónami a jejich distribucí mezi zóny.  Zkontrolujte [zóny dostupnosti,](../availability-zones/az-overview.md) kde najdou pokyny k tomu, jaké zóny dostupnosti jsou, které oblasti aktuálně podporují zóny dostupnosti a další související koncepty a produkty. Zóny dostupnosti v kombinaci se standardním vyvažovačem zatížení je rozsáhlá a flexibilní sada funkcí, která může vytvořit mnoho různých scénářů.  Projděte si tento dokument, abyste [pochopili](#concepts) tyto koncepty a základní [pokyny k návrhu scénáře](#design).

## <a name="availability-zones-concepts-applied-to-load-balancer"></a><a name="concepts"></a>Koncepty zón dostupnosti použité pro vykladač zatížení

Samotný prostředek vyrovnávání zatížení je místní a nikdy zonální. Členitost, co můžete nakonfigurovat, je omezena každou konfigurací definice front-endu, pravidla a back-endového fondu.
V kontextu zón dostupnosti chování a vlastnosti pravidla vyrovnávání zatížení jsou popsány jako zóna redundantní nebo zónální.  Zóna-redundantní a zonální popsat zonality vlastnosti.  V kontextu Balancer na zatížení zóny redundantní vždy znamená *více zón* a zónální znamená izolaci služby do *jedné zóny*.
Veřejné i interní scénáře podpory vyrovnávání zatížení zóna redundantní a zónové scénáře a oba mohou směrovat provoz napříč zónami podle potřeby *(vyrovnávání zatížení mezi zónami).* 

### <a name="frontend"></a>Front-end

Front-end nástroje pro vyrovnávání zatížení je konfigurace ip adres front-endu odkazující na prostředek veřejné IP adresy nebo privátní IP adresu v podsíti prostředku virtuální sítě.  Tvoří koncový bod s vyrovnáváním zatížení, kde je vystavena vaše služba.
Prostředek vyrovnávání zatížení může obsahovat pravidla se zónovými a zónově redundantními front-endy současně. Pokud je veřejný prostředek IP nebo privátní IP adresa zaručena pro zónu, zonality (nebo jeho nedostatek) není proměnlivá.  Pokud chcete změnit nebo vynechat zonalitu veřejné ip adresy nebo privátní ip adresy front-endu, je třeba znovu vytvořit veřejnou IP adresu v příslušné zóně.  Zóny dostupnosti nemění omezení pro více front-endů, zkontrolujte [více front-endů pro vyvažovač zatížení](load-balancer-multivip-overview.md) podrobnosti o této schopnosti.

#### <a name="zone-redundant"></a>Zóna redundantní 

V oblasti s dostupností zón může být front-end standardního vykladače zatížení zónově redundantní.  Zóna redundantní znamená, že všechny příchozí nebo odchozí toky jsou obsluhovány více zón dostupnosti v oblasti současně pomocí jedné adresy IP. Schémata redundance DNS nejsou vyžadována. Jedna front-endová IP adresa může přežít selhání zóny a může být použita k dosažení všech (neovlivněných) back-endových členů fondu bez ohledu na zónu. Jedna nebo více zón dostupnosti může selhat a cesta k datům přežije tak dlouho, dokud jedna zóna v oblasti zůstane v pořádku. Jediná IP adresa front-endu je obsluhována současně několika nezávislými infrastrukturními nasazeními ve více zónách dostupnosti.  To neznamená, že cesta dat bez zásahu, ale všechny pokusy nebo obnovení bude úspěšné v jiných zónách, které nejsou ovlivněny selhání zóny.   

#### <a name="optional-zone-isolation"></a>Volitelná izolace zóny

Můžete si vybrat, zda má front-end zaručený pro jednu zónu, která se označuje jako *zónový frontend*.  To znamená, že příchozí nebo odchozí tok obsluhuje jedna zóna v oblasti.  Váš frontend sdílí osud se zdravím zóny.  Cesta k datům není ovlivněna selháními v jiných zónách, než kde byla zaručena. Zónové front-endy můžete použít k vystavení IP adresy podle zóny dostupnosti.  

Kromě toho můžete spotřebovat zónové frontendy přímo pro koncové body s vyrovnáváním zatížení v rámci každé zóny. Můžete také použít k vystavení koncové body s vyrovnáváním zatížení na zónu pro individuální sledování jednotlivých zón.  Nebo pro veřejné koncové body je můžete integrovat s produktem dns vyrovnávání zatížení, jako je [Traffic Manager](../traffic-manager/traffic-manager-overview.md) a použít jeden název DNS. Klient pak převede na tento název DNS na více zonálních ADRES IP.  

Pokud chcete kombinovat tyto koncepty (zóna redundantní a zónální pro stejný back-end), zkontrolujte [více frontendů pro Azure Load Balancer](load-balancer-multivip-overview.md).

Pro veřejný front-end nástroje pro vyrovnávání zatížení přidáte parametr *zóny* do veřejného prostředku IP, na který odkazuje konfigurace ip adresy front-endu používaná příslušným pravidlem.

Pro interní front-end nástroje pro vyrovnávání zatížení přidejte parametr *zóny* do interní konfigurace ip adres front-endnástroje pro vyrovnávání zatížení. Zonální front-end způsobí, že vyrovnávání zatížení zaručit IP adresu v podsíti do určité zóny.

### <a name="cross-zone-load-balancing"></a>Vyrovnávání zatížení mezi zónami

Vyrovnávání zatížení mezi zónami je schopnost vykladače zatížení dosáhnout koncového bodu back-endu v libovolné zóně a je nezávislá na front-endu a jeho zonalitě.  Jakékoli pravidlo vyrovnávání zatížení může cílit na back-endovou instanci v libovolné zóně dostupnosti nebo v místních instancích.

Je třeba dbát na vytvoření scénáře způsobem, který vyjádřil pojem zóny dostupnosti. Například je třeba zaručit nasazení virtuálního počítače v rámci jedné zóny nebo více zón a zarovnat zónové front-endové a zónové back-endové prostředky do stejné zóny.  Pokud projdete zóny dostupnosti pouze zónové prostředky, scénář bude fungovat, ale nemusí mít jasný režim selhání s ohledem na zóny dostupnosti. 

### <a name="backend"></a>Back-end

Nástroj pro vyrovnávání zatížení pracuje s instancemi virtuálních počítačů.  Mohou to být samostatné sady dostupnosti nebo škálovací sady virtuálních strojů.  Jakákoli instance virtuálního počítače v jedné virtuální síti může být součástí back-endového fondu bez ohledu na to, zda byla zaručena pro zónu nebo která zóna byla zaručena.

Pokud chcete zarovnat a zaručit front-end a back-end s jednou zónou, umístěte pouze virtuální počítače ve stejné zóně do příslušného back-endového fondu.

Pokud chcete adresovat virtuální počítače ve více zónách, jednoduše umístěte virtuální počítače z více zón do stejného back-endového fondu.  Při použití škálovacích sad virtuálních strojů můžete umístit jednu nebo více škálovacích sad virtuálních strojů do stejného back-endového fondu.  A každá z těchto škálovacích sad virtuálních strojů může být v jedné nebo více zónách.

### <a name="outbound-connections"></a>Odchozí připojení

Stejné zóny redundantní a zónové vlastnosti platí pro [odchozí připojení](load-balancer-outbound-connections.md).  Zónově redundantní veřejná IP adresa používaná pro odchozí připojení je obsluhována všemi zónami. Zonální veřejná IP adresa je obsluhována pouze zónou, ve které je garantována.  Odchozí připojení SNAT přidělení portů přežít selhání zóny a váš scénář bude i nadále poskytovat odchozí připojení SNAT, pokud není ovlivněna selhání zóny.  To může vyžadovat přenosy nebo připojení, která mají být obnovena pro scénáře redundantní zóny, pokud tok obsluhovala ovlivněná zóna.  Toky v jiných zónách, než jsou zasažené zóny, nejsou ovlivněny.

Algoritmus předběžného přidělení portu SNAT je stejný s nebo bez zón dostupnosti.

### <a name="health-probes"></a>Sondy stavu

Existující definice sondy stavu zůstávají stejně jako bez zón dostupnosti.  Model stavu jsme však rozšířili na úrovni infrastruktury. 

Při použití front-endů redundantních zón nástroj pro vyrovnávání zatížení rozšiřuje svůj interní model stavu tak, aby nezávisle zkoumal dosažitelnost virtuálního počítače z každé zóny dostupnosti a vypnul cesty napříč zónami, které mohly selhat bez zásahu zákazníka.  Pokud daná cesta není k dispozici z infrastruktury nástroje pro vyrovnávání zatížení jedné zóny do virtuálního počítače v jiné zóně, nástroj pro vyrovnávání zatížení může tuto chybu rozpoznat a vyhnout se jí. Ostatní zóny, které můžou dosáhnout tohoto virtuálního jevu, můžou dál obsluhovat virtuální hospodařící s sebou z jejich příslušných front-endů.  V důsledku toho je možné, že během selhání události, každá zóna může mít mírně odlišné rozdělení nových toků při ochraně celkového stavu služby dokončení do konce.

## <a name="design-considerations"></a><a name="design"></a>Aspekty návrhu

Vykladač zatížení je záměrně flexibilní v kontextu zón dostupnosti. Můžete se rozhodnout zarovnat k zónám nebo můžete zvolit, zda chcete být pro každé pravidlo nadbytečné.  Zvýšená dostupnost může přijít za cenu zvýšené složitosti a musíte navrhnout dostupnost pro optimální výkon.  Podívejme se na některé důležité aspekty designu.

### <a name="automatic-zone-redundancy"></a>Automatická zónová redundance

Vykladač zatížení usnadňuje mít jednu IP jako front-end redundantní zóny. Zónově redundantní adresa IP může bezpečně obsluhovat zónový prostředek v libovolné zóně a může přežít jednu nebo více selhání zóny, pokud jedna zóna zůstane v oblasti v pořádku. Naopak zonální frontend je snížení služby do jedné zóny a sdílí osud s příslušnou zónou.

Redundance zóny neznamená bezzásahovou datovou cestu nebo řídicí rovinu;  jedná se výslovně o rovinu dat. Zónově redundantní toky mohou používat libovolné zóny a toky zákazníka budou používat všechny zóny v pořádku v oblasti. V případě selhání zóny nejsou ovlivněny toky provozu pomocí zón v pořádku v tomto okamžiku.  Dopravní toky pomocí zóny v době selhání zóny může být ovlivněna, ale aplikace lze obnovit. Tyto toky můžete pokračovat ve zbývajících zón ách v pořádku v rámci oblasti po opakovaném přenosu nebo opětovném zřízení, jakmile Azure konvergoval kolem selhání zóny.

### <a name="cross-zone-boundaries"></a><a name="xzonedesign"></a>Přes hranice zóny

Je důležité si uvědomit, že kdykoli služba end-to-end překročí zóny, sdílíte osud s ne jednou zónou, ale potenciálně s více zónami.  V důsledku toho vaše služba od konce pravděpodobně nezískala žádnou dostupnost oproti nasazením mimo zónu.

Vyhněte se zavádění nezamýšlených závislostí mezi zónami, které zruší dostupnost zisky při použití zón dostupnosti.  Pokud se aplikace skládá z více součástí a chcete být odolné vůči selhání zóny, je třeba dbát na zajištění přežití dostatečné kritické součásti v případě selhání zóny.  Například jedna kritická součást pro vaši aplikaci může ovlivnit celou aplikaci, pokud existuje pouze v jiné zóně, než je zóna přežití.  Kromě toho také zvažte obnovení zóny a jak bude aplikace konvergovat. Musíte pochopit, jak vaše aplikace důvody s ohledem na selhání částí. Podívejme se na některé klíčové body a použijte je jako inspiraci pro otázky, jak si myslíte, že prostřednictvím svého konkrétního scénáře.

- Pokud vaše aplikace má dvě součásti, jako je IP adresa a virtuální počítač se spravovaným diskem a jsou zaručeny v zóně 1 a zóně 2, pokud zóna 1 selže, vaše služba dokončení nepřežije, když zóna 1 selže.  Neprotínejte zóny se zónovými scénáři, pokud plně neporozumíte, že vytváříte potenciálně nebezpečný režim selhání.  Tento scénář je povoleno poskytnout flexibilitu.

- Pokud vaše aplikace má dvě součásti, jako je IP adresa a virtuální počítač se spravovaným diskem a je zaručeno, že jsou zóna redundantní a zóna 1 v uvedeném pořadí, vaše služba end-to-end přežije selhání zóny zóny 2, zóny 3 nebo obojí, pokud zóna 1 selhala.  Ztratíte však určitou schopnost uvažovat o stavu vaší služby, pokud vše, co pozorujete, je dosažitelnost frontendu.  Zvažte vývoj rozsáhlejšího modelu zdraví a kapacity.  Můžete použít zóny redundantní a zónové koncepty společně rozšířit přehled a možnosti správy.

- Pokud vaše aplikace obsahuje dvě součásti, jako je front-end nástroje pro vyrovnávání zatížení redundantní zóna a škálování virtuálního počítače mezi zónami ve třech zónách, budou k dispozici prostředky v zónách, které nejsou ovlivněny selháním, ale může dojít ke snížení kapacity služby začátku do konce. během poruchy zóny. Z hlediska infrastruktury může vaše nasazení přežít jednu nebo více selhání zóny, což vyvolává následující otázky:
  - Chápete, jak vaše aplikace důvody o těchto selhání a sníženou kapacitu?
  - Potřebujete mít ve své službě ochranná opatření, která v případě potřeby vynutí převzetí služeb při selhání dvojici oblastí?
  - Jak budete sledovat, detekovat a zmírnit takový scénář? Můžete být schopni použít standardní nástroj pro vyrovnávání zatížení diagnostiky rozšířit monitorování výkonu služby end-to-end. Zvažte, co je k dispozici a co může potřebovat augmentace pro úplný obraz.

- Zóny mohou způsobit, že selhání budou snadněji srozumitelná a obsažená.  Selhání zóny se však neliší od jiných selhání, pokud jde o koncepty, jako jsou časové přeběhy, opakování a backoff algoritmy. I když Azure Load Balancer poskytuje zónově redundantní cesty a snaží se rychle obnovit, na úrovni paketů v reálném čase může dojít k opakovaným přenosům nebo obnovením během nástupu selhání a je důležité pochopit, jak se vaše aplikace vyrovnává s Selhání. Vaše schéma vyrovnávání zatížení přežije, ale je třeba naplánovat následující:
  - Když zóna selže, rozumí to vaše služba od konce a pokud dojde ke ztrátě stavu, jak se obnovíte?
  - Když se zóna vrátí, rozumí vaše aplikace, jak bezpečně sblížit?

Zkontrolujte [vzory návrhu cloudu Azure](https://docs.microsoft.com/azure/architecture/patterns/) a vylepšete odolnost vaší aplikace vůči scénářům selhání.

## <a name="next-steps"></a>Další kroky
- Další informace o [zónách dostupnosti](../availability-zones/az-overview.md)
- Další informace o [Load Balanceru úrovně Standard](load-balancer-standard-overview.md)
- Naučte se, jak [vyvážit virtuální počítačv rámci zóny pomocí standardního vykladače zatížení s zónovou frontendovou](load-balancer-standard-public-zonal-cli.md)
- Zjistěte, jak [vyvážit virtuální virtuální mích napříč zónami pomocí standardního vykladače zatížení s front-endem redundantním zónou](load-balancer-standard-public-zone-redundant-cli.md)
- Seznamte se se [vzory návrhu cloudu Azure](https://docs.microsoft.com/azure/architecture/patterns/) pro zlepšení odolnosti vaší aplikace k selhání scénáře.
