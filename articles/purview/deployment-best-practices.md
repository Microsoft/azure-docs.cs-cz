---
title: Osvědčené postupy pro nasazení
description: Tento článek poskytuje osvědčené postupy pro nasazení služby Azure dosah. Azure dosah umožňuje každému uživateli registrovat, zjišťovat, pochopit a využívat zdroje dat.
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: 1b2841f69ebe91dac748a4b2e24dc0c33756b1da
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400688"
---
# <a name="azure-purview-deployment-best-practices"></a>Osvědčené postupy pro nasazení Azure dosah

Tento článek popisuje běžné úlohy, které vám pomůžou nasadit dosah do produkčního prostředí. Tyto úlohy mohou být dokončeny ve fázích, a to v průběhu měsíce nebo více. I organizace, které už mají nasazené dosah, můžou Tento průvodce použít k zajištění toho, aby využívali své investice na maximum.

Dobře plánované nasazení platformy pro řízení dat (například Azure dosah) může mít následující výhody:

- Lepší zjišťování dat
- Vylepšená analytická spolupráce
- Maximalizace návratnosti investic.

## <a name="prerequisites"></a>Předpoklady

* Přístup k Microsoft Azure pomocí vývojového nebo produkčního předplatného
* Možnost vytvářet prostředky Azure, včetně dosah
* Přístup ke zdrojům dat, jako je Azure Data Lake Storage nebo Azure SQL, v testovacích, vývojových nebo produkčních prostředích
  * Pro Data Lake Storage je požadovaná role ke kontrole čtenář.
  * Pro SQL musí být identita schopná dotazovat se na tabulky pro vzorkování klasifikací.
* Přístup k Azure Security Center nebo schopnost spolupracovat se správcem Security Center pro označování dat

## <a name="identify-objectives-and-goals"></a>Určení cílů a cílů

Mnoho organizací zahájilo svou cestu zásad správného řízení dat vývojem jednotlivých řešení, která se týkají konkrétních požadavků izolované skupiny a datových domén v celé organizaci. I když se prostředí můžou lišit v závislosti na odvětví, produktech a jazykové verzi, většina organizací bude obtížně udržovat konzistentní ovládací prvky a zásady pro tyto typy řešení.

Mezi běžné cíle zásad správného řízení dat, které byste mohli chtít v počátečních fázích identifikovat, patří:

* Maximalizace obchodní hodnoty vašich dat
* Povolení datové kultury, kde příjemci dat můžou snadno najít, interpretovat a důvěřovat datům
* Zvýšení spolupráce mezi různými obchodními jednotkami a poskytování konzistentního prostředí dat
* Podpora inovací díky urychlení analýz dat, aby se těžit výhody cloudu
* Zkrácení doby pro zjišťování dat prostřednictvím samoobslužných možností pro různé skupiny dovedností
* Omezení doby uvedení na trh pro doručování analytických řešení, která zlepšují službu svým zákazníkům
* Omezení provozních rizik způsobených používáním nástrojů specifických pro doménu a nepodporovanou technologií

Obecným přístupem je rozdělení těchto cílů do různých kategorií a cílů. Tady je několik příkladů:

|Kategorie|Cíl|
|---------|---------|
|Zjišťování|Uživatelé s oprávněním správce by měli být schopni kontrolovat zdroje dat v Azure a mimo Azure (včetně místních zdrojů) a automaticky tak shromažďovat informace o datových prostředcích.|
|Classification|Platforma by měla automaticky klasifikovat data na základě vzorkování dat a umožnit ruční přepsání pomocí vlastních klasifikací.|
|Consumption|Obchodní uživatelé by měli být schopni najít informace o jednotlivých prostředcích pro obchodní i technická metadata.|
|Lineage|Každý Asset musí zobrazit grafické zobrazení podkladových datových sad, aby uživatelé pochopili původní zdroje a jaké změny byly provedeny.|
|Spolupráce|Platforma musí uživatelům umožňovat spolupráci tím, že poskytuje další informace o jednotlivých datových prostředcích.|
|Generování sestav|Uživatelé musí být schopni zobrazit vykazování na datovém majetku, včetně citlivých dat a dat, která vyžadují další obohacení.|
|Zásady správného řízení dat|Platforma musí správci umožňovat definovat zásady pro řízení přístupu a automaticky vymáhat přístup k datům na základě jednotlivých uživatelů.|
|Pracovní postup|Platforma musí mít možnost vytvářet a upravovat pracovní postup, aby bylo možné snadno škálovat a automatizovat různé úlohy v rámci platformy.|
|Integrace|Další technologie jiných výrobců, jako je vytváření lístků nebo orchestrace, musí být schopné integrovat do platformy prostřednictvím skriptu nebo rozhraní REST API.|

