---
title: Běžné případy použití a scénáře pro Azure Cosmos DB
description: 'Přečtěte si o pěti hlavních případech použití pro Azure Cosmos DB: uživatelem generovaný obsah, protokolování událostí, data katalogu, data uživatelských předvoleb a Internet věcí (IoT).'
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: 04de6dce6cbab4dc0716ae841707b1d61d6bc375
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93073753"
---
# <a name="common-azure-cosmos-db-use-cases"></a>Běžné případy použití Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Tento článek poskytuje přehled několika běžných případů použití pro Azure Cosmos DB.  Doporučení v tomto článku slouží jako výchozí bod při vývoji aplikace pomocí Cosmos DB.

Po přečtení tohoto článku budete moci zodpovědět následující otázky: 

* Jaké jsou běžné případy použití pro Azure Cosmos DB?
* Jaké jsou výhody použití Azure Cosmos DB pro maloobchodní aplikace?
* Jaké jsou výhody použití Azure Cosmos DB jako úložiště dat pro systémy Internet věcí (IoT)?
* Jaké jsou výhody používání Azure Cosmos DB pro webové a mobilní aplikace?

## <a name="introduction"></a>Úvod

[Azure Cosmos DB](../cosmos-db/introduction.md) je databáze Microsoftu pro rychlé NoSQL s otevřenými rozhraními API pro jakékoli škálování. Služba je navržená tak, aby zákazníkům umožnila elasticky (a nezávisle) škálovat propustnost a úložiště napříč libovolným počtem geografických oblastí. Azure Cosmos DB je první globálně distribuovaná databázová služba na trhu dnes, která nabízí komplexní [smlouvy o úrovni služeb](https://azure.microsoft.com/support/legal/sla/cosmos-db/) zahrnující propustnost, latenci, dostupnost a konzistenci.

Azure Cosmos DB je globální distribuovaná databáze s více modely, která se používá v široké škále aplikací a případech použití. Je vhodným řešením pro jakékoli aplikace bez [serveru](https://azure.com/serverless) , které potřebují méně času odezvy v milisekundách a které musí rychle a globálně škálovat. Podporuje více datových modelů (klíč-hodnota, dokumenty, grafy a sloupcové) a mnoho rozhraní API pro přístup k datům, [Azure Cosmos DB včetně rozhraní API pro MongoDB](mongodb-introduction.md), rozhraní API [SQL](./introduction.md), rozhraní [Gremlin API](graph-introduction.md)a [tabulkových rozhraní API](table-introduction.md) nativně a rozšiřitelným způsobem. 

Níže jsou uvedeny některé atributy Azure Cosmos DB, které jsou vhodné pro vysoce výkonné aplikace s globálním Ambition.

* Azure Cosmos DB nativně rozdělí data pro zajištění vysoké dostupnosti a škálovatelnosti. Azure Cosmos DB nabízí 99,99% záruky dostupnosti, propustnosti, nízké latence a konzistence pro všechny účty v jedné oblasti a všechny účty ve více oblastech s odlehčenou konzistencí a 99,999% dostupnosti čtení pro všechny účty databáze ve více oblastech.
* Azure Cosmos DB má úložiště s podporou SSD s nízkou latencí – milisekundy odezvy.
* Podpora Azure Cosmos DB úrovní konzistence, jako je například případná, konzistentní předpona, relace a ohraničená – neaktuálnost, umožňuje celou flexibilitu a nízký poměr nákladů na výkon. Žádná databázová služba nenabízí větší flexibilitu jako Azure Cosmos DB v konzistenci úrovní. 
* Azure Cosmos DB má flexibilní cenový modelově přívětivý datový model, který měřiče úložiště a propustnost nezávisle.
* Model rezervované propustnosti Azure Cosmos DB vám umožní vzít v úvahu místo procesoru/paměti/IOPs základního hardwaru na základě počtu operací čtení a zápisu.
* Návrh Azure Cosmos DB umožňuje škálování na obrovský objem požadavků v pořadí bilióny požadavků za den.

Tyto atributy jsou užitečné v aplikacích pro web, mobilní zařízení, hry a aplikace IoT, které potřebují dobu odezvy s nízkou odezvou a potřebují zpracovávat obrovské objemy čtení a zápisu.

## <a name="iot-and-telematics"></a>IoT a telematika

Případy použití IoT obvykle sdílejí některé vzorce v tom, jak jejich příjem, zpracování a ukládání dat.  Nejprve tyto systémy potřebují ingestovat shluky dat ze senzorů zařízení různých národních prostředí. V dalším kroku tyto systémy zpracovávají a analyzují streamovaná data pro odvození přehledů v reálném čase. Data se pak archivují do studeného úložiště pro dávkovou analýzu. Microsoft Azure nabízí obsáhlé služby, které se dají použít pro případy použití IoT, včetně Azure Cosmos DB, Azure Event Hubs, Azure Stream Analytics, Azure Notification hub, Azure Machine Learning, Azure HDInsight a Power BI. 

:::image type="content" source="./media/use-cases/iot.png" alt-text="Referenční architektura IoT Azure Cosmos DB" border="false":::

Azure Event Hubs může ingestovat nárůsty dat, protože nabízí příjem dat s vysokou propustností s nízkou latencí. Data ingestovaná, která je potřeba zpracovat pro účely přehledu v reálném čase, je možné rozfiltrovat na Azure Stream Analytics pro analýzy v reálném čase. Data je možné načíst do Azure Cosmos DB pro dotazování ad hoc. Jakmile se data načtou do Azure Cosmos DB, data jsou připravená k dotazování. Kromě toho je možné číst nová data a změny stávajících dat v kanálu změn. Změna kanálu je trvalá, připojovat pouze protokol, který ukládá změny do kontejnerů Cosmos v sekvenčním pořadí. Všechna data nebo pouze změny dat v Azure Cosmos DB lze použít jako součást analýz v reálném čase. Kromě toho je možné data dále zdokonalovat a zpracovávat připojením Azure Cosmos DB dat ke službě HDInsight pro prase, podregistr nebo mapování/snížení úloh.  Data v kontrastu se pak načtou zpátky do Azure Cosmos DB pro vytváření sestav.   

Ukázkové řešení IoT s využitím Azure Cosmos DB, EventHubs a zaplavení najdete na webu [GitHub úložiště s příklady HDInsight-](https://github.com/hdinsight/hdinsight-storm-examples/)zaplave.

Další informace o nabídkách Azure pro IoT najdete v tématu [Vytvoření Internetu vašich věcí](https://www.microsoft.com/en-us/internet-of-things). 

## <a name="retail-and-marketing"></a>Maloobchod a marketing
Azure Cosmos DB se často používá v rámci vlastních platforem elektronického obchodování od Microsoftu, které spouštějí Windows Store a XBox Live. Používá se také v maloobchodním průmyslu pro ukládání dat katalogu a pro zdroje událostí v pořadí zpracování kanálů.

Scénáře použití dat katalogu zahrnují ukládání a dotazování sady atributů pro entity, jako jsou lidé, místa a produkty. Příklady dat katalogu jsou uživatelské účty, katalogy produktů, Registry zařízení IoT a systémy pro vyúčtování materiálů. Atributy pro tato data se můžou lišit a můžou se časem měnit podle požadavků aplikace.

Vezměte v úvahu příklad katalogu produktů pro dodavatele automobilových součástí. Každá část může mít kromě běžných atributů, které sdílí všechny části, vlastní atributy. Kromě toho mohou atributy pro určitou část po uvolnění nového modelu změnit následující rok. Azure Cosmos DB podporuje flexibilní schémata a hierarchická data, takže je vhodný pro ukládání dat katalogu produktů.

:::image type="content" source="./media/use-cases/product-catalog.png" alt-text="Referenční architektura IoT Azure Cosmos DB" border="false":::

Azure Cosmos DB se často používá k zajištění, aby se v architektuře řízených událostmi na základě jeho funkcí [změny](change-feed.md) prováděla architektura událostí. Kanál změn poskytuje podřízené mikroslužby schopnost spolehlivě a přírůstkově číst vkládání a aktualizace (například události objednávek) provedené na Azure Cosmos DB. Tato funkce se dá využít k tomu, aby poskytovala trvalé úložiště událostí jako zprostředkovatele zpráv pro události změny stavu a pracovní postupy zpracování pořadí jednotek mezi mnoha mikroslužbami (které je možné implementovat jako [Azure Functions bez serveru](https://azure.com/serverless)).

:::image type="content" source="./media/use-cases/event-sourcing.png" alt-text="Referenční architektura IoT Azure Cosmos DB" border="false":::

Data uložená v Azure Cosmos DB je navíc možné integrovat se službou HDInsight pro analýzy velkých objemů dat prostřednictvím Apache Spark úloh. Podrobnosti o konektoru Spark pro Azure Cosmos DB najdete v tématu [spuštění úlohy Sparku pomocí Cosmos DB a HDInsight](spark-connector.md).

## <a name="gaming"></a>Hry
Databázová úroveň je důležitou součástí herních aplikací. Moderní hry zpracovávají grafiku na mobilních nebo konzolových klientech, ale využívají cloud k doručování přizpůsobeného a individuálního obsahu, jako jsou herní statistiky, integrace sociálních sítí a žebříčky nejvyšších skóre. Hry často vyžadují latenci v milisekundách pro čtení a zápisy, které poskytují poutavý zážitek z her. Během uvedení nové hry na trh a aktualizace funkcí musí být databáze hry rychlá a schopná zvládnout obrovský nárůst poptávky.

Azure Cosmos DB se používají ve hrách, jako [je nedoručená](https://azure.microsoft.com/blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/) doba nedostatku člověka – [Další hry](https://www.nextgames.com/)a [Halo 5: strážes](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). Azure Cosmos DB poskytuje vývojářům her následující výhody:

* Azure Cosmos DB umožňuje elastické nebo vypnuté škálování výkonu. Díky tomu můžou hry zpracovávat aktualizace profilu a statistiky z desítek na miliony souběžných hráčů díky jednomu volání rozhraní API.
* Azure Cosmos DB podporuje čtení a zápisy milisekund, které vám pomůžou při hraní prodlevy při hraní her.
* Automatické indexování Azure Cosmos DB umožňuje filtrování s více různými vlastnostmi v reálném čase, například hledání hráčů podle jejich interních ID hráčů nebo jejich GameCenter, Facebook, Google ID nebo dotaz na základě členství v přehrávači v Guild. To je možné bez vytváření složitých indexů nebo infrastruktury horizontálního dělení.
* Funkce sociálních sítí, včetně zpráv v herních konverzacích, členství v přehrávači Guild, dokončené úkoly, žebříčky s vysokým skóre a sociální grafy, je snazší implementovat pomocí flexibilního schématu.
* Azure Cosmos DB jako spravovaná platforma jako služba (PaaS) vyžaduje minimální práci s nastavením a správou, která umožňuje rychlou iteraci a zkracuje dobu uvedení na trh.

:::image type="content" source="./media/use-cases/gaming.png" alt-text="Referenční architektura IoT Azure Cosmos DB" border="false":::

## <a name="web-and-mobile-applications"></a>Webové a mobilní aplikace
Azure Cosmos DB se běžně používá u webových a mobilních aplikací a hodí se k modelování sociálních interakcí, integraci se službami třetích stran a vytváření silně přizpůsobených prostředí. Sady Cosmos DB SDK se dají použít k vytváření bohatých aplikací pro iOS a Android s využitím oblíbené [architektury Xamarin](mobile-apps-with-xamarin.md).  

### <a name="social-applications"></a>Sociální aplikace
Běžným případem použití pro Azure Cosmos DB je ukládání a dotazování obsahu generovaného uživatelem (UGC) pro webové, mobilní a sociální aplikace. Mezi příklady UGC patří konverzace, tweety, příspěvky na blogu, hodnocení a komentáře. UGC v aplikacích sociálních médií je často směsí bezplatného textu, vlastností, značek a relací, které nejsou ohraničeny tuhou strukturou. Obsah, jako jsou chaty, komentáře a příspěvky, lze uložit v Cosmos DB bez nutnosti transformace nebo složitého objektu na relační mapování vrstev.  Vlastnosti dat je možné snadno přidat nebo upravit tak, aby se shodovaly s požadavky, jako vývojáři iterují přes kód aplikace, a tak podporují rychlý vývoj.  

Aplikace, které se integrují se sociálními sítěmi třetích stran, musí reagovat na změnu schémat z těchto sítí. Data jsou ve výchozím nastavení automaticky indexována v Cosmos DB a data jsou připravena k dotazování kdykoli. Proto mají tyto aplikace flexibilitu, aby načetla projekce podle jejich odpovídajících potřeb.

Mnohé z sociálních aplikací běží v globálním měřítku a můžou vykazovat nepředvídatelné vzorce používání. Flexibilita při škálování úložiště dat je zásadní, protože aplikační vrstva se škáluje podle požadavků na využití.  Horizontální navýšení kapacity můžete rozšířit přidáním dalších datových oddílů pod Cosmos DB účet.  Kromě toho můžete také vytvořit další Cosmos DB účty napříč několika oblastmi. Dostupnost Cosmos DBho regionu služby najdete v tématu [oblasti Azure](https://azure.microsoft.com/regions/#services).

:::image type="content" source="./media/use-cases/apps-with-global-reach.png" alt-text="Referenční architektura IoT Azure Cosmos DB" border="false":::

### <a name="personalization"></a>Personalizace
Současné době moderní aplikace se dodávají se složitými zobrazeními a prostředími. Jsou to obvykle dynamické, v stravovacích uživatelských preferencích a potřebách brandingu. Aplikace proto potřebují mít možnost efektivně načítat individuální nastavení, aby bylo možné rychle vykreslovat prvky uživatelského rozhraní a jejich prostředí. 

Formát JSON, který je podporován Cosmos DB, je efektivní formát pro reprezentaci dat rozložení uživatelského rozhraní, protože není pouze odlehčený, ale lze jej snadno interpretovat pomocí JavaScriptu. Cosmos DB nabízí přizpůsobitelné úrovně konzistence, které umožňují rychlé čtení pomocí zápisů s nízkou latencí. Proto ukládání dat rozložení uživatelského rozhraní, včetně individuálních nastavení jako dokumentů JSON Cosmos DB, je efektivní způsob, jak tato data získat napříč vodiči.

:::image type="content" source="./media/use-cases/personalization.png" alt-text="Referenční architektura IoT Azure Cosmos DB" border="false":::

## <a name="next-steps"></a>Další kroky

* Pokud chcete začít s Azure Cosmos DB, postupujte podle našeho [rychlého startu](create-sql-api-dotnet.md), které vás provedou vytvořením účtu a začínáme s Cosmos DB.

* Pokud chcete získat další informace o zákaznících, kteří používají Azure Cosmos DB, přečtěte si stránku [případové studie zákazníka](https://azure.microsoft.com/case-studies/?service=cosmos-db) .