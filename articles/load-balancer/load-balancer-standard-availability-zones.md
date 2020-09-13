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
ms.date: 05/07/2020
ms.author: allensu
ms.openlocfilehash: 541aa7da3e804931c1793e455bcbfca83c809dae
ms.sourcegitcommit: 5d7f8c57eaae91f7d9cf1f4da059006521ed4f9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89669190"
---
# <a name="standard-load-balancer-and-availability-zones"></a>Load Balancer úrovně Standard a zóny dostupnosti

Azure Standard Load Balancer podporuje scénáře zón dostupnosti. Pomocí nástroje Load Balancer úrovně Standard můžete zvýšit dostupnost v celém scénáři tím, že zarovnáte prostředky k a rozmístěte mezi zónami. Zóny dostupnosti v kombinaci s nástrojem Load Balancer úrovně Standard jsou obsáhlém a flexibilní sada funkcí, která může vytvářet mnoho různých scénářů.  Přečtěte si tento dokument, abyste pochopili tyto [Koncepty](#concepts) a základní [pokyny k návrhu](#design)scénářů.

## <a name="availability-zones-concepts-applied-to-load-balancer"></a><a name="concepts"></a> Zóny dostupnosti koncepty použité pro Load Balancer

Nástroj pro vyrovnávání zatížení dědí konfiguraci zóny z jeho součástí: 

* Front-end
* Pravidla
* Definice fondu back-endu

V kontextu zón dostupnosti se chování a vlastnosti pravidla nástroje pro vyrovnávání zatížení popisují jako redundantní nebo oblasti.  V kontextu nástroje pro vyrovnávání zatížení zóna – redundantní vždy znamená **více zón a oblastí** znamená izolaci služby do **jedné zóny**. Azure Load Balancer má dva typy, Public a Internal. Oba typy nástroje pro vyrovnávání zatížení podporují redundanci a nasazení v zóně.  Oba typy nástroje pro vyrovnávání zatížení můžou v případě potřeby směrovat provoz mezi zónami.

## <a name="frontend"></a>Front-end

Front-endu nástroje pro vyrovnávání zatížení je konfigurace IP adresy front-endu odkazující buď na veřejnou IP adresu, nebo na privátní IP adresu v rámci podsítě virtuální sítě. Vytvoří koncový bod s vyrovnáváním zatížení, ve kterém je vaše služba vystavená.

Prostředek nástroje pro vyrovnávání zatížení může obsahovat pravidla s oblastmi a současně redundantními frontami typu zóna. Pokud je zaručena veřejná nebo privátní IP adresa zóny, zonality (nebo její nepřítomnost) nelze změnit. Pokud chcete změnit nebo vynechat zonality veřejné nebo soukromé IP adresy, vytvořte znovu IP adresu v příslušné zóně. 

Zóny dostupnosti nemění omezení pro více front-endu. Podrobnosti o této funkci najdete u [více front-endu pro Load Balancer](load-balancer-multivip-overview.md) .

### <a name="zone-redundant"></a>Zóna redundantní 

V oblasti se zónami dostupnosti může být front-endu standardního vyrovnávání zatížení zóna redundantní. V oblasti může být příchozí nebo odchozí více zón. Tento provoz se obsluhuje pomocí jediné IP adresy. Schémata redundance DNS se nevyžadují. 

Jedna IP adresa front-endu bude zachována při selhání zóny. Front-end IP adresu můžete použít k přístupu ke všem (bez ovlivněných) členů fondu back-endu bez ohledu na zónu. Jedna nebo více zón dostupnosti můžou selhat a cesta k datům zůstane v pořádku, dokud jedna zóna v oblasti zůstane v dobrém stavu. 

IP adresa front-endu je souběžně obsluhována několika nezávislými nasazeními infrastruktury v několika zónách dostupnosti. Jakékoli opakované pokusy nebo opětovné vytvoření budou úspěšné v jiných zónách, které nejsou ovlivněny selháním zóny. 

<p align="center">
  <img src="./media/az-zonal/zone-redundant-lb-1.svg" alt="Figure depicts a zone-redundant standard load balancer directing traffic in three different zones to three different subnets in a zone redundant configuration." width="512" title="Virtual Network NAT">
</p>

*Obrázek: redundantní služba Vyrovnávání zatížení zóny*

### <a name="zonal"></a>Zónové

Můžete zvolit, aby front-end byl zaručen pro jednu zónu, která se nazývá *oblast front-endu*.  Tento scénář znamená, že jakýkoliv příchozí nebo odchozí tok je obsluhován jedinou zónou v oblasti.  Vaše sdílená složka front-endu sepravila se stavem zóny.  Cesta k datům není ovlivněná chybami v jiných zónách, než kde byla zaručena. K vystavení IP adresy na jednu zónu dostupnosti můžete použít oblast front-endu.  

Kromě toho se podporuje použití oblastí front-endu přímo pro koncové body s vyrovnáváním zatížení v rámci každé zóny. Tuto konfiguraci můžete použít k vystavení koncovým bodům pro vyrovnávání zatížení zóny pro samostatné monitorování každé zóny. U veřejných koncových bodů je můžete integrovat s produktem pro vyrovnávání zatížení DNS, jako je [Traffic Manager](../traffic-manager/traffic-manager-overview.md) , a používat jeden název DNS.


<p align="center">
  <img src="./media/az-zonal/zonal-lb-1.svg" alt="Figure depicts three zonal standard load balancers each directing traffic in a zone to three different subnets in a zonal configuration." width="512" title="Virtual Network NAT">
</p>

*Obrázek: oblast pro vyrovnávání zatížení*

Pokud chcete tyto koncepty (zóny redundantní a oblasti pro stejný back-end) kombinovat, Projděte si téma [více front-endu pro Azure Load Balancer](load-balancer-multivip-overview.md).

U veřejného front-endu nástroje pro vyrovnávání zatížení přidejte parametr **zóny** do veřejné IP adresy. Na tuto veřejnou IP adresu odkazuje konfigurace IP adresy front-endu, kterou používá příslušné pravidlo.

U front-endu interního nástroje pro vyrovnávání zatížení přidejte parametr **zóny** do konfigurace protokolu IP front-endu interního nástroje pro vyrovnávání zatížení. Oblast s front-endu garantuje IP adresu v podsíti do konkrétní zóny.

## <a name="backend"></a>Back-end

Azure Load Balancer funguje s instancemi virtuálních počítačů. Tyto instance můžou být samostatné, skupiny dostupnosti nebo sady škálování virtuálních počítačů. Každý virtuální počítač v jedné virtuální síti může být součástí fondu back-endu, bez ohledu na zónu, na kterou je virtuální počítač zaručený.

Chcete-li zařadit a zaručovat front-end a back-end s jedinou zónou, umístěte virtuální počítače do stejné zóny pouze do odpovídajícího back-end fondu.

Chcete-li virtuální počítače řešit v rámci více zón, umístěte virtuální počítače z několika zón do stejného back-end fondu. 

Při použití virtuálních počítačů Virtual Machine Scale Sets Umístěte jednu nebo více sad škálování pro virtuální počítače do stejného back-end fondu. Sady škálování můžou být v jedné nebo několika zónách.

## <a name="outbound-connections"></a>Odchozí připojení

Zóna – redundantní a ploché platí pro [odchozí připojení](load-balancer-outbound-connections.md). Veřejná IP adresa redundantní v zóně používaná pro odchozí připojení je obsluhována všemi zónami. Veřejná IP adresa oblasti je dodávána pouze v zóně, ve které je zaručena. 

Odchozí připojení port SNAT zachová selhání zóny a váš scénář bude i nadále poskytovat odchozí připojení SNAT, pokud to není ovlivněno selháním zóny. Selhání zóny může vyžadovat, aby se připojení znovu navázala v případě redundantních scénářů zóny, pokud byl provoz obsluhován ovlivněnou zónou. Toky v jiných zónách než ovlivněné zóny nejsou ovlivněny.

Algoritmus předalokace portu SNAT je stejný jako u zóny dostupnosti nebo bez ní.

## <a name="health-probes"></a>Sondy stavu

Vaše existující definice sondy stavu zůstávají, protože jsou bez zón dostupnosti. Rozšířili jsme ale model stavu na úrovni infrastruktury. 

Při použití zón redundantních ve front-endu Nástroj pro vyrovnávání zatížení rozbalí svou interní kontrolu stavu. Nástroj pro vyrovnávání zatížení nezávisle vyhledá dostupnost virtuálního počítače z každé zóny a vypne cesty mezi zónami, které selhaly bez zásahu.  

Jiné zóny, které se můžou připojit k tomuto virtuálnímu počítači, můžou dál obsluhovat virtuální počítač ze svých front-endu. V průběhu událostí selhání mohou mít každá zóna různé distribuce nových toků a současně chránit celkový stav služby.

## <a name="design-considerations"></a><a name="design"></a> Faktory návrhu

Nástroj pro vyrovnávání zatížení je flexibilní v kontextu zón dostupnosti. Pro každé pravidlo můžete vybrat, že se mají zarovnat do zón nebo být redundantní pro zónu. Vyšší dostupnost se může zvýšit na cenu zvýšené složitosti. Návrh dostupnosti pro zajištění optimálního výkonu.

### <a name="zone-redundancy"></a>Zóna – redundance

Load Balancer usnadňuje jednu IP adresu jako front-redundantní front-end. Redundantní IP adresa v zóně může sloužit k práci s geografickými prostředky v libovolné zóně.  Tato IP adresa může přetrvat jednu nebo více selhání zóny, pokud jedna zóna zůstane v pořádku v rámci oblasti.  Místo toho je oblast front-endu omezením služby na jednu zónu a podílí se v ní osud v příslušné zóně.

Zóna – redundance neznamená hitlessou datacestu nebo rovinu ovládacího prvku; rovina dat. Neredundantní toky v zóně můžou používat libovolné zóny a toky zákazníka použijí všechny v pořádku zóny v oblasti. V případě selhání v zóně nejsou ovlivněny přenosové toky pomocí zón v pořádku. 

Může to ovlivnit přenos toků v době selhání zóny, ale může se stát, že se aplikace obnoví. Provoz pokračuje v nefunkčních zónách v oblasti po opětovném přenosu, když se Azure Sblíženo okolo selhání zóny.

### <a name="cross-zone-boundaries"></a><a name="xzonedesign"></a> Hranice mezi zónami

Je důležité si uvědomit, že kdykoli služba protíná zóny, můžete sdílet osud bez jedné zóny, ale potenciálně více zón. V důsledku toho vaše služba pravděpodobně nezískala žádnou dostupnost v rámci nasazení mimo oblast.

Vyhněte se zavlečení nezamýšlených závislostí mezi zónami, které při používání zón dostupnosti budou nezruší zisky dostupnosti. Pokud vaše aplikace obsahuje více důležitých součástí, zajistěte přežití, pokud se zóna nezdařila.

Jedna kritická součást vaší aplikace může mít vliv na celou aplikaci, pokud existuje jenom v jiné zóně než zóny, na kterých se nachází. Vezměte v úvahu obnovení zóny a to, jak se vaše aplikace konverguje. Pochopte, jak vaše aplikace reaguje na selhání částí. Projděte si klíčové body a používejte je pro otázky, jak si myslíte podle svého konkrétního scénáře.

- Pokud má vaše aplikace dvě komponenty:

    * IP adresa
    * Virtuální počítač se spravovaným diskem

Nakonfigurují se v zóně 1 a 2. Pokud se zóna 1 nezdařila, vaše služba nebude zachována. Nevybírejte mezi zónami scénářů oblastí, pokud plně nerozumíte, že vytváříte potenciálně nebezpečný režim selhání. Tento scénář je povolený pro zajištění flexibility.

- Pokud má vaše aplikace dvě komponenty:

    * IP adresa
    * Virtuální počítač se spravovaným diskem

Jsou nastavené tak, aby byly redundantní v zóně a 1. Vaše služba bude zachována při selhání zóny 2, zóny 3 nebo obou, pokud se nezdařila zóna 1. Nemůžete ale přijít o případnou schopnost vaší služby, pokud se vám všechno, co právě sledujete, dosáhlo dostupnosti front-endu.  Zvažte vývoj rozsáhlejšího modelu stavu a kapacity.  K rozšíření přehledu a možností správy můžete využít koncepty a oblasti v zóně.

- Pokud má vaše aplikace dvě komponenty:

    * Zóna – redundantní služba Vyrovnávání zatížení – front-end
    * Sada škálování virtuálních počítačů mezi zónami ve třech zónách

Prostředky v zónách neovlivněné selháním budou k dispozici. Vaše kapacita služby může být během selhání snížena. Z hlediska infrastruktury může nasazení zachováno při jedné nebo více selháních zóny. Tento scénář vyvolává následující otázky:

  - Rozumíte tomu, jak vaše aplikace reaguje na selhání a sníženou kapacitu?
  - Potřebujete v rámci služby zabezpečení a v případě potřeby vynutit převzetí služeb při selhání dvojici oblastí?
  - Jak budete monitorovat, zjišťovat a zmírnit takový scénář? Pomocí nástroje Load Balancer úrovně Standard můžete rozšířit monitorování výkonu služby. Zvažte, co je k dispozici a co může být potřeba rozšířit.

- Zóny můžou chyby snadněji pochopit a zahrnout. Selhání zóny se neliší od jiných selhání, když přichází k časovým limitům, opakovaným pokusům a omezení rychlosti algoritmům. Azure Load Balancer poskytuje cesty redundantní v zóně. Nástroj pro vyrovnávání zatížení se v reálném čase snaží rychle obnovit na úrovni paketů. Opětovné odeslání nebo opětovné vytvoření může nastat během navazování selhání. Je důležité pochopit, jak se aplikace kopiemi s chybami. Vaše schéma pro vyrovnávání zatížení bude zachováno, ale budete muset naplánovat následující otázky:

  - Pokud dojde k selhání zóny, vaše služba porozuměla této chybě a pokud dojde ke ztrátě stavu, jak budete obnovovat?
  - Když se zóna vrátí, aplikace porozumí, jak bezpečně konvergovat?

Projděte si [vzory návrhu cloudu Azure](https://docs.microsoft.com/azure/architecture/patterns/) , abyste vylepšili odolnost vaší aplikace vůči scénářům selhání.

## <a name="next-steps"></a>Další kroky
- Další informace o [zóny dostupnosti](../availability-zones/az-overview.md)
- Další informace o [Load Balanceru úrovně Standard](load-balancer-standard-overview.md)
- Přečtěte si, jak [vyrovnávat zatížení virtuálních počítačů v rámci zóny pomocí Standard Load Balancer s oblastí front-endu](load-balancer-standard-public-zonal-cli.md) .
- Přečtěte si, jak [vyrovnávat zatížení virtuálních počítačů napříč zónami pomocí Standard Load Balancer s frontou redundantním front-endu](load-balancer-standard-public-zone-redundant-cli.md)
- Přečtěte si o [vzorcích návrhu Azure pro Cloud](https://docs.microsoft.com/azure/architecture/patterns/) , abyste vylepšili odolnost vaší aplikace vůči scénářům selhání.
