---
title: Úvod do mikroslužeb v Azure | Dokumentace Microsoftu
description: Přehled vytváření cloudových aplikací s přístup založený na mikroslužbách Proč je důležité pro vývoj moderních aplikací a jak Azure Service Fabric poskytuje platformu pro můžete toho dosáhnout.
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
ms.openlocfilehash: 48df598ab12c509952b7712bb8cc9fe21355325f
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2018
ms.locfileid: "51613677"
---
# <a name="why-a-microservices-approach-to-building-applications"></a>Proč se k vytváření aplikací, přístup založený na mikroslužbách?

Jako vývojáři softwaru není nic nového v tom, jak přistupujeme k které budou zohledňovat aplikace do součásti. Vrstveného přístupu se obvykle používá s back endové úložiště, střední vrstvy obchodní logiky a front-endu uživatelského rozhraní (UI). Co *má* mění v průběhu posledních několika letech je, že společnost Microsoft, jako vývojáři, zodpovídají za tvorbu distribuovaných aplikací pro cloud.

Měnící se potřeby organizace jsou:

* Služba, která je vytvořená a pracuje ve velkém měřítku k oslovení zákazníků v nové geografické oblasti.
* Rychlejší doručování funkce a možnosti, abyste mohli reagovat na požadavky zákazníků pružně.
* Využití prostředků vylepšené ke snížení nákladů.

Tyto obchodní potřeby. ovlivňují *jak* budeme vytvářet aplikace.

Další informace o přístupu Azure na mikroslužby, najdete v článku [Mikroslužeb: revolution aplikace založené na cloudu](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="monolithic-vs-microservice-design-approach"></a>Monolitické vs. přístup v rámci návrhu mikroslužeb

Aplikace v průběhu času vyvíjejí. Tím, že je užitečné pro osoby, vyvíjí úspěšné aplikace. Neúspěšné aplikace není vyvíjí a nakonec jsou zastaralé. Otázka: kolik víte o vaše požadavky ještě dnes, a co se bude v budoucnu? Například Řekněme, že vytváříte aplikace pro vytváření sestav pro oddělení. Jste si jisti, že aplikace použije pouze v rámci oboru vaší společnosti a že jsou krátkodobé a jednorázové sestavy. Podle vašeho výběru přístup se liší od, Řekněme, že, vytvářet služby, který poskytuje obsahu videa na desítky milionů zákazníků.

V některých případech něco letos uvedli na trh jako testování konceptu je řízení faktoru, když víte, že aplikace může později přepracován. Je trochu bod v over-pass-the technické něco, který se nikdy používá. Je to obvyklé engineering kompromis. Na druhé straně společnosti mluvit o sestavování pro cloud, že budou při růstu a využití. Tento problém je, že nepředvídatelné růst a škálování. Rádi bychom mohli k rychlému vytvoření prototypu rychle zároveň také budete vědět, že jsme se na cestě se budoucímu úspěchu. Jedná se o postup úsporného startupu: sestavování, měření, další informace a iterovat.

Během období klient server jsme ošetření soustředit na vytváření vrstvené aplikace s použitím konkrétní technologie v každé úrovni. Termín *monolitické* aplikace se stal pro tyto přístupy. Rozhraní pro ošetření bude mezi vrstvami a návrh s větší provázaností použila mezi součástmi v rámci jednotlivých úrovní. Vývojáři navržené a započítané třídy, které byly kompilovány do knihoven a propojeny na několik knihoven DLL a spustitelné soubory. 

Existují výhody a monolitický návrh přístup. Často je jednodušší návrh a má rychlejší volání mezi součástmi, protože tato volání jsou často přes meziprocesová komunikace (IPC). Navíc všechny testy jeden produkt, který je spíše více lidem – zdroj efektivní. Nevýhodou je, že je úzkou svázanost mezi vrstvené vrstvy a už nebude možné škálovat jednotlivé komponenty. Pokud potřebujete provádět opravy nebo upgrade, budete muset počkat pro ostatní uživatele k dokončení jejich testování. Je obtížnější pružnost.

Mikroslužby vyřešit tyto nevýhody a více těsně propojit s předchozím obchodní požadavky, ale mají také výhody a závazků. Mikroslužeb je, že každý z nich obvykle zapouzdřuje jednodušší obchodní funkce, které můžete vertikálně navyšovat nebo snižovat, otestovat, nasadit a spravovat nezávisle na sobě. Důležitou výhodou mikroslužeb je, že týmy se řídí informace podle obchodní scénáře než technologie – což může vést ke vzniku vrstveného přístupu. V praxi menší týmy vývoj mikroslužeb založené na scénářích zákazníka a používat libovolný technologie, která si vyberou.

Jinými slovy organizace nemusí ke standardizaci Odborný udržovat vyladěných aplikací mikroslužeb. Jednotlivé týmy, co dává smysl pro ně podle znalostí týmu nebo co je nejvhodnější k vyřešení problému můžete provést vlastní služby. V praxi sadu doporučené technologií, jako je například konkrétní NoSQL ukládat nebo architektura webové aplikace, je vhodnější.

Nevýhodou mikroslužeb je k dispozici ve správě zvýšeného počtu samostatné entity a zabývající se složitější nasazení a správy verzí. Síťový provoz mezi mikroslužby zvyšuje i odpovídající síťovou latenci. Mnoho přetížení granulárních služeb jsou předpisu u připomínající výkonu. Bez nástroje, které pomáhají zobrazení těchto závislostí, je obtížné "v tématu" v celém systému.

Standardy zkontrolujte přístup mikroslužeb fungují tak, že souhlas vyjádří na tom, jak komunikovat a je odolný vůči chybám pouze věcí, je nutné ze služby, nikoli od rigidních smluv. Je důležité definovat ještě před zahájením těchto smluv v návrhu, protože aktualizace služeb nezávisle na sobě. Další popis poprvé použit pro návrh s přístup založený na mikroslužbách je "podrobných orientované na služby architektura (SOA)."

***V nejjednodušším přístupem návrhu mikroslužeb spočívá oddělující federačních služeb, nezávislých změn u každého a odsouhlaseným normy pro komunikaci.***

Protože se vytvářejí další cloudové aplikace, uživatelé zjistili, že tento rozložené celkové aplikace do služby nezávislé, zaměřuje scénář je lépe dlouhodobý přístup.

## <a name="comparison-between-application-development-approaches"></a>Porovnání mezi dvěma způsoby vývoje aplikací

![Vývoj aplikací pro platformy Service Fabric][Image1]

1) Monolitické aplikace obsahuje funkci specifického pro doménu a obvykle dělený funkční vrstvy, jako jsou webové, obchodní a data.

