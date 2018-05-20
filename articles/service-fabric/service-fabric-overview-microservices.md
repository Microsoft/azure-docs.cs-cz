---
title: Úvod do mikroslužeb v Azure | Microsoft Docs
description: Přehled vytváření cloudových aplikací s přístupem mikroslužeb Proč je důležité pro vývoj moderních aplikací a jak Azure Service Fabric poskytuje platformu pro dosáhnout.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: fae2be85-0ab4-4cd3-9d1f-e0d95fe1959b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/02/2017
ms.author: msfussell
ms.openlocfilehash: 16757af0bab7cfd43488118f62300fb167c193a3
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="why-a-microservices-approach-to-building-applications"></a>Proč mikroslužeb přístupu k sestavení aplikací?
Jako vývojáři softwaru není nic nového v tom, jak jsme vezměte v úvahu řešení aplikace do součásti aplikace. Je centrální zlepší orientaci objektu, abstrakce softwaru a componentization. V současné době se tento factorization obvykle mít formu třídy a rozhraní mezi sdílené knihovny a technologických vrstev. Vrstvený přístup je obvykle prováděné s back-end úložiště, střední vrstvu obchodní logiky a klientské uživatelské rozhraní (UI). Co *má* změněno v posledních několika letech je, že jsme, jako vývojáři, jsou vytváření distribuované aplikace, které jsou pro cloud a vycházejí z firmy.

Změna obchodních potřeb jsou:

* Služba, která je vytvořená a pracuje ve velkém měřítku k dosažení zákazníkům nové zeměpisné oblasti (například).
* Rychlejší dodání funkcí a možností mohli odpovídat na požadavky zákazníků agilní způsobem.
* Využití vylepšené prostředků, jak snížit náklady.

Mají vliv na těchto obchodních potřeb *jak* jsme sestavení aplikace.

