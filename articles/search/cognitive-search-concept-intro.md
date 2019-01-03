---
title: Kognitivní vyhledávání, extrakce dat, zpracování přirozeného jazyka AI – Azure Search
description: Extrakce obsahu, přirozeného jazyka (NLP) zpracování a vytvořte prohledávatelného obsahu ve službě Azure Search indexování s využitím kognitivních dovedností a algoritmy AI pro zpracování obrázků.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 08/07/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 2bb32ccaeb5960fa69dcdc356523abc199fd5f4f
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53633789"
---
# <a name="what-is-cognitive-search-in-azure-search"></a>Co je "kognitivního vyhledávání" ve službě Azure Search?

Kognitivní vyhledávání vytvoří prohledávatelná informace z jiných prohledávatelný obsah připojením algoritmy AI na kanál indexování. O integraci AI je prostřednictvím *kognitivní dovednosti*, rozšíření cestě zdroje dokumentů do indexu vyhledávání. 

**Zpracování přirozeného jazyka** dovednosti zahrnují [rozpoznávání entit](cognitive-search-skill-named-entity-recognition.md), detekce jazyka [klíče extrakce frází](cognitive-search-skill-keyphrases.md), manipulaci s textem a rozpoznávání mínění. Tyto znalosti se stane nestrukturovaného textu strukturovaná, namapované na prohledávatelný a filtrovatelných polích v indexu.

**Zpracování obrázků** zahrnuje [OCR](cognitive-search-skill-ocr.md) a identifikace [vizuální funkce](cognitive-search-skill-image-analysis.md), jako je například rozpoznávání tváře, bitovou kopii interpretaci, obrázků, rozpoznávání (slavných lidech a památek) nebo atributy, například barvy nebo orientaci obrázku. Můžete vytvořit textové reprezentace obsahu bitové kopie s možností vyhledávání pomocí všech možností dotazu služby Azure Search.

![Kognitivní vyhledávání kanálu diagram](./media/cognitive-search-intro/cogsearch-architecture.png "přehled kanálu Kognitivního vyhledávání")

Kognitivní dovednosti v Azure Search je založena na stejné algoritmy umělé používaných pro rozhraní API služeb Cognitive Services: [Rozhraní API pro rozpoznávání entit s názvem](cognitive-search-skill-named-entity-recognition.md), [klíč frázi extrakce API](cognitive-search-skill-keyphrases.md), a [OCR API](cognitive-search-skill-ocr.md) jsou jenom pár. 

Přirozený jazyk a zpracování obrázků se použije během fáze příjem dat s výsledky se stávají součástí kompozice dokumentu v prohledávatelný index ve službě Azure Search. Data Source jako Azure datové sady a poté vloženo přes kanál indexování pomocí podle toho, co [integrované znalosti](cognitive-search-predefined-skills.md) potřebujete. Tato architektura je rozšiřitelný, takže pokud předdefinované dovednosti nestačí, můžete vytvořit a připojit [vlastních dovedností](cognitive-search-create-custom-skill-example.md) integrovat vlastní zpracování. Příklady může být vlastní entitu modulu nebo dokumentu třídění, cílení na konkrétní domény, jako jsou finance, vědecké publikace nebo lékařství.

