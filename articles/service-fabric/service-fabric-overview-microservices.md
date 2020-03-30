---
title: Úvod do mikroslužeb v Azure
description: Přehled toho, proč vytváření cloudových aplikací s přístupem mikroslužeb je důležité pro moderní vývoj aplikací a jak Azure Service Fabric poskytuje platformu k dosažení tohoto cíle.
ms.topic: conceptual
ms.date: 01/07/2020
ms.custom: sfrev
ms.openlocfilehash: af18a6cb45808c0af5ec2782a3fd2100e3b7bf99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75750615"
---
# <a name="why-use-a-microservices-approach-to-building-applications"></a>Proč používat přístup mikroslužeb k vytváření aplikací

Pro vývojáře softwaru není faktoring aplikace do součástí nic nového. Obvykle se používá vrstvený přístup s back-endového úložiště, obchodní logikou střední vrstvy a uživatelským rozhraním front-endu (UI). Co se v posledních několika letech *změnilo,* je to, že vývojáři vytvářejí distribuované aplikace pro cloud.

Zde jsou některé měnící se obchodní potřeby:

* Služba, která je vytvořena a provozována ve velkém měřítku, aby oslovila zákazníky v nových geografických oblastech.
* Rychlejší poskytování funkcí a funkcí pro agilní reakci na požadavky zákazníků.
* Lepší využití prostředků pro snížení nákladů.

Tyto obchodní potřeby ovlivňují *způsob, jakým* vytváříme aplikace.

Další informace o přístupu Azure k mikroslužbám najdete v [tématu Mikroslužby: Revoluce aplikací využívající cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="monolithic-vs-microservices-design-approach"></a>Monolitický vs. přístup k návrhu mikroslužeb

Aplikace se v průběhu času vyvíjejí. Úspěšné aplikace se vyvíjejí tím, že jsou užitečné pro lidi. Neúspěšné aplikace se nevyvíjejí a jsou nakonec zastaralé. Zde je otázka: kolik toho víte o svých požadavcích dnes a co budou v budoucnu? Řekněme například, že vytváříte aplikaci pro vytváření sestav pro oddělení ve vaší společnosti. Jste si jisti, že aplikace platí pouze v rámci vaší společnosti a že sestavy nebudou uchovávány dlouho. Váš přístup se bude lišit od přístupu, například budování služby, která poskytuje video obsah desítkám milionů zákazníků.

Někdy, dostat něco ze dveří jako důkaz koncepce je hnacím faktorem. Víte, že aplikace může být později přepracována. Nemá smysl přepracovávat něco, co se nikdy nepoužije. Na druhou stranu, když společnosti budují pro cloud, očekává se růst a využití. Růst a rozsah jsou nepředvídatelné. Chceme prototyp rychle a zároveň s vědomím, že jsme na cestě, která zvládne budoucí úspěch. Toto je přístup štíhlého spuštění: sestavení, měření, učení a iterovat.

Během éry klienta/serveru jsme se soustředili na vytváření vrstvených aplikací pomocí konkrétních technologií v každé vrstvě. Termín *monolitické* aplikace se objevil popsat tyto přístupy. Rozhraní inklinoval být mezi úrovněmi a těsněji spojený návrh byl použit mezi součástmi v rámci každé vrstvy. Vývojáři navrhli a započítali třídy, které byly zkompilovány do knihoven a propojeny do několika spustitelných souborů a knihoven DLL.

Monolitický design má své výhody. Monolitické aplikace jsou často jednodušší navrhnout a volání mezi součástmi jsou rychlejší, protože tato volání jsou často přes meziprocesovou komunikaci (IPC). Také každý testuje jeden produkt, který má tendenci být efektivnější využití lidských zdrojů. Nevýhodou je, že existuje těsná spojka mezi vrstvené vrstvy, a nemůžete škálovat jednotlivé komponenty. Pokud potřebujete provést opravy nebo upgrady, budete muset počkat na ostatní dokončit jejich testování. Je těžší být agilní.

Mikroslužeb řešit tyto nevýhody a více sladit s předchozími obchodními požadavky. Mají však také výhody i závazky. Výhody mikroslužeb jsou, že každý z nich obvykle zapouzdřuje jednodušší obchodní funkce, které můžete vertikálně navýšit nebo snížit kapacitu, testovat, nasazovat a spravovat nezávisle. Jednou z důležitých výhod přístupu mikroslužeb je, že týmy jsou řízeny více obchodní scénáře než technologie. Menší týmy vyvinou mikroslužbu na základě scénáře zákazníka a používají všechny technologie, které chtějí používat.

Jinými slovy organizace nemusí standardizovat technologie pro údržbu aplikací mikroslužeb. Jednotlivé týmy, které vlastní služby, mohou dělat to, co pro ně má smysl, na základě týmových odborných znalostí nebo toho, co je nejvhodnější k vyřešení problému. V praxi je vhodnější soubor doporučených technologií, jako je konkrétní úložiště NoSQL nebo rámec webových aplikací.

Nevýhodou mikroslužeb je, že je třeba spravovat více samostatných entit a řešit složitější nasazení a správu verzí. Síťový provoz mezi mikroslužbami se zvyšuje, stejně jako odpovídající latence sítě. Spousta chattých, granulárních služeb může způsobit noční můru o výkonu. Bez nástrojů, které vám pomohou zobrazit tyto závislosti, je těžké vidět celý systém.

Standardy, aby přístup mikroslužeb funguje zadáním, jak komunikovat a tolerovat pouze věci, které potřebujete ze služby, spíše než rigidní smlouvy. Je důležité definovat tyto smlouvy předem v návrhu, protože služby aktualizovat nezávisle na sobě. Dalším popisem pro navrhování s přístupem mikroslužeb je "jemně odstupňovaná architektura orientovaná na služby (SOA)."

***V nejjednodušším případě je přístup návrhu mikroslužeb o oddělené federaci služeb s nezávislými změnami jednotlivých a dohodnutými standardy pro komunikaci.***

Jak se vytváří více cloudových aplikací, lidé zjistili, že tento rozklad celkové aplikace na nezávislé služby zaměřené na scénáře je lepší dlouhodobý přístup.

## <a name="comparison-between-application-development-approaches"></a>Porovnání přístupů vývoje aplikací

![Vývoj aplikací platformy Service Fabric][Image1]

1) Monolitická aplikace obsahuje funkce specifické pro doménu a je obvykle rozdělena do funkčních vrstev, jako je web, podnikání a data.

