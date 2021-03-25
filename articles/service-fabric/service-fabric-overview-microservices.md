---
title: Úvod do mikroslužeb v Azure
description: Přehled důvodů, proč je vytváření cloudových aplikací s přístupem k mikroslužbám důležité pro vývoj moderních aplikací a způsob, jakým Azure Service Fabric poskytuje platformu k tomuto účelu.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 46d4be7321057bd7a5ee19e0aca136c8c60d43bc
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105044426"
---
# <a name="why-use-a-microservices-approach-to-building-applications"></a>Proč používat při sestavování aplikací přístup k mikroslužbám

Pro vývojáře softwaru není nic nového. Obvykle se používá vrstvený přístup s back-end úložištěm, obchodní logikou střední vrstvy a klientským uživatelským rozhraním (UI). V posledních několika letech *se změnily* vývojáři, kteří sestavují distribuované aplikace pro Cloud.

Tady je několik měnících se obchodních potřeb:

* Služba, která je sestavená a provozovaná s cílem oslovit zákazníky v nových geografických oblastech.
* Rychlejší doručování funkcí a možností, které reagují na požadavky zákazníků agilním způsobem.
* Zlepšilo se využití prostředků, aby se snížily náklady.

Tyto obchodní potřeby mají vliv na to, *jak* sestavíme aplikace.

Další informace o přístupu k platformě Azure pro mikroslužby najdete v tématu [mikroslužby: otáčky aplikací poháněné cloudem](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="monolithic-vs-microservices-design-approach"></a>Monolitické vs. přístup k návrhu mikroslužeb

Aplikace se v průběhu času vyvíjí. Úspěšné aplikace se vyvíjejí, protože jsou užitečné pro lidi. Neúspěšné aplikace se nevyvíjí a jsou nakonec zastaralé. Tady je otázka: jak moc víte o svých požadavcích ještě dnes a co budou v budoucnu? Řekněme například, že vytváříte aplikaci pro vytváření sestav pro oddělení ve vaší společnosti. Ujistěte se, že se aplikace používá pouze v rámci oboru vaší společnosti a že sestavy nebudou uchovány dlouho. Váš přístup se bude lišit od toho, řekněme, že vytváříte službu, která doručuje obsah videa na desítky milionů zákazníků.

V některých případech je k dispozici jako důkaz konceptu jako zkušební faktor. Víte, že aplikaci lze později změnit. Je málo místa ve více technikách, která se nikdy nepoužívá. Na druhé straně, když společnosti sestavují pro Cloud, očekává se nárůst a využití. Růst a škálování jsou nepředvídatelné. Chceme rychle vytvořit prototyp a zároveň vědět, že máme cestu, která dokáže zpracovat budoucí úspěšnost. Toto je postup štíhlého spuštění: sestavení, měření, učení a iterace.

Během období klienta/serveru se můžeme zaměřit na vytváření vrstvených aplikací s využitím specifických technologií v jednotlivých úrovních. V rámci aplikace *monolitické* se ukázalo, že se tyto přístupy popíší. Rozhraní, která jsou mezi vrstvami a bylo použito více úzce vázaných návrhů mezi komponentami v rámci jednotlivých vrstev. Vývojáři navrhli a vyhodnotili třídy, které byly zkompilovány do knihoven a propojeny dohromady do několika spustitelných souborů a knihoven DLL.

Existují výhody přístupu k návrhu monolitické. Aplikace monolitické jsou často jednodušší pro navrhování a volání mezi komponentami je rychlejší, protože tato volání často využívají meziprocesovou komunikaci (IPC). Všichni také testuje jeden produkt, který představuje efektivnější využití lidských zdrojů. Nevýhodou je to, že existuje těsné propojení mezi vrstvenými vrstvami a nemůžete škálovat jednotlivé součásti. Pokud potřebujete provést opravy nebo upgrady, musíte počkat, až uživatelé dokončí testování. Je těžší je agilní.

Mikroslužby řeší tyto downsides a podrobněji se rovnají s předchozími obchodními požadavky. Ale zároveň mají i výhody i závazky. Výhodou mikroslužeb je, že každý z nich obvykle zapouzdřuje zjednodušené obchodní funkce, které můžete škálovat nebo v, testovat, nasazovat a spravovat nezávisle. Jednou z důležitých výhod přístupu k mikroslužbám je, že týmy jsou v rámci podnikových scénářů více zaměřené než technologie. Menší týmy vyvíjejí mikroslužby založené na scénáři zákazníka a využívají libovolné technologie, které chtějí použít.

Jinými slovy organizace nemusí standardizovat technickou správu aplikací mikroslužeb. Jednotlivé týmy, které vlastní služby, umožňují jejich smysl na základě odborných znalostí v týmu nebo podle toho, co je nejvhodnější k vyřešení problému. V praxi je vhodnější sada doporučených technologií, jako je konkrétní NoSQL úložiště nebo architektura webové aplikace.

Nevýhodou mikroslužeb je, že musíte spravovat více samostatných entit a zabývat se složitějšími nasazeními a správou verzí. Síťový provoz mezi mikroslužbami se zvyšuje, stejně jako odpovídající latence sítě. Spousta chatů, podrobných služeb může způsobit Nightmare výkonu. Bez nástrojů, které vám pomůžou tyto závislosti zobrazit, je obtížné zobrazit celý systém.

Standardy usnadňují práci mikroslužeb tím, že určují, jak komunikovat a tolerovat jenom věci, které potřebujete ze služby, a ne pevné smlouvy. Je důležité tyto smlouvy v návrhu předem definovat, protože služby se aktualizují nezávisle na sobě. Další popis mincí pro návrh s přístupem k mikroslužbám je "jemně odstupňované architektury orientované na služby" (SOA). "

***V nejjednodušším případě přístup k návrhu mikroslužeb je zhruba o oddělenou federaci služeb, ale nezávislé změny v každé a dohodnuté normě pro komunikaci.***

Při vytvoření většího počtu cloudových aplikací si uživatelé zjistili, že toto dekompozice celkové aplikace je nezávislá na nezávislém scénáři, což je lepší dlouhodobý přístup.

## <a name="comparison-between-application-development-approaches"></a>Porovnání mezi přístupy k vývoji aplikací

![Vývoj aplikací Service Fabric Platform][Image1]

1) Aplikace monolitické obsahuje funkce specifické pro doménu a je obvykle rozdělená do funkčních vrstev, jako jsou web, podnikání a data.