## <a name="top-questions-to-ask"></a>Nejčastější dotazy k dotazování

Jakmile vaše organizace souhlasí s cíli a cíli na vysoké úrovni, bude mnoho dotazů z více skupin. Je důležité shromáždit tyto otázky, aby bylo možné vytvořit plán pro řešení všech obav. Některé příklady otázek, které můžete během úvodní fáze spustit:

1. Jaké jsou hlavní zdroje dat organizace a datové systémy?
2. U zdrojů dat, které dosah ještě nepodporuje, máte k dispozici možnosti?
3. Kolik instancí dosah potřebujeme?
4. Kdo jsou uživatelé?
5. Kdo může kontrolovat nové zdroje dat?
6. Kdo může upravovat obsah v rámci dosah?
7. Jaký proces můžu použít ke zlepšení kvality dat v dosah?
8. Jak nastavovat platformu pomocí stávajících důležitých prostředků, glosářových termínů a kontaktů?
9. Jak integrovat se stávajícími systémy?
10. Jak získat zpětnou vazbu a vytvořit udržitelný proces?

I když možná nemáte přímo odpověď na většinu těchto otázek, může vaše organizace povýšit tento projekt na rámec a zajistit, aby bylo možné splnit všechny požadavky "musí být".

## <a name="include-the-right-stakeholders"></a>Zahrnutí správných zúčastněných stran

Abychom zajistili úspěch s implementací dosah pro celý podnik, je důležité, abyste zahrnovali správné účastníky. V první fázi se účastní jenom několik lidí. Nicméně při rozšíření oboru budete potřebovat další osoby k přispívání do projektu a poskytnutí zpětné vazby.

Mezi klíčové zúčastněné strany, které byste mohli chtít zahrnout:

|Nežádoucí|Role|
|---------|---------|
|**Ředitel pro data**|Rozhraní CDO se dohlíží na řadu funkcí, které mohou zahrnovat správu dat, kvalitu dat, správu hlavních dat, datové vědy, business intelligence a vytváření strategie pro data. Můžou být sponzorem projektu implementace dosah.|
|**Vlastník domény/podniku**|Obchodní osoba, která ovlivňuje využití nástrojů a má kontrolu rozpočtu|
|**Datový analytik**|Umožňuje vytvořit snímek podnikového problému a analyzovat data, abychom mohli dělat podniková rozhodnutí.|
|**Architekt dat**|Navrhněte databáze pro klíčové podnikové aplikace společně s návrhem a implementací zabezpečení dat.|
|**Odborník na data**|Provoz a údržba datových zásobníků, vyžádání dat z různých zdrojů, integrace a Příprava dat, nastavení datových kanálů|
|**Odborník na datové vědy**|Sestavování analytických modelů a nastavení datových produktů, pro které budou mít k dispozici rozhraní API|
|**Správce databáze**|Vlastní, sledovat a řešit incidenty a požadavky související s databází v rámci smluv o úrovni služeb (SLA); Může nastavit datové kanály|
|**DevOps**|Vývoj a implementace obchodních aplikací; může zahrnovat psaní skriptů a možností orchestrace.|
|**Specialista na zabezpečení dat**|Posouzení celkové bezpečnosti sítě a dat, která zahrnuje data přicházející z dosah a z nich|

## <a name="identify-key-scenarios"></a>Identifikujte klíčové scénáře

Dosah se dá použít k centrální správě zásad správného řízení dat napříč daty organizace, které pokrývá Cloud a místní prostředí. Chcete-li mít úspěšnou implementaci, je nutné identifikovat klíčové scénáře, které jsou pro firmu zásadní. Mezi tyto scénáře patří hranice obchodní jednotky nebo dopad na více uživatelů, ať už osoby, nebo pro odesílání dat.