2) Škálování monolitické aplikace klonováním na více serverech/virtuálních počítačích/kontejnerech.

3) Aplikace mikroslužeb odděluje funkce do samostatných menších služeb.

4) Přístup mikroslužeb se škáluje nasazením každé služby nezávisle a vytvářením instancí těchto služeb napříč servery/virtuálními počítači/kontejnery.

Navrhování s přístupem mikroslužeb není vhodné pro všechny projekty, ale lépe zarovnává s obchodními cíli popsanými výše. Počínaje monolitický přístup může mít smysl, pokud víte, že budete mít možnost přepracovat kód později do návrhu mikroslužeb. Častěji začnete s monolitickou aplikací a pomalu ji rozbíjíte postupně, počínaje funkčními oblastmi, které musí být škálovatelnější nebo agilnější.

Při použití přístupu mikroslužeb, můžete vytvořit aplikaci mnoha malých služeb. Tyto služby spustit v kontejnerech, které jsou nasazeny v clusteru počítačů. Menší týmy vyvinout službu, která se zaměřuje na scénář a nezávisle testovat, verze, nasazovat a škálovat každou službu tak, aby se celá aplikace mohla vyvíjet.

## <a name="what-is-a-microservice"></a>Co je mikroslužba?

Existují různé definice mikroslužeb. Většina těchto charakteristik mikroslužeb je však široce akceptována:

* Zapouzdřit zákazníka nebo obchodní scénář. Jaký problém řešíte?
* Vyvinuto malým technickým týmem.
* Napsáno v libovolném programovacím jazyce, pomocí jakéhokoli rámce.
* Skládá se z kódu a volitelně stavu, které jsou nezávisle verzí verzí, nasazení a škálování.
* Interakce s jinými mikroslužbami přes dobře definované rozhraní a protokoly.
* Mají jedinečné názvy (adresy URL), které se používají k vyřešení jejich umístění.
* Zůstaňte konzistentní a k dispozici v přítomnosti selhání.