2) Škálujte aplikaci monolitické tak, že ji naklonujte na více serverech/virtuálních počítačích nebo kontejnerech.

3) Aplikace mikroslužeb odděluje funkce na samostatné menší služby.

4) Přístup k mikroslužbám se škáluje tak, že se jednotlivé služby nasazují nezávisle a vytvářejí se instance těchto služeb napříč servery/virtuálními počítači nebo kontejnery.

Navrhování pomocí přístupu ke mikroslužbám není vhodné pro všechny projekty, ale úzce se zarovnává s obchodními záměry popsanými výše. Od monolitické přístupu může smysl objasnit, pokud víte, že budete mít příležitost znovu pracovat kód na návrh mikroslužeb. Častěji začínáte s aplikací monolitické a pomalu ji rozdělíte ve fázích, počínaje funkčními oblastmi, které je potřeba lépe škálovat nebo agilní.

Když použijete přístup k mikroslužbám, vytvoříte aplikaci řady malých služeb. Tyto služby jsou spouštěny v kontejnerech, které jsou nasazeny v rámci clusteru počítačů. Menší týmy vyvíjejí službu, která se zaměřuje na scénář a nezávisle na testování, verzi, nasazení a škálování jednotlivých služeb, aby bylo možné vyvíjet celou aplikaci.

## <a name="what-is-a-microservice"></a>Co je mikroslužba?

Existují různé definice mikroslužeb. Většina těchto vlastností mikroslužeb se ale široce přijímá:

* Zapouzdřte scénář pro zákazníky nebo firmy. Jaký problém řešíte?
* Vyvinuto malým technickým týmem.
* Napsané v libovolném programovacím jazyce pomocí libovolného rozhraní.
* Se skládá z kódu a volitelně také v obou případech nezávisle na verzi, nasazení a škálování.
* Interakce s dalšími mikroslužbami v dobře definovaných rozhraních a protokolech.
* Mít jedinečné názvy (adresy URL), které se používají k překladu jejich umístění.
* V případě výskytu selhání zůstat konzistentní a dostupná.