Tyto scénáře lze zapsat různými způsoby, ale měli byste zahrnout alespoň tyto pět dimenzí:

1. Uživatel – kdo jsou vaši uživatelé?
2. Zdrojový systém – Jaké jsou zdroje dat, například Azure Data Lake Storage Gen2 nebo Azure SQL Database?
3. Oblast dopadu – jaká je kategorie tohoto scénáře?
4. Scénáře s podrobnostmi – způsob, jakým uživatelé používají dosah k řešení problémů?
5. Očekával se výsledek – jaká jsou kritéria úspěchu?

Scénáře musí být specifické, napadnutelné a spustitelné soubory s měřitelnými výsledky. Příklady scénářů, které můžete použít:

|Scénář|Podrobnosti|Nežádoucí|
|---------|---------|---------|
|Klíčové materiály pro obchodní účely katalogu|Potřebuji mít informace o každé sadě dat, abyste měli dobré znalosti o tom, co je. Tento scénář zahrnuje data z obchodních i technických metadat o datových sad v katalogu. Zdroje dat zahrnují Azure Data Lake Storage Gen2, Azure synapse DW a/nebo Power BI. Tento scénář zahrnuje i místní prostředky, jako je SQL Server.|Obchodní analytik, datový vědecký Specialist, data inženýr|
|Zjištění důležitých podnikových prostředků|Potřebuji vyhledávací modul, který může prohledávat všechna metadata v katalogu. Mělo by být schopné hledat pomocí jednoduchého nebo složitého zástupného termínu použití zástupných znaků.|Obchodní analytik, datový vědecký Specialist, data inženýr, správce dat|
|Sledování dat a pochopení jejich původu a řešení potíží s daty|Potřebuji mít datovou čáru ke sledování dat v sestavách, předpovědi nebo modelech zpátky do původního zdroje a pochopení změn a umístění dat, která se nacházejí v průběhu životního cyklu dat. Tento scénář musí podporovat datové kanály s upřednostněním Azure Data Factory a datacihly.|Data inženýr, vědecký Specialist|
|Obohacení metadat v kritických datových prostředcích|Potřebuji rozšířit datovou sadu v katalogu s technickými metadaty, která se generují automaticky. Mezi příklady patří klasifikace a označování.|Data inženýr, doména/vlastník firmy|
|Řízení datových assetů pomocí uživatelsky přívětivého uživatelského rozhraní|Potřebuji mít obchodní Glosář pro konkrétní obchodní metadata. Obchodní uživatelé mohou používat dosah pro samoobslužné scénáře k přidávání poznámek ke svým datům a umožňují snadné zjištění dat pomocí vyhledávání.|Doména/vlastník firmy, obchodní analytik, datový vědecký Specialist, data inženýr|

## <a name="deployment-models"></a>Modely nasazení

Pokud máte jenom jednu malou skupinu s použitím dosah se základními případy použití, může být přístup stejně jednoduchý jako jeden dosah instance pro celou skupinu. Můžete ale také zajímat, jestli vaše organizace potřebuje víc než jednu instanci dosah. A pokud používáte více instancí dosah, jak mohou zaměstnanci zvýšit úroveň prostředků z jedné fáze na jinou.

### <a name="determine-the-number-of-purview-instances"></a>Určení počtu instancí dosah

Ve většině případů by měl být pro celou organizaci jenom jeden účet dosah. Tento přístup přijímá maximální výhody "vlivů na síť", kde se hodnota platformy zvyšuje exponenciálně jako funkce dat, která se nachází v rámci platformy.

Existují však výjimky tohoto vzoru:

