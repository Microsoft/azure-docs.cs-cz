---
title: Běžné případy použití a scénáře pro Azure Cosmos DB | Microsoft Docs
description: 'Další informace o horní pět případy používání pro Azure Cosmos DB: uživatel vygeneruje obsah, protokolování událostí, data katalogu, předvolby data uživatele a Internetu věcí (IoT).'
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/7/2017
ms.author: sngun
ms.openlocfilehash: ab24dc98ef4c767f04dc083ac6208e2c9d831935
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34616800"
---
# <a name="common-azure-cosmos-db-use-cases"></a>Běžné případy použití Azure Cosmos DB
Tento článek obsahuje přehled několik běžné případy použití pro Azure Cosmos DB.  Doporučení v tomto článku sloužit jako výchozí bod, když budete vyvíjet aplikace s Cosmos DB.   

Po přečtení tohoto článku, budete moct odpovězte si na následující otázky: 

* Jaké jsou běžné případy použití pro databázi Cosmos Azure?
* Jaké jsou výhody používání Azure Cosmos DB pro maloobchodní aplikace?
* Jaké jsou výhody používání Azure Cosmos DB jako úložiště dat pro systémy Internet věcí (IoT)?
* Jaké jsou výhody používání Azure Cosmos DB pro webové a mobilní aplikace?

