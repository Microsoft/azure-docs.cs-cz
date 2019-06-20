---
title: Úvod do mikroslužeb v Azure | Dokumentace Microsoftu
description: Přehled vytváření cloudových aplikací s přístup založený na mikroslužbách Proč je důležité pro vývoj moderních aplikací a jak Azure Service Fabric poskytuje platformu pro můžete toho dosáhnout.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: fae2be85-0ab4-4cd3-9d1f-e0d95fe1959b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/18/2019
ms.author: atsenthi
ms.openlocfilehash: 5bcb52165c7cae18b807eff03c80b51eae8e2717
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204797"
---
# <a name="why-use-a-microservices-approach-to-building-applications"></a>Proč používat k vytváření aplikací přístup založený na mikroslužbách?

Pro vývojáře softwaru které budou zohledňovat aplikace do součásti je není nic nového. Vrstveného přístupu se obvykle používá, s back endové úložiště, střední vrstvy obchodní logiky a front-endu uživatelského rozhraní (UI). Co *má* mění v průběhu posledních několika letech je, že vývojáři vytvářejí distribuovaných aplikací pro cloud.

Tady jsou některé měnícím se potřebám organizace:

* Služba, která má vybudovala a provozovala ve velkém měřítku k oslovení zákazníků v nové geografické oblasti.
* Rychlejší doručování funkcí a možností pružně reagovat na požadavky zákazníků.
* Využití prostředků vylepšené ke snížení nákladů.

Tyto obchodní potřeby. ovlivňují *jak* budeme vytvářet aplikace.

Další informace o Azure přístup k mikroslužeb najdete v tématu [Mikroslužeb: Revoluci aplikací založené na cloudu](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="monolithic-vs-microservices-design-approach"></a>Monolitické vs. přístup v rámci návrhu mikroslužeb

Aplikace v průběhu času vyvíjejí. Tím, že je užitečné pro osoby, vyvíjí úspěšné aplikace. Neúspěšné žádosti není vyvíjí a nakonec se považují za zastaralé. Tady je Otázka: kolik víte o vaše požadavky ještě dnes a co budou v budoucnu? Řekněme například, že vytváříte aplikace pro vytváření sestav pro oddělení ve vaší společnosti. Jste si jisti, aplikace použije pouze v rámci oboru vaší společnosti a, že sestavy nebudou uloženy dlouho. Váš přístup se bude lišit od, Řekněme, že, vytvářet služby, který poskytuje obsahu videa na desítky milionů zákazníků.

V některých případech něco letos uvedli na trh jako testování konceptu je řízení faktor. Víte, že aplikace může později přepracován. Je trochu bod v over-pass-the technické něco, který se nikdy používá. Na druhé straně společnosti build pro cloud, že budou při růstu a využití. Růst a škálování nepředvídatelné. Chceme, aby k rychlému vytvoření prototypu rychle zároveň také budete vědět, že jsme na cestu, která dokáže zpracovat budoucímu úspěchu. Jedná se o postup úsporného startupu: sestavování, měření, další informace a iterovat.

Během období klient/server jsme ošetření soustředit na vytváření vrstvené aplikace s použitím konkrétní technologie v každé úrovni. Termín *monolitické* aplikace se stal pro popis těchto přístupů. Rozhraní pro ošetření bude mezi vrstvami a návrh s větší provázaností použila mezi součástmi v rámci jednotlivých úrovní. Vývojáři navržené a dostaneme tříd, které byly kompilovány do knihoven a propojeny na několik knihoven DLL a spustitelné soubory.

Existují výhody monolitický návrh přístup. Monolitické aplikace jsou často jednodušší návrh a volání mezi součástmi jsou rychlejší, protože tato volání jsou často přes meziprocesová komunikace (IPC). Navíc všechny testy jeden produkt, který je spíše zefektivnit tak využívání lidských zdrojů. Nevýhodou je, že je úzkou svázanost mezi vrstvené vrstvy a už nebude možné škálovat jednotlivé komponenty. Pokud je potřeba provést opravy nebo upgrade, budete muset počkat pro ostatní uživatele k dokončení jejich testování. Být agilní je obtížnější.

Mikroslužby vyřešte tyto nevýhody a více těsně propojit s předchozím obchodní požadavky. Ale mají také výhody a závazků. Mikroslužeb je, že každý z nich obvykle zapouzdřuje jednodušší obchodní funkce, které můžete vertikálně navyšovat nebo snižovat, otestovat, nasadit a spravovat nezávisle na sobě. Důležitou výhodou přístup založený na mikroslužbách je, že týmy se řídí informace podle obchodní scénáře než technologie. Menší týmy vývoj mikroslužeb založené na scénářích zákazníka a používat libovolný technologie, které chcete použít.

Jinými slovy organizace nemusí ke standardizaci Odborný udržovat vyladěných aplikací mikroslužeb. Jednotlivé týmy, co dává smysl pro ně podle znalostí týmu nebo co je nejvhodnější k vyřešení problému můžete provést vlastní služby. V praxi sadu doporučené technologie, jako jsou konkrétní NoSQL s dvojicí nebo architektura webové aplikace, je vhodnější.

Nevýhodou mikroslužeb je, že máte ke správě více samostatné entity a složitější nasazení a správy verzí. Síťový provoz mezi mikroslužby zvyšuje, stejně jako odpovídající síťovou latenci. Mnoho přetížení granulárních služeb může způsobit připomínající výkonu. Bez nástroje k zobrazení těchto závislostí je špatně vidět celý systém.

Standardy zkontrolujte přístup založený mikroslužbách fungují tak, že určíte, jak komunikovat a únosnost pouze věci, třeba ze služby, nikoli od rigidních smluv. Je důležité definovat ještě před zahájením těchto smluv v návrhu, protože aktualizace služeb nezávisle na sobě. Další popis poprvé použit pro návrh s přístup založený na mikroslužbách je "podrobných orientované na služby architektura (SOA)."

***V nejjednodušším přístupem návrhu mikroslužeb spočívá oddělující federačních služeb nezávislých změn u každého a normy odsouhlaseným pro komunikaci.***

Protože se vytvářejí další cloudové aplikace, uživatelé zjistili, že tento rozložené celkové aplikace do služby nezávislé, zaměřuje scénář je lépe dlouhodobý přístup.

## <a name="comparison-between-application-development-approaches"></a>Porovnání mezi dvěma způsoby vývoje aplikací

![Vývoj aplikací pro platformy Service Fabric][Image1]

1) Monolitické aplikace obsahuje funkci specifického pro doménu a je obvykle rozdělena do funkční vrstvy, jako jsou webové, obchodní a data.