Abych to shrnul:

***Aplikace mikroslužeb se skládají z malých, nezávisle verzí a škálovatelných služeb zaměřených na zákazníky, které spolu komunikují prostřednictvím standardních protokolů s dobře definovanými rozhraními.***

### <a name="written-in-any-programming-language-using-any-framework"></a>Napsáno v libovolném programovacím jazyce, s použitím jakéhokoli rámce

Jako vývojáři chceme mít možnost zvolit si jazyk nebo rámec, v závislosti na našich dovednostech a potřebách služby, kterou vytváříme. U některých služeb můžete ocenit výhody výkonu jazyka C++ nad cokoli jiného. Pro ostatní může být důležitější snadnost spravovaného vývoje, který získáte z jazyka C# nebo Java. V některých případech může být nutné použít konkrétní partnerskou knihovnu, technologii úložiště dat nebo metodu pro vystavení služby klientům.

Po výběru technologie je třeba zvážit správu provozního nebo životního cyklu a škálování služby.

### <a name="allows-code-and-state-to-be-independently-versioned-deployed-and-scaled"></a>Umožňuje nezávisle nařazené, nasazované a škálované kódy kódu a stavu.

Bez ohledu na to, jak píšete mikroslužeb, kód a volitelně stav, by měl nezávisle nasadit, upgradovat a škálovat. Tento problém je těžké vyřešit, protože to přijde na váš výběr technologií. Pro škálování, pochopení, jak rozdělit (nebo horizontálního oddílu) kód a stav je náročné. Když kód a stav používají různé technologie, což je dnes běžné, musí být skripty nasazení pro vaši mikroslužbu schopny je škálovat. Toto oddělení je také o agility a flexibilitu, takže můžete upgradovat některé mikroslužeb bez nutnosti upgradovat všechny najednou.

Vraťme se k našemu porovnání monolitických a mikroslužeb přístupy na chvíli. Tento diagram znázorňuje rozdíly v přístupech ke stavu ukládání:

#### <a name="state-storage-for-the-two-approaches"></a>Státní skladování pro oba přístupy

![Úložiště stavu platformy Service Fabric][Image2]

***Monolitický přístup, vlevo, má jednu databázi a vrstvy specifických technologií.***

***Přístup mikroslužeb na pravé straně má graf propojených mikroslužeb, kde stav je obvykle vymezena na mikroslužby a různé technologie se používají.***

V monolitické matné aplikace obvykle používá jednu databázi. Výhodou použití jedné databáze je, že je na jednom místě, což usnadňuje nasazení. Každá komponenta může mít jednu tabulku pro uložení jejího stavu. Týmy musí striktně oddělit stát, což je výzva. Nevyhnutelně bude někdo v pokušení přidat sloupec do existující tabulky zákazníků, provést spojení mezi tabulkami a vytvořit závislosti ve vrstvě úložiště. Poté nelze škálovat jednotlivé součásti.

V přístupu mikroslužeb každá služba spravuje a ukládá svůj vlastní stav. Každá služba je zodpovědná za škálování kódu a stavu společně, aby splňovaly požadavky služby. Nevýhodou je, že když potřebujete vytvořit zobrazení nebo dotazy dat vaší aplikace, musíte dotazovat ve více stavových úložištích. Tento problém je obvykle vyřešen samostatné mikroslužby, která vytváří zobrazení v rámci kolekce mikroslužeb. Pokud potřebujete spustit více improvizované dotazy na data, měli byste zvážit zápis jednotlivých mikroslužeb data do služby datového skladu pro offline analýzy.

Mikroslužeb jsou verzí. Je možné pro různé verze mikroslužby spustit vedle sebe. Novější verze mikroslužby může selhat během upgradu a je třeba vrátit zpět na starší verzi. Správa verzí je také užitečná pro testování A/B, kde různí uživatelé zaznamenat různé verze služby. Například je běžné upgradovat mikroslužbu pro konkrétní sadu zákazníků k testování nových funkcí před jejich zavedením v širším měřítku.