Další informace o přístupu Azure a mikroslužeb, najdete v tématu [Mikroslužeb: revolution aplikace používá technologii cloudu](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="monolithic-vs-microservice-design-approach"></a>Monolitický oproti přístup v rámci mikroslužbu návrhu
Všechny aplikace v průběhu času vyvíjejí. Úspěšné aplikace vyvíjet se užitečné i pro uživatele. Neúspěšná aplikace není momentální a nakonec jsou zastaralé. Změní na otázku: kolik víte o své požadavky ještě dnes, a co se bude v budoucnu? Předpokládejme například že vytváříte vytváření sestav aplikace pro oddělení. Jste si jisti, že aplikace zůstává v rámci oboru vaší společnosti a že jsou krátkodobou sestavy. Zvoleného přístupu se liší od, Řekněme, vytváření služby, které přináší obsahu videa desítkami milionů zákazníků. 

V některých případech přístupu k něčemu se dvířka jako testování konceptu je řízení faktoru, když víte, že aplikace můžete později přepracovali. Není moc bod v přepsání technici něco, který získá nikdy nepoužívali. Je obvyklé engineering kompromis. Na druhé straně společnosti mluvit o sestavení pro cloud, se očekává při růstu a využití. Problém je, že nepředvídatelným růstu a škálování. Rádi bychom mohli prototypu rychle a také zároveň budete vědět Snažíme se v cestě k řešení budoucí úspěch. Jedná se o postup štíhlého spuštění: sestavení, měřit, zjistěte a procházení.

Během letopočtu klient server jsme měly soustředit na budování vrstvené aplikace pomocí konkrétní technologie v jednotlivých vrstvách z. Termín *monolitický* vyplývá aplikace pro tyto přístupy. Měly být mezi vrstvami z rozhraní a více úzce párované návrh byl použit mezi součástmi v jednotlivých vrstvách. Vývojáři určené a započítané třídy, které byly zkompilovány do knihovny a spojené do několika spustitelné soubory a knihovny DLL. 

Existují výhody o monolitický návrhu přístupu. Často je jednodušší návrhu a má rychlejší volání mezi součástmi, protože tyto volání je často přes meziprocesová komunikace (IPC). Navíc všechny testy jednoho produktu, které se obvykle více osob prostředku efektivní a musí obsahovat. Nevýhodou je, že je úzkou párování mezi vrstvami vrstev a nemůže škálovat jednotlivých součástí. Pokud potřebujete provést opravy nebo upgrade, budete muset čekat na další osoby mohly dokončit jejich testování. Je obtížnější agilní.

Mikroslužeb adres tyto downsides a další úzce zarovnané s předchozím obchodní požadavky, ale mají také výhody a závazky. Pro mikroslužeb výhody každé z nich obvykle zapouzdří jednodušší obchodní funkce, které škálování směrem nahoru nebo dolů, testovací, nasadit a spravovat nezávisle. Důležité výhodou mikroslužbu přístupu je, že týmy se řídí více podle obchodních scénářů než technologii, která podporuje častější vrstvený přístup. V praxi menší týmy vyvíjet mikroslužbu závislosti na scénáři zákazníka a používat žádné technologie, kterou si zvolí. 

Jinými slovy organizace nemusí standardizovat technické udržovat mikroslužbu aplikace. Jednotlivé týmy, vlastní služby můžete udělat, co má smysl pro ně podle tým odborných znalostí nebo co je nejvhodnější problém vyřešit. V praxi sadu doporučené technologií, jako je například konkrétní NoSQL uložení nebo webové aplikační rozhraní, je vhodnější.

Nevýhodou mikroslužeb se dodává v správu vyšší počet samostatné entity a zabývající se složitější nasazení a správa verzí. Síťový provoz mezi mikroslužeb zvyšuje a také odpovídající síťovou latenci. Velké množství chatty, podrobné služby jsou recept na při důvodů výkonu. Bez nástrojů, které pomůžou zobrazení tyto závislosti, je obtížné "viz" celý systém. 

Standardy zkontrolujte přístup mikroslužbu pracovní kterým Odsouhlasíte o tom, jak komunikovat a použití odolný vůči chybám pouze věcí, je nutné ze služby, nikoli pevné smluv. Je důležité zadat tyto smlouvy předem v návrhu jednotného přihlašování, protože services aktualizovat nezávisle na sobě navzájem. Další popis poprvé použit k návrhu, s přístupem mikroslužeb je "podrobných orientované na služby architektura (SOA)."

***V nejjednodušším přístupem návrhu mikroslužeb je o odpojeného federace služeb, s nezávislé změny každé a dohodnuté standardy pro komunikaci.***

Jako další cloudové aplikace vytváří, osoby zjistit, že tento rozložením celkové aplikace do služby nezávislé, zaměřené na scénář je lépe dlouhodobý přístup.

## <a name="comparison-between-application-development-approaches"></a>Porovnání mezi přístupy vývoj aplikací
![Vývoj aplikací platformy Service Fabric][Image1]

1) Monolitický aplikace obsahuje funkce specifické pro doménu a obvykle dělení funkční vrstvy, jako je web, obchodních a data.

2) Škálování monolitický aplikace klonováním na více serverů a virtuálních počítačů nebo kontejnerů.

3) Mikroslužbu aplikace odděluje funkce do samostatné služby menší.

4) Měřítka přístup mikroslužeb odhlašování nasazením na jednotlivé služby nezávisle, vytváření instancí těchto služeb mezi servery a virtuálních počítačů nebo kontejnerů.

Navrhování s mikroslužbu přístup není všelék pro všechny projekty, ale lépe zarovnat obchodním cílům popsané výše. Počínaje monolitický přístup může být přijatelné, pokud víte, že budete mít možnost přepracovat kód později do návrhu mikroslužeb. Běžně začínat monolitický aplikace a pomalu jej rozdělte ve fázích, počínaje funkčním oblastem, které musí být více škálovatelné nebo agile.

To Shrneme, mikroslužbu možností je vytvořit aplikaci malé služby. Služba se spouští v kontejnerech, které jsou nasazeny v clusteru s podporou počítačů. Menší týmy vyvíjet služba, která se zaměřuje na scénáři nezávisle, verze, nasazení a testování škálovat jednotlivé služby, takže můžete rozvíjet bude celá aplikace.

## <a name="what-is-a-microservice"></a>Co je mikroslužbu?
Existují různé definice mikroslužeb. Pokud hledání v Internetu, najdete mnoho užitečné zdroje, které poskytují vlastní hlediska a definice. Ale většinu následující vlastnosti mikroslužeb jsou široce mezi:

* Zapouzdření zákazník nebo obchodní scénáře. Co je problému, který se řešení?
* Vyvinuté malé technickému týmu.
* Napsané v žádném programovací jazyk a použít libovolnou architekturu.
* Sestávají z kódu a (volitelně) stavu obě tyto položky jsou nezávisle verzí, nasazení a škálovat.
* Pracovat s další mikroslužeb přes dobře definované rozhraní a protokoly.
* Mít jedinečné názvy (URL) sloužící k přeložení jejich umístění.
* Zůstala konzistentní a k dispozici v případě selhání.

Je možné vytvořit tyto charakteristiky do shrnutí:

***Mikroslužbu aplikace se skládají z malých, nezávisle na nástroji verzí a škálovatelné zaměřený na zákazníky služby, které vzájemně komunikovat pomocí standardních protokolů s dobře definované rozhraní.***

Jsme popsaná první dva body v předchozí části, a teď nemůžeme rozbalte na a vysvětlení, ostatní.

### <a name="written-in-any-programming-language-and-use-any-framework"></a>Napsané v žádném programovací jazyk a použít libovolnou architekturu
Vývojáři jsme musí být volné vyberte jazyk nebo rozhraní, které chceme, v závislosti na našem dovednosti nebo potřeby služby. V některých služeb může hodnotu výkonu výhod C++ nad všemi else. V jiných služeb může být nejdůležitější snadné spravovaný vývoj v jazyce C# nebo Java. V některých případech možná budete muset použít konkrétní partnerskou knihovny, technologie úložiště dat nebo znamená vystavení služby klientům.

Po jste vybrali technologie, můžete se do provozní nebo životního cyklu správy a škálování služby.

### <a name="allows-code-and-state-to-be-independently-versioned-deployed-and-scaled"></a>Umožňuje kód a stav, který má být nezávisle verzí, nasazení a škálovat
Ale rozhodnete zapisovat vaše mikroslužeb, kód a volitelně stavu by měl nezávisle nasazení, upgrade a škálování. Toto je ve skutečnosti jeden z problémů těžší chcete vyřešit, protože pochází do zvoleného technologie. Pro škálování, pochopení jak oddílu (nebo komplikovaného skládání architektury) kódu a stavu je náročná. Při kód a stavu použijte samostatné technologií, které je dnes běžné, skriptů nasazení pro vaše mikroslužbu musí být schopen zvládnout škálování je obou. Toto je také o její agilnost a flexibilitu, a některé mikroslužeb aktualizovat bez nutnosti upgradovat všechny najednou.

Následující diagram vrácením monolitický versus mikroslužbu přístup na chvíli, jsou uvedeny rozdíly v přístupu k ukládání stavu.

#### <a name="state-storage-between-application-styles"></a>Stav úložiště mezi aplikací styly
![Úložiště stavu platformy Service Fabric][Image2]

***Monolitický přístup na levé straně má jedné databáze a úrovně konkrétní technologie.***

***Mikroslužeb přístup na pravé straně má graf vzájemně propojena mikroslužeb, kde mikroslužbu obvykle rozsah stavu a se používají různé technologie.***

V rámci monolitický přístupu obvykle aplikace používá jednu databázi. Výhodou je, že se jedná o jediného umístění, což usnadňuje nasazení. Jednotlivé komponenty může mít jednu tabulku pro ukládání stavu. Týmy musí striktně oddělit stav, který je výzvu. Existují nevyhnutelnou temptations do existující tabulky zákazníka umožňuje přidat nový sloupec, uděláte spojení mezi tabulkami a vytvoření závislosti ve vrstvě úložiště. Jakmile k tomu dojde, nelze škálovat jednotlivých součástí. 

V metodě mikroslužeb každou službu spravuje a ukládá svůj stav. Každá služba je zodpovědná za škálování stavu společně, aby vyhovovalo požadavkům služby i kód. Nevýhodou je, pokud je potřeba vytvořit zobrazení, nebo dotazy dat aplikace, budete muset dotazování mezi různorodých stav úložiště. To je obvykle vyřeší tak, že samostatné mikroslužbu, který vytvoří zobrazení v kolekci mikroslužeb. Pokud potřebujete provést více režimu dotazů na data, každý mikroslužby zvažte zápis svá data do datového skladu služby pro offline analýzu.