1. **Testování nových konfigurací** – organizace můžou chtít vytvořit víc instancí pro testování konfigurací skenování nebo klasifikací v izolovaných prostředích. I když je v některých oblastech platformy, jako je například Glosář, funkce správy verzí, bylo by snazší mít k dispozici instanci "na jedno straně", která by se mohla volně testovat.
2. **Oddělení testů, předprodukčních a produkčních verzí** – organizace chtějí vytvářet různé platformy pro různé druhy dat uložených v různých prostředích. Nedoporučuje se, protože tyto typy dat jsou různé typy obsahu. Můžete použít Glosář termínu na nejvyšší úrovni hierarchie nebo kategorii a oddělit typy obsahu.
3. **Konglomeráty a federované modely** – konglomeráty mají často spoustu obchodních jednotek (BUs), které pracují samostatně, a v některých případech dokonce ani vzájemně nesdílejí fakturaci. V těchto případech bude organizace ukončit vytváření instance dosah pro každou BU. Tento model není ideální, ale může být nutný, obzvláště proto, že sběrnice často není ochotná ke sdílení fakturace.
4. **Dodržování předpisů** – existují některé přísné režimy dodržování předpisů, které považují i metadata za citlivá a vyžadují, aby byla v konkrétní zeměpisné oblasti. Pokud má společnost více geografických oblastí, jediné řešení má mít několik instancí dosah, jednu pro každou zeměpisnou oblast.

### <a name="create-a-process-to-move-to-production"></a>Vytvoření procesu pro přesun do produkčního prostředí

Některé organizace se můžou rozhodnout, že budou pracovat s jednou produkční verzí dosah. Pravděpodobně nepotřebují jít nad rámec zjišťování, hledání a procházení. Pokud některé prostředky mají nesprávné glosářové výrazy, je poměrně striktní tak, aby umožňovala automatickým opravám lidí. Většina organizací, které chtějí nasazovat dosah napříč různými obchodními jednotkami, ale budou chtít mít nějakou formu procesu a řízení.

Dalším důležitým aspektem, který se má zahrnout do vašeho produkčního procesu, je způsob, jakým se dají migrovat klasifikace a popisky. Dosah má více než 90 systémových klasifikátorů. Můžete použít systémové nebo vlastní klasifikace pro prostředky soubor, tabulka nebo sloupec. Klasifikace jsou například značky předmětu a slouží k označení a identifikaci obsahu určitého typu nalezeného v rámci vašich datových nemovitostí během kontroly. Popisky citlivosti slouží k identifikaci kategorií typů klasifikace v rámci dat vaší organizace a pak Seskupte zásady, které chcete použít u jednotlivých kategorií. Využívá stejné typy citlivých informací jako Microsoft 365, což vám umožňuje roztáhnout stávající zásady zabezpečení a ochranu mezi celý obsah a datovou podstatu. Může prohledávat a automaticky klasifikovat dokumenty. Například pokud máte soubor s názvem multiple.docx a v jeho obsahu je číslo národního ID, dosah na stránce s podrobnostmi o Assetu přidá klasifikaci, jako je národní identifikační číslo EU.

V dosah existuje několik oblastí, ve kterých správci katalogu potřebují zajistit osvědčené postupy konzistence a údržby v průběhu svého životního cyklu:

* **Datové assety** – zdroje dat bude nutné prohledat v různých prostředích. Nedoporučuje se kontrolovat jenom vývoj a pak je znovu vygenerovat pomocí rozhraní API v produkčním prostředí. Hlavním důvodem je, že dosah skenery na pozadí v datových prostředcích zvládnou více kabelů, což může být složité ho přesunout do jiné instance dosah. Stačí přidat stejný zdroj dat do produkčního prostředí a znovu zkontrolovat zdroje. Obecný osvědčený postup je mít dokumentaci ke všem kontrolám, připojením a používaným ověřovacím mechanismům.
* **Prohledat sady pravidel** – jedná se o kolekci pravidel přiřazených ke konkrétní kontrole, jako je například typ a klasifikace souborů k detekci. Pokud toto množství sad pravidel skenování nemáte, je možné je znovu vytvořit znovu prostřednictvím produkčního prostředí. To bude vyžadovat interní proces a dobrou dokumentaci. Pokud se však pravidla změní na každý den nebo každý týden, může to být řešeno zkoumáním REST API trasy.
* **Vlastní klasifikace** – vaše klasifikace se nemusí v pravidelných intervalech také měnit. Během prvotní fáze nasazení může trvat nějakou dobu, než se pochopí různé požadavky, aby se mohly pracovat s vlastními klasifikacemi. Po tom, co se vyřeší, to však bude vyžadovat jen malou změnu. Proto tady doporučujeme ručně migrovat libovolné vlastní klasifikace nebo použít REST API.
* **Glosář** – je možné exportovat a importovat terminologické výrazy prostřednictvím uživatelského rozhraní. V případě scénářů automatizace můžete také použít REST API.
* **Zásady vzoru sady prostředků** – Tato funkce je velmi pokročilá pro všechny typické organizace, které je potřeba použít. V některých případech má vaše Azure Data Lake Storage konvence pojmenovávání složek a specifickou strukturu, která může způsobit problémy, které dosah vygeneruje sadu prostředků. Vaše obchodní jednotka také může chtít změnit konstrukci sady prostředků s dalšími přizpůsobeními, aby odpovídala obchodním potřebám. V tomto scénáři je nejlepší sledovat všechny změny přes REST API a zdokumentovat změny prostřednictvím platformy externích verzí.
* **Přiřazení role** – Toto je místo, kde řídíte, kdo má přístup k dosah a jaká oprávnění mají. Dosah má taky REST API k podpoře exportu a importu uživatelů a rolí, ale toto není kompatibilní s rozhraním API pro Atlas. Doporučení je přiřadit skupinu zabezpečení Azure a místo toho spravovat členství ve skupině.