> [!NOTE]
> Od 21. prosince 2018 se budou moct přidružit dovednosti Azure Search prostředku služeb Cognitive Services. To vám umožní spouštění poplatků za využití jeho dovedností. K tomuto datu také začneme pro extrakci image jako součást fáze hádání dokumentu. Extrakce textu z dokumentů se bude dál nabízet bez dalších poplatků.
>
> Provádění předdefinované dovednosti budou účtovat stávající [přejít ceny služeb Cognitive Services, platit jako můžete](https://azure.microsoft.com/pricing/details/cognitive-services/) . Ceny za extrakce Image se bude účtovat ceny verze preview a je popsaný na [stránce s cenami Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400). Přečtěte si [Další](cognitive-search-attach-cognitive-services.md).

## <a name="components-of-cognitive-search"></a>Součástí kognitivního vyhledávání

Kognitivní vyhledávání je funkce ve verzi preview [Azure Search](search-what-is-azure-search.md), která je dostupná na všech úrovních, střed USA – jih a západní Evropa. 

Kanál kognitivní vyhledávání je založena na [Azure Search *indexery* ](search-indexer-overview.md) , procházení zdroje dat a index začátku do konce zátěži. Dovednosti je teď připojený ke indexery, zachycení a rozšíření dokumentů podle zkušenostech definujete. Až indexovat, dostanete obsahu prostřednictvím vyhledávacích přes všechny [dotazování typy podporované službou Azure Search](search-query-overview.md).  Pokud jste ještě na indexery, tato část vás provede kroky.

### <a name="source-data-and-document-cracking-phase"></a>Zdrojová data a analýzy fáze dokumentu

Na začátku profilace, budete mít ukládání nestrukturovaných textových nebo netextový obsah (například image a soubory JPEG naskenovaného dokumentu). Data musí existovat v rámci služby úložiště dat Azure, který je přístupný pomocí indexeru. Indexery můžete "rozlousknutí" zdroj dokumenty k extrakci textu z zdrojová data.

![Fáze analýzy dokumentu](./media/cognitive-search-intro/document-cracking-phase-blowup.png "hádání dokumentu")

 Podporované zdroje patří Azure blob storage, Azure table storage, Azure SQL Database a Azure Cosmos DB. Textový obsah může být extrahována z následujících typů souborů: Soubory PDF, Wordu, PowerPointu a souborů CSV. Úplný seznam najdete v tématu [podporované formáty](search-howto-indexing-azure-blob-storage.md#supported-document-formats).

### <a name="cognitive-skills-and-enrichment-phase"></a>Kognitivní dovednosti a rozšiřování fáze

Rozšíření je prostřednictvím *kognitivní dovednosti* provádění atomických operací. Například až budete mít textový obsah z formátu PDF, můžete použít zjišťování rozpoznávání jazyka entity nebo extrakce klíčových frází vytvoří nové pole v indexu, které nejsou k dispozici nativně ve zdroji. Úplně se vynechá, se nazývá kolekce dovednosti, které používají ve vašem kanálu *dovednosti*.  

![Obohacení fáze](./media/cognitive-search-intro/enrichment-phase-blowup.png "fáze rozšíření")

Je na základě dovedností [předdefinované kognitivní dovednosti](cognitive-search-predefined-skills.md) nebo [vlastních dovedností](cognitive-search-create-custom-skill-example.md) poskytují a připojte se k zkušenostech. Dovedností může být minimální nebo velice složitých a určuje nejen typ zpracování, ale také pořadí operací. Dovedností a mapování polí definovaná jako součást indexer Určuje plně rozšíření kanálu. Další informace o stahování vším nadhled společně, naleznete v tématu [definování dovedností](cognitive-search-defining-skillset.md).

Interně kanálu vytvoří kolekce dokumentů, bohatších možností. Můžete se rozhodnout, jaké části bohatších možností dokumenty by měly být namapované na indexované pole v indexu vyhledávání. Pokud jste použili, extrakce klíčových frází a rozpoznání dovednosti entity, pak tato nová pole by se mohla stát součástí bohatších možností dokumentu a můžou být mapované na pole v indexu. Zobrazit [poznámky](cognitive-search-concept-annotations-syntax.md) získat další informace o vstupu a výstupu sestavy.

### <a name="search-index-and-query-based-access"></a>Index vyhledávání a přístupu na základě dotazu

Po dokončení zpracování se mají ve vyhledávacím korpusu služby skládající se z bohatších možností dokumenty, plně text prohledávat ve službě Azure Search. [Dotazování indexu](search-query-overview.md) je, jak vývojáři a uživatelé získat přístup k bohatších možností obsah vygenerovaný pomocí kanálu. 

![Index s ikonu hledání](./media/cognitive-search-intro/search-phase-blowup.png "Index vyhledávání ikonou ve tvaru")

Index je jako jakékoli jiné můžete například vytvořit pro Azure Search: můžete doplnit vlastní analyzátory, vyvolat dotazy vyhledávání přibližných shod, doplnili o funkce hledání filtrovaný nebo experimentovat s bodovací profily tvaru výsledky hledání.

Indexy jsou generovány z schématu indexu, který definuje pole, atributy, a jiných objektů, které jsou připojené do konkrétního indexu, jako je například profily skórování a mapy synonym. Index je definovaný a vyplní, můžete postupně indexovat ke sbírání zdroje nových a aktualizovaných dokumentů. Některé změny vyžadují úplné opětovné sestavení. Malá datová sada by měl používat, dokud je stabilní návrhu schématu. Další informace najdete v článku o tom, [jak znovu sestavit index](search-howto-reindex.md).

<a name="feature-concepts"></a>

## <a name="key-features-and-concepts"></a>Klíčové funkce a koncepty

| Koncept | Popis| Odkazy |
|---------|------------|-------|
| Dovednosti | Nejvyšší úrovně s názvem prostředek obsahující kolekci dovednosti. Dovedností se rozšíření kanálu. Vyvolá se při indexování pomocí indexeru. | [Definování dovedností](cognitive-search-defining-skillset.md) |
| Kognitivní dovednosti | Atomic transformace v rozšíření kanálu. Často je součást, která extrahuje nebo struktura odvodí a proto argumentech pochopíte vstupní data. Téměř vždy výstup je založený na textu a zpracování je zpracování přirozeného jazyka nebo zpracování obrázků, který extrahuje nebo generuje text z obrázků vstupy. Výstup z konkrétní dovednosti můžete namapováno na pole v indexu, nebo použít jako vstup pro příjem dat rozšíření. Předdefinované a poskytovány společností Microsoft nebo custom dovednost: vytvoření a nasazení vy. | [Předdefinované dovednosti](cognitive-search-predefined-skills.md) |
| Extrakce dat | Zahrnuje širokou škálu zpracování, ale vztahující se na kognitivního vyhledávání, dovednosti rozpoznávání pojmenovaných entit se obvykle používá k extrakci dat (entita) ze zdroje, tyto informace neposkytuje nativní. | [Dovednosti rozpoznávání pojmenovaných entit](cognitive-search-skill-named-entity-recognition.md)| 
| Zpracování obrázků | Odvodí z něj text pomocí bitové kopie, jako je například schopnost rozpoznat památek nebo extrahuje text z obrázku. Běžné příklady: OCR pro zrušení znaků ze souboru naskenovaného dokumentu (JPEG) nebo uznání ulici v fotografie obsahující znaménko ulice. | [Obrázek analýzy dovednosti](cognitive-search-skill-image-analysis.md) nebo [OCR dovedností](cognitive-search-skill-ocr.md)
| Zpracování přirozeného jazyka | Přehledy a informace o textovými vstupy zpracování textu. Rozpoznávání jazyka, analýzu subjektivního hodnocení a extrakci klíčových frází jsou dovednosti, které spadají pod zpracování přirozeného jazyka.  | [Klíč frázi extrakce dovednosti](cognitive-search-skill-keyphrases.md), [dovednosti detekce jazyka](cognitive-search-skill-language-detection.md), [dovednosti analýzy mínění](cognitive-search-skill-sentiment.md) |
| Analýzy dokumentu | Proces extrahování nebo vytvoření textového obsahu z jiné textové zdrojích během indexování. Optické rozpoznávání znaků (OCR) je příklad, ale obvykle odkazuje na základní indexer funkce jako indexeru extrahuje obsah ze souborů aplikace. Zdroj dat poskytuje umístění zdrojových souborů a poskytuje mapování polí, definice indexeru jsou obě klíčové faktory analýzy dokumentu. | Zobrazit [indexerů](search-indexer-overview.md) |
| Strukturování | Konsolidace fragmenty textu do větší struktury, nebo naopak rozdělit větší bloky textu do vhodnou velikostí pro další zpracování příjmu dat. | [Dovednosti Shaper](cognitive-search-skill-shaper.md), [Text fúze dovednosti](cognitive-search-skill-textmerger.md), [Text rozdělit dovedností](cognitive-search-skill-textsplit.md) |
| Bohatších možností dokumenty | Přechodné interní strukturu, není přímo přístupný v kódu. Bohatších možností dokumenty jsou generovány během zpracování, ale pouze konečný výstupy se uchovávají v indexu vyhledávání. Mapování polí určují, které datové prvky jsou přidány do indexu. | Zobrazit [přístupu k dokumentům bohatších možností](cognitive-search-tutorial-blob.md#access-enriched-document). |
| Indexovací modul |  Prohledávací modul, který extrahuje prohledávatelná data a metadata z externího zdroje dat a naplní index na základě mapování pole pole mezi indexem a zdroj dat pro hádání dokumentu. Pro obohacení kognitivního vyhledávání indexeru vyvolá dovedností a obsahuje mapování polí přiřazení rozšíření výstup do cílového pole v indexu. Definice indexeru obsahuje všechny pokyny a odkazy pro operace kanálu a kanál se vyvolá při spuštění indexeru. | [Indexery](search-indexer-overview.md) |
| Zdroj dat  | Objekt používaný indexerem pro připojení k externímu zdroji dat z podporovaných typů v Azure. | Zobrazit [indexerů](search-indexer-overview.md) |
| Index | Trvalé ve vyhledávacím korpusu služby ve službě Azure Search, od schématu indexu, který definuje pole struktury a využití. | [Indexy ve službě Azure Search](search-what-is-an-index.md) | 


## <a name="where-do-i-start"></a>Kde mám začít?

**Krok 1: Vytvořte službu vyhledávání v oblasti poskytuje rozhraní API** 

+ Západní střed USA
+ Středojižní USA
+ USA – východ
+ Východní USA 2
+ Západní USA 2
+ Kanada – střed
+ Západní Evropa
+ Velká Británie – jih
+ Severní Evropa
+ Brazílie – jih
+ Jihovýchodní Asie
+ Střed Indie
+ Austrálie – východ

**Krok 2: Praktické zkušenosti na hlavní server pracovního postupu**

+ [Rychlý start (portál)](cognitive-search-quickstart-blob.md)
+ [Kurzu práce (požadavků HTTP)](cognitive-search-tutorial-blob.md)
+ [Příklad vlastních dovedností (C#)](cognitive-search-create-custom-skill-example.md)

**Krok 3: Projděte si rozhraní API (REST jenom)**

V současné době jsou k dispozici pouze rozhraní REST API. Použití `api-version=2017-11-11-Preview` u všech požadavků. Použijte následující rozhraní API k vytvoření řešení kognitivního vyhledávání. Pouze dvě rozhraní API se přidá nebo rozšířené pro kognitivního vyhledávání. Další rozhraní API mají stejnou syntaxi jako všeobecně dostupné verze.

| REST API | Popis |
|-----|-------------|
| [Vytvoření zdroje dat](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | Prostředek identifikuje externího zdroje dat poskytuje zdroj dat použitých k vytvoření bohatších možností dokumenty.  |
| [Vytvoření dovedností (api-version = 2017-11-11-Preview)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Prostředek koordinace použití [předdefinované dovednosti](cognitive-search-predefined-skills.md) a [vlastního kognitivních dovedností](cognitive-search-custom-skill-interface.md) používané rozšíření kanálu během indexování. |
| [Vytvoření indexu](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Schéma vyjádření indexu Azure Search. Mapování polí v indexu pole ve zdrojových datech nebo pole vyrobenými během fáze rozšíření (například pole pro názvy organizací vytvořené rozpoznávání entit). |
| [Vytvoření indexeru (rozhraní api-version = 2017-11-11-Preview)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Zdroj definice součástí použitých při indexování: včetně zdroje dat, dovedností, přidružení pole ze zdroje a struktury pracovních dat na cílový index a index, samotného. Spuštění indexeru je aktivační událost pro příjem dat a rozšíření. Výstup je ve vyhledávacím korpusu služby na základě schématu indexu, naplní se zdrojovými daty, rozšiřují prostřednictvím dovednosti.  |

**Kontrolní seznam: Typický pracovní postup**

1. Dílčí Azure zdroje dat do reprezentativní vzorek. Indexování trvá určitou dobu tedy začít s datovou sadou malé, reprezentativní a pak ho přírůstkové sestavování zrání vašeho řešení.

1. Vytvoření [objektu zdroje dat](https://docs.microsoft.com/rest/api/searchservice/create-data-source) ve službě Azure Search zadat připojovací řetězec pro načítání dat.

1. Vytvoření [dovednosti](https://docs.microsoft.com/rest/api/searchservice/create-skillset) s kroky pro rozšíření.

1. Definovat [schéma indexu](https://docs.microsoft.com/rest/api/searchservice/create-index). *Pole* kolekce obsahuje pole z datového zdroje. Také by měl zástupných procedur na další pole pro uložení generované hodnoty pro obsah vytvořený během rozšiřování.

1. Definovat [indexer](https://docs.microsoft.com/rest/api/searchservice/create-skillset) odkazování na zdroj dat, dovednosti a index.

1. V rámci indexeru, přidejte *outputFieldMappings*. Tato část mapuje výstup z dovedností (v kroku 3) do vstupů polí ve schématu indexu (v kroku 4).

1. Odeslat *vytvoření indexeru* žádosti jste právě vytvořili (požadavek pomocí definice indexeru v textu požadavku POST) vyjádřit indexeru ve službě Azure Search. Tento krok je, jak spustit indexer, volání kanálu.

1. Spouštění dotazů k vyhodnocení výsledky a upravit kód pro aktualizace dovednosti, schéma nebo konfigurace indexeru.

1. [Resetování indexeru](search-howto-reindex.md) před znovu sestavit kanálu.

Další informace o konkrétní dotazy nebo problémy, najdete v části [tipy pro řešení potíží](cognitive-search-concept-troubleshooting.md).

## <a name="next-steps"></a>Další postup

+ [Dokumentace ke kognitivnímu vyhledávání](cognitive-search-resources-documentation.md)
+ [Rychlý start: Vyzkoušení kognitivního vyhledávání v portálu návodu](cognitive-search-quickstart-blob.md)
+ [Kurz: Další rozhraní API pro kognitivní vyhledávání](cognitive-search-tutorial-blob.md)
