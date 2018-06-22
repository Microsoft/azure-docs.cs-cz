---
title: Kognitivní hledat data extrakce, přirozeného jazyka zpracování ve službě Azure Search | Microsoft Docs
description: Extrakce dat, zpracování přirozeného jazyka (NLP) a zpracovávání obrázků k vytvoření s možností vyhledávání obsahu ve službě Azure Search pomocí kognitivní dovednosti indexování.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/04/2018
ms.author: heidist
ms.openlocfilehash: 64b4c0a315e206cd260f2f1108362e92f55d1843
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2018
ms.locfileid: "36304630"
---
# <a name="what-is-cognitive-search"></a>Co je kognitivní hledání?

Kognitivní vyhledávání je funkce preview [Azure Search](search-what-is-azure-search.md), k dispozici na všechny vrstvy v jihu USA a západní Evropa, který přidává AI indexování úloh. Extrakce dat, zpracování přirozeného jazyka a zpracovávání obrázků během indexování vyhledá latentní informace v nestrukturovaných nebo vyhledávat obsah a umožňuje vyhledávat ve službě Azure Search.

AI integrace se prostřednictvím *kognitivní dovednosti* který zlepšit komunikaci oddělení zdroj dokumentů prostřednictvím sekvenční procesy v trasu k indexu vyhledávání. 

![Diagram kanálu kognitivní vyhledávání](./media/cognitive-search-intro/cogsearch-architecture.png "kognitivní vyhledávání kanálu přehled")

Dovednosti použít během indexování mohou být předdefinované nebo vlastní:

+ [Předdefinované dovednosti](cognitive-search-predefined-skills.md) jsou založené na stejné AI algoritmy používané v kognitivní rozhraní API služby: [s názvem rozpoznávání Entity](cognitive-search-skill-named-entity-recognition.md), [klíč frázi extrakce](cognitive-search-skill-keyphrases.md), a [rozpoznávání znaků](cognitive-search-skill-ocr.md)jsou několika. 

+ [Vlastní dovednosti](cognitive-search-create-custom-skill-example.md) mohou být vytvořeny vámi pro žádné speciální zpracování, které požadujete. Příklady vlastní dovednosti může být vlastní entity modulu nebo dokumentu třídění cílení na konkrétní doméně, jako je třeba finanční, vědecké publikace nebo lékařství.

> [!NOTE]
> Kognitivní vyhledávání je ve verzi public preview a spuštění skillset aktuálně nabízí zadarmo. Cenu této funkce oznámíme později.

## <a name="components-of-cognitive-search"></a>Součástí kognitivní vyhledávání

Kanál kognitivní vyhledávání je založena na [Azure Search *indexery* ](search-indexer-overview.md) , procházení zdroje dat a zadejte zpracování index začátku do konce. Dovednosti jsou teď připojené k indexery, brání a rozšíření dokumentů podle skillset definovat. Jakmile indexované, můžete přístup k obsahu prostřednictvím požadavky search, přes všechny [dotaz typy podporované systémem Azure Search](search-query-overview.md).  Pokud jste ještě indexery, tato část vás provede kroky.

### <a name="source-data-and-document-cracking-phase"></a>Zdroje dat a dokumentu odhalení fáze

Na začátku kanálu, máte nestrukturovaných textu nebo jiné textové obsahu (například bitové kopie a naskenovaného dokumentu JPEG – soubory). Data, musí existovat ve službě úložiště dat Azure, který je přístupný pomocí indexer. Indexery můžete "rozlousknutí" zdroje dokumentů rozbalte text z zdrojová data.