### <a name="plan-and-implement-different-integration-points-with-purview"></a>Plánování a implementace různých integračních bodů pomocí dosah

Je pravděpodobný, že vyspělá organizace už má existující katalog dat. Klíčovou otázkou je, jestli se má nadále používat stávající technologie a synchronizovat s dosah. Dosah umožňuje publikovat informace prostřednictvím rozhraní API pro Atlas, ale ve skutečnosti nejsou určené pro podporu tohoto druhu scénáře. Některé organizace se můžou zpočátku rozhodnout, že budou používat dosah, a to migrací na stávající datové prostředky z jiných řešení Data Catalog. Dá se to udělat prostřednictvím rozhraní API pro Atlas jako jednosměrný přístup. K synchronizaci mezi různými technologiemi katalogu by se nemělo brát v úvahu v dlouhodobém designu. K tomu obvykle dochází v případě, že každá organizační jednotka může nadále používat stávající řešení pro starší datové prostředky, zatímco dosah se používá k prohledávání s novějšími zdroji dat.

Pro jiné scénáře integrace, jako je vytváření lístků, vlastní uživatelské rozhraní a orchestrace, můžete použít rozhraní API pro Atlas a koncové body Kafka. Obecně existují čtyři integrační body s dosah:

* **Datový Asset** – umožňuje dosah vyhledat prostředky v úložišti, aby bylo možné vytvořit výčet toho, jak jsou tyto prostředky a shromažďovat všechna snadno dostupná metadata. Takže pro SQL to může být seznam databáze, tabulek, uložených procedur, zobrazení a konfiguračních dat, která se uchovávají na místech, jako je `sys.tables` . Pro něco podobného jako Azure Data Factory (ADF) by se mohlo zobrazit výčet všech kanálů a získání dat při jejich vytvoření, posledního spuštění, aktuální stav.
* Vydaná **čára** – umožňuje dosah shromažďovat informace z systému analýzy/data mutací, jak se data pohybují. Něco jako Spark může být shromažďování informací z spuštění poznámkového bloku, aby se zobrazila data, která Poznámkový blok ingestoval, jak ho transformoval a kde ho vyhodnotil. Pro něco, co jako SQL, je možné analyzovat protokoly dotazů a provést zpětnou analýzu, které operace mutace byly provedeny a k čemu došlo. V závislosti na potřebách podporujeme zápis na základě nabízených oznámení i na vyžádání.
* **Klasifikace** – díky tomu mohou dosah přebírat fyzické vzorky ze zdrojů dat a spouštět je prostřednictvím našeho klasifikačního systému. Klasifikační systém vyhodnotí sémantiku částí dat. Můžeme například zjistit, že soubor je soubor Parquet a má tři sloupce a třetí druhý je řetězec. Klasifikátory, které na ukázkách spouštíme, nám sdělí, že řetězec je jméno, adresa nebo telefonní číslo. Rozsvícení tohoto integračního bodu znamená, že jsme definovali, jak může dosah otevřít objekty, jako jsou poznámkové bloky, kanály, soubory Parquet, tabulky a kontejnery.
* **Integrované prostředí** – produkty, které mají zkušenosti, jako je například ADF, synapse, SQL Studio, PBI a Dynamics), obvykle chtějí uživatelům umožnit zjišťování dat, se kterými chtějí pracovat, a také najít místa pro výstupní data. Katalog dosah vám může přispět k urychlení těchto prostředí tím, že poskytuje prostředí pro vkládání. K tomuto prostředí může dojít v rozhraní API nebo na úrovni uživatelského rozhraní na možnosti partnera. Když zaškrtnete volání dosah, může organizace využít mapu prvků dosah na datové assety a najít datové assety. Projděte si informace o prostředcích, kontrolách schémat, Prohlédněte si hodnocení, kontakty atd.

