---
title: Běžné případy použití a scénáře pro službu Azure Cosmos DB
description: 'Další informace o nejvyšší pět případy použití pro službu Azure Cosmos DB: uživatel vygeneroval obsah, protokolování událostí, data katalogu, data předvolby uživatele a Internetu věcí (IoT).'
services: cosmos-db
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 05/07/2017
ms.openlocfilehash: c93b53a78891eaf46e5455bd13198f7b9bd90903
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2018
ms.locfileid: "53808004"
---
# <a name="common-azure-cosmos-db-use-cases"></a>Běžné případy použití služby Azure Cosmos DB
Tento článek poskytuje přehled o několik běžných případů použití pro službu Azure Cosmos DB.  Doporučení v tomto článku slouží jako výchozí bod při vývoji aplikace pomocí služby Cosmos DB.   

Po přečtení tohoto článku, budete moci odpovědět na následující otázky: 

* Jaké jsou běžné případy použití pro službu Azure Cosmos DB?
* Jaké jsou výhody používání služby Azure Cosmos DB pro aplikace maloobchodního prodeje?
* Jaké jsou výhody používání služby Azure Cosmos DB k ukládání dat pro systémy Internet of Things (IoT)?
* Jaké jsou výhody používání služby Azure Cosmos DB pro webové a mobilní aplikace?

