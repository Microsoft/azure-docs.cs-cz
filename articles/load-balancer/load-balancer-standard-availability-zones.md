---
title: Standard Load Balancer a Zóny dostupnosti Azure
titleSuffix: Azure Load Balancer
description: Pomocí této cesty výukového programu Začínáme s Azure Standard Load Balancer a Zóny dostupnosti.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "78197143"
---
# <a name="standard-load-balancer-and-availability-zones"></a>Load Balancer úrovně Standard a zóny dostupnosti

Azure Standard Load Balancer podporuje scénáře [zón dostupnosti](../availability-zones/az-overview.md) . Standard Load Balancer můžete použít k optimalizaci dostupnosti v celém koncovém scénáři tím, že zarovnáte prostředky se zónami a rozdělujete je mezi zónami.  Projděte si [zóny dostupnosti](../availability-zones/az-overview.md) , kde najdete pokyny k tomu, jaké zóny dostupnosti jsou, které oblasti aktuálně podporují zóny dostupnosti, a další související koncepty a produkty. Zóny dostupnosti v kombinaci s Standard Load Balancer jsou flexibilní sada funkcí obsáhlém, která může vytvářet mnoho různých scénářů.  Přečtěte si tento dokument, abyste pochopili tyto [Koncepty](#concepts) a základní [pokyny k návrhu](#design)scénářů.

## <a name="availability-zones-concepts-applied-to-load-balancer"></a><a name="concepts"></a>Zóny dostupnosti koncepty použité pro Load Balancer

Prostředek Load Balancer sám o sobě není oblast. Členitost toho, co můžete nakonfigurovat, je omezené každou konfigurací front-endu, pravidla a definice fondu back-endu.
V kontextu zón dostupnosti se chování a vlastnosti pravidla Load Balancer popisují jako redundantní nebo oblasti.  Redundantní zóna a oblast popisují zonality vlastnosti.  V souvislosti s Load Balancer se v zóně – redundantní vždy znamená, že *několik zón a oblastí* znamená izolaci služby do *jedné zóny*.
Veřejné i interní Load Balancer podporují scénáře redundantních a oblastí a oba můžou směrovat provoz napříč zónami podle potřeby (*Vyrovnávání zatížení mezi zónami*). 

### <a name="frontend"></a>Front-end

Front-end Load Balancer je konfigurace IP adresy front-endu, která odkazuje buď na prostředek veřejné IP adresy, nebo na privátní IP adresu v rámci sítě virtuálního síťového prostředku.  Vytvoří koncový bod s vyrovnáváním zatížení, ve kterém je vaše služba vystavená.
Prostředek Load Balancer může obsahovat pravidla s oblastmi a současně redundantními frontami typu zóna. Pokud je pro zónu zaručený prostředek veřejné IP adresy nebo privátní IP adresa, zonality (nebo nejeho absence) není proměnlivý.  Pokud chcete změnit nebo vynechat zonality veřejné IP adresy nebo front-endu privátních IP adres, musíte znovu vytvořit veřejnou IP adresu v příslušné zóně.  Zóny dostupnosti nemění omezení pro více front-endu, Projděte si [více front-endu pro Load Balancer](load-balancer-multivip-overview.md) , kde najdete podrobnosti o této možnosti.

#### <a name="zone-redundant"></a>Zóna redundantní 

V oblasti se zónami dostupnosti může být Standard Load Balancer front-endu zóny redundantní.  Redundantní zóna znamená, že všechny příchozí nebo odchozí toky jsou obsluhovány několika zónami dostupnosti v oblasti současně pomocí jediné IP adresy. Schémata redundance DNS se nevyžadují. Jedna IP adresa front-endu může překonat selhání zóny a dá se použít k přístupu ke všem (neovlivněným) členům fondu back-endu bez ohledu na zónu. Jedna nebo více zón dostupnosti můžou selhat a cesta k datům zůstane v pořádku, dokud jedna zóna v oblasti zůstane v dobrém stavu. Jedna IP adresa front-endu je souběžně obsluhována několika nezávislými nasazeními infrastruktury v několika zónách dostupnosti.  To neznamená hitless cestu k datům, ale všechny opakované pokusy nebo opětovné vytvoření budou úspěšné v jiných zónách, které neovlivní selhání zóny.   

#### <a name="optional-zone-isolation"></a>Volitelná izolace zóny

Můžete zvolit, aby front-end byl zaručen pro jednu zónu, která se nazývá *oblast front-endu*.  To znamená, že jakýkoliv příchozí nebo odchozí tok je obsluhován jedinou zónou v oblasti.  Vaše sdílená složka front-endu sepravila se stavem zóny.  Cesta k datům není ovlivněná chybami v jiných zónách, než kde byla zaručena. K vystavení IP adresy na jednu zónu dostupnosti můžete použít oblast front-endu.  

Kromě toho můžete využít oblasti front-endu přímo pro koncové body s vyrovnáváním zatížení v rámci každé zóny. Tuto možnost můžete použít také k vystavení koncovým bodům pro vyrovnávání zatížení zóny pro samostatné monitorování každé zóny.  Nebo veřejné koncové body můžete integrovat s produktem pro vyrovnávání zatížení DNS, jako je [Traffic Manager](../traffic-manager/traffic-manager-overview.md) a používat jeden název DNS. Klient pak bude tento název DNS překládat na více IP adres na více oblastech.  

Pokud chcete tyto koncepty (zóny redundantní a oblasti pro stejný back-end) kombinovat, Projděte si téma [více front-endu pro Azure Load Balancer](load-balancer-multivip-overview.md).

U veřejné Load Balancer front-endu přidejte parametr *Zones* do prostředku veřejné IP adresy, na který odkazuje konfigurace IP adresy front-endu používané příslušným pravidlem.

U interního front-endu Load Balancer přidejte do konfigurace protokolu IP front-endu interní Load Balancer parametr *Zones* . Oblast front-end způsobí, že Load Balancer garantuje IP adresu v podsíti s konkrétní zónou.

### <a name="cross-zone-load-balancing"></a>Vyrovnávání zatížení mezi zónami

Vyrovnávání zatížení mezi zónami je schopnost Load Balancer získat přístup ke koncovému bodu back-endu v libovolné zóně a nezávisle na front-endu a zonality.  Jakékoli pravidlo vyrovnávání zatížení může cílit na instanci back-endu v jakékoli zóně dostupnosti nebo regionálních instancích.

Musíte se postarat o vytvoření scénáře způsobem, který vyjádří zóny dostupnosti. Například je třeba zaručit nasazení virtuálních počítačů v rámci jedné nebo více zón a v oblasti front-endu a back-endu na stejné zóně.  Pokud jste provedli zóny pro různé oblasti dostupnosti jenom s využitím oblastí, bude scénář fungovat, ale nemusí mít jasný režim selhání s ohledem na zóny dostupnosti. 

### <a name="backend"></a>Back-end

Load Balancer funguje s instancemi virtuálních počítačů.  Můžou to být samostatné, skupiny dostupnosti nebo sady škálování virtuálních počítačů.  Každá instance virtuálního počítače v jedné virtuální síti může být součástí fondu back-endu bez ohledu na to, jestli je nebo není zaručená zóna nebo která zóna byla zaručena.

Pokud chcete zařadit a zaručovat front-end a back-end s jedinou zónou, umístěte virtuální počítače pouze do příslušné zóny do příslušného back-endu.

Pokud chcete virtuálním počítačům vymezit více zón, jednoduše umístěte virtuální počítače z několika zón do stejného back-end fondu.  Při používání virtuálních počítačů Virtual Machine Scale Sets můžete do stejného back-endu umístit jednu nebo víc sad škálování virtuálního počítače.  Všechny tyto sady škálování virtuálních počítačů můžou být v jedné nebo několika zónách.

### <a name="outbound-connections"></a>Odchozí připojení

Stejné vlastnosti – redundantní a ploché vlastnosti se vztahují na [odchozí připojení](load-balancer-outbound-connections.md).  Veřejná IP adresa redundantní v zóně používaná pro odchozí připojení je obsluhována všemi zónami. Veřejná IP adresa oblasti je dodávána pouze v zóně, ve které je zaručena.  Odchozí připojení port SNAT zachová selhání zóny a váš scénář bude i nadále poskytovat odchozí připojení SNAT, pokud to nebude mít vliv na selhání zóny.  To může vyžadovat přenos nebo pro připojení, která se mají znovu zřídit pro scénáře redundantní v zóně, pokud byl tok obsluhován ovlivněnou zónou.  Toky v jiných zónách, než jsou ovlivněné zóny, to neovlivní.

Algoritmus předalokace portu SNAT je stejný jako u zóny dostupnosti nebo bez ní.

### <a name="health-probes"></a>Sondy stavu

Vaše existující definice sondy stavu zůstávají, protože jsou bez zón dostupnosti.  Rozšířili jsme ale model stavu na úrovni infrastruktury. 

Pokud používáte zóny, které jsou redundantní, Load Balancer rozbalí svůj interní model stavu, aby nezávisle provedl test dostupnosti virtuálního počítače z každé zóny dostupnosti, a vypíná cesty mezi zónami, které se mohly nezdařily bez zásahu zákazníka.  Pokud daná cesta není k dispozici z Load Balancer infrastruktury jedné zóny do virtuálního počítače v jiné zóně, Load Balancer může tuto chybu detekovat a vyhnout se. Jiné zóny, které se můžou připojit k tomuto virtuálnímu počítači, můžou dál obsluhovat virtuální počítač ze svých front-endu.  V důsledku toho se může stát, že během událostí selhání budou mít každá zóna mírně odlišnou distribuci nových toků a současně chrání celkový stav komplexní služby.

## <a name="design-considerations"></a><a name="design"></a>Faktory návrhu

Load Balancer je účelově flexibilní v kontextu zón dostupnosti. Můžete si vybrat, že se mají zarovnat do zón, nebo můžete pro každé pravidlo zvolit, že se má pro každé pravidlo redundantní zóna.  Vyšší dostupnost může zacházet z ceny zvýšené složitosti a je třeba navrhnout dostupnost pro zajištění optimálního výkonu.  Pojďme se podívat na důležité informace o návrhu.

### <a name="automatic-zone-redundancy"></a>Automatická redundance zóny

Load Balancer usnadňuje jednu IP adresu jako front-redundantní front-end. Redundantní IP adresa v zóně může bezpečně obsluhovat prostředky oblastí v libovolné zóně a může zabývat jedné nebo více selhání zóny, pokud jedna zóna zůstane v dobrém stavu v oblasti. Naopak front-end je omezení služby na jednu zónu a podílí se na ní osud v příslušné zóně.

Zóna – redundance neznamená hitlessou datacestu nebo rovinu ovládacího prvku;  rovina dat je Express. Neredundantní toky v zóně můžou používat libovolné zóny a toky zákazníka použijí všechny v pořádku zóny v oblasti. V případě selhání zóny nebudou ovlivněny provozní toky, které používají zóny v pořádku v daném časovém okamžiku.  Může to mít vliv na přenosové toky pomocí zóny v době selhání zóny, ale aplikace se můžou zotavit. Tyto toky můžou pokračovat ve zbývajících zónách v oblasti po opětovném přenosu nebo opětovném vytvoření, jakmile se Azure Sblíženo se selháním zóny.

### <a name="cross-zone-boundaries"></a><a name="xzonedesign"></a>Hranice mezi zónami

Je důležité si uvědomit, že při každém přechodu mezi koncovými službami procházejí všechny zóny, takže můžete sdílet rozpad bez jedné zóny, ale potenciálně více zón.  V důsledku toho vaše koncová služba pravděpodobně nezískala žádnou dostupnost v rámci nasazení mimo oblast.

Vyhněte se zavlečení nezamýšlených závislostí mezi zónami, které při používání zón dostupnosti budou nezruší zisky dostupnosti.  Pokud se vaše aplikace skládá z několika součástí a chcete být odolná vůči selhání zóny, musíte se ujistit, že v případě selhání zóny budete mít dostatečné kritické součásti.  Například jediná kritická součást vaší aplikace může mít vliv na celou aplikaci, pokud existuje pouze v jiné zóně než zóny, na kterých se nachází.  Kromě toho zvažte také obnovení zóny a způsob sblížení aplikace. Potřebujete pochopit, jakým způsobem vaše aplikace vyplývají z důvodu selhání částí. Pojďme se podívat na některé klíčové body a použít je jako inspiraci pro otázky, jak si myslíte podle svého konkrétního scénáře.

- Pokud má vaše aplikace dvě komponenty, jako je IP adresa a virtuální počítač se spravovaným diskem a jsou zaručené v zóně 1 a 2, v případě, že zóna 1 selže, nebude vaše koncová služba zachována, když zóna 1 selže.  Nevybírejte mezi zónami scénářů oblastí, pokud plně nerozumíte, že vytváříte potenciálně nebezpečný režim selhání.  Tento scénář je povolený pro zajištění flexibility.

- Pokud má vaše aplikace dvě komponenty, jako je IP adresa a virtuální počítač se spravovaným diskem, a zaručuje se, že vaše koncová služba bude zachována při selhání zóny 2, zóny 3 nebo obou, pokud se nezdařila zóna 1.  Nemůžete ale přijít o případnou schopnost vaší služby, pokud je všechno, co se vám právě vystavuje, dostupnost front-endu.  Zvažte vývoj rozsáhlejšího modelu stavu a kapacity.  K rozšíření přehledu a možností správy můžete využít koncepty a oblasti v zóně.

- Pokud má vaše aplikace dvě komponenty, jako je například zóna redundantní Load Balancer front-endu a škálování virtuálního počítače mezi zónami ve třech zónách, budou k dispozici prostředky v zónách neovlivněné selháním, ale vaše koncová kapacita služby může být během selhání zóny snížena. Z hlediska infrastruktury může vaše nasazení zamezit selhání jedné nebo více zón a tato akce vyvolá následující otázky:
  - Porozumíte tomu, jak vaše aplikace vede k takovým selháním a snížení kapacity?
  - Potřebujete v rámci služby zabezpečení a v případě potřeby vynutit převzetí služeb při selhání dvojici oblastí?
  - Jak budete monitorovat, zjišťovat a zmírnit takový scénář? Je možné používat diagnostiku Standard Load Balancer k rozšíření monitorování komplexního výkonu služby. Zvažte, co je k dispozici a co může pro kompletní obrázek vyžadovat rozšíření.

- Zóny můžou chyby snadněji pochopit a zahrnout.  Selhání zóny se ale neliší od jiných selhání, když přichází k koncepcím, jako jsou časové limity, opakování a omezení rychlosti algoritmy. I když Azure Load Balancer poskytuje cesty redundantní v zóně a snaží se rychle obnovit na úrovni paketů v reálném čase, může dojít k opakovanému odeslání nebo opětovnému navázání. je důležité pochopit, jak se aplikace kopiemi s chybami. Vaše schéma vyrovnávání zatížení bude zachováno, ale je třeba naplánovat následující:
  - Pokud dojde k chybě zóny, bude vaše koncová služba rozumět této službě a pokud dojde ke ztrátě stavu, jak budete obnovovat?
  - Když se zóna vrátí, aplikace porozumí, jak bezpečně konvergovat?

Projděte si [vzory návrhu cloudu Azure](https://docs.microsoft.com/azure/architecture/patterns/) , abyste vylepšili odolnost vaší aplikace vůči scénářům selhání.

## <a name="next-steps"></a>Další kroky
- Další informace o [zóny dostupnosti](../availability-zones/az-overview.md)
- Další informace o [Load Balanceru úrovně Standard](load-balancer-standard-overview.md)
- Přečtěte si, jak [vyrovnávat zatížení virtuálních počítačů v rámci zóny pomocí Standard Load Balancer s oblastí front-endu](load-balancer-standard-public-zonal-cli.md) .
- Přečtěte si, jak [vyrovnávat zatížení virtuálních počítačů napříč zónami pomocí Standard Load Balancer s frontou redundantním front-endu](load-balancer-standard-public-zone-redundant-cli.md)
- Přečtěte si o [vzorcích návrhu Azure pro Cloud](https://docs.microsoft.com/azure/architecture/patterns/) , abyste vylepšili odolnost vaší aplikace vůči scénářům selhání.