## <a name="phase-1-pilot"></a>Fáze 1: pilotní nasazení

V této fázi je třeba vytvořit dosah a nakonfigurovat pro velmi malou skupinu uživatelů. Obvykle se jedná o skupinu 2-3 lidí, které pracují dohromady, aby běžely prostřednictvím koncových scénářů. Považují se za poradce dosah ve své organizaci. Hlavním cílem této fáze je zajistit, aby bylo možné splnit klíčové funkce a zda jsou o projektu informováni přímo zúčastněné strany.

### <a name="tasks-to-complete"></a>Úkoly k dokončení

|Úloha|Podrobnosti|Doba trvání|
|---------|---------|---------|
|Shromáždění & souhlasit s požadavky|Diskuze se všemi zúčastněnými stranami a Shromážděte kompletní sadu požadavků. Různé osoby se musí účastnit souhlasu s podmnožinou požadavků, které je potřeba dokončit pro každou fázi projektu.|1 týden|
|Nastavení startovní sady|Projděte si [dosah rychlé zprovoznění](create-catalog-portal.md) a nastavte [dosah Starter Kit](tutorial-scan-data.md) pro demo dosah všem zúčastněným stranám.|1 den|
|Navigace v dosah|Naučte se používat dosah z domovské stránky.|1 den|
|Konfigurace ADF pro vydaný řádek|Identifikujte klíčové kanály a datové assety. Shromážděte všechny informace potřebné pro připojení k internímu účtu ADF.|1 den|
|Naskenujte zdroj dat, například Azure Data Lake Storage|Přidejte zdroj dat a nastavte kontrolu. Ujistěte se, že kontrola úspěšně detekuje všechny prostředky.|2 den|
|Hledat a procházet|Umožněte koncovým uživatelům přístup k dosah a provádět ucelené scénáře hledání a procházení.|1 den|

### <a name="acceptance-criteria"></a>Kritéria přijetí

* Účet dosah se úspěšně vytvořil v předplatném organizace v rámci tenanta organizace.
* K dosah může přistupovat malá skupina uživatelů s více rolemi.
* Dosah je nakonfigurován tak, aby kontroloval alespoň jeden zdroj dat.
* Uživatelé by měli být schopni extrahovat klíčové hodnoty dosah, například:
  * Hledat a procházet
  * Lineage
* Uživatelé by měli být schopni přiřadit vlastnictví prostředků na stránce assetů.
* Prezentace a ukázka pro zvýšení povědomí o klíčových zúčastněných osobách
* Kupte si ze správy, abyste schválili další zdroje pro fázi MVP.

## <a name="phase-2-minimum-viable-product"></a>Fáze 2: minimální životaschopný produkt

Až budete mít dohodnuté požadavky a podílované obchodní jednotky na dosah, bude dalším krokem práce s minimální verzí životaschopného produktu (MVP). V této fázi rozšíříte využití dosah pro více uživatelů, kteří budou mít další potřeby vodorovně a svisle. K dispozici jsou klíčové scénáře, které je potřeba splnit vodorovně pro všechny uživatele, jako jsou glosářové podmínky, hledání a procházení. U každé obchodní jednotky nebo skupiny se budou pokrývat také podrobné požadavky, které pokrývají konkrétní ucelené scénáře, jako je například Azure Data Lake Storage do služby Azure synapse DW Power BI.

### <a name="tasks-to-complete"></a>Úkoly k dokončení

