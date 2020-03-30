---
title: Běžné případy použití a scénáře pro Azure Cosmos DB
description: 'Seznamte se s pěti nejlepšími případy použití pro Azure Cosmos DB: obsah generovaný uživateli, protokolování událostí, data katalogu, data uživatelských předvoleb a Internet věcí (IoT).'
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: de2bc551547706fb820813e57996e77bf49148d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73888935"
---
# <a name="common-azure-cosmos-db-use-cases"></a>Běžné případy použití Azure Cosmos DB
Tento článek obsahuje přehled několika běžných případů použití pro Azure Cosmos DB.  Doporučení v tomto článku slouží jako výchozí bod při vývoji aplikace s Cosmos DB.   

Po přečtení tohoto článku budete moci odpovědět na následující otázky: 

* Jaké jsou běžné případy použití pro Azure Cosmos DB?
* Jaké jsou výhody používání Azure Cosmos DB pro maloobchodní aplikace?
* Jaké jsou výhody používání Azure Cosmos DB jako úložiště dat pro systémy Internetu věcí (IoT)?
* Jaké jsou výhody používání Azure Cosmos DB pro webové a mobilní aplikace?

## <a name="introduction"></a>Úvod
[Azure Cosmos DB](../cosmos-db/introduction.md) je globálně distribuovaná databázová služba Microsoftu. Služba je navržena tak, aby zákazníkům umožňovala pružně (a nezávisle) škálovat propustnost a úložiště v libovolném počtu geografických oblastí. Azure Cosmos DB je první globálně distribuovaná databázová služba na trhu, která dnes nabízí komplexní [smlouvy o úrovni služeb](https://azure.microsoft.com/support/legal/sla/cosmos-db/) zahrnující propustnost, latenci, dostupnost a konzistenci. 

Azure Cosmos DB je globální distribuovaná databáze s více modely, která se používá v široké škále aplikací a případů použití. Je dobrou volbou pro všechny aplikace [bez serveru,](https://azure.com/serverless) které potřebuje nízké pořadí milisekund odezvy a je třeba škálovat rychle a globálně. Podporuje více datových modelů (hodnota klíče, dokumenty, grafy a sloupcové) a mnoho rozhraní API pro přístup k datům, včetně [rozhraní API Azure Cosmos DB pro MongoDB](mongodb-introduction.md), [SQL API](documentdb-introduction.md), [Gremlin API](graph-introduction.md)a rozhraní API [tabulek](table-introduction.md) nativně a rozšiřitelným způsobem. 

Níže jsou uvedeny některé atributy Azure Cosmos DB, které jsou vhodné pro vysoce výkonné aplikace s globální ambice.

* Azure Cosmos DB nativně rozdělí vaše data pro vysokou dostupnost a škálovatelnost. Azure Cosmos DB nabízí 99,99% záruky dostupnosti, propustnosti, nízké latence a konzistence na všech účtech s jednou oblastí a na všech účtech s více oblastmi s uvolněnou konzistencí a 99,999 % dostupnosti čtení na všech databázových účtech s více oblastmi.
* Azure Cosmos DB má úložiště podporované ssd s nízkou latencí, aby milisekundy doby odezvy.
* Podpora Azure Cosmos DB pro úrovně konzistence, jako je případná, konzistentní předpona, relace a vázaná neaktuálnost, umožňuje plnou flexibilitu a nízký poměr nákladů na výkon. Žádná databázová služba nenabízí takovou flexibilitu jako Azure Cosmos DB v konzistenci úrovní. 
* Azure Cosmos DB má flexibilní cenový model vhodný pro data, který nezávisle měří úložiště a propustnost.
* Azure Cosmos DB je vyhrazená propustnost model umožňuje přemýšlet z hlediska počtu čtení nebo zápisy namísto CPU/paměti/IOP základního hardwaru.
* Návrh Azure Cosmos DB umožňuje škálovat na objemy masivních požadavků v řádu bilionů požadavků za den.

Tyto atributy jsou prospěšné ve webových, mobilních, herních a ioT aplikacích, které potřebují nízkou dobu odezvy a potřebují zvládnout obrovské množství čtení a zápisů.

## <a name="iot-and-telematics"></a>IoT a telematika
Případy použití IoT běžně sdílejí některé vzory v tom, jak ingestují, zpracovávají a ukládají data.  Za prvé, tyto systémy je třeba splet výbuchy dat ze senzorů zařízení různých národních prostředí. Dále tyto systémy zpracovávají a analyzují streamovaná data za účelem odvození přehledů v reálném čase. Data jsou pak archivována do studeného úložiště pro dávkovou analýzu. Microsoft Azure nabízí bohaté služby, které se můžou použít pro případy použití IoT, včetně Azure Cosmos DB, Azure Event Hubs, Azure Stream Analytics, Azure Notification Hub, Azure Machine Learning, Azure HDInsight a Power BI. 

![Referenční architektura Azure Cosmos DB IoT](./media/use-cases/iot.png)

Shluky dat můžete ingestovat pomocí Centra událostí Azure, protože nabízí vysokou propustnost data ingestion s nízkou latencí. Data, která je potřeba zpracovat pro přehled y v reálném čase, se můžou převést do Azure Stream Analytics pro analýzy v reálném čase. Data lze načíst do Azure Cosmos DB pro dotazování adhoc. Po načtení dat do Azure Cosmos DB, data je připravena k dotazování. Kromě toho lze nová data a změny stávajících dat číst ve zdroji změn. Kanál změn je trvalý, připojit pouze protokol, který ukládá změny do kontejnerů Cosmos v sekvenčním pořadí. Všechna data nebo jen změny dat v Azure Cosmos DB lze použít jako referenční data jako součást analýzy v reálném čase. Kromě toho lze data dále vylepšit a zpracovat připojením dat Azure Cosmos DB k HDInsight pro úlohy Pig, Hive nebo Map/Reduce.  Rafinovaná data se pak načtou zpět do Azure Cosmos DB pro vytváření sestav.   

Ukázkové řešení IoT pomocí Azure Cosmos DB, EventHubs a Storm najdete v tématu [úložiště hdinsight-storm-examples na GitHubu](https://github.com/hdinsight/hdinsight-storm-examples/).

Další informace o nabídkách Azure pro IoT najdete [v tématu Vytvoření internetu vašich věcí](https://www.microsoft.com/en-us/internet-of-things). 

## <a name="retail-and-marketing"></a>Maloobchod a marketing
Azure Cosmos DB se ve velké míře používá ve vlastních platformách elektronického obchodování microsoftu, které používají Windows Store a XBox Live. Používá se také v maloobchodním průmyslu pro ukládání dat katalogu a pro získávání událostí v potrubí zpracování objednávek.

Scénáře využití dat katalogu zahrnují ukládání a dotazování sadu atributů pro entity, jako jsou osoby, místa a produkty. Příklady dat katalogu jsou uživatelské účty, katalogy produktů, registry zařízení IoT a systémy kusovníků. Atributy pro tato data se mohou lišit a mohou se v průběhu času měnit tak, aby vyhovovaly požadavkům aplikace.

Vezměme si příklad katalogu produktů pro dodavatele automobilových dílů. Každá část může mít své vlastní atributy kromě běžných atributů, které sdílejí všechny části. Atributy pro určitou část mohou navíc změnit následující rok, kdy je vydán nový model. Azure Cosmos DB podporuje flexibilní schémata a hierarchická data, a proto je vhodný pro ukládání dat katalogu produktů.

![Referenční architektura maloobchodního katalogu Azure Cosmos DB](./media/use-cases/product-catalog.png)

Azure Cosmos DB se často používá pro získávání událostí k napájení architektury řízené událostmi pomocí jeho funkce [kanálu změn.](change-feed.md) Kanál změn poskytuje podřízené mikroslužby možnost spolehlivě a postupně číst vloží a aktualizace (například události pořadí) provedené v Db Azure Cosmos. Tuto funkci lze využít k poskytování trvalé úložiště událostí jako zprostředkovatel e-mailové zprávy pro události měnící se stav a řídit zpracování objednávek pracovnípostup mezi mnoha mikroslužeb (které lze implementovat jako [funkce Azure bez serveru).](https://azure.com/serverless)

![Architektura odkazů na kanál Azure Cosmos DB](./media/use-cases/event-sourcing.png)

Kromě toho data uložená v Azure Cosmos DB lze integrovat s HDInsight pro analýzu velkých objemů dat prostřednictvím úloh Apache Spark. Podrobnosti o konektoru Spark pro Azure Cosmos DB najdete v [tématu Spuštění úlohy Spark s Cosmos DB a HDInsight](spark-connector.md).

## <a name="gaming"></a>Hry
Databázová úroveň je klíčovou součástí herních aplikací. Moderní hry provádějí grafické zpracování na mobilních /konzolových klientech, ale spoléhají se na cloud, aby poskytovaly přizpůsobený a personalizovaný obsah, jako jsou herní statistiky, integrace sociálních médií a žebříčky s vysokým skóre. Hry často vyžadují jednomilisekundové latence pro čtení a zápisy, aby poskytovaly poutavé herní prostředí. Herní databáze musí být rychlá a musí být schopna zvládnout masivní špičky v požadavcích během spuštění nové hry a aktualizací funkcí.

Azure Cosmos DB používají hry jako [The Walking Dead: No Man's Land](https://azure.microsoft.com/blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/) by Next [Games](https://www.nextgames.com/)a [Halo 5: Guardians](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). Azure Cosmos DB poskytuje vývojářům her následující výhody:

* Azure Cosmos DB umožňuje výkon škálovat nahoru nebo dolů elasticky. To umožňuje hrám zvládnout aktualizaci profilu a statistiky od desítek až po miliony současných hráčů tím, že jeden volání ROZHRANÍ API.
* Azure Cosmos DB podporuje milisekundčtení a zápisy, aby se zabránilo jakékoli zpoždění během hry.
* Automatické indexování Služby Azure Cosmos DB umožňuje filtrování proti více různým vlastnostem v reálném čase, například vyhledejte hráče podle jejich interních ID přehrávače nebo jejich GameCenter, Facebook, Google ID nebo dotaz na základě členství hráče v cechu. To je možné bez vytváření komplexní indexování nebo vytváření návěsů infrastruktury.
* Sociální funkce, včetně herních chatových zpráv, členství v cechu hráčů, dokončených výzev, žebříčků s vysokým skóre a sociálních grafů, se snadněji implementují pomocí flexibilního schématu.
* Azure Cosmos DB jako spravovaná platforma jako služba (PaaS) vyžaduje minimální nastavení a správu práce, aby pro rychlou iteraci a zkrátit dobu na trhu.

![Architektura herních referencí Azure Cosmos DB](./media/use-cases/gaming.png)

## <a name="web-and-mobile-applications"></a>Webové a mobilní aplikace
Azure Cosmos DB se běžně používá ve webových a mobilních aplikacích a je vhodný pro modelování sociálních interakcí, integraci se službami třetích stran a pro vytváření bohatých přizpůsobených prostředí. Kosmos DB SDK s lze použít sestavení bohaté aplikace pro iOS a Android pomocí populární [xamarin framework](mobile-apps-with-xamarin.md).  

### <a name="social-applications"></a>Sociální aplikace
Běžným případem použití pro Azure Cosmos DB je ukládání a dotazování uživatelem generovaný obsah (UGC) pro webové, mobilní a sociální mediální aplikace. Některé příklady uživatelem vytvořeného obsahu jsou relace konverzace, tweety, příspěvky blogu, hodnocení a komentáře. Často je uživatelem vytvořený obsah v aplikacích sociálních médií kombinací textu volného tvaru, vlastností, značek a vztahů, které nejsou ohraničeny pevnou strukturou. Obsah, jako jsou chaty, komentáře a příspěvky, lze uložit do cosmos DB bez nutnosti transformace nebo složitého objektu do vrstev relačního mapování.  Vlastnosti dat lze snadno přidat nebo upravit tak, aby odpovídaly požadavkům, jak vývojáři iterate přes kód aplikace, čímž podporují rychlý vývoj.  

Aplikace, které se integrují se sociálními sítěmi třetích stran, musí reagovat na měnící se schémata z těchto sítí. Jako data jsou automaticky indexovány ve výchozím nastavení v Cosmos DB, data je připraven k dotazování kdykoli. Proto tyto aplikace mají flexibilitu získat projekce podle jejich příslušných potřeb.

Mnoho sociálních aplikací běží v globálním měřítku a může vykazovat nepředvídatelné vzorce použití. Flexibilita při škálování úložiště dat je nezbytná, protože aplikační vrstva se škáluje tak, aby odpovídala poptávce po využití.  Můžete horizontální navýšení kapacity přidáním dalších datových oddílů pod účet Cosmos DB.  Kromě toho můžete také vytvořit další účty Cosmos DB ve více oblastech. Dostupnost oblasti služeb Cosmos DB najdete v [tématu Oblasti Azure](https://azure.microsoft.com/regions/#services).

![Referenční architektura webových aplikací Azure Cosmos DB](./media/use-cases/apps-with-global-reach.png)

### <a name="personalization"></a>Personalizace
V současné době přicházejí moderní aplikace s komplexními pohledy a zkušenostmi. Jedná se o typicky dynamické, catering na uživatelské preference nebo nálady a značky potřeby. Proto aplikace musí být schopen efektivně načíst individuální nastavení k vykreslení prvků uživatelského rozhraní a prostředí rychle. 

JSON, formát podporovaný Cosmos DB, je efektivní formát pro reprezentaci dat rozložení uživatelského rozhraní, protože je nejen lehký, ale také lze snadno interpretovat pomocí JavaScriptu. Cosmos DB nabízí laditelné úrovně konzistence, které umožňují rychlé čtení s nízkou latencí zápisy. Proto ukládání dat rozložení uživatelského rozhraní včetně přizpůsobených nastavení jako dokumentů JSON v Cosmos DB je účinným prostředkem k získání těchto dat přes drát.

![Referenční architektura webových aplikací Azure Cosmos DB](./media/use-cases/personalization.png)

## <a name="next-steps"></a>Další kroky

* Pokud chcete začít s Azure Cosmos DB, postupujte podle našich [rychlých startů](create-sql-api-dotnet.md), které vás provedou vytvořením účtu a začínáme s Cosmos DB.

* Pokud si chcete přečíst další informace o zákaznících, kteří používají Azure Cosmos DB, podívejte se na stránku [případové studie zákazníků.](https://azure.microsoft.com/case-studies/?service=cosmos-db)