Správa verzí je specifická pro nasazené verzi mikroslužbu, že několik různých verzí, nasazení a spuštění vedle sebe. Správa verzí řeší scénáře, kde na novější verzi mikroslužbu během upgradu se nezdaří a musí se vrátit zpět starší verze. Další scénáře pro správu verzí provádí, testování A/B-style, kde různé uživatele zaznamenat různé verze služby. Například je běžné pro upgrade mikroslužbu pro konkrétní skupinu zákazníků k testování nových funkcí před distribucí další široce. Po správu životního cyklu mikroslužeb přináší tato nyní nám pro komunikaci mezi nimi.

### <a name="interacts-with-other-microservices-over-well-defined-interfaces-and-protocols"></a>Komunikuje s další mikroslužeb přes dobře definované rozhraní a protokoly
Toto téma vyžaduje málo pozornost tady, protože rozsáhlou dokumentaci o architektuře orientované na služby, který byl publikován v posledních 10 letech popisuje komunikačních schémat. Obecně platí používá komunikace služby REST přístup s protokoly HTTP a TCP a XML nebo JSON jako formát serializace. Z hlediska rozhraní je o přijetí přístup návrhu web. Ale nic zabraňuje pomocí binární protokoly nebo vlastní formáty dat. Připravte pro uživatele, kteří mají mít těžší čas pomocí vaší mikroslužeb, pokud tyto protokoly a formáty nejsou zveřejněno k dispozici.

### <a name="has-a-unique-name-url-used-to-resolve-its-location"></a>Má jedinečný název (URL) sloužící k přeložení její umístění
Mějte na paměti, jak jsme zachovat varování, že přístup mikroslužbu je jako webu? Jako web musí být adresovatelný, bez ohledu na běží vaší mikroslužby. Pokud přemýšlíte o počítačích a které z nich je spuštěna konkrétní mikroslužbu, přejděte věcí chybný rychle. 

Stejným způsobem, že server DNS řeší konkrétní adresu URL pro konkrétní počítač musí mít jedinečný název, tak, aby jeho aktuálního umístění zjistitelný vaší mikroslužby. Mikroslužeb potřebovat adresovatelné názvy, které je nezávislé na infrastrukturu, která běží na. Z toho vyplývá, že existuje interakci mezi nasazení služby a jak se zjistí, protože je potřeba registru služby. Stejným způsobem Pokud se počítači nezdaří, službu registru musí zjistíte kde služba teď běží. 

To nám přináší další téma: odolnost a konzistence.

### <a name="remains-consistent-and-available-in-the-presence-of-failures"></a>Zůstane konzistentní a k dispozici v případě selhání
Práci s neočekávanými chybami je jedním z nejtěžší problémů, které mají vyřešit, zejména v distribuovaného systému. Většinu kódu, který jsme zapsat jako vývojáři je zpracování výjimek, a to je také kde je nejvíce času stráveného při testování. Problém je složitější než psaní kódu pro zpracování chyby. Co se stane, když je počítač se spuštěným mikroslužbu nezdaří? Nejen potřebujete zjistit toto selhání mikroslužbu (pevné potíže na vlastní), ale budete potřebovat ještě něco restartovat vaší mikroslužby. 

Mikroslužbu musí být odolné vůči selhání a restartování často na jiném počítači, z důvodů dostupnosti. To také obsahuje do stavu, který byl uložen jménem mikroslužbu, kde mikroslužbu můžete obnovit tento stav z, a jestli je možné úspěšně spustit mikroslužby. Jinými slovy je potřeba odolnost v výpočetní (proces restartování) a také odolnost proti stavu, nebo data (nedošlo ke ztrátě dat a data zůstanou konzistentní).

Při další scénáře, jako je například při selhání dojít během upgradu aplikace jsou kombinovaných problémy odolnosti. Mikroslužbu, práce s systému nasazení, není nutné obnovit. Také musí rozhodnout, jestli můžete nadále přejít na novější verzi nebo místo toho vrátit k předchozí verzi udržovat konzistentním stavu. Je potřeba zvážit otázky, jako je například jestli je možné zachovat přesunutí dál dost počítačů a jak obnovit předchozí verze mikroslužby. To vyžaduje mikroslužbu pro vydávání informace o stavu, abyste mohli tyto rozhodnutí.