## <a name="introduction"></a>Úvod
[Azure Cosmos DB](../cosmos-db/introduction.md) je globálně distribuované databáze služby společnosti Microsoft. Služba je navržena k umožnění zákazníkům pružně (a nezávisle) škálování propustnost a úložiště napříč libovolný počet zeměpisné oblasti. Azure Cosmos DB je první služba globálně distribuované databáze na trhu Dnes nabízí komplexní [smlouvy o úrovni služeb](https://azure.microsoft.com/support/legal/sla/cosmos-db/) zahrnující propustnosti, latenci, dostupnosti a konzistence. 

Azure Cosmos DB je globální databáze distribuované, více modelu, která se používá v celé řadě aplikací a případy použití. Je vhodné použít pro žádné [bez serveru](http://azure.com/serverless) aplikace, která musí nízkou pořadí milisekundu odezvy a musí se rychle a globálně škálovat. Podporuje několik datových modelech (klíč hodnota, dokumenty, grafy a sloupcové) a přístup k mnoha rozhraní API pro data, včetně [MongoDB API](mongodb-introduction.md), [rozhraní SQL API](documentdb-introduction.md), [rozhraní Graph API (Gremlin)](graph-introduction.md), a [tabulky API](table-introduction.md) nativně a rozšiřitelné způsobem. 

Zde jsou některé atributy Azure Cosmos DB, které dobře hodí pro vysoce výkonné aplikace s globální ctižádost.

* Azure Cosmos DB nativně oddíly data pro vysokou dostupnost a škálovatelnost. Azure Cosmos DB nabízí záruky 99,99 % dostupnost, propustnost, nízkou latencí a konzistence na všechny účty jedné oblasti a všechny oblasti s více účty s volný konzistence a 99.999 % číst dostupnosti pro všechny účty databáze více oblast.
* Azure Cosmos DB má úložiště SSD zálohovaný s nízkou latencí pořadí milisekundu odezvy.
* Azure Cosmos DB je podpora pro úrovně konzistence jako předpona případné a konzistentní, relace a typu ohraničenou prošlostí umožňuje úplnou flexibilitu a nízké náklady na výkon poměr. Žádná databáze služba nabízí tolik flexibilitu jako databázi Cosmos Azure v úrovně konzistence. 
* Azure Cosmos DB má flexibilní cenový model dat popisný, který měřidla úložiště a propustnost nezávisle.
* Azure Cosmos DB – model vyhrazenou propustností umožňuje vezměte v úvahu z hlediska počet čtení/zápisu místo procesoru nebo paměti nebo IOPs základní hardware.
* Návrh umožňuje Azure Cosmos DB škálování do svazků masivní žádosti v pořadí bilión požadavků za den.

Tyto atributy jsou užitečné v webové, mobilní a herní a aplikace IoT, které musíte nízkou odezvy a musí zpracovávat obrovské množství čtení a zápisy.

## <a name="iot-and-telematics"></a>IoT a telematika
Případy použití IoT běžně některé vzory v tom, jak se ingestování, proces, sdílet a ukládat data.  Tyto systémy je nejprve nutné ingestování shluky dat ze senzorů zařízení různých národních prostředí. Tyto systémy dále zpracovávat a analyzovat data streamovaná odvození přehledy v reálném čase. Data se pak archivovat na studené úložiště pro analýzu batch. Microsoft Azure nabízí bohaté služby, které lze použít pro IoT používat případech včetně Azure Cosmos DB, Azure Event Hubs, Azure Stream Analytics, centra oznámení Azure, Azure Machine Learning, Azure HDInsight a PowerBI. 

![Azure referenční architektura Cosmos DB IoT](./media/use-cases/iot.png)

Shluky dat můžete konzumaci Azure Event Hubs jako nabízí přijímání dat Vysoká propustnost s nízkou latencí. Požity data, která je potřeba zpracovat pro přehledy v reálném čase může funneled do služby Azure Stream Analytics pro analýzu v reálném čase. Data je možné načíst do Azure Cosmos DB pro zadávání dotazů ad hoc. Po načtení dat do Azure Cosmos DB, je připraven zadat dotaz na data. Kromě toho nová data a změny existujících dat lze číst na změnu informačního kanálu. Změna kanálu je trvalé, připojit pouze protokolu, která ukládá změny do kolekcí Cosmos DB v sekvenčním pořadí. Všechna data nebo pouze změny dat v Azure Cosmos DB lze použít jako referenční data v rámci analýzy v reálném čase. Data navíc další může být vylepšení a zpracovává připojení Azure Cosmos DB data do HDInsight pro úlohy Pig, Hive nebo mapy nebo snižte.  Přesnějších dat je pak načten zpět do Azure Cosmos DB pro vytváření sestav.   

Ukázka řešení IoT pomocí Azure Cosmos DB, EventHubs a Storm, najdete v článku [hdinsight-storm příklady úložišti na Githubu](https://github.com/hdinsight/hdinsight-storm-examples/).

Další informace o Azure nabídky pro IoT najdete v tématu [vytvořit Internet vaše věcí](http://www.microsoft.com/server-cloud/internet-of-things.aspx). 

## <a name="retail-and-marketing"></a>Prodej a marketing
Azure Cosmos DB se hojně používá v platformy společnosti Microsoft vlastní elektronické obchodování, se systémem Windows Store a XBox Live. Je je také použít v odvětví prodejní pro ukládání dat katalogu a pro událost sourcing v pořadí zpracování kanály.

Scénáře použití data CATALOG zahrnuje ukládání a dotazování sadu atributů pro entity, jako je například osob, míst a produkty. Některé příklady data katalogu jsou uživatelské účty, katalogy produktů, registrech zařízení IoT a faktury systémů materiálů. Atributy pro tato data se může lišit a může časem podle požadavků aplikace změnit.

Zvažte například katalogu produktů pro dodavatele automobilu částí. Všechny části může mít svůj vlastní atributy kromě běžné atributy, které sdílejí všechny části. Atributy pro určitou část kromě toho můžete změnit následující roku, kdy je vydán nový model. Azure Cosmos DB podporuje flexibilní schémata a hierarchických dat, a proto se skvěle hodí pro ukládání dat katalogu produktů.

![Azure Cosmos DB prodejní katalogu referenční architektura](./media/use-cases/product-catalog.png)

Azure Cosmos DB se často používá pro události sourcing do architektury událostí řízené power pomocí jeho [změnit informačního kanálu](change-feed.md) funkce. Informační kanál změnu poskytuje podřízené mikroslužeb možnost spolehlivě a přírůstkově číst vložení a aktualizace (například pořadí událostí) k databázi Azure Cosmos. Tuto funkci lze využít k poskytování úložiště trvalé událostí jako zprostředkovatel zpráva události změny stavu a jednotky pořadí zpracování pracovního postupu mezi mnoha mikroslužeb (které může být implementováno jako [bez serveru Azure Functions](http://azure.com/serverless)).

![Azure DB Cosmos řazení kanálu referenční architektura](./media/use-cases/event-sourcing.png)

Kromě toho data uložená v Azure Cosmos DB lze integrovat s HDInsight pro analýzu velkých objemů dat pomocí Apache Spark úlohy. Podrobnosti o konektoru Spark pro Azure Cosmos DB najdete v tématu [spustit úlohu Spark s Cosmos databáze a HDInsight](spark-connector.md).

## <a name="gaming"></a>Hraní her
Databázové vrstvy je zásadní součástí herní aplikace. Moderní hry provádět grafické zpracování na klientech mobilních nebo konzoly, ale závisí na cloudu a zprostředkovávat přizpůsobené a přizpůsobený obsah jako statistiky ve hře, sociální média integrace a nejlepších výsledků tabulky. Hry často vyžadují jedním milisekundu latenci pro čtení a zápisů zajistit zapojení ve hře prostředí. Herní databáze musí být rychlé a moct zpracovávat obrovské špičky v požadavků během nové herní spustí a funkci aktualizace.

Používá Azure Cosmos DB hry jako [chodí neaktivní: Ne Man krajině](https://azure.microsoft.com/blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/) podle [další hry](http://www.nextgames.com/), a [bylo 5: vychovatelům](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). Azure Cosmos DB se herní vývojářům nabízí následující výhody:

* Azure Cosmos DB umožňuje výkon a škálovat nahoru nebo dolů, pružně. To umožňuje hry pro zpracování aktualizaci profilu a statistiky z desítek na miliony současně hráči tím, že jednoho volání rozhraní API.
* Azure Cosmos DB podporuje milisekundu čte a zapisuje do vyhnout žádné pomalou průběhu hry.
* Automatické indexování Azure Cosmos DB umožňuje filtrování více různých vlastností v reálném čase, například najít přehrávače podle jejich interní player ID nebo jejich GameCenter, Facebook, Google ID nebo dotaz na základě player členství v sdružení. To je možné bez vytváření složitých indexování nebo infrastrukturu horizontálního dělení.
* Jsou sociálních funkcí serveru včetně ve hře chatovací zprávy, player sdružení členství ve skupinách, výzvy dokončit, nejlepších výsledků tabulky a sociálních grafy usnadňují implementaci se schématem, flexibilní.
* Azure DB Cosmos jako spravované platformy jako služba (PaaS) vyžaduje minimální nastavení a správa pracovních umožňující rychlé iterace a zkraťte dobu uvedení na trh.

![Azure Cosmos DB herní referenční architektura](./media/use-cases/gaming.png)

## <a name="web-and-mobile-applications"></a>Webové a mobilní aplikace
Azure Cosmos DB se často používá v rámci webové a mobilní aplikace a je dobře hodí pro modelování sociálních interakce integrace služeb třetích stran a pro vytváření bohatě přizpůsobené prostředí. Sady SDK DB Cosmos lze použít sestavení bohaté iOS a Android aplikací pomocí oblíbených [Xamarin framework](mobile-apps-with-xamarin.md).  

### <a name="social-applications"></a>Sociální aplikace
Běžně používá pro Azure Cosmos DB je ukládat a dotaz na uživatelem generovaný obsah (UGC) pro webové, mobilní a sociálních médií aplikace. Některé příklady UGC jsou relace konverzace, tweetů, příspěvcích na blogu, hodnocení a komentáře. UGC v sociálních sítích aplikace je často blend volného formátu textu, vlastností, značky a vztahy, které nejsou ohraničené pevné struktura. Obsah, jako je například konverzace, komentáře a příspěvky můžete uloženy v Cosmos databázi bez nutnosti transformace nebo komplexní objekt na relační mapování vrstvy.  Vlastnosti dat můžete přidat nebo upravit snadno tak, aby odpovídalo požadavkům jako vývojáři iterace v kódu aplikace, proto povýšení rychlý vývoj.  

Aplikace, které integraci se sociálními sítěmi třetích stran reagovat na měnící se schémata z těchto sítí. Jako data se automaticky indexovaný ve výchozím nastavení v Cosmos DB, data jsou připravena k být dotazována kdykoli. Proto tyto aplikace mají možnost načíst projekce podle jejich potřeb příslušné.

Spousta sociálních aplikací spusťte v globálním měřítku a mohou vykazovat vzorce nepředvídatelné. Flexibilita při škálování úložiště dat je důležité jako aplikační vrstvu škáluje tak, aby odpovídaly vyžádání využití.  Můžete škálovat tak, že přidáte další data oddíly pod účtem Cosmos DB.  Kromě toho můžete také vytvořit další účty Cosmos DB nad několika oblastmi. Dostupnost služby oblast Cosmos DB, najdete v části [oblasti Azure](https://azure.microsoft.com/regions/#services).

![Azure Cosmos DB webové aplikace referenční architektura](./media/use-cases/apps-with-global-reach.png)

### <a name="personalization"></a>Personalizace
V současné době moderních aplikací mít komplexní zobrazení a prostředí. Toto jsou obvykle dynamická, stravovací uživatelských předvoleb nebo atmosféru a značka potřebám. Proto aplikace musí být možné načíst individuální nastavení efektivně k vykreslení prvky uživatelského rozhraní a prostředí rychle. 

Formát JSON, formátu nepodporuje Cosmos databáze, je ve formátu efektivní představující rozložení data uživatelského rozhraní, jako je nejen lightweight, ale také můžete snadno interpretovat JavaScript. Cosmos DB nabízí přizpůsobitelné úrovně konzistence umožňující rychlé čtení s nízkou latencí a zápisu. Ukládání dat rozložení uživatelského rozhraní, včetně individuální nastavení jako dokumenty JSON v Cosmos DB proto je účinnou k získání těchto dat napříč drátové síti.

![Azure Cosmos DB webové aplikace referenční architektura](./media/use-cases/personalization.png)

## <a name="next-steps"></a>Další postup
Chcete-li začít s Azure Cosmos DB, postupujte podle našich [rychlé zahájení](create-sql-api-dotnet.md), který vás provede procesem vytvoření účtu a úvod do Cosmos DB. 

Nebo, pokud vás zajímají další informace o použití Cosmos DB, jsou dostupné následující scénáře zákazníka:

* [Jet.com](https://jet.com). Elektronické obchodování challenger eyes nejvyšší pozici, běží v cloudu Microsoft, využívá Cosmos DB v globálním měřítku.
* [Asos.com](http://www.asos.com/). Asos.com je British online způsobem a kosmetické úložiště. Především zaměřené na malí dospělí, Asos prodává přes 850 značky, jakož i vlastní rozsah oblečení a příslušenství.
* [Toyota](https://www.toyota.com/). Toyota Motor Corporation je japonské automobilu výrobcem. Toyota využít Cosmos DB pro globální aplikaci IoT.
* [Citrix](https://customers.microsoft.com/story/citrix). Citrix sama vyvinula jednotného přihlašování řešení pomocí Azure Service Fabric a Azure Cosmos DB
* [TEXA](https://customers.microsoft.com/story/texaspa) na TEXA revoluční řešení IoT pro vlastníky vehicle pomáhá šetřit čas, peníze, plynu – a případně je umístěn.
* [Na Domino Pizza](https://www.dominos.com). Na Domino Pizza Inc. je American pizza restaurace řetězec.
* [Ovládací prvky Janíček](http://www.johnsoncontrols.com). Ovládací prvky Janíček je globální smíšené technologie a více průmyslových vedoucí obsluhující celou řadu zákazníků ve víc než 150 zemích.
* [Microsoft Windows, univerzální úložiště, Azure IoT Hub, Xbox Live a dalším službám internetových](https://azure.microsoft.com/blog/how-azure-documentdb-planet-scale-nosql-helps-run-microsoft-s-own-businesses/). Jak Microsoft sestavení enormně škálovatelné služby Azure Cosmos DB.
* [Tým Microsoft Data a analýzy](https://customers.microsoft.com/story/microsoftdataandanalytics). Společnosti Microsoft Data a analýzy tým dosahuje velkých objemů dat kolekce planetu škálování s Azure Cosmos DB
* [Sulekha.com](https://customers.microsoft.com/story/sulekha-uses-azure-documentdb-to-connect-customers-and-businesses-across-india). Sulekha používá k připojení zákazníků a firmy napříč Indie Azure Cosmos DB.
* [NewOrbit](https://customers.microsoft.com/story/neworbit-takes-flight-with-azure-documentdb). NewOrbit trvá letu s Azure Cosmos DB.
* [Affinio](https://customers.microsoft.com/doclink/affinio-switches-from-aws-to-azure-documentdb-to-harness-social-data-at-scale). Affinio přepíná z AWS k databázi Cosmos Azure pro svůj sociální data ve velkém měřítku.
* [Další hry](https://azure.microsoft.com//blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/). Vycházkové zpráv: Ne Man demokratická krajině herní soars # 1 nepodporuje Azure Cosmos DB.
* [Bylo](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). Tom, jak bylo 5 implementují sociálních hraní her pomocí Azure Cosmos DB.
* [Galerie Analytics Cortana](https://azure.microsoft.com/blog/cortana-analytics-gallery-a-scalable-community-site-built-on-azure-documentdb/). Cortana Analytics Galerie - lokalitu škálovatelné komunity vytvořené v Azure Cosmos DB.
* [Uloženy](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18602). Úvodní integrátor poskytuje globální přehledy mezinárodní podniky v minutách s flexibilní cloudové technologie.
* [Příspěvky republika](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18639). Přidávání do zprávy s informacemi s účelem aktivnější občanů intelligence. 
* [Mezinárodní skupině](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18653). Pro konzistentní barvu po celém světě zapněte většiny hlavních značek k skupině. A skupině převede do Azure.
* [Telenor](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18608). Neoprávněných Telenor používá cloudu přesunout s rychlostí spuštění. 
* [XOMNI](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18667). Úložiště budoucí běží na rychlé vyhledávání a snadno tok dat.
* [Nucleo](https://customers.microsoft.com/story/azure-based-software-platform-breaks-down-barriers-bet). Platforma softwaru na základě Azure rozpis překážek mezi podnikům a zákazníků
* [Weka](https://customers.microsoft.com/story/weka-smart-fridge-improves-vaccine-management-so-more-people-can-be-protected-against-diseases). Inteligentní ledničky weka zlepšuje správu očkovací tak lidem se dají chránit proti nákaz
* [Oranžové Tribes](https://customers.microsoft.com/story/theres-more-to-that-food-app-than-meets-the-eye-or-the-mouth). Existuje více k této aplikaci jídlo než splňuje oko nebo úst.
* [Skutečné Madridu](https://customers.microsoft.com/story/real-madrid-brings-the-stadium-closer-to-450-million-f). Skutečné Madridu přináší stadium blíže 450 milionů ventilátory po celém světě s cloudem Microsoftu.
* [Tuku](https://customers.microsoft.com/story/tuku-makes-car-buying-fun-with-help-from-azure-services). TUKU díky car kdybyste kupovali fun pomoci ze služby Azure