2) Škálování monolitické aplikace naklonováním na více serverech a virtuálních počítačů a kontejnerů.

3) Aplikace mikroslužeb odděluje funkcí do samostatných menších služeb.

4) Škálování přístup mikroslužeb navýšení kapacity o nasazení na jednotlivé služby nezávisle na sobě, vytváření instancí těchto služeb na různých serverech a virtuálních počítačů a kontejnerů.

Návrh s mikroslužbě přístup není všelék pro všechny projekty, ale lépe zarovnat s obchodními záměry popsané výše. Počínaje monolitického přístupu může být přijatelné, pokud víte, že budete mít možnost přepracovat kód později do návrhu mikroslužeb. Častěji začněte s monolitickými aplikacemi a pomalu jej rozdělte ve fázích, počínaje funkčních oblastí, které musí být škálovatelné nebo agile.

Přístup mikroslužeb znamená, že vytvoříte aplikaci mnoha malých služeb. Služba se spouští v kontejnerech, které jsou nasazeny napříč clusterem počítačů. Menší týmy vývoji služeb, který se zaměřuje na scénář nezávisle na sobě, verze, nasazení a testování každou službu škálovat tak, aby celé aplikace můžete vyvíjet.

## <a name="what-is-a-microservice"></a>Co je mikroslužby?

Existují různé definice mikroslužeb. Ale většinu následující charakteristiky mikroslužeb jsou běžně dohodnutých:

* Zapouzdření scénáři zákazníka nebo podnik. V čem je problém, který jste řešení?
* Vyvinutá společností malému technickému týmu.
* Napsat v jakémkoli programovacím jazyce a použít libovolné architektury.
* Skládají z kódu a (volitelně) stavu, které jsou nezávisle vyvíjených, nasadit a škálovat.
* Pracovat s další mikroslužeb prostřednictvím kvalitně definovanými rozhraními a protokoly.
* Mít jedinečné názvy (adresy URL) používá k překladu jejich umístění.
* Zůstávají konzistentní a k dispozici v případě chyb.

Souhrn:

***Aplikace Mikroslužeb se skládají z malých, nezávisle vyvíjených a škálovatelné zaměřených na zákazníka služby, které komunikují pomocí standardních protokolů s kvalitně definovanými rozhraními.***

### <a name="written-in-any-programming-language-and-use-any-framework"></a>Napsat v jakémkoli programovacím jazyce a použít libovolné architektury

Jako vývojáři chcete zvolit jazyk nebo rozhraní, které chceme, aby v závislosti na naše dovednosti, nebo potřebuje služby. V některých služeb může být hodnota přinese zlepšení výkonu C++ nad všemi else. V dalších službách může být jednoduchý spravovaný vývoj v jazyce C# nebo Java nejdůležitější. V některých případech budete muset použít konkrétní partnerskou knihovny, technologie úložiště dat nebo prostředky vystavení služby klientům.

Poté co jste vybrali technologie, Přijďte na provozní databázi nebo životního cyklu správy a škálování služby.

### <a name="allows-code-and-state-to-be-independently-versioned-deployed-and-scaled"></a>Umožňuje kódu a stav nezávisle vyvíjených, nasadit a škálovat

Ale můžete rozhodnout pro zápis mikroslužby, kód a volitelně stavu, by měl nezávisle na sobě nasazení, upgrade a škálování. Toto je jedna z obtížnější problémy vyřešit, protože jde o technologií podle vlastního výběru. Pro škálování, porozumění oddíl (nebo horizontální oddíl) kódu a stav je náročná. Kód a stav použití samostatných technologie, které je dnes běžné, skripty nasazení pro vaši mikroslužeb musí být oba škálovat. Jde o agilnost a flexibilita, proto můžete upgradovat některé mikroslužby nemusíte upgradovat všechny najednou.

Vrácení na monolitické oproti mikroslužeb na chvíli zastavíme, následující diagram znázorňuje rozdíly v přístupu k ukládání stavu.

#### <a name="state-storage-between-application-styles"></a>Stav úložiště mezi aplikace styly

![Úložiště stavu platformy Service Fabric][Image2]

***Monolitického přístupu na levé straně má izolovaná databáze a úrovně konkrétní technologie.***

***Přístup založený mikroslužbách na pravé straně se graf z propojených mikroslužeb, kde stavu obvykle působí mikroslužbách a používají se různé technologie.***

V monolitického přístupu aplikace obvykle používá jednu databázi. Výhodou je, že se jedná o jediného umístění, které umožňuje snadno nasadit. Každá komponenta může mít jednu tabulku pro ukládání stavu. Týmy musí striktně oddělení stavu, což je náročné. Existují nevyhnutelně temptations přidat nový sloupec do existující tabulky zákazníků, proveďte spojení mezi tabulkami a vytvořte závislosti ve vrstvě úložiště. Jakmile k tomu dojde, už nebude možné škálovat jednotlivé komponenty.

V přístup založený mikroslužbách každá služba spravuje a ukládá svůj stav. Každá služba je zodpovědná za škálování kódu a stav společně s cílem splnit požadavky služby. Nevýhodou je, když je potřeba vytvořit zobrazení, nebo dotazy dat vaší aplikace, budete muset dotazování napříč více úložišti stavu. To je obvykle vyřešit tím, že samostatné mikroslužeb, která vytvoří zobrazení v kolekci z mikroslužeb. Pokud potřebujete provádět více neocenitelní dotazy na data, zvažte jednotlivých mikroslužeb zápisu svá data do datového skladu služby pro offline analýzu.