### <a name="reports-health-and-diagnostics"></a>Sestavy stav a Diagnostika
To nemusí připadat zřejmé, bývá často přehlížen, ale mikroslužbu musí nahlásit jeho stav a Diagnostika. Jinak se jen málo informací z hlediska operace. Korelace diagnostických událostí mezi sadu nezávislých služeb a plánování práce s zkosí hodiny počítače k smysl pořadí událostí je náročná. V stejným způsobem, který budete používat mikroslužbu přes dohodnuté protokolů a datových formátů ukáže potřebu standardizace v protokolování stavu a diagnostické události, které nakonec uložená v úložišti událostí pro dotazování a zobrazení. V rámci mikroslužeb přístupu je klíč, na formát jeden protokolování shodnou různé týmy. Je potřeba jednotný přístup k zobrazení diagnostických událostí v aplikaci jako celek.

Stav se liší od diagnostiky. Stav je o mikroslužbu jeho aktuální stav, provést příslušné akce pro vytváření sestav. Dobrým příkladem je práce pro upgrade a nasazení mechanismy pro zachování dostupnosti. I když služba může být aktuálně v chybném stavu z důvodu selhání procesu nebo restartování počítače, může být služba stále funkční. Poslední věcí, kterou je nutné je nastavit tento zhoršení provedením upgradu. Nejlepším postupem je nejdřív provést šetření, nebo můžete nastavit dobu mikroslužbu k obnovení. Stav události z mikroslužbu Pomozte nám informovaně rozhodnout a v důsledku toho vám pomohou vytvořit samoopravitelné služby.

## <a name="service-fabric-as-a-microservices-platform"></a>Service Fabric jako platformu mikroslužeb
Azure Service Fabric této služby z přechod společností Microsoft z doručování pole produkty, které byly obvykle monolitický ve stylu, k poskytování služeb. Prostředí sestavování a provoz velké služby, jako je Azure SQL Database a Azure Cosmos databáze, ve tvaru Service Fabric. Platforma vyvinuly časem více služeb přijatá ho. Je důležité Service Fabric museli spustit pouze v Azure, ale také v samostatných nasazeních systému Windows Server.

***Cílem Service Fabric je řešení pevný problémů sestavení a spuštění služby a efektivně využívat prostředky infrastruktury tak, aby týmy můžete řešení obchodních problémů mikroslužeb přístup.***

Service Fabric nabízí tři obecné oblasti můžete vytvářet aplikace, které používají mikroslužeb přístup:

* Platforma, která poskytuje systémové služby pro nasazení, upgrade, zjišťovat a restartujte služby se nezdařila, zjišťování služby, směrování zpráv, Správa stavu a sledování stavu. Tyto systémové služby platí povolit mnoho společných vlastností mikroslužeb popsaných výše.
* Možnost nasazování aplikací buď běží v kontejnerech nebo procesy. Service Fabric je kontejner a procesu orchestrator.
* Produktivní programovací rozhraní API, můžete vytvářet aplikace, jako mikroslužeb: [ASP.NET Core, Reliable Actors a spolehlivé služby](service-fabric-choose-framework.md). Můžete žádný kód k vytvoření vašeho mikroslužby. Ale tato rozhraní API proveďte úlohy více přehledné a jejich integraci s platformou na podrobnější úrovni. Tímto způsobem, například stav a Diagnostika informace můžete získat, nebo můžete využít integrovanou vysokou dostupnost.

***Service Fabric nerozlišuje na tom, jak sestavit služby a všechny technologii můžete použít. Však poskytuje integrované programovací rozhraní API, která usnadňují sestavení mikroslužeb.***

### <a name="migrating-existing-applications-to-service-fabric"></a>Migrace existujících aplikací do Service Fabric
Klíče přístup k Service Fabric je znovu použít stávající kód, který lze potom modernized s novou mikroslužeb. Sestává ze pět fází modernizace aplikace a může spuštění a zastavení na kterémkoli z fází. Toto jsou;

1) Trvat tradiční monolitický aplikace  
2) Navýšení a posunutí – použijte kontejnery nebo spustitelné soubory hosta k hostování existující kód v Service Fabric.  
3) Modernizace - nové mikroslužeb přidat společně se stávající kontejnerizované kód.  
4) Inovacemi. Zajistěte – rozdělit monolitický založený výhradně na potřeba mikroslužeb.  
5) Převede na mikroslužeb - transformace existující monolitický aplikace nebo vytváření nových greenfield aplikací.

![Migrace do Mikroslužeb][Image3]

Je nutné zdůraznit znovu, můžete **spuštění a zastavení na kterémkoli z těchto fází**. Nevyžaduje průběhu do další fáze. Nyní Podíváme se na příklady pro každou z těchto fází.