K navýšení součtu:

***Aplikace mikroslužeb se skládají z malých, nezávisle se správou verzí a škálovatelných služeb zaměřených na zákazníky, které spolu komunikují přes standardní protokoly s dobře definovanými rozhraními.***

### <a name="written-in-any-programming-language-using-any-framework"></a>Napsaný v libovolném programovacím jazyce s použitím libovolného rozhraní

Jako vývojáři chceme zvolit jazyk nebo platformu v závislosti na našich dovednostích a potřebách služby, kterou vytváříme. U některých služeb můžete využít výhody výkonu jazyka C++ nad cokoliv jiného. Pro ostatní je pravděpodobné, že je snazší spravovaný vývoj, který získáte z C# nebo Java, i tak důležitější. V některých případech může být nutné pro vystavení služby klientům používat konkrétní knihovnu partnerů, technologii úložiště dat nebo metodu.

Po výběru technologie je potřeba vzít v úvahu provozní nebo životní cyklus správy a škálování služby.

### <a name="allows-code-and-state-to-be-independently-versioned-deployed-and-scaled"></a>Umožňuje kódu a stavu nezávisle na verzi, nasazení a škálování.

Bez ohledu na to, jakým způsobem píšete mikroslužby, kód a volitelně také stav, by měl nezávisle nasadit, upgradovat a škálovat. Tento problém je obtížné vyřešit, protože se nejedná o zvolenou technologii. Pro škálování můžete pochopit, jak rozdělit (nebo horizontálních oddílů) kód i stav je náročné. Když kód a stát používají různé technologie, které jsou běžné v současnosti, skripty nasazení pro vaši mikroslužbu musí být schopné škálovat obojí. Toto oddělení je také zaměřené na flexibilitu a flexibilitu, takže můžete upgradovat některé mikroslužby, aniž byste museli upgradovat všechny najednou.

Pojďme se vrátit k našemu porovnání přístupů k monolitické a mikroslužbám za chvíli. Tento diagram znázorňuje rozdíly v přístupů k uložení stavu:

#### <a name="state-storage-for-the-two-approaches"></a>Stavové úložiště pro dvě přístupy

![Service Fabric úložiště stavu platformy][Image2]

***Přístup monolitické na levé straně má izolovanou databázi a úrovně konkrétních technologií.***

***Přístup k mikroslužbám na pravé straně má graf vzájemně propojených mikroslužeb, kde je stav obvykle vymezen na mikroslužbu a používají se různé technologie.***

V monolitické přístupu aplikace obvykle používá jedinou databázi. Výhodou použití jedné databáze je, že je v jednom umístění, které usnadňuje jejich nasazení. Každá komponenta může mít jednu tabulku, do které se uloží svůj stav. Týmy potřebují výhradně samostatný stav, což je výzva. Je nevyhnutelné, že někdo bude zvážit přidání sloupce do existující tabulky zákazníků, provede spojení mezi tabulkami a vytvoří závislosti ve vrstvě úložiště. Až k tomu dojde, nemůžete škálovat jednotlivé součásti.

V přístupu k mikroslužbám spravuje každá služba a ukládá svůj vlastní stav. Každá služba zodpovídá za to, že je možné škálovat kód i stav dohromady, aby splňovaly požadavky služby. Nevýhodou je, že pokud potřebujete vytvořit zobrazení nebo dotazy na data vaší aplikace, musíte se dotazovat na více úložišť stavů. Tento problém je obvykle vyřešen samostatnou mikroslužbou, která sestaví zobrazení v rámci kolekce mikroslužeb. Pokud potřebujete pro data spustit více dotazů Impromptu, měli byste zvážit zápis dat jednotlivých mikroslužeb do služby datového skladu pro offline analýzu.

Mikroslužby mají verzi. Je možné, že se různé verze mikroslužby spouštějí souběžně. V průběhu upgradu může dojít k selhání novější verze mikroslužeb a je potřeba se vrátit zpátky na předchozí verzi. Správa verzí je užitečná také pro testování A/B, kde různí uživatelé nastanou různé verze služby. Například je běžné upgradovat mikroslužbu pro konkrétní skupinu zákazníků, aby bylo možné testovat nové funkce před tím, než budou rozšířeny.