|Úloha|Podrobnosti|Doba trvání|
|---------|---------|---------|
|[Kontrola Azure synapse Analytics](register-scan-azure-synapse-analytics.md)|Zprovoznění zdrojů databáze a jejich kontrola, aby bylo možné naplnit klíčové prostředky|2 dny|
|[Vytváření vlastních klasifikací a pravidel](create-a-custom-classification-and-classification-rule.md)|Po prohledání vašich prostředků si uživatelé můžou uvědomit, že další případy použití pro další klasifikaci vedle výchozích klasifikací z dosah jsou další.|2-4 týdnů|
|[Kontrola Power BI](register-scan-power-bi-tenant.md)|Pokud vaše organizace používá Power BI, můžete Power BI skenovat za účelem shromáždění všech datových assetů, které používají odborníci přes data nebo analytiky dat, které mají požadavky na zahrnutí řádků z vrstvy úložiště.|1-2 týdnů|
|[Importovat terminologie glosáře](how-to-create-import-export-glossary.md)|Ve většině případů je možné, že vaše organizace už vyvíjí shromažďování termínů glosáře a přiřazení termínů k assetům. Tato operace bude vyžadovat import do dosah prostřednictvím souboru. csv.|1 týden|
|Přidávání kontaktů do assetů|U nejdůležitějších prostředků může být vhodné vytvořit proces, který umožňuje jiným osoby přiřadit kontakty nebo importovat pomocí rozhraní REST API.|1 týden|
|Přidání citlivých popisků a skenování|To může být pro některé organizace nepovinné v závislosti na použití popisků z M365.|1-2 týdnů|
|Získání klasifikace a citlivých přehledů|Pro vytváření sestav a přehled v dosah můžete získat přístup k této funkci, abyste získali různé sestavy a poskytovali prezentaci pro správu.|1 den|
|Přidávání uživatelů do zprovoznění pomocí dosah spravovaných uživateli|Tento krok bude vyžadovat, aby správce dosah spolupracoval se správcem Azure Active Directory a navázal nové skupiny zabezpečení, které budou udělovat přístup k dosah.|1 týden|

### <a name="acceptance-criteria"></a>Kritéria přijetí

* Úspěšně připojit větší skupinu uživatelů k dosah (50 +)
* Kontrola důležitých podnikových zdrojů dat
* Import a přiřazení všech kritických pojmů glosáře
* Test důležitých popisků pro klíčové prostředky je úspěšný
* Úspěšné splnění minimálních scénářů pro uživatele zapojených obchodních jednotek

## <a name="phase-3-pre-production"></a>Fáze 3: předprodukční verze

Po úspěšné fázi MVP je čas naplánovat předprodukční milník. Vaše organizace se může rozhodnout, že bude mít samostatnou instanci dosah pro předprodukční a produkční prostředí, nebo zachovat stejnou instanci, ale omezit přístup. V této fázi je také vhodné zahrnout kontrolu místních zdrojů dat, například SQL Server. Pokud je ve zdrojích dat nějaká mezera, kterou dosah nepodporuje, je čas prozkoumat rozhraní API pro Atlas, abyste porozuměli dalším možnostem.

### <a name="tasks-to-complete"></a>Úkoly k dokončení

|Úloha|Podrobnosti|Doba trvání|
|---------|---------|---------|
|Zpřesnit kontrolu pomocí sady pravidel skenování|Vaše organizace bude mít spoustu zdrojů dat pro předprodukční prostředí. Je důležité předem definovat klíčová kritéria pro kontrolu, aby se klasifikace a přípony souborů mohly použít konzistentně napříč panelem.|1-2 dní|
|Vyhodnotit dostupnost oblasti pro kontrolu|V závislosti na oblasti zdrojů dat a požadavcích organizace na dodržování předpisů a zabezpečení je vhodné zvážit, jaké oblasti musí být k dispozici ke skenování.|1 den|
|Pochopení konceptu brány firewall při kontrole|Tento krok vyžaduje určitý průzkum toho, jak organizace konfiguruje bránu firewall a jak se může dosah sám ověřit pro přístup ke zdrojům dat ke skenování.|1 den|
|Pochopení konceptu privátního propojení při kontrole|Pokud vaše organizace používá privátní odkaz, je nutné v rámci požadavků rozvrhnout základ zabezpečení sítě tak, aby zahrnoval privátní propojení.|1 den|
|[Kontrolovat místní SQL Server](register-scan-on-premises-sql-server.md)|To je volitelné, pokud máte místní SQL Server. Kontrola bude vyžadovat nastavení [Integration runtime](manage-integration-runtimes.md) v místním prostředí a přidání SQL Server jako zdroje dat.|1-2 týdnů|
|Použití dosah REST API pro scénáře integrace|Pokud máte požadavky na integraci dosah s jinými technologiemi jiných výrobců, jako je orchestrace nebo systém lístků, možná budete chtít prozkoumat oblast REST API.|1-4 týdnů|
|Pochopení cen dosah|Tento krok poskytne organizaci důležité finanční informace, které je potřeba udělat.|1-5 dní|

