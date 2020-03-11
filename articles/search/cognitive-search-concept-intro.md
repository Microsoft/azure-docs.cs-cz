---
title: Seznámení s rozšířením AI
titleSuffix: Azure Cognitive Search
description: Extrakce obsahu, zpracování přirozeného jazyka (NLP) a zpracování obrázků pro vytváření prohledávatelných obsahu v Azure Kognitivní hledání indexování pomocí dovedností rozpoznávání a algoritmů AI.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 0a4dd3247a9931de3ae2c699bdf7800407695c86
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2020
ms.locfileid: "79080211"
---
# <a name="introduction-to-ai-in-azure-cognitive-search"></a>Úvod do AI v Azure Kognitivní hledání

Rozšíření AI je schopnost indexování služby Azure Kognitivní hledání, která se používá k extrakci textu z obrázků, objektů BLOB a dalších nestrukturovaných zdrojů dat – vylepšuje obsah, aby bylo lépe prohledávatelné úložiště v indexu nebo ve znalostní bázi. Extrakce a obohacení jsou implementovány prostřednictvím *dovedností rozpoznávání* připojených k kanálu indexování. K rozpoznávání dovedností integrovaných do služby patří do těchto kategorií: 

+ Mezi dovednosti při **zpracování přirozeného jazyka** patří [rozpoznávání entit](cognitive-search-skill-entity-recognition.md), rozpoznávání [jazyka](cognitive-search-skill-language-detection.md), [extrakce klíčových frází](cognitive-search-skill-keyphrases.md), manipulace s textem, [detekce mínění](cognitive-search-skill-sentiment.md)a [zjišťování PII](cognitive-search-skill-pii-detection.md). S těmito dovednostmi může nestrukturovaný text předpokládat nové formuláře, namapované jako prohledávatelné a filtrovatelné pole v indexu.

+ Mezi dovednosti **zpracování obrazu** patří [optické rozpoznávání znaků (OCR)](cognitive-search-skill-ocr.md) a identifikace [vizuálních funkcí](cognitive-search-skill-image-analysis.md), jako je detekce obličeje, interpretace obrázků, rozpoznávání obrázků (slavných lidi a orientačních bodů) nebo atributy, jako je například orientace obrázku. Můžete vytvořit textová reprezentace obsahu obrázku, prohledávatelné pomocí všech možností dotazů v Azure Kognitivní hledání.

![Diagram kanálu obohacení](./media/cognitive-search-intro/cogsearch-architecture.png "Přehled kanálu obohacení")