### <a name="interacts-with-other-microservices-over-well-defined-interfaces-and-protocols"></a>Vzájemně spolupracuje s dalšími mikroslužbami v dobře definovaných rozhraních a protokolech.

Za posledních 10 let byly publikovány obsáhlé informace popisující způsoby komunikace v architekturách orientovaných na služby. Obecně platí, že komunikace služby používá jako formát serializace přístup REST s protokoly HTTP a TCP a XML nebo JSON. Z perspektivy rozhraní se chystá přístup k návrhu webu. Ale nic by vám nemělo zastavovat používání binárních protokolů ani vlastních formátů dat. Mějte na paměti, že pokud tyto protokoly a formáty nejsou k dispozici, budou mít uživatelé obtížnější čas pomocí mikroslužeb.

### <a name="has-a-unique-name-url-used-to-resolve-its-location"></a>Má jedinečný název (URL), který se používá k překladu jeho umístění.

Vaše mikroslužba musí být adresovatelná bez ohledu na to, kde je spuštěná. Pokud uvažujete o počítačích a na jednom z nich je spuštěná konkrétní mikroslužba, může jít o něco rychle.

Stejným způsobem, jakým server DNS překládá konkrétní adresu URL na konkrétní počítač, potřebuje vaše mikroslužba jedinečný název, aby bylo možné zjistit jeho aktuální umístění. Mikroslužby potřebují adresovatelné názvy, které jsou nezávislé na infrastruktuře, na které běží. To znamená, že existuje interakce mezi tím, jak je vaše služba nasazená, a jak je zjištěná, protože je potřeba mít registr služby. Když dojde k chybě počítače, služba registru musí sdělit, kam se služba přesunula.

### <a name="remains-consistent-and-available-in-the-presence-of-failures"></a>Zůstává konzistentní a k dispozici v přítomnosti selhání

Řešení neočekávaných selhání je jedním z nejzávažných problémů, které je potřeba vyřešit, zejména v distribuovaném systému. Většina kódu, který píšete jako vývojáři, je určená ke zpracování výjimek. Během testování také strávíte nejvíce času při zpracování výjimek. Proces je více zapojen než psaní kódu pro zpracování selhání. Co se stane, když počítač, na kterém běží mikroslužba, se nezdařil? Je nutné zjistit selhání, což je pevný problém. Je ale také potřeba restartovat mikroslužbu.

V případě dostupnosti musí být mikroslužba odolná vůči chybám a může se restartovat na jiném počítači. Kromě těchto požadavků na odolnost proti chybám by nemělo dojít ke ztrátě dat a data musí zůstat konzistentní.

Odolnost proti chybám je obtížné dosáhnout, když dojde k selhání během upgradu aplikace. Mikroslužba, která pracuje se systémem nasazení, nemusí obnovovat. Je potřeba určit, jestli se může dál přesouvat vpřed na novější verzi, nebo vrátit zpět k předchozí verzi a zachovat tak konzistentní stav. Je potřeba vzít v úvahu několik otázek, jako je třeba to, jestli je k dispozici dostatek počítačů pro pokračování a obnovení předchozích verzí mikroslužeb. K provedení těchto rozhodnutí budete potřebovat mikroslužbu k vygenerování informací o stavu.

### <a name="reports-health-and-diagnostics"></a>Oznamuje stav a diagnostiku.

Může se zdát být zřejmé a často je přehlédnutíná, ale mikroslužba potřebuje, aby nahlásila svůj stav a diagnostiku. Jinak máte z perspektivy operací malý přehled o svém stavu. V souvislosti s diagnostickými událostmi v rámci sady nezávislých služeb a při práci s hodinami strojového času je náročné, že je obtížné. Stejným způsobem, jakým komunikujete s mikroslužbami přes dohodnuté protokoly a formáty dat, je potřeba standardizovat způsob protokolování stavových a diagnostických událostí, které nakonec skončí v úložišti událostí pro dotazování a zobrazení. S přístupem k mikroslužbám musí různé týmy souhlasit s jedním formátem protokolování. Musí existovat konzistentní přístup k zobrazení diagnostických událostí v aplikaci jako celku.