**Navýšení a posunutí** -velkého počtu společností jsou zrušení a přejdou existující monolitický aplikace do kontejnerů dvou důvodů;

- Snížení nákladů buď z důvodu konsolidace a odebrání stávající hardware nebo spuštění aplikací v vyšší hustotu. 
- Konzistentní nasazení kontrakt mezi vývoj a provoz.

Snížení nákladů jsou srozumitelné a v rámci Microsoftu velkého počtu existujících aplikací jsou právě kontejnerizované jednoduše k uložení milionů dolarů. Těžší k vyhodnocení, ale stejně důležitá je konzistentní nasazení. Zobrazuje, že vývojáři mohou být stále mohou zvolit technologie této sady je, ale operace bude přijímat pouze jeden způsob, jak nasadit a spravovat tyto aplikace. Ho nebude operace z nutnosti složitosti mnoha různých technologií nebo vynucení vývojářům vybrat jenom některé formáty. V podstatě každá aplikace je kontejnerizované do bitové kopie samostatná nasazení.

Mnoho organizací zastavit sem. Už mají výhody kontejnery a Service Fabric nabízí prostředí pro kompletní správu z nasazení, upgrady, Správa verzí, odvolání, stav monitorování atd.

**Modernizace** -je přidání nových služeb společně se stávající kontejnerizované kód. Pokud chcete zadat nový kód, je vhodné přijmout malé kroky na cestu mikroslužeb. To může přidávat nové koncový bod REST API nebo nové obchodní logiku. Tímto způsobem, spusťte na cestu mikroslužeb nové sestavení a postup vývoje a jejich nasazení.

**Inovacemi. Zajistěte** -mějte na paměti tyto původní změna obchodních potřeb na začátku tohoto článku, které řídí přístup mikroslužeb? V této fázi je rozhodnutí jsou tyto děje Moje aktuální aplikací a pokud ano, je nutné spustit rozdělení monolitu nebo innovating. Je zde například když se databáze stane kritický bod zpracování, protože je používán jako fronty pracovního postupu. Jako počet pracovní postup žádosti o zvýšení práce musí distribuována pro škálování. Proto pro tuto konkrétní aplikace, která není potřeba aktualizovat častěji škálování, nebo můžete, to zjistit rozdělením mikroslužbu a inovacemi. Zajistěte. 

**Převede na mikroslužeb** – to je, kde vaše aplikace je plně složený z (nebo rozložená do) mikroslužeb. Pokud chcete dostat zde, které jste vytvořili mikroslužeb cesty. Začněte zde, ale k tomu bez mikroslužeb platformu, která vám pomůže je důležité investice. 

### <a name="are-microservices-right-for-my-application"></a>Jsou mikroslužeb pro Moje aplikace?
Podle potřeby. Jsme došlo se, že jako více týmů v aplikaci Microsoft začal vytvářet pro cloud z pracovních důvodů, kolika z nich uskutečňovat výhody tak mikroslužbu jako přístup. Bing, například má byla vývoj mikroslužeb ve vyhledávání let. Pro jiné týmy mikroslužeb přístup byl nový. Týmy zjištěno, že existuje byly pevné problémy k vyřešení mimo jejich oblasti základní sílu. Z tohoto důvodu Service Fabric získávají trakční technologií výběru pro vytváření služeb.

Cílem Service Fabric je ke snížení složitosti vytváření aplikací s přístupem mikroslužbu, takže není nutné projít jako mnoho nákladných redesigns. Začněte v malém rozsahu, škálovat v případě potřeby, přestat používat služby, přidat nové a momentální s zákazníka využití je přístup. Také vědět, že jsou mnohé problémy ještě mají být vyřešeny Pokud chcete, aby mikroslužeb více srozumitelné pro Většina vývojářů. Kontejnery a programovací model objektu actor jsou příklady malé kroky v tomto směru a jsme si jisti, že pro zjednodušení budou vznikat další inovace.
 
<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Další postup
* [Přehled terminologie Service Fabric](service-fabric-technical-overview.md)
* [Mikroslužeb: Aplikaci revolution používá technologii cloudu](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)

[Image1]: media/service-fabric-overview-microservices/monolithic-vs-micro.png
[Image2]: media/service-fabric-overview-microservices/statemonolithic-vs-micro.png
[Image3]: media/service-fabric-overview-microservices/microservices-migration.png