Mikroslužeb mají samostatné verze a je možné, že různé verze mikroslužby mohou běžet vedle sebe. Novější verzi mikroslužbě může selhat při upgradu a nutné se vrátit zpátky na starší verzi. Správa verzí je také užitečné pro testování A/B – vizuální styl, kde různí uživatelé vyzkoušet různé verze služby. Například je společné pro upgrade mikroslužeb pro konkrétní sadu odběratelů před distribucí více široce otestovat nové funkce.

### <a name="interacts-with-other-microservices-over-well-defined-interfaces-and-protocols"></a>Komunikuje s další mikroslužeb prostřednictvím kvalitně definovanými rozhraními a protokoly

V průběhu posledních 10 let popisující, schémata komunikace se publikoval rozsáhlou dokumentaci o architektura orientovaná na služby. Obecně platí komunikace služeb používá přístup REST s protokoly HTTP a TCP a XML nebo JSON jako formát serializace. Z hlediska rozhraní jde o postup návrhu webové středu. Ale nic zabraňuje pomocí binární protokoly nebo vlastních datových formátů. Připravit lidem, kteří mají obtížnější času pomocí mikroslužby, pokud nejsou tyto protokoly a formáty otevřeně dostupné.

### <a name="has-a-unique-name-url-used-to-resolve-its-location"></a>Má jedinečný název (URL) používá k překladu umístění

Vaše mikroslužeb musí být adresovatelný bez ohledu na to funguje. Pokud přemýšlíte o počítačích a která z nich je spuštěna konkrétní mikroslužeb, něco chybný rychle. 

Stejným způsobem, že DNS překládá určité adresy URL na konkrétní počítač musí vaše mikroslužeb jedinečného názvu tak, aby jeho aktuálního umístění zjistitelné. Mikroslužby potřebovat adresovatelný názvy, které jsou nezávislé na vaší stávající infrastrukturou, na kterém běží. To znamená, že interakci mezi způsob nasazení vaší služby a jak je zjištěno, protože musí být služba registru. Pokud se počítači nezdaří, služba registru musí zjistit, kde služby byl přesunut do.

### <a name="remains-consistent-and-available-in-the-presence-of-failures"></a>Zůstává konzistentní a k dispozici v případě chyb

Práci s neočekávanými chybami je jedním z těch nejtěžších problémy vyřešit, zejména v distribuovaném systému. Velká část kódu, který jsme napsali jako vývojáři je zpracování výjimek, a to je také kde je nejvíce času stráveného při testování. Problém se tak zapojí víc než psaní kódu pro zpracování chyb. Co se stane, když selže na počítači, kde je spuštěná mikroslužbách? Jenom je potřeba ke zjištění selhání (pevné problém sama o sobě), ale bude také nutné restartovat vaše mikroslužeb.

Mikroslužby důvodů dostupnosti, musí být odolné vůči selhání a možné spustit na jiném počítači. Kromě spuštěním kódu je odolné by neměl být ztrátě dat a data musí zůstat konzistentní vzhledem k aplikacím.

Odolnost proti chybám je obtížné dosáhnout při během upgradu aplikace dochází k chybám. Mikroslužby, práce s nasazení systému, nemusí obnovení. Je potřeba rozhodnout, jestli můžete nadále posunout vpřed na novější verzi nebo místo toho vrátit zpět k předchozí verzi pro zachování konzistentního stavu. Třeba zvážit otázky, jako je například, jestli je možné zachovat přesun vpřed dost počítačů a jak obnovit předchozí verze mikroslužbách. To vyžaduje mikroslužeb ke generování informací o stavu tato rozhodnutí.

### <a name="reports-health-and-diagnostics"></a>Sestavy stav a Diagnostika

To se může zdát zřejmé a je často přehlédnuta, ale mikroslužby se musí hlásit svůj stav a Diagnostika. V opačném případě je trochu přehled o tom, jeho stav z hlediska operace. Korelace diagnostické události sady nezávislých služeb a práci s počítači nepřesnostem dávat smysl pořadí událostí, je náročné. Stejným způsobem, který budete moct používat mikroslužbě přes odsouhlaseným protokolů a datových formátů budete muset standardizovat jak stavu a diagnostické události, které bude nakonec ukládaly do úložiště událostí pro dotazování a zobrazení protokolu. V přístup založený na mikroslužbách, ji má klíče, které různé týmy se dohodly na formát jednoho protokolování. Musí být konzistentní vzhledem k aplikacím přístup k zobrazení diagnostických událostí v aplikaci jako celek.

