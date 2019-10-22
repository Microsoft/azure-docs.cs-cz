---
title: Úvod do rozpoznávání vyhledávání a rozšíření AI – Azure Search
description: Extrakce obsahu, zpracování přirozeného jazyka (NLP) a zpracování obrázků pro vytváření prohledávatelných obsahu v Azure Search indexování pomocí schopností rozpoznávání a algoritmů AI.
manager: nitinme
author: HeidiSteen
services: search
ms.service: search
ms.topic: overview
ms.date: 08/15/2019
ms.author: heidist
ms.openlocfilehash: aa8ab3a3bc2f243e8b0ee404228926ef46bc46db
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "71266394"
---
# <a name="what-is-cognitive-search-in-azure-search"></a>Co je "rozpoznávání hledání" v Azure Search?

Rozpoznávání rozpoznávání je funkce AI v Azure Search, která se používá k extrakci textu z obrázků, objektů BLOB a dalších nestrukturovaných zdrojů dat – vylepšuje obsah, aby bylo možné snadněji prohledávat v indexu Azure Search. Extrakce a obohacení jsou implementovány prostřednictvím *dovedností rozpoznávání* připojených k kanálu indexování. Rozšíření AI jsou podporovaná následujícími způsoby: 

+ Mezi dovednosti při **zpracování přirozeného jazyka** patří [rozpoznávání entit](cognitive-search-skill-entity-recognition.md), rozpoznávání [jazyka](cognitive-search-skill-language-detection.md), [extrakce klíčových frází](cognitive-search-skill-keyphrases.md), manipulace s textem a [mínění zjišťování](cognitive-search-skill-sentiment.md). S těmito dovednostmi může nestrukturovaný text předpokládat nové formuláře, namapované jako prohledávatelné a filtrovatelné pole v indexu.

+ Mezi dovednosti **zpracování obrazu** patří [optické rozpoznávání znaků (OCR)](cognitive-search-skill-ocr.md) a identifikace [vizuálních funkcí](cognitive-search-skill-image-analysis.md), jako je detekce obličeje, interpretace obrázků, rozpoznávání obrázků (slavných lidi a orientačních bodů) nebo atributy jako barvy nebo orientace obrázku. Můžete vytvořit textová reprezentace obsahu obrázku, prohledávatelné pomocí všech možností dotazu Azure Search.

![Diagram kanálu vyhledávání pro rozpoznávání](./media/cognitive-search-intro/cogsearch-architecture.png "Přehled kanálu vyhledávání rozpoznávání")