Stav se liší od diagnostiky. Stav je o mikroslužbě, která hlásí svůj aktuální stav, aby mohla přijmout příslušné akce. Dobrým příkladem je práce s mechanismem upgradu a nasazení za účelem zachování dostupnosti. I když může být služba v současné době poškozená kvůli chybě procesu nebo restartování počítače, služba může pořád fungovat. Poslední věc, kterou potřebujete, je udělat při zahájení upgradu horší situaci. Nejlepším řešením je prozkoumat první nebo čas, kdy se mikroslužba obnoví. Události stavu z mikroslužeb nám pomáhají dělat kvalifikovaná rozhodnutí a v důsledku toho pomáhat vytvářet samoobslužné služby.

## <a name="guidance-for-designing-microservices-on-azure"></a>Doprovodné materiály k navrhování mikroslužeb v Azure

Pokyny k [navrhování a vytváření mikroslužeb v Azure](/azure/architecture/microservices/)najdete v centru architektury Azure.

## <a name="service-fabric-as-a-microservices-platform"></a>Service Fabric jako platforma mikroslužeb

Služba Azure Service Fabric v případě, kdy Microsoft přešla z doručování zabalených produktů, které se obvykle monolitické, na poskytování služeb. Prostředí pro vytváření a provozování velkých služeb, jako je Azure SQL Database a Azure Cosmos DB, Service Fabric tvarování. Platforma se v průběhu času vyvinula jako další služby, které ji přijaly. Service Fabric musela běžet nejen v Azure, ale také v samostatných nasazeních Windows serveru.

***Cílem Service Fabric je vyřešit závažné problémy s vytvářením a provozem služby a efektivně využívat prostředky infrastruktury, takže týmy mohou řešit obchodní problémy pomocí přístupu k mikroslužbám.***

Toto krátké video představuje Service Fabric a mikroslužby:
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Service-Fabric/player]

Service Fabric vám pomůže vytvářet aplikace, které využívají přístup k mikroslužbám, poskytováním těchto možností:

* Platforma, která poskytuje systémové služby pro nasazení, upgrade, detekci a restartování neúspěšných služeb, zjišťování služeb, směrování zpráv, správu stavu a monitorování stavu.
* Možnost nasazovat aplikace buď v kontejnerech, nebo jako procesy. Service Fabric je kontejner a proces Orchestrator.
* Rozhraní API pro programování, které vám pomůžou sestavovat aplikace jako mikroslužby: [ASP.NET Core, Reliable Actors a Reliable Services](service-fabric-choose-framework.md). Můžete například získat informace o stavu a diagnostice nebo můžete využít integrovanou vysokou dostupnost.

***Service Fabric se nezávislá o tom, jak službu sestavíte, a můžete použít libovolnou technologii. Poskytuje ale Vestavěná rozhraní API pro programování, která usnadňují vytváření mikroslužeb.***

### <a name="migrating-existing-applications-to-service-fabric"></a>Migrace stávajících aplikací na Service Fabric

Service Fabric umožňuje znovu použít stávající kód a modernizovat ho s novými mikroslužbami. Modernizace aplikací je pět fází a můžete začít a zastavovat v libovolné fázi. Fáze jsou:

1) Začněte s tradiční aplikací monolitické.  
2) Přenes. Pomocí kontejnerů nebo spustitelných souborů hosta můžete hostovat existující kód v Service Fabric.  
3) Modernizovat. Přidejte nové mikroslužby spolu s existujícím kontejnerovým kódem.  
4) Inovace. Podělte monolitické aplikaci na mikroslužby podle potřeby.  
5) Transformujte aplikace na mikroslužby. Transformujte existující aplikace monolitické nebo vytvářejte nové aplikace bezserverová.

![Migrace na mikroslužby][Image3]

Nezapomeňte, že můžete *začít a zastavovat v kterékoli z těchto fází*. Nemusíte postupovat do další fáze. 

Pojďme se podívat na příklady pro každou z těchto fází.

**Migrate**  
Ve dvou případech mnoho společností migruje existující aplikace monolitické do kontejnerů:

* Snížení nákladů, buď z důvodu konsolidace a odebrání stávajícího hardwaru, nebo z důvodu spuštěných aplikací s vyšší hustotou.
* Konzistentní smlouva k nasazení mezi vývojem a provozem.