2) Škálování jednotlivou aplikaci naklonováním na více serverech a virtuálních počítačů a kontejnerů.

3) Aplikace mikroslužeb odděluje funkcí do samostatných menších služeb.

4) Škálování přístup mikroslužeb navýšení kapacity o nasazení na jednotlivé služby nezávisle na sobě, vytváření instancí těchto služeb na různých serverech a virtuálních počítačů a kontejnerů.

Návrh s založený na mikroslužbách přístup není vhodná pro všechny projekty, ale lépe zarovnat s obchodními záměry popsané výše. Počínaje monolitického přístupu může mít smysl, pokud víte, že budete mít možnost přepracovat kód později do návrhu mikroslužeb. Častěji začněte s monolitickými aplikacemi a pomalu jej rozdělte ve fázích, počínaje funkčních oblastí, které musí být škálovatelné nebo agile.

Použijete-li přístup založený na mikroslužbách, vytváříte aplikaci mnoha malých služeb. Tyto služby jsou spuštěny v kontejnerech, které jsou nasazeny napříč clusterem počítačů. Menší týmy vývoj služba, která se zaměřuje na scénáře a nezávisle testovat, verze, nasadit a škálovat jednotlivé služby, takže můžete vyvíjet v celé aplikaci.

## <a name="what-is-a-microservice"></a>Co je mikroslužby?

Existují různé definice mikroslužeb. Ale většina tyto charakteristiky mikroslužeb jsou široce přijat:

* Zapouzdření scénáři zákazníka nebo podnik. Jaký problém jste řešení?
* Vyvinutá společností malému technickému týmu.
* Napsané v jakémkoli programovacím jazyce a využitím libovolné architektury.
* Skládají z kódu, a volitelně stavu, které jsou nezávisle vyvíjených, nasadit a škálovat.
* Pracovat s další mikroslužeb prostřednictvím kvalitně definovanými rozhraními a protokoly.
* Mít jedinečné názvy (adresy URL), které se používají k řešení jejich umístění.
* Zůstávají konzistentní a k dispozici v případě chyb.

Provede součet, který:

***Aplikace Mikroslužeb se skládají z malých, nezávisle vyvíjených a škálovatelné zaměřených na zákazníka služby, které komunikují pomocí standardních protokolů s kvalitně definovanými rozhraními.***

### <a name="written-in-any-programming-language-using-any-framework"></a>Napsané v jakémkoli programovacím jazyce a využitím libovolné architektury

Jako vývojáři chcete zvolit na jazyk nebo architekturu, v závislosti na naše dovednosti a požadavky na službu, kterou vytváříme. U některých služeb může být hodnota přinese zlepšení výkonu C++ nad cokoli jiného. Ostatní uživatelé snadno spravovaný vývoj, který obdržíte od C# nebo Java může být důležité. V některých případech můžete potřebovat použít konkrétní partnerskou knihovny, technologie úložiště dat nebo metodu pro vystavení služby klientům.

Až zvolíte, technologie, budete muset vezměte v úvahu provozní nebo životního cyklu správy a škálování služby.

### <a name="allows-code-and-state-to-be-independently-versioned-deployed-and-scaled"></a>Umožňuje kódu a stav nezávisle vyvíjených, nasadit a škálovat

Bez ohledu na to, jak psát mikroslužby, kód a volitelně stav by měl nezávisle na sobě nasazení, upgrade a škálování. Tento problém se těžko řeší, protože jde o technologií podle vlastního výběru. Pro škálování, porozumění oddíl (nebo horizontální oddíl) kód a stav je náročná. Použijete-li kód a stav různých technologií, které je dnes běžné, skripty nasazení pro vaši mikroslužeb musí být oba škálovat. Toto oddělení je také o agilnost a flexibilita, takže některé mikroslužby můžete upgradovat bez upgradu je všechny najednou.

Vraťme se k naší porovnání přístupy monolitické a mikroslužby na chvíli zastavíme. Tento diagram znázorňuje rozdíly v přístupy k ukládání stavu:

#### <a name="state-storage-for-the-two-approaches"></a>Stavu úložiště pro tyto dvě metody

![Úložiště stavu platformy Service Fabric][Image2]

***Monolitického přístupu na levé straně, má izolovaná databáze a úrovně konkrétní technologie.***

***Přístup založený mikroslužbách, na pravé straně, je graf propojených mikroslužeb, kde stavu obvykle působí mikroslužbách a používají se různé technologie.***

V monolitického přístupu aplikace obvykle používá jednu databázi. Výhodou použití jedné databáze je, že je na jednom místě, které umožňuje snadno nasadit. Každá komponenta může mít jednu tabulku pro ukládání stavu. Týmy musí striktně oddělení stavu, což je náročné. Nevyhnutelně uživatel bude mít tendenci získat přidání sloupce do existující tabulky zákazníků, proveďte spojení mezi tabulkami a vytvoření závislostí ve vrstvě úložiště. Jakmile k tomu dojde, už nebude možné škálovat jednotlivé komponenty.

V přístup založený mikroslužbách každá služba spravuje a ukládá svůj stav. Každá služba je zodpovědná za škálování kódu a stav společně s cílem splnit požadavky služby. Nevýhodou je, když je potřeba vytvořit zobrazení nebo dotazy dat vaší aplikace, budete muset dotazování napříč více úložišti stavu. Tento problém se běžně řeší samostatné mikroslužeb, která vytvoří zobrazení v kolekci z mikroslužeb. Pokud potřebujete spustit více neocenitelní dotazů na data, měli byste zvážit, zápis jednotlivých mikroslužeb dat do datového skladu služby pro offline analýzu.

Mikroslužby se systémovou správou verzí. Je možné pro různé verze mikroslužeb pro spuštění vedle sebe. Novější verzi mikroslužba by mohla převzetí služeb při během upgradu a musí se vrátit zpátky na starší verzi. Správa verzí je také užitečné pro A / B testování, kde různí uživatelé vyzkoušet různé verze služby. Například je společné pro upgrade mikroslužeb pro konkrétní sadu odběratelů před distribucí více široce otestovat nové funkce.