Rozpoznávání dovedností v Azure Search jsou založené na modelech strojového učení v rozhraní API služeb Cognitive Services: [počítačové zpracování obrazu](https://docs.microsoft.com/azure/cognitive-services/computer-vision/) a [Analýza textu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview). 

Během fáze příjmu dat se použije přirozený jazyk a zpracování obrazu s výsledky, které se stanou součástí kompozice dokumentu v Azure Searchovém indexu v. Data se nastavují jako datová sada Azure a pak se provedou prostřednictvím kanálu indexování s použitím libovolných [integrovaných dovedností](cognitive-search-predefined-skills.md) , které potřebujete. Architektura je rozšiřitelná, takže pokud nejsou integrované dovednosti dostatečné, můžete vytvořit a připojit [vlastní dovednosti](cognitive-search-create-custom-skill-example.md) pro integraci vlastního zpracování. Příkladem může být modul vlastních entit nebo třídění dokumentů cílící na konkrétní doménu, jako je finance, vědecké publikace nebo lékařství.

> [!NOTE]
> Když rozbalíte rozsah zvýšením četnosti zpracování, přidáním dalších dokumentů nebo přidáním dalších algoritmů AI, budete muset [připojit fakturovatelné Cognitive Services prostředku](cognitive-search-attach-cognitive-services.md). Poplatky se účtují při volání rozhraní API v Cognitive Services a pro extrakci obrázků jako součást fáze pro vystavování dokumentů v Azure Search. Pro extrakci textu z dokumentů se neúčtují žádné poplatky.
>
> Při provádění integrovaných dovedností se účtují poplatky za stávající [Cognitive Services průběžných plateb](https://azure.microsoft.com/pricing/details/cognitive-services/). Ceny za extrakci obrázků jsou popsány na [stránce s cenami Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).

## <a name="when-to-use-cognitive-search"></a>Kdy použít vyhledávání rozpoznávání

Vyhledávání v rozpoznávání pomocí předem připravených dovedností je vhodné pro následující scénáře aplikací:

+ Naskenované dokumenty (JPEG), u kterých chcete provádět fulltextové vyhledávání. Můžete připojit dovednost optického rozpoznávání znaků (OCR) k identifikaci, extrakci a přijímání textu ze souborů JPEG.

+ Soubory PDF s kombinovaným obrázkem a textem Text v souborech PDF lze extrahovat během Azure Search indexování bez použití vyhledávání rozpoznávání, ale přidání image a zpracování přirozeného jazyka může často způsobit lepší výsledek, než poskytuje standardní indexování.

+ Vícejazyčný obsah, pro který chcete použít detekci jazyka a případně převod textu.

+ Nestrukturované nebo částečně strukturované dokumenty obsahující obsah, který má podstatný význam nebo kontext, který je ve větším dokumentu skrytý. 

  Objekty blob často obsahují velké tělo obsahu, které se zabalí do jednoho pole. Připojením obrazu a dovedností pro zpracování přirozeného jazyka k indexeru můžete vytvořit nové informace, které se Extant v nezpracovaném obsahu, ale ne jinak naplochit jako různá pole. Některé předem připravené schopnosti pro rozpoznávání řeči, které vám můžou pomáhat: extrakce klíčových frází, analýza mínění a rozpoznávání entit (lidé, organizace a umístění).

  Předem připravené dovednosti taky můžete použít k restrukturování obsahu prostřednictvím operací rozdělení textu, sloučení a tvaru.

Vlastní dovednosti můžou podporovat složitější scénáře, jako je rozpoznávání formulářů nebo detekce vlastních entit pomocí modelu, který zadáte a zabalíte do [vlastního webového rozhraní s dovednostmi](cognitive-search-custom-skill-interface.md). Mezi vlastní dovednosti patří i [Nástroj pro rozpoznávání formulářů](/azure/cognitive-services/form-recognizer/overview), integraci [rozhraní API Bingu pro vyhledávání entit](https://docs.microsoft.com/azure/search/cognitive-search-create-custom-skill-example)a [rozpoznávání vlastních entit](https://github.com/Microsoft/SkillsExtractorCognitiveSearch).


## <a name="component-pipeline-of-cognitive-search"></a>Kanál součásti pro hledání vnímání

Kanál hledání rozpoznávání je založen na [Azure Search *indexerů* ](search-indexer-overview.md) , které procházejí zdroji dat a poskytují ucelené zpracování indexu. Dovednosti jsou teď připojené k indexerům, zachycení a obohacení dokumentů podle dovednosti, které definujete. Po indexování můžete k obsahu přistupovat prostřednictvím žádostí o hledání prostřednictvím všech [typů dotazů podporovaných Azure Search](search-query-overview.md).  Pokud s indexery začínáte, Tato část vás provede jednotlivými kroky.

### <a name="step-1-connection-and-document-cracking-phase"></a>Krok 1: fáze připojení a odhalující dokumentu

Na začátku kanálu máte nestrukturovaný text nebo obsah, který není v textu (například obrázek a naskenovaný dokument JPEG). Data musí existovat ve službě úložiště dat Azure, ke které je možné přistupovat indexerem. Indexery mohou "prolomené" zdrojové dokumenty pro extrakci textu ze zdrojových dat.

![Fáze pro trhliny dokumentů](./media/cognitive-search-intro/document-cracking-phase-blowup.png "Trhliny dokumentů")

 Mezi podporované zdroje patří Azure Blob Storage, Azure Table Storage, Azure SQL Database a Azure Cosmos DB. Textový obsah se dá extrahovat z následujících typů souborů: PDF, Word, PowerPoint, soubory CSV. Úplný seznam najdete v tématu [podporované formáty](search-howto-indexing-azure-blob-storage.md#supported-document-formats).

### <a name="step-2-cognitive-skills-and-enrichment-phase"></a>Krok 2: vnímání dovedností a fáze obohacení

Obohacení je prostřednictvím *odbornosti rozpoznávání* provádět atomické operace. Například když máte textový obsah z formátu PDF, můžete použít detekci jazyka rozpoznávání entit nebo extrakci klíčových frází pro vytvoření nových polí v indexu, která nejsou nativně dostupná ve zdroji. Kolekce dovedností použité ve vašem kanálu se zcela nazývá *dovednosti*.  

![Fáze obohacení](./media/cognitive-search-intro/enrichment-phase-blowup.png "fáze obohacení")

Dovednosti je založen na [předdefinovaných dovednostech rozpoznávání](cognitive-search-predefined-skills.md) nebo na [vlastních dovednostech](cognitive-search-create-custom-skill-example.md) , které poskytnete, a připojení k dovednosti. Dovednosti může být minimální nebo velmi složitá a určuje nejen typ zpracování, ale také pořadí operací. Dovednosti plus mapování polí definovaná jako součást indexeru plně určuje kanál rozšíření. Další informace o tom, jak všechny tyto části pohromadě získat, najdete v tématu [definice dovednosti](cognitive-search-defining-skillset.md).

Kanál interně vytvoří kolekci obohacených dokumentů. Můžete určit, které části obohacených dokumentů by měly být namapovány na indexovaná pole v indexu vyhledávání. Pokud jste například použili extrakci klíčových frází a dovednosti pro rozpoznávání entit, pak se tato nová pole stanou součástí obohaceného dokumentu a mohou být mapována na pole v indexu. Další informace o vstupních/výstupních formách najdete v tématu [poznámky](cognitive-search-concept-annotations-syntax.md) .

#### <a name="add-a-knowledgestore-element-to-save-enrichments"></a>Přidat element knowledgeStore pro uložení obohacení

[Rozhraní REST API služby Search – Version = 2019-05-06](search-api-preview.md) rozšiřuje dovednosti s definicí knowledgeStore, která poskytuje připojení a projekce úložiště Azure, které popisují, jak jsou rozšíření uložená. 

Přidání znalostní báze do dovednosti vám dává možnost zastupovat na základě rozšíření pro jiné scénáře než fulltextové vyhledávání. Další informace najdete v [článku Co je znalostní báze Knowledge Store](knowledge-store-concept-intro.md).

### <a name="step-3-search-index-and-query-based-access"></a>Krok 3: hledání indexu a přístupu na základě dotazů

Po dokončení zpracování máte index vyhledávání sestávající z obohacených dokumentů, který je v Azure Search plněný pro hledání textu. [Dotazování indexu](search-query-overview.md) je způsob, jakým vývojáři a uživatelé přistupují k obohacenému obsahu vygenerovanému kanálem. 

![Index s ikonou hledání](./media/cognitive-search-intro/search-phase-blowup.png "Index s ikonou hledání")

Index je stejný jako jiný, který můžete pro Azure Search vytvořit: můžete doplnit vlastní analyzátory, vyvolat přibližné vyhledávací dotazy, přidat filtrované hledání nebo experimentovat s profily vyhodnocování, aby bylo možné změnit tvar výsledků hledání.

Indexy jsou generovány z schématu indexu definující pole, atributy a další konstrukce připojené ke konkrétnímu indexu, například profily vyhodnocování a mapy synonym. Po definování a naplnění indexu můžete postupně indexovat a vybírat nové a aktualizované zdrojové dokumenty. Některé změny vyžadují úplné opětovné sestavení. Měli byste použít malou datovou sadu, dokud nebude návrh schématu stabilní. Další informace najdete v článku o tom, [jak znovu sestavit index](search-howto-reindex.md).

<a name="feature-concepts"></a>

## <a name="key-features-and-concepts"></a>Klíčové funkce a koncepty

| Koncept | Popis| Odkazy |
|---------|------------|-------|
| dovednosti | Nejvyšší úroveň s názvem prostředek obsahující kolekci dovedností. Dovednosti je kanál pro obohacení. Vyvolá se při indexování indexerem. | [Definovat dovednosti](cognitive-search-defining-skillset.md) |
| Vnímání znalostí | Atomická transformace v kanálu rozšíření. Často se jedná o komponentu, která extrahuje nebo odvodí strukturu, a proto rozšiřuje vaše znalosti vstupních dat. Téměř vždy je výstupem na základě textu a zpracování je zpracování přirozeného jazyka nebo zpracování obrázků, které extrahuje nebo generuje text ze vstupů z obrázku. Výstup dovedností lze namapovat na pole v indexu nebo použít jako vstup pro obohacení pro podřízené služby. Dovednost je předdefinovaná a poskytnutá společností Microsoft, nebo vlastní: vytvořená a nasazená vámi. | [Předdefinované dovednosti](cognitive-search-predefined-skills.md) |
| Extrakce dat | Pokrývá širokou škálu zpracování, ale týká se rozpoznávání rozpoznávání entit, se většinou používá k extrakci dat (entity) ze zdroje, který tyto informace neposkytuje nativně. | [Dovednost pro rozpoznávání entit](cognitive-search-skill-entity-recognition.md)| 
| Zpracování obrázků | Odvodí text z obrázku, jako je například schopnost rozpoznat orientační bod, nebo extrahuje text z obrázku. Mezi běžné příklady patří rozpoznávání znaků pro zvedání znaků ze souboru naskenovaného dokumentu (JPEG) nebo rozpoznávání názvu ulice ve fotografii obsahující značku ulice. | Dovednost k [analýze obrázků](cognitive-search-skill-image-analysis.md) nebo [rozpoznávání OCR](cognitive-search-skill-ocr.md)
| Zpracování přirozeného jazyka | Zpracování textu pro přehledy a informace o vstupech textu. Rozpoznávání jazyka, analýza mínění a extrakce klíčových frází jsou dovednosti, které spadají do zpracování přirozeného jazyka.  | [Extrakce klíčových frází dovednost](cognitive-search-skill-keyphrases.md), [Rozpoznávání jazyka dovednosti](cognitive-search-skill-language-detection.md), [Analýza mínění dovednost](cognitive-search-skill-sentiment.md) |
| Trhliny dokumentů | Proces extrahování nebo vytváření textového obsahu z netextových zdrojů během indexování. Optické rozpoznávání znaků (OCR) je příklad, ale obecně odkazuje na základní funkce indexeru, protože indexer extrahuje obsah ze souborů aplikace. Zdroj dat, který poskytuje umístění zdrojových souborů, a definice indexeru, které poskytují mapování polí, jsou klíčové faktory při trhlinách dokumentů. | Viz [indexery](search-indexer-overview.md) |
| Shaping | Konsolidujte fragmenty textu do větší struktury nebo naopak rozdělte větší textové bloky do spravovatelné velikosti pro další zpracování pro příjem dat. | Dovednost [Shaper](cognitive-search-skill-shaper.md), dovednost pro [spojování textu](cognitive-search-skill-textmerger.md), dovednost [rozdělení textu](cognitive-search-skill-textsplit.md) |
| Obohacené dokumenty | Přechodná interní struktura generovaná během zpracování s konečným výstupem, který se odráží ve vyhledávacím indexu. Dovednosti určuje, které obohacení se provádí. Mapování polí určují, které datové prvky jsou přidány do indexu. Volitelně můžete vytvořit znalostní bázi pro uchování a prozkoumání obohacených dokumentů pomocí nástrojů, jako jsou Průzkumník služby Storage, Power BI nebo jakýkoli jiný nástroj, který se připojuje ke službě Azure Blob Storage. | Viz [znalostní báze Knowledge Store (Preview)](knowledge-store-concept-intro.md). |
| Indexovací modul |  Prohledávací modul, který extrahuje hledaná data a metadata z externího zdroje dat a naplní index založený na mapování polí mezi indexem a zdrojem dat pro trhliny dokumentů. Pro obohacení vyhledávacích hledání vyvolá indexer dovednosti a obsahuje mapování polí, které přidružuje výstup obohacení do cílových polí v indexu. Definice indexeru obsahuje všechny pokyny a odkazy na operace kanálu a kanál je vyvolán při spuštění indexeru. | [Indexery](search-indexer-overview.md) |
| Zdroj dat  | Objekt používaný indexerem pro připojení k externímu zdroji dat s podporovanými typy v Azure. | Viz [indexery](search-indexer-overview.md) |
| Index | Trvalý vyhledávací index v Azure Search sestavený z schématu indexu, který definuje strukturu a využití polí. | [Indexy v Azure Search](search-what-is-an-index.md) | 

<a name="where-do-i-start"></a>

## <a name="where-do-i-start"></a>Kde mám začít?

**Krok 1: [vytvoření prostředku Azure Search](search-create-service-portal.md)** 

**Krok 2: Vyzkoušejte si některé rychlé starty a příklady pro praktická prostředí**

+ [Rychlý Start (portál)](cognitive-search-quickstart-blob.md)
+ [Kurz (požadavky HTTP)](cognitive-search-tutorial-blob.md)
+ [Příklad: Vytvoření vlastní dovednosti pro vyhledávání rozpoznávání (C#)](cognitive-search-create-custom-skill-example.md)

Pro účely učení doporučujeme bezplatnou službu, ale počet bezplatných transakcí je omezený na 20 dokumentů za den. Pokud chcete spustit rychlý Start i kurz za jeden den, použijte menší sadu souborů (10 dokumentů), abyste se mohli vejít do obou cvičení, nebo odstranit indexer, který jste použili v rychlém startu nebo kurzu.

**Krok 3: Kontrola rozhraní API**

V žádostech nebo v sadě .NET SDK můžete použít REST `api-version=2019-05-06`. 

Tento krok používá rozhraní REST API k sestavení řešení pro hledání vnímání. Pro vyhledávání rozpoznávání se přidávají nebo rozšiřují jenom dvě rozhraní API. Další rozhraní API mají stejnou syntaxi jako všeobecně dostupné verze.

| Rozhraní REST API | Popis |
|-----|-------------|
| [Vytvoření zdroje dat](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | Prostředek identifikující externí zdroj dat, který poskytuje zdrojová data používaná k vytváření obohacených dokumentů.  |
| [Create dovednosti (API-Version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Toto rozhraní API je specifické pro rozpoznávání vyhledávání. Je to prostředek, který koordinuje používání [předdefinovaných dovedností](cognitive-search-predefined-skills.md) a [vlastní vnímání zkušeností](cognitive-search-custom-skill-interface.md) , které se v kanálu rozšíření používají během indexování. |
| [Vytvořit index](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Schéma, které vyjadřuje index Azure Search. Pole v indexu jsou mapována na pole ve zdrojových datech nebo na pole vyráběná během fáze obohacení (například pole pro názvy organizací vytvořená rozpoznáváním entit). |
| [Vytvoření indexeru (rozhraní API-Version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Prostředek definující součásti používané při indexování: včetně zdroje dat, dovednosti, přidružení polí ze zdrojových a zprostředkujících datových struktur do cílového indexu a samotného indexu. Spuštění indexeru je triggerem pro přijímání a obohacení dat. Výstupem je index hledání založený na schématu indexu, vyplněný zdrojovými daty, obohacený přes dovednosti. Toto existující rozhraní API je rozšířené pro scénáře hledání rozpoznávání s zahrnutím vlastnosti dovednosti. |

**Kontrolní seznam: Typický pracovní postup**

1. Oddělit vaše zdrojová data Azure do reprezentativního vzorku. Indexování trvá čas, takže začne používat malou reprezentativní datovou sadu a pak ji vytvoří přírůstkově jako vaše řešení.

1. Vytvořte [objekt zdroje dat](https://docs.microsoft.com/rest/api/searchservice/create-data-source) v Azure Search pro zadání připojovacího řetězce pro načtení dat.

1. Vytvořte [dovednosti](https://docs.microsoft.com/rest/api/searchservice/create-skillset) s kroky obohacení.

1. Definujte [schéma indexu](https://docs.microsoft.com/rest/api/searchservice/create-index). Kolekce *polí* obsahuje pole ze zdrojových dat. Měli byste také odhlásit další pole, která budou obsahovat vygenerované hodnoty pro obsah vytvořený během obohacení.

1. Definujte [indexer](https://docs.microsoft.com/rest/api/searchservice/create-skillset) , který odkazuje na zdroj dat, dovednosti a index.

1. V indexeru přidejte *outputFieldMappings*. Tento oddíl mapuje výstup z dovednosti (v kroku 3) na pole vstupy ve schématu indexu (v kroku 4).

1. Odešlete žádost o *Vytvoření indexeru* , kterou jste právě vytvořili (požadavek post s definicí indexeru v textu požadavku), aby se indexer do Azure Search vyjádřil. Tímto krokem spustíte indexer, vyvoláte kanál.

1. Spusťte dotazy pro vyhodnocení výsledků a upravte kód pro aktualizaci dovednosti, schématu nebo konfigurace indexeru.

1. Před opětovným sestavením kanálu [resetujte indexer](search-howto-reindex.md) .

Další informace o konkrétních otázkách a problémech najdete v tématu [tipy k odstraňování potíží](cognitive-search-concept-troubleshooting.md).

## <a name="next-steps"></a>Další kroky

+ [Dokumentace pro hledání vnímání](cognitive-search-resources-documentation.md)
+ [Rychlý Start: Vyzkoušejte si vyzkoušení vnímání v návodu k portálu](cognitive-search-quickstart-blob.md)
+ [Kurz: informace o rozhraních API pro vyhledávání rozpoznávání](cognitive-search-tutorial-blob.md)
+ [Přehled znalostní báze Knowledge Store](knowledge-store-concept-intro.md)
+ [Návod k znalostnímu obchodu](knowledge-store-howto.md)