### <a name="acceptance-criteria"></a>Kritéria přijetí

* Úspěšně provedla se aspoň jedna organizační jednotka se všemi uživateli.
* Kontrolovat místní zdroj dat, například SQL Server
* OVĚŘENÍ alespoň jednoho scénáře integrace pomocí REST API
* Dokončete plán, který by měl přejít do produkčního prostředí, které by mělo zahrnovat klíčové oblasti pro infrastrukturu a zabezpečení.

## <a name="phase-4-production"></a>Fáze 4: výroba

Výše uvedené fáze by měly následovat za účelem vytvoření efektivního řízení informací, což je základem pro lepší programy zásad správného řízení. Řízení dat pomůže vaší organizaci připravit se na rostoucí trendy, jako jsou AI, Hadoop, IoT a blockchain. Je to jenom začátek velkého množství dat a analýz a existuje spousta dalších věcí, které je možné probrat. Výsledek tohoto řešení by poskytoval:

* **Zaměření na firmu** – řešení, které je zarovnané na obchodní požadavky a scénáře nad technickými požadavky.
* V **budoucnu připravené** – řešení maximalizuje výchozí funkce platformy a použije standardizované oborové postupy pro aktivity konfigurace nebo skriptování, aby podporovaly budoucí využití a vývoj platformy.

### <a name="tasks-to-complete"></a>Úkoly k dokončení

|Úloha|Podrobnosti|Doba trvání|
|---------|---------|---------|
|Kontrola zdrojů produkčních dat s povolenou bránou firewall|Pokud je to volitelné, pokud je brána firewall zavedena, ale je důležité prozkoumat možnosti pro posílení vaší infrastruktury.|1-5 dní|
|Povolit privátní propojení|Pokud je to volitelné při použití privátního odkazu. V opačném případě můžete tuto hodnotu přeskočit, protože je to kritérium typu "musí být", pokud je povoleno soukromé.|1-5 dní|
|Vytvořit automatizovaný pracovní postup|Pracovní postup je důležitý pro automatizaci procesů, jako je schvalování, eskalace, kontrola a správa problémů.|2-3 týdnů|
|Dokumentace k operaci|Řízení dat není jednorázovým projektem. Jedná se o průběžný program pro účely rozhodování a vytváření příležitostí pro firmy na základě dat. Je důležité dokumentovat klíčovou proceduru a obchodní standardy.|1 týden|

### <a name="acceptance-criteria"></a>Kritéria přijetí

* Úspěšná zprovoznění všech obchodních jednotek a jejich uživatelů
* Úspěšné splnění infrastruktury a požadavků na zabezpečení pro produkční prostředí
* Úspěšné splnění všech případů použití vyžadovaných uživateli

## <a name="platform-hardening"></a>Posílení zabezpečení platformy

Je možné provést další kroky posílení zabezpečení:

* Zvýšení zabezpečení stav povolením kontroly prostředků brány firewall nebo použitím privátního odkazu
* Doladit kontrolu rozsahu pro zlepšení výkonu kontroly
* Použití rozhraní REST API k exportu důležitých metadat a vlastností pro zálohování a obnovení
* Použití pracovního postupu k automatizaci lístků a událostí, aby nedocházelo k lidským chybám

## <a name="next-steps"></a>Další kroky

- [Kurz: spuštění Starter Kit a kontrola dat](tutorial-scan-data.md)
- [Kurz: navigace na domovské stránce a vyhledání assetu](tutorial-asset-search.md)