### <a name="interacts-with-other-microservices-over-well-defined-interfaces-and-protocols"></a>Spolupracuje s jinými mikroslužbami přes dobře definovaná rozhraní a protokoly

Během posledních 10 let byly zveřejněny rozsáhlé informace popisující komunikační vzorce v architekturách orientovaných na služby. Obecně platí, že služba komunikace používá přístup REST s protokoly HTTP a TCP a XML nebo JSON jako formát serializace. Z hlediska rozhraní, je to o přijetí web design přístup. Nic by vám však nemělo bránit v používání binárních protokolů nebo vlastních datových formátů. Jen si uvědomte, že lidé budou mít těžší čas pomocí mikroslužeb, pokud tyto protokoly a formáty nejsou otevřeně k dispozici.

### <a name="has-a-unique-name-url-used-to-resolve-its-location"></a>Má jedinečný název (URL) používaný k vyřešení jeho umístění

Vaše mikroslužba musí být adresovatelná, ať už běží kdekoli. Pokud uvažujete o počítačích a který z nich běží konkrétní mikroslužbu, věci se mohou rychle zvrtnout.

Stejným způsobem, že dns řeší konkrétní adresu URL na konkrétní počítač, vaše mikroslužba potřebuje jedinečný název, aby jeho aktuální umístění je zjistitelné. Mikroslužby potřebují adresovatelné názvy, které jsou nezávislé na infrastruktuře, na které běží. To znamená, že existuje interakce mezi jak je vaše služba nasazena a jak je zjištěna, protože musí existovat registr služeb. Pokud počítač selže, musí vám služba registru sdělit, kam byla služba přesunuta.

### <a name="remains-consistent-and-available-in-the-presence-of-failures"></a>Zůstává konzistentní a je k dispozici v případě selhání

Řešení neočekávaných selhání je jedním z nejtěžších problémů, které je třeba vyřešit, zejména v distribuovaném systému. Velká část kódu, který píšeme jako vývojáři je pro zpracování výjimek. Během testování také trávíme nejvíce času na zpracování výjimek. Proces je více než psaní kódu pro zpracování selhání. Co se stane, když se nezdaří počítač, na kterém je spuštěna mikroslužba? Musíte zjistit selhání, což je sám o sobě těžký problém. Ale také je třeba restartovat mikroslužbu.

Z důvodu dostupnosti musí být mikroslužba odolná vůči selhání a může restartovat v jiném počítači. Kromě těchto požadavků odolnosti proti chybám by neměly být ztraceny a data musí zůstat konzistentní.

Odolnost proti chybám je obtížné dosáhnout, když dojde k selhání během upgradu aplikace. Mikroslužba, která pracuje se systémem nasazení, se nemusí obnovovat. Je třeba určit, zda můžete pokračovat v přechodu na novější verzi nebo vrátit zpět na předchozí verzi zachovat konzistentní stav. Je třeba zvážit několik otázek, jako je například zda dostatek počítačů jsou k dispozici, aby dál vpřed a jak obnovit předchozí verze mikroslužeb. Chcete-li provést tato rozhodnutí, potřebujete mikroslužbu k vyzařují informace o stavu.

### <a name="reports-health-and-diagnostics"></a>Hlásí stav a diagnostiku.

Může se to zdát zřejmé a často se přehlíží, ale mikroslužba musí hlásit své zdraví a diagnostiku. V opačném případě máte malý přehled o jeho stavu z hlediska operací. Korelování diagnostických událostí v rámci sady nezávislých služeb a řešení zkosení hodin počítače, aby to dávalo smysl pořadí událostí, je náročné. Stejným způsobem, že budete pracovat s mikroslužeb přes dohodnuté protokoly a formáty dat, je třeba standardizovat, jak protokolovat stav a diagnostické události, které nakonec skončí v úložišti událostí pro dotazování a prohlížení. S přístupem mikroslužeb se různé týmy musí dohodnout na jednom formátu protokolování. Je třeba konzistentní přístup k zobrazení diagnostických událostí v aplikaci jako celku.