### <a name="interacts-with-other-microservices-over-well-defined-interfaces-and-protocols"></a>Komunikuje s další mikroslužeb prostřednictvím kvalitně definovanými rozhraními a protokoly

V průběhu posledních 10 let byl publikován rozsáhlé informace popisující, schémata komunikace v architektury orientované na služby. Obecně platí komunikace služeb používá přístup REST s protokoly HTTP a TCP a XML nebo JSON jako formát serializace. Z hlediska rozhraní jde o díky přístupu návrh webové. Ale co by se měla zastavit pomocí binární protokoly nebo vlastních datových formátů. Právě mějte na paměti, že uživatelé budou mít obtížnější času pomocí mikroslužby, pokud nejsou otevřeně dostupné těchto protokolů a formátů.

### <a name="has-a-unique-name-url-used-to-resolve-its-location"></a>Má jedinečný název (URL) používá k překladu umístění

Vaše mikroslužeb musí být adresovatelný bez ohledu na to funguje. Pokud uvažujete o počítačích, který z nich je spuštěn konkrétní mikroslužeb může něco chybný rychle.

Stejným způsobem, že DNS překládá určité adresy URL na konkrétní počítač musí vaše mikroslužeb jedinečného názvu tak, aby jeho aktuálního umístění zjistitelné. Mikroslužby potřebují adresovatelný názvy, které jsou nezávislé na infrastrukturu, které běží na. To znamená, že interakci mezi způsob nasazení vaší služby a jak je zjištěno, protože musí být služba registru. Když selže na počítači, musí zjistíte, kde se služba přesunul do registru služby.

### <a name="remains-consistent-and-available-in-the-presence-of-failures"></a>Zůstává konzistentní a k dispozici v případě chyb

Práci s neočekávanými chybami je jedním z těch nejtěžších problémy vyřešit, zejména v distribuovaném systému. Velká část kódu, který jsme napsali jako vývojáři je pro zpracování výjimek. Při testování jsme také strávit nejvíce času na zpracování výjimek. Proces je složitější než psaní kódu pro zpracování chyb. Co se stane, když selže počítače, na kterém je spuštěný mikroslužbách? Budete muset zjistit chyby, které je obtížné problém sama o sobě. Ale bude také nutné restartovat vaše mikroslužeb.

Mikroslužby dostupnosti, musí být odolné vůči selhání a možné spustit na jiném počítači. Kromě těchto požadavků na odolnost proti chybám nesmí dojít ke ztrátě dat a data musí zůstat konzistentní vzhledem k aplikacím.

Odolnost proti chybám je obtížné dosáhnout při během upgradu aplikace dochází k chybám. Mikroslužby, práce s nasazení systému, nemusí obnovení. Je potřeba určit, jestli můžete nadále posunout vpřed na novější verzi nebo vrátit zpět na předchozí verzi k zachování konzistentního stavu. Je potřeba zvážit několik otázek, například zda jsou k dispozici zajistit přesun vpřed dost počítačů a jak obnovit předchozí verze mikroslužbách. Tato rozhodnutí, budete potřebovat mikroslužeb ke generování informací o stavu.

### <a name="reports-health-and-diagnostics"></a>Sestavy stav a Diagnostika

To může zdát zřejmé a je často přehlédnuta, ale mikroslužbě potřebuje nahlásit jeho stav a Diagnostika. Jinak máte malý přehled o tom, jeho stav z hlediska operace. Korelace diagnostické události sady nezávislých služeb a práci s počítači nepřesnostem dávat smysl pořadí událostí, je náročné. Stejným způsobem, který budete moct používat mikroslužbě přes odsouhlaseným protokolů a datových formátů budete muset standardizovat jak stavu a diagnostické události, které bude nakonec ukládaly do úložiště událostí pro dotazování a zobrazení protokolu. S přístup založený na mikroslužbách různé týmy musí shodnout na formát jednoho protokolování. Musí být konzistentní vzhledem k aplikacím přístup k zobrazení diagnostických událostí v aplikaci jako celek.