## <a name="introduction"></a>Úvod
[Azure Cosmos DB](../cosmos-db/introduction.md) je globálně distribuovaná databázová služba od Microsoftu. Služba je navržena k umožnění zákazníkům Elasticky (a nezávisle na sobě) škálovat propustnost a úložiště napříč libovolným počtem geografických oblastí. Azure Cosmos DB je první globálně distribuovaná databázová služba na trhu Dnes nabízí komplexní [smlouvy o úrovni služeb](https://azure.microsoft.com/support/legal/sla/cosmos-db/) zahrnující propustnosti, latence, dostupnosti a konzistence. 

Azure Cosmos DB je globální distribuovaná a vícemodelová databáze, která se používá v široké škály aplikací a případy použití. Je to Dobrá volba pro všechny [bez serveru](https://azure.com/serverless) aplikaci, která potřebuje nízkou pořadí milisekund dobou odezvy a je potřeba rychle a globálně škálovat. Podporuje více datových modelů (klíč hodnota, dokumentů, grafů a sloupcových) a přístup k mnoha rozhraní API pro data, včetně [rozhraní API služby Azure Cosmos DB pro MongoDB](mongodb-introduction.md), [rozhraní SQL API](documentdb-introduction.md), [rozhraní Gremlin API](graph-introduction.md), a [rozhraní Table API](table-introduction.md) nativně a postupně přibývají. 

Tady jsou některé atributy služby Azure Cosmos DB, kterým jsou vhodné pro vysoce výkonných aplikací s globální dále.

* Azure Cosmos DB nativně dělí data pro vysokou dostupnost a škálovatelnost. Azure Cosmos DB nabízí garanci 99,99 % dostupnosti, propustnosti, nízké latence a konzistence na všechny účty v jedné oblasti a všechny účty ve více oblastech s mírnější konzistencí a 99,999 % dostupnosti pro všechny účty databáze pro více oblastí pro čtení.
* Azure Cosmos DB má úložiště se zálohováním SSD s dobou odezvy pořadí milisekund s nízkou latencí.
* Podpora Azure Cosmos DB pro úrovně konzistence jako omezená neaktuálnost, konečný výsledek, konzistentní Předpona a relace umožňuje úplnou flexibilitu a nízké náklady na výkon poměr. Žádné databázová služba nabízí úrovně konzistence tolik flexibilitu jako Azure Cosmos DB. 
* Azure Cosmos DB má flexibilní cenový model přizpůsobený data, která měřiče využití úložiště a propustnost nezávisle na sobě.
* Model vyhrazenou propustností, které služby Azure Cosmos DB umožňuje uvažují v rámci počet čtení/zápisy místo využití procesoru/paměti/IOPs hardwarových komponent.
* Umožňuje návrh služby Azure Cosmos DB škálování pro masivní požadavek svazky v pořadí triliony požadavků denně.

Tyto atributy jsou užitečné v webové, mobilní a herní aplikace a aplikace IoT, která potřebují nízkou dobou odezvy a potřebují zpracovávat obrovské množství čtení a zápisu.

## <a name="iot-and-telematics"></a>IoT a telematika
Případy použití IoT běžně sdílí některé vzorů v tom, jak jejich ingestování, proces a ukládat data.  Nejprve je třeba tyto systémy ingestování nárůstům data ze senzorů zařízení různých národních prostředích. Tyto systémy dále zpracovávat a analyzovat streamovaná data k závěry v reálném čase. Data se potom archivovat do studeného úložiště pro dávkové analýzy. Microsoft Azure nabízí bohaté služby, které mohou být použity pro IoT případy použití, včetně služby Azure Cosmos DB, Azure Event Hubs, Azure Stream Analytics, centra oznámení Azure, Azure Machine Learning, Azure HDInsight a Power BI. 

![Referenční architektura Azure Cosmos DB IoT](./media/use-cases/iot.png)

Nárůstem dat můžete ingestovat řešením Azure Event Hubs, protože nabízí vysokou propustnost příjem dat s nízkou latencí. Data ingestována, kterou je potřeba zpracovat za vytváření přehledů v reálném čase můžete funneled ke službě Azure Stream Analytics pro analýzu v reálném čase. Data je možné načíst do služby Azure Cosmos DB pro dotazování ad hoc. Po načtení dat do služby Azure Cosmos DB jsou data připravená bude Dotazováno. Kromě toho můžete nová data a změny v existujících dat čtení na kanálu změn. Kanál změn je trvalé, připojit pouze protokol, který ukládá změny do služby Cosmos DB kontejnerů v sekvenčním pořadí. Všechna data nebo jenom změny dat ve službě Azure Cosmos DB může sloužit jako referenční data jako součást analýzy v reálném čase. Data navíc další může být kontrast a zpracovány připojením dat služby Azure Cosmos DB k HDInsight pro úlohy Pig, Hive nebo mapování/zmenšování.  Zpracovaných dat je pak načten zpět do služby Azure Cosmos DB k vytváření sestav.   

Ukázkové řešení IoT pomocí služby Azure Cosmos DB, EventHubs a Storm, najdete v článku [příklady použití hdinsight storm úložišti na Githubu](https://github.com/hdinsight/hdinsight-storm-examples/).

Další informace o nabídky Azure for IoT, najdete v části [vytvářejte Internet vlastních věcí](https://www.microsoft.com/en-us/internet-of-things). 

## <a name="retail-and-marketing"></a>Maloobchodní prodej a marketing
Azure Cosmos DB je často používány v platformách pro elektronické obchodování vlastní, na kterých běží Windows Store a XBox Live. Slouží také v odvětví maloobchodního prodeje pro ukládání katalogu dat a modelu event sourcing v pořadí zpracování kanálů.

Scénáře použití data CATALOG zahrnuje ukládání a dotazování na sadu atributů pro entity, jako jsou lidé, místa a produkty. Některé příklady data katalogu jsou uživatelské účty, katalogy produktů, registrů zařízení IoT a vyúčtování systémů materiály. Atributy pro tato data se můžou lišit a může změnit v čase podle požadavků aplikací.

Vezměte v úvahu příklad katalog produktů pro dodavatele průmyslu částí. Všechny části může mít svůj vlastní atributy kromě společné atributy, které sdílejí všechny části. Kromě toho atributy pro určitou část můžete změnit následující rok po vydání nového modelu. Azure Cosmos DB podporuje flexibilní schémata a hierarchických dat, a proto se skvěle hodí k ukládání dat katalogu produktů.

![Azure Cosmos DB prodejní katalog referenční architektury](./media/use-cases/product-catalog.png)

Azure Cosmos DB se často používá pro model event sourcing power architektury řízené událostmi pomocí jeho [kanálu změn](change-feed.md) funkce. Kanál změn umožňuje příjem mikroslužeb spolehlivě a postupně čtení vkládání a aktualizace (například pořadí události) ke službě Azure Cosmos DB. Tuto funkci můžete využít k poskytování úložiště trvalých událostí jako zprostředkovatel zpráv událostí změny stavu a pracovního postupu zpracování objednávky disku mezi mnoha mikroslužeb (které je možné implementovat jako [Azure Functions bez serveru](https://azure.com/serverless)).

![Řazení kanálu referenční architektura Azure Cosmos DB](./media/use-cases/event-sourcing.png)

Kromě toho data uložená ve službě Azure Cosmos DB je možné integrovat s HDInsight pro analýzy velkých objemů dat prostřednictvím úlohy Apache Spark. Podrobnosti o konektoru Spark pro Azure Cosmos DB najdete v tématu [spustit úlohu Spark pomocí služby Cosmos DB a HDInsight](spark-connector.md).

## <a name="gaming"></a>Hraní her
Databázová vrstva je zásadní součástí herní aplikace. Moderní hry provádění grafické zpracování na klientech mobilních/konzoly, ale závisí na cloudu a zprostředkovávat přizpůsobené a přizpůsobený obsah, jako jsou statistiky ve hře, integraci sociálních médií a vysoce skóre žebříčky. Hry často vyžadují latenci na úrovni jednotlivých milisekund pro čtení a zápis k poskytování zapojení ve hře prostředí. Herní databáze musí být rychlé a být schopen zpracovat uvedení v požadavků v průběhu nové spuštění her a aktualizace funkcí.

Azure Cosmos DB používá hry jako [The Walking Dead: No Man's Land](https://azure.microsoft.com/blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/) podle [Next Games](https://www.nextgames.com/), a [Halo 5: Strážce](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). Azure Cosmos DB poskytuje následující výhody pro vývojáře her:

* Azure Cosmos DB umožňuje výkonu škálovat nahoru nebo dolů Elasticky. To umožňuje hry pro zpracování aktualizací profilu a statistiky z desítek na miliony současně hráčům prostřednictvím jediného volání rozhraní API.
* Azure Cosmos DB podporuje milisekund čtení a zápisu zabránit tomu, aby všechny zjevně demonstruje výskyt pomalé během hry.
* Automatické indexování služby Azure Cosmos DB umožní je filtrovat proti více různých vlastností v reálném čase, například najít hráčů pomocí jejich interní player ID nebo jejich GameCenter, Facebook, Google ID nebo dotaz na základě player členství v guild. To je možné bez nutnosti složité indexování nebo infrastrukturu horizontálního dělení.
* Sociální funkce včetně zprávy chatu v rámci her, přehrávače guild členství ve skupinách, možné problémy dokončeno, žebříčky nejlepších výsledků a sociální grafy jsou usnadnil s flexibilním schématem.
* Azure Cosmos DB jako spravované platform-as-a-service (PaaS) vyžaduje minimální instalaci a správu práce umožňující rychlé iterace a zkraťte přitom dobu uvedení na trh.

![Referenční architektura herní služby Azure Cosmos DB](./media/use-cases/gaming.png)

## <a name="web-and-mobile-applications"></a>Webové a mobilní aplikace
Azure Cosmos DB se běžně používá v rámci webové a mobilní aplikace a je velmi vhodná pro modelování sociální interakce, integrace se službami třetích stran a pro vytváření bohatých individuálních prostředí. Sady SDK Cosmos DB může být bohaté iOS používané sestavení a aplikací pro Android pomocí Oblíbené [Xamarin framework](mobile-apps-with-xamarin.md).  

### <a name="social-applications"></a>Sociální aplikace
Běžným případem použití pro službu Azure Cosmos DB je k ukládání a dotazování na uživatelem generovaný obsah (UGC) pro webové, mobilní a sociální média aplikace. Některé příklady UGC jsou relace chatu, tweety, příspěvky blogu, hodnocení a komentáře. UGC v aplikace sociálních sítí je často kombinace text s volným tvarem, vlastnosti, značky a vztahy, které nejsou pevně daná struktura ohraničené. Obsah, jako je například konverzace, komentáře a příspěvky mohou být uloženy v databázi Cosmos DB bez nutnosti transformace nebo komplexní objekt pro relační mapování vrstvy.  Vlastnosti dat lze přidat nebo upravit jednoduše tak, aby odpovídaly požadavkům, jak vývojáři iterovat kód aplikace, proto povýšení rychlý vývoj.  

Aplikace, které integrace se sociálními sítěmi třetích stran reagovat na měnící se schémata z těchto sítí. Jak se ve výchozím nastavení ve službě Cosmos DB automaticky indexuje data, data připravená k dotazování v každém okamžiku. Proto tyto aplikace si můžou načítat projekce podle svých potřeb příslušné.

Mnoho sociální aplikace spustit v globálním měřítku a způsobit nepředvídatelným způsobem. Flexibilní škálování úložiště dat je nezbytné jako aplikační vrstvu se škáluje, aby pokryl využití.  Můžete škálovat přidáváním oddílů dalších dat do účtu služby Cosmos DB.  Kromě toho můžete také vytvořit další účty služby Cosmos DB napříč několika oblastmi. Dostupnost oblastí služby Cosmos DB, najdete v části [oblastí Azure](https://azure.microsoft.com/regions/#services).

![Architektura referenční aplikace webové služby Azure Cosmos DB](./media/use-cases/apps-with-global-reach.png)

### <a name="personalization"></a>Personalizace
V současné době obsahuje komplexní zobrazení a prostředí moderních aplikací. Toto jsou obvykle dynamická a společnosti předvolby uživatele nebo náladu a branding potřebám. Proto potřeba aplikace nebude možné získat efektivní k vykreslení prvků uživatelského rozhraní a prostředí rychle přizpůsobená nastavení. 

Formát JSON, formát podporovaný službou Cosmos DB, je ve formátu platné ke znázornění dat rozložení uživatelského rozhraní není jen jednoduchý, ale také můžete snadno interpretovat jazyka JavaScript. Cosmos DB nabízí přizpůsobitelných úrovní konzistence, které umožňují rychlé čtení pomocí zápisu s nízkou latencí. Ukládání dat rozložení uživatelského rozhraní, včetně individuální nastavení jako dokumenty JSON ve službě Cosmos DB, proto je efektivní způsob k získání těchto dat napříč přenosu.

![Architektura referenční aplikace webové služby Azure Cosmos DB](./media/use-cases/personalization.png)

## <a name="next-steps"></a>Další postup
Začínáme se službou Azure Cosmos DB, postupujte podle našich [rychlých startech](create-sql-api-dotnet.md), který vás provede vytvořením účtu a začátky práce se službou Cosmos DB. 

Nebo, pokud chcete další informace o zákaznících pomocí služby Cosmos DB, jsou k dispozici následující příběhy zákazníků:

* [Jet.com](https://jet.com). Elektronický obchodník špici, spouští v cloudu Microsoftu, využívá služby Cosmos DB v globálním měřítku.
* [Asos.com](https://www.asos.com/). Asos.com je British online způsobem a krásy úložiště. Primárně zaměřených na mladé dospělé, Asos prodává více než 850 značky, stejně jako vlastní rozsah oblečení a příslušenství.
* [Toyota](https://www.toyota.com/). Toyota Motor Corporation je japonský automobilový průmysl výrobce. Toyota využít Cosmos DB pro globální aplikace IoT.
* [Citrix](https://customers.microsoft.com/story/citrix). Citrix vyvíjí řešení jednotného přihlašování pomocí Azure Service Fabric a Azure Cosmos DB
* [TEXA](https://customers.microsoft.com/story/texaspa) společnosti TEXA revoluční řešení IoT pro vlastníky vozidla pomáhá šetřit čas, peníze, plynu – a případně se nachází.
* [Společnosti Domino Pizza](https://www.dominos.com). Společnosti Domino Pizza A.s. je řetěz American pizza restauraci.
* [Ovládací prvky Johnsonem](https://www.johnsoncontrols.com). Ovládací prvky Johnsonem je globální smíšené technologie a více průmyslové vedoucí instance obsluhuje celou řadu zákazníků ve víc než 150 zemích.
* [Microsoft Windows Universal Store, Azure IoT Hub, Xbox Live a další služby v internetovém měřítku](https://azure.microsoft.com/blog/how-azure-documentdb-planet-scale-nosql-helps-run-microsoft-s-own-businesses/). Postupy: sestavení Microsoft masivně škálovatelné služby pomocí služby Azure Cosmos DB.
* [Microsoft Data a analytický tým](https://customers.microsoft.com/story/microsoftdataandanalytics). Společnosti Microsoft Data a analytický tým dosahuje globálním měřítku kolekcí velkých objemů dat pomocí služby Azure Cosmos DB
* [Sulekha.com](https://customers.microsoft.com/story/sulekha-uses-azure-documentdb-to-connect-customers-and-businesses-across-india). Sulekha využívá Azure Cosmos DB k propojení zákazníky a firmy v Indii.
* [NewOrbit](https://customers.microsoft.com/story/neworbit-takes-flight-with-azure-documentdb). NewOrbit trvá letu pomocí služby Azure Cosmos DB.
* [Affinio](https://customers.microsoft.com/doclink/affinio-switches-from-aws-to-azure-documentdb-to-harness-social-data-at-scale). Affinio přechází od AWS ke službě Azure Cosmos DB k využívat sociální data ve velkém měřítku.
* [Next Games](https://azure.microsoft.com//blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/). The Walking Dead: No Man's Land her soars # 1 podporovány službou Azure Cosmos DB.
* [Halo](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). Jak Halo 5 implementuje hraní na sociálních sítích využívající službu Azure Cosmos DB.
* [Galerie Cortana Analytics](https://azure.microsoft.com/blog/cortana-analytics-gallery-a-scalable-community-site-built-on-azure-documentdb/). Galerii Cortana Analytics – web komunity škálovatelné postavená na službě Azure Cosmos DB.
* [Rychlé](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18602). Úvodní integrátor poskytuje globální přehled o nadnárodní podniky během několika minut s využitím flexibilních cloudových technologií.
* [Společnost News Republic](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18639). Zvyšování sofistikovanosti na novinky a poskytuje informace s jasným cílem pro angažovanosti občanů. 
* [Společnost SGS International](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18653). Konzistentní Barva po celém světě hlavních značek zapněte do skupiny zabezpečení. A využívá skupiny zabezpečení Azure.
* [Telenor](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18608). Světová Špička Telenor využívá cloud mohli přejít bez rychlostí začínající. 
* [XOMNI](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18667). Úložiště do budoucna běží na rychlé hledání a snadno datový tok.
* [Nucleo](https://customers.microsoft.com/story/azure-based-software-platform-breaks-down-barriers-bet). Platforma založená na Azure softwaru boří překážky mezi firmy a zákazníků
* [Weka](https://customers.microsoft.com/story/weka-smart-fridge-improves-vaccine-management-so-more-people-can-be-protected-against-diseases). Inteligentní ledničky weka vylepšuje správu vakcín, tak více lidí je chránit před nemocemi chránit
* [Oranžové Tribes](https://customers.microsoft.com/story/theres-more-to-that-food-app-than-meets-the-eye-or-the-mouth). Existuje víc informací, které tuto aplikaci potravin než vyhovují okem nebo úst.
* [Real Madrid](https://customers.microsoft.com/story/real-madrid-brings-the-stadium-closer-to-450-million-f). Real Madrid přibližuje svůj stadion 450 milionům fanoušků z celého světa pomocí cloudu společnosti Microsoft.
* [Tuku](https://customers.microsoft.com/story/tuku-makes-car-buying-fun-with-help-from-azure-services). TUKU díky car nákup Zábava s pomocí služby Azure