Zdraví se liší od diagnostiky. Stav je o mikroslužbu hlášení jeho aktuální stav přijmout vhodná opatření. Dobrým příkladem je práce s mechanismy upgradu a nasazení k udržení dostupnosti. Ačkoli služba může být aktuálně není v pořádku z důvodu selhání procesu nebo restartování počítače, služba může být stále funkční. Poslední věc, kterou potřebujete, je, aby se situace ještě horší spuštěním upgrade. Nejlepší přístup je nejprve prozkoumat nebo povolit čas pro mikroslužbu obnovit. Zdravotní události z mikroslužeb nám pomáhají činit informovaná rozhodnutí a ve skutečnosti pomáhají vytvářet samoléčebné služby.

## <a name="guidance-for-designing-microservices-on-azure"></a>Pokyny pro navrhování mikroslužeb v Azure

Pokyny k navrhování a [vytváření mikroslužeb](https://docs.microsoft.com/azure/architecture/microservices/)v Azure najdete v Centru architektury Azure.

## <a name="service-fabric-as-a-microservices-platform"></a>Service Fabric jako platforma mikroslužeb

Azure Service Fabric se objevil, když Microsoft přešel z doručování krabicových produktů, které byly obvykle monolitické, na poskytování služeb. Prostředí vytváření a provozu velkých služeb, jako je Azure SQL Database a Azure Cosmos DB, ve tvaru Service Fabric. Platforma se vyvíjela v průběhu času, jak ji přijalo více služeb. Service Fabric musel běžet nejen v Azure, ale také v samostatných nasazeních Windows Serveru.

***Cílem Service Fabric je vyřešit náročné problémy vytváření a spouštění služby a efektivně využívat prostředky infrastruktury, aby týmy mohly řešit obchodní problémy pomocí přístupu mikroslužeb.***

Service Fabric pomáhá vytvářet aplikace, které používají přístup mikroslužeb tím, že poskytuje:

* Platforma, která poskytuje systémové služby pro nasazení, upgrade, detekci a restartování neúspěšných služeb, zjišťování služeb, směrování zpráv, správu stavu a sledování stavu.
* Možnost nasazovat aplikace spuštěné v kontejnerech nebo jako procesy. Service Fabric je kontejner a proces orchestrátor.
* Produktivní rozhraní API programování, která vám pomohou vytvářet aplikace jako mikroslužby: [ASP.NET core, reliable actors a reliable services](service-fabric-choose-framework.md). Můžete například získat informace o stavu a diagnostice nebo můžete využít integrované vysoké dostupnosti.

***Service Fabric je agnostik o tom, jak vytvořit službu a můžete použít libovolnou technologii. Ale poskytuje integrované programování rozhraní API, které usnadňují vytváření mikroslužeb.***

### <a name="migrating-existing-applications-to-service-fabric"></a>Migrace existujících aplikací do service fabricu

Service Fabric umožňuje znovu použít existující kód a modernizovat jej pomocí nových mikroslužeb. K dispozici je pět fází modernizace aplikace a můžete začít a zastavit v jakékoli fázi. Etapy jsou:

1) Začněte s tradiční monolitickou aplikací.  
2) Migrovat. Pomocí kontejnerů nebo spustitelných souborů hosta můžete hostovat existující kód v service fabric.  
3) Modernizovat. Přidejte nové mikroslužby vedle existujícího kontejnerizovaného kódu.  
4) Inovovat. Break monolitické aplikace do mikroslužeb na základě potřeby.  
5) Transformace aplikací do mikroslužeb. Transformujte existující monolitické aplikace nebo vytvářejte nové aplikace na zelené louce.

![Migrace na mikroslužby][Image3]

Pamatujte si, že můžete *spustit a zastavit v některé z těchto fází*. Nemusíte postoupit do další fáze. 

Podívejme se na příklady pro každou z těchto fází.

**Migrovat**  
Ze dvou důvodů mnoho společností migruje stávající monolitické aplikace do kontejnerů:

* Snížení nákladů, a to buď v důsledku konsolidace a odstranění stávajícího hardwaru, nebo v důsledku spuštění aplikací s vyšší hustotou.
* Konzistentní kontrakt nasazení mezi vývojem a operacemi.