Stav se liší od diagnostiky. Stav je o mikroslužbách jeho aktuální stav přijmout vhodná opatření pro vytváření sestav. Dobrým příkladem je práce s mechanismy upgradu a nasazení dostupnost. I když se služba může být aktuálně v chybném stavu z důvodu chybové ukončení procesu nebo restartování počítače, služba může být stále provozní. Poslední věcí, které potřebujete je, aby to horší provedením upgradu. Nejlepším řešením je nejdřív proveďte šetření nebo nějakou dobu počkat, mikroslužeb pro obnovení. Události stavu v mikroslužbě Pomozte nám umožňují přijímat informovaná rozhodnutí a v důsledku toho vám pomůžou vytvořit samoopravení služby.

## <a name="service-fabric-as-a-microservices-platform"></a>Service Fabric jako platformu mikroslužeb

Azure Service Fabric se stala při Microsoftu přešel ze doručování zabalené produkty, které byly obvykle monolitické ve stylu, k zajištění služeb. Prostředí pro sestavování a provoz velkých služby, jako je Azure SQL Database a Azure Cosmos DB ve tvaru Service Fabric. Platforma se v čase, jako další a další služby přijal. Service Fabric se museli spouštět pouze v Azure, ale také v samostatných nasazeních systému Windows Server.

***Cílem Service Fabric je řeší těžké problémy sestavení a spuštění služby a efektivně využívat prostředky infrastruktury tak, aby týmy mohou řešení obchodních problémů pomocí přístup založený na mikroslužbách.***

Service Fabric umožňuje vytvářet aplikace, které používají přístup založený na mikroslužbách tím, že poskytuje následující operace:

* Platforma, která poskytuje systémové služby pro nasazení, upgrade, zjišťovat a restartujte služby se nezdařilo, zjišťovat služby, směrování zpráv, Správa stavu a monitorovat stav.
* Možnost nasazovat aplikace buď spuštěný v kontejnerech nebo jako procesy. Service Fabric je kontejner a procesu orchestrator.
* Produktivní programovací rozhraní API, které vám pomůžou vytvářet aplikace jako mikroslužeb: [ASP.NET Core a Reliable Actors, Reliable Services](service-fabric-choose-framework.md). Například můžete získat informace o stavu a diagnostické nástroje, nebo můžete využít integrovanou vysokou dostupnost.

***Service Fabric je nezávislá na jak vytvářet služby, a můžete použít libovolné technologii. Však nabízí integrovanou programovací rozhraní API, která usnadňují vytváření mikroslužeb.***

### <a name="migrating-existing-applications-to-service-fabric"></a>Migraci stávajících aplikací do Service Fabric

Service Fabric umožňuje opakovaně používat existující kód, který lze potom modernizovala s nové mikroslužby. Existuje pět fází k modernizaci aplikací, a můžete spustit a zastavit na libovolné z těchto fází. Jsou to:

1) Začněte s tradiční monolitické aplikace.  
2) Zvedněte a Shift - kontejnerech nebo spustitelných souborech hostů používat k hostování existující kód ve službě Service Fabric.  
3) Modernizace – nové mikroslužby přidali souběžně s existující kontejnerizovaných kód.  
4) Inovace – rozdělit monolitické na mikroslužby čistě podle potřeb.  
5) Transformuje na mikroslužby – transformace existujících monolitické aplikace nebo vytváření nových aplikací úplně nové.

![Migrace do Mikroslužeb][Image3]

Je důležité zdůraznit, že je možné znovu **spouštět a zastavovat na libovolné z těchto fází**. Nejsou nám vznikne povinnost neodpovídajících do další fáze. Nyní Podívejme se na příklady pro každý z těchto fází.

**Lift and Shift**  
Velký počet společnosti jsou zrušení a posunutí existující monolitické aplikace do kontejnerů dvou důvodů:

- Snížení nákladů buď z důvodu konsolidace a odebrání stávající hardware nebo spuštěné aplikace na vyšší hustota.
- Konzistentní nasazování kontrakt mezi vývojem a provozem.

Snížení nákladů, jsou srozumitelné a v rámci Microsoftu, jsou právě kontejnerizovaných velkého počtu stávajících aplikací jednoduše do ušetřit miliony dolarů. Konzistentní nasazování je obtížnější k vyhodnocení, ale stejně jako důležité. Znamená to, aby vývojáři mohli být stále zvolit technologie, která jim vyhovuje, ale operace bude přijímat pouze jeden způsob, jak nasadit a spravovat tyto aplikace. Zmírňuje operací z museli potýkat se složitost mnoha různých technologií nebo vynucení vývojářům vybrat jenom některé z nich. V podstatě každá aplikace je kontejnerizovaných do bitové kopie samostatná nasazení.

Mnoho organizací ukončit. Už mají výhody kontejnerů a Service Fabric poskytuje prostředí pro kompletní správu z nasazení, upgrady, správy verzí, vrácení zpět, monitorování stavu atd.

**Modernizace**  
Přidání nových služeb společně s existující kontejnerizovaných kódu. Pokud chcete zadat nový kód, je nejlepší rozhodnout postupujte po menších krůčcích rozhodli mikroslužeb. To může přidání nového koncového bodu rozhraní REST API nebo nové obchodní logiku. Tímto způsobem, spusťte na cestě vytváření nových mikroslužeb a postupů vývoje a jejich nasazování.

**Inovace**  
Musí se měnící obchodní mikroslužeb přístup zahrnete. V této fázi je rozhodnutí, jestli je potřeba spustit rozdělení monolitické aplikace do služby nebo inovace. Je zde například když se databáze používá jako pracovní postup fronty stane kritickým bodem zpracování. Jako počet pracovního postupu žádostí o zvýšení, práci potřebuje pro škálování. Pro tento konkrétní aplikace, která není škálování nebo, který je potřeba aktualizovat častěji to zjistit rozdělit do mikroslužeb a inovace.

**Transformuje na mikroslužby**  
To je, kde je vaše aplikace plně skládá z (nebo rozložená na) mikroslužeb. K dosažení tady, provedli cesty mikroslužeb. Začněte tady, ale k tomu bez založený na mikroslužbách platformy, abychom vám pomůže je významnou investici. 

### <a name="are-microservices-right-for-my-application"></a>Jsou to mikroslužby pro mé aplikace?

Podle potřeby. Jsme zaznamenali bylo, že jako více týmů v Microsoftu začala k vývoji pro cloud pro podnik, mnoho z nich realizované výhody díky přístupu jako mikroslužeb. Bing, například má byl vývoj mikroslužeb pomocí let. Pro jiné týmy se nový přístup založený mikroslužbách. Týmy zjištěno, že existuje bylo těžké problémy vyřešit mimo jejich klíčové oblasti síly. To je důvod, proč Service Fabric získala výrazně nabývá na dynamice technologií volbou pro vytváření služeb.

Cílem Service Fabric je ke snížení složitosti vytváření vyladěných aplikací mikroslužeb, takže není nutné kvůli tomu provádět tolik nákladné pracovali. Začněte v malém, škálovat v případě potřeby, přestat používat služby, přidat nové a budou vyvíjet současně s využití ze strany zákazníků. Víme také, že existují mnoho problémů ještě mají být vyřešeny, chcete-li více přístupné mikroslužeb pro většinu vývojářů. Příkladem malých kroků v tomto směru jsou kontejnery a programovací model objektu actor a jsme si jisti, že budou vznikat další inovací pro zjednodušení.
 
<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Další postup

* [Přehled terminologie Service Fabric](service-fabric-technical-overview.md)
* [Mikroslužeb: Aplikace revolution vytvořená v cloudu](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)

[Image1]: media/service-fabric-overview-microservices/monolithic-vs-micro.png
[Image2]: media/service-fabric-overview-microservices/statemonolithic-vs-micro.png
[Image3]: media/service-fabric-overview-microservices/microservices-migration.png