Stav se liší od diagnostiky. Stav je o mikroslužbách jeho aktuální stav přijmout vhodná opatření pro vytváření sestav. Dobrým příkladem je práce s mechanismy upgradu a nasazení dostupnost. I když služba může být aktuálně v chybném stavu chybové ukončení procesu nebo počítač restartovat počítač, služba může být stále provozní. Poslední věcí, které potřebujete je, aby situaci horší spuštěním upgradu. Nejlepším řešením je prozkoumat nejdříve, nebo počkejte na mikroslužeb pro obnovení. Události stavu v mikroslužbě Pomozte nám umožňují přijímat informovaná rozhodnutí a v důsledku toho vám pomůžou vytvořit samoopravení služby.

## <a name="guidance-for-designing-microservices-on-azure"></a>Pokyny pro návrh mikroslužeb v Azure 
Navštivte centrum architektury Azure pokyny na [návrh a vytváření mikroslužeb v Azure](https://docs.microsoft.com/azure/architecture/microservices/).

## <a name="service-fabric-as-a-microservices-platform"></a>Service Fabric jako platformu mikroslužeb

Azure Service Fabric se stala při Microsoftu přešel ze doručování zabalené produkty, které byly obvykle monolitické, k zajištění služeb. Prostředí pro sestavování a provoz velkých služby, jako je Azure SQL Database a Azure Cosmos DB ve tvaru Service Fabric. Platforma se v čase, jako další služby přijal. Service Fabric se museli spouštět pouze v Azure, ale také v samostatných nasazeních systému Windows Server.

***Cílem Service Fabric je řeší těžké problémy sestavení a spuštění služby a efektivně využívat prostředky infrastruktury, takže týmy mohou pomocí přístup založený na mikroslužbách řešení obchodních problémů.***

Service Fabric umožňuje vytvářet aplikace, které používají přístup založený na mikroslužbách tím, že poskytuje následující operace:

* Platforma, která poskytuje systémové služby pro nasazení, upgrade, zjišťovat a restartujte služby se nezdařilo, zjišťovat služby, směrování zpráv, Správa stavu a monitorovat stav.
* Možnost nasazovat aplikace buď spuštěný v kontejnerech nebo jako procesy. Service Fabric je kontejner a procesu orchestrator.
* Produktivní programovací rozhraní API můžete vytvářet aplikace jako mikroslužeb: [ASP.NET Core a Reliable Actors, Reliable Services](service-fabric-choose-framework.md). Například můžete získat informace o stavu a diagnostické nástroje, nebo můžete využít integrovanou vysokou dostupnost.

***Service Fabric je nezávislá, o jak vytvářet služby, a můžete použít libovolné technologii. Ale nabízí integrovanou programovací rozhraní API, která usnadňují vytváření mikroslužeb.***

### <a name="migrating-existing-applications-to-service-fabric"></a>Migraci stávajících aplikací do Service Fabric

Service Fabric umožňuje znovu použít existující kód a modernizujte pomocí nové mikroslužby. Existuje pět fází k modernizaci aplikací, a můžete spustit a zastavit v jakékoli fázi. Stavy jsou:

1) Začněte s tradiční monolitické aplikace.  
2) Migrace. K hostování existující kód ve službě Service Fabric použít kontejnerech nebo spustitelných souborech hostů.  
3) Modernizujte. Přidáte nové mikroslužby souběžně s existující kontejnerizovaných kód.  
4) Inovujte. Rozdělte monolitické aplikace do mikroslužeb podle potřeb.  
5) Transformujte aplikace do mikroslužeb. Transformace existujících monolitických aplikací nebo vytvářet nové aplikace úplně nové.

![Migrace do mikroslužeb][Image3]

Mějte na paměti, můžete *spouštět a zastavovat na libovolné z těchto fází*. Není nutné mohla pokračovat do další fáze. 

Podívejme se na příklady pro každý z těchto fází.

**Migrace**  
Mnoho společností dvou důvodů migrujete existující monolitické aplikace do kontejnerů:

* Snížení nákladů, z důvodu konsolidace a odebrání stávající hardware nebo souvislosti se spuštěnými aplikacemi v s vyšší hustotou.
* Konzistentní nasazování kontrakt mezi vývojem a provozem.