Snížení nákladů je jednoduché. V Microsoftu je mnoho existujících aplikací v kontejneru, což vede k dosažení milionů dolarů. Konzistentní nasazení je těžší vyhodnocovat, ale stejně důležité. To znamená, že vývojáři si můžou vybrat technologie, které jim vyhovují, ale operace budou akceptovat jenom jednu metodu pro nasazení a správu aplikací. Rozlišuje operace od nutnosti zabývat se složitostí podpory různých technologií, aniž by se museli vynutit, aby si vývojáři zvolili jenom některé z nich. V podstatě je každá aplikace rozdělená do samostatných imagí nasazení.

Mnoho organizací se tady zastaví. Již mají výhody kontejnerů a Service Fabric poskytuje kompletní prostředí pro správu, včetně nasazení, upgradů, správy verzí, vrácení zpět a sledování stavu.

**Modernizovat**  
Modernizace je přidání nových služeb společně s existujícím kontejnerovým kódem. Pokud budete psát nový kód, doporučujeme, abyste v cestě k mikroslužbám prohlédli malými kroky. To může znamenat přidání nového koncového bodu REST API nebo nové obchodní logiky. Tímto způsobem začnete vytvářet nové mikroslužby a postupy pro vývoj a nasazování.

**Inovace**  
Přístup k mikroslužbám se přizpůsobí měnícím se potřebám firmy. V této fázi se musíte rozhodnout, jestli se má začít s rozdělením aplikace monolitické do služeb nebo při inovacích. Tady je klasický příklad, kdy databáze, kterou používáte jako frontu pracovních postupů, se stane kritickým bodem zpracování. Jak se zvyšuje počet požadavků pracovního postupu, musí být práce distribuována pro škálování. Využijte tuto konkrétní část aplikace, která není škálovatelná, nebo které je potřeba aktualizovat častěji, a rozdělte ji jako mikroslužby a inovovat.

**Transformace aplikací na mikroslužby**  
V této fázi je vaše aplikace plně složena z (nebo rozdělena do) mikroslužeb. Aby se dosáhlo tohoto bodu, provedli jste cestu mikroslužeb. Můžete začít, ale k tomu je potřeba bez platformy mikroslužeb, která vám bude pomáhat s tím, že vyžaduje významnou investici.

### <a name="are-microservices-right-for-my-application"></a>Jsou pro moji aplikaci nejvhodnější mikroslužby?

Možná. V Microsoftu, protože tým začali sestavovat Cloud z obchodních důvodů, spousta z nich využila výhod pořizování přístupu typu mikroslužeb. Služba Bing například používala mikroslužby pro roky. Pro jiné týmy byl přístup k mikroslužbám nový. Týmy zjistily, že byly závažné problémy, které je potřeba vyřešit mimo jejich základní oblasti. To je důvod, proč Service Fabric získal jako technologii pro vytváření služeb trakci.

Cílem Service Fabric je snížit složitosti vytváření aplikací mikroslužeb, abyste se nemuseli předávat tolik nákladných přepracování. Začněte s malým, Škálujte v případě potřeby, vyřadí služby, přidejte nové a vývoj s využitím zákazníků. Víme také, že je ještě mnoho dalších problémů vyřešených pro většinu vývojářů pro mikroslužby. Kontejnery a programovací model actor jsou příklady malých kroků v tomto směru. Máme jistotu, že k usnadnění přístupu k mikroslužbám se ukážeme víc.

## <a name="next-steps"></a>Další kroky

* [Mikroslužby: aplikace s revoluce, kterou Cloud využívá](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)
* [Cetrum architektury Azure: vytváření mikroslužeb v Azure](/azure/architecture/microservices/)
* [Osvědčené postupy pro clustery a aplikace Azure Service Fabric](./service-fabric-best-practices-security.md)
* [Přehled terminologie Service Fabric](service-fabric-technical-overview.md)

[Image1]: media/service-fabric-overview-microservices/monolithic-vs-micro.png
[Image2]: media/service-fabric-overview-microservices/statemonolithic-vs-micro.png
[Image3]: media/service-fabric-overview-microservices/microservices-migration.png