![Dokument krakování fáze](./media/cognitive-search-intro/document-cracking-phase-blowup.png "hádání toho dokumentu")

 Podporované zdroje patří Azure blob storage, úložiště tabulek Azure, Azure SQL Database a Azure Cosmos DB. Textový obsah lze extrahovat z následujících typů souborů: soubory PDF, Word, PowerPoint, souborů CSV. Úplný seznam najdete v tématu [podporované formáty](search-howto-indexing-azure-blob-storage.md#supported-document-formats).

### <a name="cognitive-skills-and-enrichment-phase"></a>Kognitivní znalosti a obohacení fáze

Obohacení je prostřednictvím *kognitivní dovednosti* provádění atomické operací. Například až budete mít textového obsahu z PDF, můžete použít zjišťování jazyka rozpoznávání entity nebo klíče frázi extrakce k vytvoření nové pole v indexu, které nejsou k dispozici nativně ve zdroji. Zcela, se nazývá kolekce dovednosti používat při vaší kanálu *skillset*.  

![Fáze obohacení](./media/cognitive-search-intro/enrichment-phase-blowup.png "obohacení fáze")

Skillset je založena na [předdefinované kognitivní dovednosti](cognitive-search-predefined-skills.md) nebo [vlastní dovednosti](cognitive-search-create-custom-skill-example.md) poskytují a připojte se k skillset. Skillset může být minimální nebo vysoce komplexní a určí, ne jenom typ zpracování, ale také pořadí operací. Skillset plus mapování polí definován jako součást indexer plně určuje obohacení kanálu. Další informace o vyžádání všechny tyto údaje společně najdete v tématu [definovat skillset](cognitive-search-defining-skillset.md).

Interně kanálu generuje kolekci provádět rozšířené dokumentů. Můžete rozhodnout, jaké části provádět rozšířené dokumenty by měly být namapované indexovanou pole v indexu vyhledávání. Pokud jste použili klíče frází extrakce dovednosti a znalosti rozpoznávání entity, pak by tato nová pole se stanou součástí provádět rozšířené dokumentu a může být namapovaný na pole v indexu. V tématu [poznámky](cognitive-search-concept-annotations-syntax.md) Další informace o vytváření vstupu a výstupu.

### <a name="search-index-and-query-based-access"></a>Index vyhledávání a přístupu na základě dotazu

Po dokončení zpracování se máte vyhledávání svátek, který se skládá z provádět rozšířené dokumenty, plně text-s možností vyhledávání ve službě Azure Search. [Dotazování indexu](search-query-overview.md) je, jak vývojáři a uživatelé přistupovat k provádět rozšířené obsahu generovaného kanálu. 

![Index s ikonu hledání](./media/cognitive-search-intro/search-phase-blowup.png "Index s ikonu hledání")

Index je jako jakýchkoli jiných můžete vytvořit pro službu Azure Search: můžete doplnit s vlastní analyzátorů, vyvolání přibližné vyhledávací dotazy, přidejte filtrované hledání nebo experimentovat s vyhodnocování profily, aby změna tvaru výsledky hledání.

Indexy se generují z schématu indexu definující pole atributů, a jiných objektů, které připojena do konkrétního indexu, jako je například vyhodnocování profily a synonymum mapy. Jakmile index je definovaný a naplněny, můžete se indexu přírůstkově načíst nové a aktualizované zdrojové dokumenty. Některé změny vyžadují úplné opětovné sestavení. Dokud návrhu schématu je stabilní, měli byste použít malé datové sady. Další informace najdete v článku o tom, [jak znovu sestavit index](search-howto-reindex.md).

<a name="feature-concepts"></a>

## <a name="key-features-and-concepts"></a>Klíčové funkce a koncepty

| Koncept | Popis| Odkazy |
|---------|------------|-------|
| Skillset | Nejvyšší úrovně s názvem prostředek obsahující kolekci znalostí. Skillset je obohacení kanálu. Vyvolá se během indexování podle indexer. | [Definování skillset](cognitive-search-defining-skillset.md) |
| Kognitivní dovedností | Atomic transformací v kanálu obohacení. Často je komponenty, která extrahuje nebo odvodí struktura a proto rozšiřuje vaše pochopení nástroje vstupní data. Téměř vždy výstup je založený na textu a zpracování je přirozeného jazyka zpracování a zpracování bitové kopie, který extrahuje nebo generuje text z obrázku vstupy. Výstup z kvalifikaci můžete namapovat na pole v indexu nebo použít jako vstup pro příjem dat obohacení. Předdefinované a poskytované Microsoft nebo vlastní kvalifikaci: vytvoření a nasazení vy. | [Předdefinované dovednosti](cognitive-search-predefined-skills.md) |
| Extrakce dat | Obsahuje širokou škálu zpracování, ale týkající se kognitivní hledání, odborností rozpoznávání pojmenované entity se obvykle používá k extrahování dat (entita) ze zdroje, nativně neposkytuje tyto informace. | [Odborností rozpoznávání pojmenované Entity](cognitive-search-skill-named-entity-recognition.md)| 
| Zpracování obrázků | Odvodí, že text z obrázku, jako je například schopnost rozpoznat významné, nebo extrahuje text z obrázku. Běžné příklady rozpoznávání znaků pro zrušení znaků ze souboru naskenovaného dokumentu (JPEG) nebo rozpozná ulici v fotografie obsahující ulice přihlášení. | [Obrázek Analysis odborností](cognitive-search-skill-image-analysis.md) nebo [odborností rozpoznávání znaků](cognitive-search-skill-ocr.md)
| Zpracování přirozeného jazyka | Text zpracování statistiky a informace o text vstupy. Zjišťování jazyka, postojích analýzu a rozbalení klíčů frázi jsou znalosti, které spadají pod zpracování přirozeného jazyka.  | [Klíče frázi extrakce odborností](cognitive-search-skill-keyphrases.md), [jazyk detekce odborností](cognitive-search-skill-language-detection.md), [postojích Analysis dovedností](cognitive-search-skill-sentiment.md) |
| Dokument krakování | Proces extrahování nebo vytvoření textového obsahu z jiné textové zdrojů během indexování. Optické rozpoznávání znaků (rozpoznávání znaků) je příklad, ale obecně odkazuje na základní indexer funkce jako indexeru extrahuje obsah ze souborů aplikace. Zdroj dat zadáním umístění zdrojových souborů a definice indexer poskytuje mapování polí, jsou obě klíčových faktorů v dokumentu krakování. | V tématu [indexery](search-indexer-overview.md) |
| Shaping | Konsolidovat fragmenty textu do větší struktury, nebo naopak rozdělení větší bloky text do spravovat velikost pro další zpracování příjmu dat. | [Službě Shaper odborností](cognitive-search-skill-shaper.md), [Text fúze odborností](cognitive-search-skill-textmerger.md), [Text rozdělení dovedností](cognitive-search-skill-textsplit.md) |
| Provádět rozšířené dokumenty | Přechodné interní strukturou, není přímo přístupný v kódu. Provádět rozšířené dokumenty jsou generována během zpracování, ale pouze poslední výstupy zůstávají v indexu vyhledávání. Mapování polí určete, které datové prvky jsou přidány do indexu. | V tématu [přístup k dokumentům provádět rozšířené](cognitive-search-tutorial-blob.md#access-enriched-document). |
| Indexovací modul |  Prohledávací modul, který extrahuje prohledávatelná data a metadata z externího zdroje dat a naplní index na základě mapování pole pole mezi indexem a zdrojem dat. pro výkon dokumentu. Pro kognitivní vyhledávání enrichments indexeru vyvolá skillset a obsahuje mapování polí přidružení obohacení výstup do cílového pole v indexu. Indexer definice obsahuje všechny pokyny a odkazy pro operace kanálu a kanál volána při spuštění indexeru. | [Indexery](search-indexer-overview.md) |
| Zdroj dat  | Objekt používanou indexerem pro připojení k externímu zdroji dat podporovaných typů v Azure. | V tématu [indexery](search-indexer-overview.md) |
| Index | Trvalý vyhledávání souhrnu ve službě Azure Search, ze schématu indexu, který definuje strukturu pole a využití. | [Indexy ve službě Azure Search](search-what-is-an-index.md) | 


## <a name="where-do-i-start"></a>Kde mám začít?

**Krok 1: Vytvoření vyhledávací službu v oblasti poskytuje rozhraní API** 

+ Střed USA – jih
+ Západní Evropa

**Krok 2: Praktických zkušeností na hlavní server pracovního postupu**

+ [Rychlý start (portál)](cognitive-search-quickstart-blob.md)
+ [Kurz (počet požadavků HTTP)](cognitive-search-tutorial-blob.md)
+ [Příklad vlastní dovednosti (C#)](cognitive-search-create-custom-skill-example.md)

**Krok 3: Kontrola rozhraní API (REST pouze)**

V současné době jsou k dispozici pouze rozhraní REST API. Použití `api-version=2017-11-11-Preview` u všech požadavků. Použijte následující rozhraní API k vytvoření řešení kognitivní vyhledávání. Pouze dvě rozhraní API jsou přidány nebo o kognitivní vyhledávání. Jiná rozhraní API mají stejnou syntaxi jako všeobecně dostupná verze.

| REST API | Popis |
|-----|-------------|
| [Vytvoření zdroje dat](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | Prostředek identifikace externího zdroje dat poskytuje zdroje data použít k vytvoření provádět rozšířené dokumenty.  |
| [Vytvoření Skillset (api-version = 2017-11-11-Preview)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Prostředek koordinace použití [předdefinované dovednosti](cognitive-search-predefined-skills.md) a [vlastní kognitivní dovednosti](cognitive-search-custom-skill-interface.md) použít v kanálu obohacení během indexování. |
| [Vytvoření indexu](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Schéma vyjadřující indexu Azure Search. Mapování polí v indexu pole v zdroj dat nebo na pole vyrobila během fáze obohacení (například u pole pro organizaci názvy vytvořené entity rozpoznávání). |
| [Vytvoření Indexer (api-version = 2017-11-11-Preview)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Prostředek definování součástí použitých při indexování: včetně zdroje dat, skillset, přidružení pole ze zdroje a zprostředkující datové struktury pro cílový index a index sám sebe. Spuštění indexeru je aktivační událost pro přijímání dat a obohacení. Výstup je vyhledávání svátek, založené na schéma indexu, naplní se zdrojovými daty, obohacená známým prostřednictvím skillsets.  |

**Kontrolní seznam: Typický pracovní postup**

1. Podmnožina Azure zdroje dat do reprezentativní vzorek. Indexování trvá určitou dobu, začínat malé, reprezentativní datové sady a pak ho přírůstkové sestavování během existence řešení.

1. Vytvoření [objekt zdroje dat](https://docs.microsoft.com/rest/api/searchservice/create-data-source) ve službě Azure Search zadat připojovací řetězec pro načítání dat ze.

1. Vytvoření [skillset](https://docs.microsoft.com/rest/api/searchservice/create-skillset) s obohacení kroky.

1. Definování [schéma indexu](https://docs.microsoft.com/rest/api/searchservice/create-index). *Pole* kolekce obsahuje pole z zdrojová data. Se zakázaným inzerováním by také na další pole pro uložení generované hodnoty pro obsah vytvořený během obohacení.

1. Definování [indexer](https://docs.microsoft.com/rest/api/searchservice/create-skillset) odkazy na zdroje dat, skillset a index.

1. V rámci indexeru, přidejte *outputFieldMappings*. Tato část mapuje výstup z skillset (v kroku 3) na pole vstupy v schéma indexu (v kroku 4).

1. Odeslat *vytvořit Indexer* žádosti jste právě vytvořili (požadavek POST k definici indexer v textu požadavku) express indexeru ve službě Azure Search. Tento krok je, jak spustit indexer, volání kanálu.

1. Spuštění dotazů k vyhodnocení výsledky a upravit kód pro aktualizace skillsets, schema či indexer konfigurace.

1. [Resetovat indexer](search-howto-reindex.md) před znovu sestavit kanálu.

Další informace o konkrétní dotazy nebo problémy, najdete v tématu [tipy pro odstraňování potíží](cognitive-search-concept-troubleshooting.md).

## <a name="next-steps"></a>Další postup

+ [Dokumentace kognitivní vyhledávání](cognitive-search-resources-documentation.md)
+ [Rychlý úvod: Zkuste kognitivní vyhledávání v portálu návodu](cognitive-search-quickstart-blob.md)
+ [Kurz: Další kognitivní hledání rozhraní API](cognitive-search-tutorial-blob.md)