Snížení nákladů jsou jednoduché. V Microsoftu mnoho existujících aplikací jsou právě kontejnerizovaná, což vede k miliony dolarů úspory. Konzistentní nasazení je obtížnější k vyhodnocení, ale stejně důležité. Znamená to, že můžou vývojáři zvolit technologie, které jim vyhovují, ale operace bude přijímat pouze jedinou metodu pro nasazení a správu aplikací. Zmírňuje operací z museli potýkat se složitost podporu různých technologií bez vynucení vývojářům vybrat jenom některé z nich. Každá aplikace je v podstatě kontejnerizovaných do bitové kopie samostatná nasazení.

Mnoho organizací ukončit. Už mají výhody kontejnerů a poskytuje prostředí pro kompletní správu včetně nasazení, upgrady, správu verzí, vrácení zpět a monitorování stavu Service Fabric.

**Modernizujte**  
Modernizace, je přidání nových služeb společně s existující kontejnerizovaných kód. Pokud se chystáte zadat nový kód, je nejlepší postupujte po menších krůčcích rozhodli mikroslužeb. To může znamenat přidání nového koncového bodu rozhraní REST API nebo nové obchodní logiku. Tímto způsobem zahájíte proces vytváření nových mikroslužeb a postupů vývoje a jejich nasazování.

**Inovace**  
Přístup založený na mikroslužbách obsáhne měnícím se potřebám organizace. V této fázi musíte se rozhodnout, jestli se má spustit rozdělení monolitické aplikace do služby nebo inovace. Klasickým příkladem je, když se stane kritickým bodem zpracování databáze, který používáte jako do fronty pracovního postupu. Jako počet pracovního postupu žádostí o zvýšení, práci potřebuje pro škálování. Využijte tuto konkrétní aplikace, která není škálování nebo, který musí být možné aktualizovat častěji a rozdělit ho jako mikroslužby a inovace.

**Transformovat aplikace do mikroslužeb**  
V této fázi je vaše aplikace plně skládá z (nebo rozdělit do) mikroslužeb. K dosažení tohoto bodu, provedli cesty mikroslužeb. Začněte tady, ale k tomu bez založený na mikroslužbách platformy můžete navíc vyžadovala významnou investici.

### <a name="are-microservices-right-for-my-application"></a>Jsou to mikroslužby pro mé aplikace?

Podle potřeby. V Microsoftu jako začal více týmů pro vývoj pro cloud pro podnik, mnoho z nich realizované výhody díky přístupu jako mikroslužeb. Bing, například používá mikroslužeb let. Pro jiné týmy se nový přístup založený mikroslužbách. Týmy zjištěno, že existuje bylo těžké problémy vyřešit mimo jejich klíčové oblasti síly. To je důvod, proč Service Fabric získala výrazně nabývá na dynamice technologií pro vytváření služeb.

Cílem Service Fabric je ke snížení složitosti vytváření vyladěných aplikací mikroslužeb, takže není nutné kvůli tomu provádět tolik nákladné pracovali. Začněte v malém, škálovat v případě potřeby, přestat používat služby, přidat nové a budou vyvíjet současně s využití ze strany zákazníků. Víme také, že existují mnoho problémů ještě mají být vyřešeny, chcete-li více přístupné mikroslužeb pro většinu vývojářů. Kontejnery a programovací model objektu actor jsou příklady malých kroků v tomto směru. Máme jistotu, že budou vznikat další inovace snazší přístup založený na mikroslužbách.


## <a name="next-steps"></a>Další postup

* [Mikroslužby: Revoluci aplikací založené na cloudu](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)
* [Azure Architecture Center: Vytváření mikroslužeb v Azure](https://docs.microsoft.com/azure/architecture/microservices/)
* [Osvědčené postupy Azure aplikace Service Fabric a clusteru](service-fabric-best-practices-overview.md)
* [Přehled terminologie Service Fabric](service-fabric-technical-overview.md)

[Image1]: media/service-fabric-overview-microservices/monolithic-vs-micro.png
[Image2]: media/service-fabric-overview-microservices/statemonolithic-vs-micro.png
[Image3]: media/service-fabric-overview-microservices/microservices-migration.png