Snížení nákladů je jednoduché. V Microsoftu je mnoho stávajících aplikací kontejnerizováno, což vede k úsporám v milionech dolarů. Konzistentní nasazení je těžší vyhodnotit, ale stejně důležité. To znamená, že vývojáři mohou zvolit technologie, které jim vyhovují, ale operace budou akceptovat pouze jednu metodu pro nasazení a správu aplikací. Zmírňuje operace z nutnosti vypořádat se se složitostí podpory různých technologií, aniž by vývojáři museli vybírat pouze určité. V podstatě každá aplikace je kontejnerována do samostatných iobrazek nasazení.

Mnoho organizací zde končí. Už mají výhody kontejnerů a Service Fabric poskytuje kompletní prostředí pro správu, včetně nasazení, upgrady, správa verzí, vrácení zpět a monitorování stavu.

**Modernizovat**  
Modernizace je přidání nových služeb vedle existujícího kontejnerizovaného kódu. Pokud se chystáte napsat nový kód, je nejlepší provést malé kroky dolů cestu mikroslužeb. To může znamenat přidání nového koncového bodu rozhraní REST API nebo nové obchodní logiky. Tímto způsobem zahájíte proces vytváření nových mikroslužeb a procvičte jejich vývoj a nasazování.

**Inovace**  
Přístup mikroslužeb vyhovuje měnícím se obchodním potřebám. V této fázi se musíte rozhodnout, zda chcete začít rozdělovat monolitickou aplikaci do služeb nebo inovovat. Klasickým příkladem je, když se databáze, kterou používáte jako frontu pracovního postupu, stane kritickým bodem zpracování. S tím, jak se zvyšuje počet požadavků pracovního postupu, je třeba práci distribuovat pro škálování. Vezměte konkrétní část aplikace, která není škálování nebo které je třeba aktualizovat častěji a rozdělit ji jako mikroslužbu a inovovat.

**Transformace aplikací na mikroslužby**  
V této fázi aplikace je plně složena z (nebo rozdělit do) mikroslužeb. Chcete-li dosáhnout tohoto bodu, jste provedli cestu mikroslužeb. Můžete začít zde, ale k tomu bez platformy mikroslužeb, které vám pomohou vyžaduje významnou investici.

### <a name="are-microservices-right-for-my-application"></a>Jsou mikroslužby správné pro mou aplikaci?

Možná. V Microsoftu, jak další týmy začaly vytvářet pro cloud z obchodních důvodů, mnoho z nich si uvědomilvýhody přijetí mikroslužeb jako přístup. Bing, například používá mikroslužeb pro let. Pro ostatní týmy byl přístup mikroslužeb nový. Týmy zjistily, že existují těžké problémy, které je třeba vyřešit mimo jejich klíčové oblasti síly. To je důvod, proč Service Fabric získal trakci jako technologie pro stavební služby.

Cílem Service Fabric je snížit složitost vytváření aplikací mikroslužeb tak, aby nebylo třeba procházet tolik nákladné redesigns. Začněte v malém měřítku, škálujte v případě potřeby, zavrhujte služby, přidávejte nové a vyvíjejte se s využitím zákazníků. Víme také, že existuje mnoho dalších problémů, které je ještě třeba vyřešit, aby mikroslužby přístupnější pro většinu vývojářů. Kontejnery a objekt actor programovací model jsou příklady malé kroky v tomto směru. Jsme si jisti, že se objeví další inovace, které usnadní přístup mikroslužeb.

## <a name="next-steps"></a>Další kroky

* [Mikroslužby: Revoluce aplikací poháněná cloudem](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)
* [Azure Architecture Center: Vytváření mikroslužeb v Azure](https://docs.microsoft.com/azure/architecture/microservices/)
* [Osvědčené postupy pro aplikace Azure Service Fabric a clustery](service-fabric-best-practices-overview.md)
* [Přehled terminologie Service Fabric](service-fabric-technical-overview.md)

[Image1]: media/service-fabric-overview-microservices/monolithic-vs-micro.png
[Image2]: media/service-fabric-overview-microservices/statemonolithic-vs-micro.png
[Image3]: media/service-fabric-overview-microservices/microservices-migration.png