Vnímání dovedností v Azure Kognitivní hledání jsou založené na předem vyškolených modelech strojového učení v rozhraní API služeb Cognitive Services: [počítačové zpracování obrazu](https://docs.microsoft.com/azure/cognitive-services/computer-vision/) a [Analýza textu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview). 

Během fáze příjmu dat se použije přirozený jazyk a zpracování obrazu s výsledky, které se stanou součástí kompozice dokumentu v prohledávatelných indexech v Azure Kognitivní hledání. Data se nastavují jako datová sada Azure a pak se provedou prostřednictvím kanálu indexování s použitím libovolných [integrovaných dovedností](cognitive-search-predefined-skills.md) , které potřebujete. Architektura je rozšiřitelná, takže pokud nejsou integrované dovednosti dostatečné, můžete vytvořit a připojit [vlastní dovednosti](cognitive-search-create-custom-skill-example.md) pro integraci vlastního zpracování. Příkladem může být modul vlastních entit nebo třídění dokumentů cílící na konkrétní doménu, jako je finance, vědecké publikace nebo lékařství.

> [!NOTE]
> Když rozbalíte rozsah zvýšením četnosti zpracování, přidáním dalších dokumentů nebo přidáním dalších algoritmů AI, budete muset [připojit fakturovatelné Cognitive Services prostředku](cognitive-search-attach-cognitive-services.md). Poplatky se účtují při volání rozhraní API v Cognitive Services a pro extrakci obrázků jako součást fáze pro vystavování dokumentů ve službě Azure Kognitivní hledání. Pro extrakci textu z dokumentů se neúčtují žádné poplatky.
>
> Při provádění integrovaných dovedností se účtují poplatky za stávající [Cognitive Services průběžných plateb](https://azure.microsoft.com/pricing/details/cognitive-services/). Ceny za extrakci imagí jsou popsané na [stránce s cenami za Azure kognitivní hledání](https://go.microsoft.com/fwlink/?linkid=2042400).

## <a name="when-to-use-cognitive-skills"></a>Kdy použít dovednosti v rozpoznávání

Měli byste zvážit použití vestavěných schopností rozpoznávání, pokud je nezpracovaný obsah nestrukturovaný text, obsah obrázku nebo obsah, který potřebuje rozpoznání a překlad jazyka. Použití AI prostřednictvím integrovaných přístupných znalostí může tento obsah odemknout, což zvyšuje jeho hodnotu a nástroj ve vašich aplikacích pro vyhledávání a datovou vědu. 

Kromě toho můžete zvážit přidání vlastní dovednosti, pokud máte otevřený zdrojový kód, třetí stranu nebo kód první strany, který byste chtěli do kanálu integrovat. Modely klasifikace, které identifikují charakteristiky nejdůležitějšími různých typů dokumentů spadají do této kategorie, ale můžou se použít i všechny balíčky, které přidávají hodnotu k obsahu.

### <a name="more-about-built-in-skills"></a>Další informace o integrovaných dovednostech

Dovednosti, která je sestavená pomocí integrovaných dovedností, je vhodná pro následující scénáře aplikací:

+ Naskenované dokumenty (JPEG), u kterých chcete provádět fulltextové vyhledávání. Můžete připojit dovednost optického rozpoznávání znaků (OCR) k identifikaci, extrakci a přijímání textu ze souborů JPEG.

+ Soubory PDF s kombinovaným obrázkem a textem Text v souborech PDF lze extrahovat během indexování bez použití kroků obohacení, ale přidání image a zpracování přirozeného jazyka může často způsobit lepší výsledek, než poskytuje standardní indexování.

+ Vícejazyčný obsah, pro který chcete použít detekci jazyka a případně převod textu.

+ Nestrukturované nebo částečně strukturované dokumenty obsahující obsah, který má podstatný význam nebo kontext, který je ve větším dokumentu skrytý. 

  Objekty blob často obsahují velké tělo obsahu, které se zabalí do jednoho pole. Připojením obrazu a dovedností pro zpracování přirozeného jazyka k indexeru můžete vytvořit nové informace, které se Extant v nezpracovaném obsahu, ale ne jinak naplochit jako různá pole. Některé předem připravené schopnosti pro rozpoznávání řeči, které vám můžou pomáhat: extrakce klíčových frází, analýza mínění a rozpoznávání entit (lidé, organizace a umístění).

  Kromě toho je možné pomocí integrovaných dovedností také změnit strukturu obsahu prostřednictvím operací rozdělení textu, sloučení a tvaru.

### <a name="more-about-custom-skills"></a>Další informace o vlastních dovednostech

Vlastní dovednosti můžou podporovat složitější scénáře, jako je rozpoznávání formulářů nebo detekce vlastních entit pomocí modelu, který zadáte a zabalíte do [vlastního webového rozhraní s dovednostmi](cognitive-search-custom-skill-interface.md). Mezi vlastní dovednosti patří i [Nástroj pro rozpoznávání formulářů](/azure/cognitive-services/form-recognizer/overview), integraci [rozhraní API Bingu pro vyhledávání entit](https://docs.microsoft.com/azure/search/cognitive-search-create-custom-skill-example)a [rozpoznávání vlastních entit](https://github.com/Microsoft/SkillsExtractorCognitiveSearch).


## <a name="components-of-an-enrichment-pipeline"></a>Součásti kanálu rozšíření

Kanál pro rozšíření je založený na [*indexerech*](search-indexer-overview.md) , které procházejí zdroji dat a poskytují ucelené zpracování indexu. Dovednosti jsou teď připojené k indexerům, zachycení a obohacení dokumentů podle dovednosti, které definujete. Po indexování můžete k obsahu přistupovat pomocí požadavků na hledání prostřednictvím všech [typů dotazů podporovaných službou Azure kognitivní hledání](search-query-overview.md).  Pokud s indexery začínáte, Tato část vás provede jednotlivými kroky.

### <a name="step-1-connection-and-document-cracking-phase"></a>Krok 1: fáze připojení a odhalující dokumentu

Na začátku kanálu máte nestrukturovaný text nebo obsah, který není v textu (například obrázek a naskenovaný dokument JPEG). Data musí existovat ve službě úložiště dat Azure, ke které je možné přistupovat indexerem. Indexery mohou "prolomené" zdrojové dokumenty pro extrakci textu ze zdrojových dat.

![Fáze pro trhliny dokumentů](./media/cognitive-search-intro/document-cracking-phase-blowup.png "trhliny dokumentů")

 Mezi podporované zdroje patří Azure Blob Storage, Azure Table Storage, Azure SQL Database a Azure Cosmos DB. Textový obsah se dá extrahovat z následujících typů souborů: PDF, Word, PowerPoint, soubory CSV. Úplný seznam najdete v tématu [podporované formáty](search-howto-indexing-azure-blob-storage.md#supported-document-formats).

### <a name="step-2-cognitive-skills-and-enrichment-phase"></a>Krok 2: vnímání dovedností a fáze obohacení

Obohacení je prostřednictvím *odbornosti rozpoznávání* provádět atomické operace. Například když máte textový obsah z formátu PDF, můžete použít detekci jazyka rozpoznávání entit nebo extrakci klíčových frází pro vytvoření nových polí v indexu, která nejsou nativně dostupná ve zdroji. Kolekce dovedností použité ve vašem kanálu se zcela nazývá *dovednosti*.  

![Fáze obohacení](./media/cognitive-search-intro/enrichment-phase-blowup.png "fáze obohacení")

Dovednosti je založen na [integrovaných dovednostech rozpoznávání](cognitive-search-predefined-skills.md) nebo na [vlastních dovednostech](cognitive-search-create-custom-skill-example.md) , které poskytnete a připojujete se k dovednosti. Dovednosti může být minimální nebo velmi složitá a určuje nejen typ zpracování, ale také pořadí operací. Dovednosti plus mapování polí definovaná jako součást indexeru plně určuje kanál rozšíření. Další informace o tom, jak všechny tyto části pohromadě získat, najdete v tématu [definice dovednosti](cognitive-search-defining-skillset.md).

Kanál interně vytvoří kolekci obohacených dokumentů. Můžete určit, které části obohacených dokumentů by měly být namapovány na indexovaná pole v indexu vyhledávání. Pokud jste například použili extrakci klíčových frází a dovednosti pro rozpoznávání entit, pak se tato nová pole stanou součástí obohaceného dokumentu a mohou být mapována na pole v indexu. Další informace o vstupních/výstupních formách najdete v tématu [poznámky](cognitive-search-concept-annotations-syntax.md) .

#### <a name="add-a-knowledgestore-element-to-save-enrichments"></a>Přidat element knowledgeStore pro uložení obohacení

[Search REST API-Version = 2019-05 -06-Preview](search-api-preview.md) rozšiřuje dovednosti s definicí `knowledgeStore`, která poskytuje připojení a projekce úložiště Azure, které popisují, jak jsou rozšíření uložená. 

Přidání znalostní báze do dovednosti vám dává možnost zastupovat na základě rozšíření pro jiné scénáře než fulltextové vyhledávání. Další informace najdete v [článku znalostní báze Knowledge Store (Preview)](knowledge-store-concept-intro.md).

### <a name="step-3-search-index-and-query-based-access"></a>Krok 3: hledání indexu a přístupu na základě dotazů

Po dokončení zpracování máte index vyhledávání sestávající z obohacených dokumentů, což je plně náročné na hledání textu v Azure Kognitivní hledání. [Dotazování indexu](search-query-overview.md) je způsob, jakým vývojáři a uživatelé přistupují k obohacenému obsahu vygenerovanému kanálem. 

![Index s ikonou hledání](./media/cognitive-search-intro/search-phase-blowup.png "Index s ikonou hledání")

Index je stejný jako jiný, který můžete pro Azure Kognitivní hledání vytvořit: můžete doplnit vlastní analyzátory, vyvolat přibližné vyhledávací dotazy, přidat filtrované hledání nebo experimentovat s profily vyhodnocování, abyste mohli změnit tvar výsledků hledání.

Indexy jsou generovány z schématu indexu definující pole, atributy a další konstrukce připojené ke konkrétnímu indexu, například profily vyhodnocování a mapy synonym. Po definování a naplnění indexu můžete postupně indexovat a vybírat nové a aktualizované zdrojové dokumenty. Některé změny vyžadují úplné opětovné sestavení. Měli byste použít malou datovou sadu, dokud nebude návrh schématu stabilní. Další informace najdete v článku o tom, [jak znovu sestavit index](search-howto-reindex.md).

<a name="feature-concepts"></a>

## <a name="key-features-and-concepts"></a>Klíčové funkce a koncepty

| Koncept | Popis| Odkazy |
|---------|------------|-------|
| Dovednosti | Nejvyšší úroveň s názvem prostředek obsahující kolekci dovedností. Dovednosti je kanál pro obohacení. Vyvolá se při indexování indexerem. | Viz [definice dovednosti](cognitive-search-defining-skillset.md) |
| Vnímání znalostí | Atomická transformace v kanálu rozšíření. Často se jedná o komponentu, která extrahuje nebo odvodí strukturu, a proto rozšiřuje vaše znalosti vstupních dat. Téměř vždy je výstupem na základě textu a zpracování je zpracování přirozeného jazyka nebo zpracování obrázků, které extrahuje nebo generuje text ze vstupů z obrázku. Výstup dovedností lze namapovat na pole v indexu nebo použít jako vstup pro obohacení pro podřízené služby. Dovednost je předdefinovaná a poskytnutá společností Microsoft, nebo vlastní: vytvořená a nasazená vámi. | [Předdefinované kognitivní dovednosti](cognitive-search-predefined-skills.md) |
| Extrakce dat | Pokrývá širokou škálu zpracování, ale vztahuje se na obohacení AI, většinou se k extrakci dat (entity) ze zdroje, který tyto informace neposkytuje, obvykle používá dovednost pro rozpoznávání entit. | Podívejte se na informace o [dovednostech rozpoznávání entit](cognitive-search-skill-entity-recognition.md) a [extrakci dokumentů (Preview)](cognitive-search-skill-document-extraction.md) .| 
| Zpracování obrázků | Odvodí text z obrázku, jako je například schopnost rozpoznat orientační bod, nebo extrahuje text z obrázku. Mezi běžné příklady patří rozpoznávání znaků pro zvedání znaků ze souboru naskenovaného dokumentu (JPEG) nebo rozpoznávání názvu ulice ve fotografii obsahující značku ulice. | Viz dovednost [analýzy obrázku](cognitive-search-skill-image-analysis.md) nebo [dovednost optického rozpoznávání znaků](cognitive-search-skill-ocr.md)
| Zpracování přirozeného jazyka | Zpracování textu pro přehledy a informace o vstupech textu. Rozpoznávání jazyka, analýza mínění a extrakce klíčových frází jsou dovednosti, které spadají do zpracování přirozeného jazyka.  | Přečtěte si téma [extrakce klíčových frází dovednosti](cognitive-search-skill-keyphrases.md), [rozpoznávání jazyka dovednosti](cognitive-search-skill-language-detection.md), [dovednosti v převádění textu](cognitive-search-skill-text-translation.md), [Analýza mínění dovednosti](cognitive-search-skill-sentiment.md), [dovednosti při detekci PII (Preview)](cognitive-search-skill-pii-detection.md) . |
| trhliny dokumentů | Proces extrahování nebo vytváření textového obsahu z netextových zdrojů během indexování. Optické rozpoznávání znaků (OCR) je příklad, ale obecně odkazuje na základní funkce indexeru, protože indexer extrahuje obsah ze souborů aplikace. Zdroj dat, který poskytuje umístění zdrojových souborů, a definice indexeru, které poskytují mapování polí, jsou klíčové faktory při trhlinách dokumentů. | Viz [indexery – přehled](search-indexer-overview.md) |
| Shaping | Konsolidujte fragmenty textu do větší struktury nebo naopak rozdělte větší textové bloky do spravovatelné velikosti pro další zpracování pro příjem dat. | Přečtěte si [Shaper dovednosti](cognitive-search-skill-shaper.md), [dovednosti při spojování textu](cognitive-search-skill-textmerger.md), [dovednosti při rozdělení textu](cognitive-search-skill-textsplit.md) |
| Obohacené dokumenty | Přechodná interní struktura generovaná během zpracování s konečným výstupem, který se odráží ve vyhledávacím indexu. Dovednosti určuje, které obohacení se provádí. Mapování polí určují, které datové prvky jsou přidány do indexu. Volitelně můžete vytvořit znalostní bázi pro uchování a prozkoumání obohacených dokumentů pomocí nástrojů, jako jsou Průzkumník služby Storage, Power BI nebo jakýkoli jiný nástroj, který se připojuje ke službě Azure Blob Storage. | Viz [znalostní báze Knowledge Store (Preview)](knowledge-store-concept-intro.md) |
| Indexer |  Prohledávací modul, který extrahuje hledaná data a metadata z externího zdroje dat a naplní index založený na mapování polí mezi indexem a zdrojem dat pro trhliny dokumentů. V případě obohacení AI vyvolá indexer dovednosti a obsahuje mapování polí, které přidružuje výstup obohacení do cílových polí v indexu. Definice indexeru obsahuje všechny pokyny a odkazy na operace kanálu a kanál je vyvolán při spuštění indexeru. S další konfigurací můžete znovu použít stávající zpracovaný obsah a provádět jenom ty kroky a dovednosti, které se změnily. | Viz [indexery](search-indexer-overview.md) a [přírůstkové obohacení (Preview)](cognitive-search-incremental-indexing-conceptual.md). |
| Zdroj dat  | Objekt používaný indexerem pro připojení k externímu zdroji dat s podporovanými typy v Azure. | Viz [indexery – přehled](search-indexer-overview.md) |
| Index | Trvalý vyhledávací index v Azure Kognitivní hledání sestavený z schématu indexu definujícího strukturu polí a použití. | Viz [Vytvoření základního indexu](search-what-is-an-index.md) | 
| Knowledge Store | Účet úložiště, ve kterém lze obohacené dokumenty nakládat a společně s indexem vyhledávání | Viz [Úvod do znalostní báze Knowledge Store](knowledge-store-concept-intro.md) | 
| Mezipaměť | Účet úložiště, který obsahuje výstup uložený v mezipaměti vytvořeným kanálem pro rozšíření. Povolení mezipaměti zachovává stávající výstup, který není ovlivněn změnami dovednosti nebo jiných komponent kanálu rozšíření. | Viz [přírůstkové obohacení](cognitive-search-incremental-indexing-conceptual.md) | 

<a name="where-do-i-start"></a>

## <a name="where-do-i-start"></a>Kde mám začít?

**Krok 1: [vytvoření prostředku Azure kognitivní hledání](search-create-service-portal.md)** 

**Krok 2: Vyzkoušejte si některé rychlé starty a příklady pro praktická prostředí**

+ [Rychlý Start (portál)](cognitive-search-quickstart-blob.md)
+ [Kurz (požadavky HTTP)](cognitive-search-tutorial-blob.md)
+ [Příklad: Vytvoření vlastní dovednosti pro rozšíření AI (C#)](cognitive-search-create-custom-skill-example.md)

Pro účely učení doporučujeme bezplatnou službu, ale počet bezplatných transakcí je omezený na 20 dokumentů za den. Pokud chcete spustit lekce víckrát, odstraňte a znovu vytvořte indexer, abyste obnovili čítač na nulu.

**Krok 3: Kontrola rozhraní API**

V žádostech nebo v sadě .NET SDK můžete použít REST `api-version=2019-05-06`. Pokud zkoumáte znalostní bázi Knowledge Store, místo toho použijte REST API verze Preview (`api-version=2019-05-06-Preview`).

Tento krok používá rozhraní REST API k sestavení řešení pro obohacení AI. Pro obohacení AI se přidávají nebo rozšiřují jenom dvě rozhraní API. Další rozhraní API mají stejnou syntaxi jako všeobecně dostupné verze.

| REST API | Popis |
|-----|-------------|
| [Vytvoření zdroje dat](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | Prostředek identifikující externí zdroj dat, který poskytuje zdrojová data používaná k vytváření obohacených dokumentů.  |
| [Create dovednosti (API-Version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Toto rozhraní API je specifické pro rozšíření AI. Jedná se o prostředek, který koordinuje používání [integrovaných dovedností](cognitive-search-predefined-skills.md) a [vlastní vnímání zkušeností](cognitive-search-custom-skill-interface.md) , které se v kanálu rozšíření používají během indexování. |
| [Vytvořit index](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Schéma, které vyjadřuje index služby Azure Kognitivní hledání. Pole v indexu jsou mapována na pole ve zdrojových datech nebo na pole vyráběná během fáze obohacení (například pole pro názvy organizací vytvořená rozpoznáváním entit). |
| [Vytvoření indexeru (rozhraní API-Version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Prostředek definující součásti používané při indexování: včetně zdroje dat, dovednosti, přidružení polí ze zdrojových a zprostředkujících datových struktur do cílového indexu a samotného indexu. Spuštění indexeru je triggerem pro přijímání a obohacení dat. Výstupem je index hledání založený na schématu indexu, vyplněný zdrojovými daty, obohacený přes dovednosti. Toto existující rozhraní API je rozšířené pro scénáře hledání rozpoznávání s zahrnutím vlastnosti dovednosti. |

**Kontrolní seznam: Typický pracovní postup**

1. Oddělit vaše zdrojová data Azure do reprezentativního vzorku. Indexování trvá čas, takže začne používat malou reprezentativní datovou sadu a pak ji vytvoří přírůstkově jako vaše řešení.

1. Vytvoření [objektu zdroje dat](https://docs.microsoft.com/rest/api/searchservice/create-data-source) v Azure kognitivní hledání k zadání připojovacího řetězce pro načtení dat.

1. Vytvořte [dovednosti](https://docs.microsoft.com/rest/api/searchservice/create-skillset) s kroky obohacení.

1. Definujte [schéma indexu](https://docs.microsoft.com/rest/api/searchservice/create-index). Kolekce *polí* obsahuje pole ze zdrojových dat. Měli byste také odhlásit další pole, která budou obsahovat vygenerované hodnoty pro obsah vytvořený během obohacení.

1. Definujte [indexer](https://docs.microsoft.com/rest/api/searchservice/create-skillset) , který odkazuje na zdroj dat, dovednosti a index.

1. V indexeru přidejte *outputFieldMappings*. Tento oddíl mapuje výstup z dovednosti (v kroku 3) na pole vstupy ve schématu indexu (v kroku 4).

1. Odešlete žádost o *Vytvoření indexeru* , kterou jste právě vytvořili (požadavek post s definicí indexeru v textu požadavku), který zařadí indexer do Azure kognitivní hledání. Tímto krokem spustíte indexer, vyvoláte kanál.

1. Spusťte dotazy pro vyhodnocení výsledků a upravte kód pro aktualizaci dovednosti, schématu nebo konfigurace indexeru.

1. Před opětovným sestavením kanálu [resetujte indexer](search-howto-reindex.md) .

Další informace o konkrétních otázkách a problémech najdete v tématu [tipy k odstraňování potíží](cognitive-search-concept-troubleshooting.md).

## <a name="next-steps"></a>Další kroky

+ [Odkazy na dokumentaci pro obohacení AI](cognitive-search-resources-documentation.md)
+ [Rychlý Start: Vyzkoušejte obohacení AI v návodu k portálu](cognitive-search-quickstart-blob.md)
+ [Kurz: informace o rozhraních API pro obohacení AI](cognitive-search-tutorial-blob.md)
+ [Znalostní báze Knowledge Store (Preview)](knowledge-store-concept-intro.md)
+ [Vytvoření úložiště znalostí v REST](knowledge-store-create-rest.md)
