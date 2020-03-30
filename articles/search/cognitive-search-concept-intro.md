---
title: Úvod do obohacení umělou a icí
titleSuffix: Azure Cognitive Search
description: Extrakce obsahu, zpracování přirozeného jazyka (NLP) a zpracování obrazu se používají k vytvoření prohledávatelného obsahu v indexech Azure Cognitive Search s předdefinovanými kognitivními dovednostmi i vlastními algoritmy AI.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/24/2020
ms.openlocfilehash: a41dcc9c7ec86f41c64a69ea1aba762b960b2633
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283017"
---
# <a name="getting-started-with-ai-enrichment"></a>Začínáme s obohacením umělou aikou

Obohacení ai je možnost azure kognitivní vyhledávání indexování slouží k extrahování textu z obrázků, objektů BLOB a dalších nestrukturovaných zdrojů dat. Obohacení a extrakce, aby váš obsah více prohledávatelné v [indexu](search-what-is-an-index.md) nebo [znalostní obchod](knowledge-store-concept-intro.md). Extrakce a obohacení jsou implementovány pomocí *kognitivních dovedností* připojených k indexovacímu kanálu. Kognitivní dovednosti zabudované do služby spadají do těchto kategorií: 

+ **Dovednosti zpracování přirozeného jazyka** zahrnují [rozpoznávání entit](cognitive-search-skill-entity-recognition.md), [detekci jazyka](cognitive-search-skill-language-detection.md), [extrakci klíčových frází](cognitive-search-skill-keyphrases.md), manipulaci s textem, [detekci mínění](cognitive-search-skill-sentiment.md)a [detekci pii](cognitive-search-skill-pii-detection.md). S těmito dovednostmi je nestrukturovaný text mapován jako prohledávatelná a filtrovatelná pole v indexu.

+ **Dovednosti v oblasti zpracování obrazu** zahrnují optické rozpoznávání znaků [(OCR)](cognitive-search-skill-ocr.md) a identifikaci [vizuálních funkcí](cognitive-search-skill-image-analysis.md), jako je detekce obličeje, interpretace obrazu, rozpoznávání obrazu (slavní lidé a orientační body) nebo atributy, jako je orientace obrazu. Tyto dovednosti vytvořit textové reprezentace obsahu obrázku, takže je prohledávat pomocí možností dotazu Azure Cognitive Search.

![Diagram kanálu obohacení](./media/cognitive-search-intro/cogsearch-architecture.png "přehled kanálu obohacení")

Kognitivní dovednosti v Azure Cognitive Search jsou založené na předem vyškolených modelech strojového učení v řešení api kognitivních služeb: [počítačové vidění](https://docs.microsoft.com/azure/cognitive-services/computer-vision/) a [analýza textu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview). 

Přirozené zpracování jazyka a obrazu se aplikuje během fáze ingestování dat, přičemž výsledky se stanou součástí složení dokumentu v indexu s prohledávatelným způsobem v Azure Cognitive Search. Data se získávají jako sada dat Azure a pak se prosazují kanálem indexování pomocí [předdefinovaných dovedností,](cognitive-search-predefined-skills.md) které potřebujete. Architektura je rozšiřitelná, takže pokud integrované dovednosti nejsou dostatečné, můžete vytvořit a připojit [vlastní dovednosti](cognitive-search-create-custom-skill-example.md) pro integraci vlastního zpracování. Příkladem může být modul vlastní entity nebo klasifikátor dokumentů zaměřený na určitou doménu, jako jsou finance, vědecké publikace nebo medicína.

## <a name="when-to-use-ai-enrichment"></a>Kdy použít obohacení umělou aiu

Pokud je nezpracovaným obsahem nestrukturovaný text, obsah obrázku nebo obsah, který vyžaduje detekci a překlad jazyka, měli byste zvážit použití integrovaných kognitivních dovedností. Použití ai prostřednictvím vestavěných kognitivních dovedností může odemknout tento obsah a zvýšit jeho hodnotu a užitečnost ve vašich vyhledávacích a datových aplikacích. 

Kromě toho můžete zvážit přidání vlastní dovednosti, pokud máte open source, kód třetí strany nebo první strany, které chcete integrovat do kanálu. Modely klasifikace, které identifikují významné charakteristiky různých typů dokumentů, spadají do této kategorie, ale jakýkoli balíček, který přidává hodnotu obsahu, by mohl být použit.

### <a name="more-about-built-in-skills"></a>Více o vestavěných dovednostech

Sada [dovedností,](cognitive-search-defining-skillset.md) která je sestavena pomocí předdefinovaných dovedností, je vhodná pro následující scénáře aplikací:

+ Naskenované dokumenty (JPEG), které chcete prohledávat v plném textu. Můžete připojit dovednost optického rozpoznávání znaků (OCR) k identifikaci, extrahování a ingestování textu ze souborů JPEG.

+ PDF s kombinovaným obrazem a textem. Text v PDF lze extrahovat během indexování bez použití kroků obohacení, ale přidání obrazu a zpracování přirozeného jazyka může často přinést lepší výsledek, než poskytuje standardní indexování.

+ Vícejazyčný obsah, proti kterému chcete použít detekci jazyka a případně překlad textu.

+ Nestrukturované nebo částečně strukturované dokumenty obsahující obsah, který má vlastní význam nebo kontext skrytý ve větším dokumentu. 

  Zejména objekty BLOB často obsahují velké množství obsahu, který je zabalen do jediného "pole". Připojením dovedností zpracování obrázků a přirozeného jazyka k indexeru můžete vytvořit nové informace, které jsou existující v nezpracovaném obsahu, ale jinak se neobjeví jako odlišná pole. Některé integrované kognitivní dovednosti připravené k použití, které mohou pomoci: extrakce klíčových frází, analýza mínění a rozpoznávání entit (osoby, organizace a umístění).

  Předdefinované dovednosti lze navíc použít restrukturalizovat obsah prostřednictvím operací rozdělení textu, sloučení a tvaru.

### <a name="more-about-custom-skills"></a>Další informace o vlastních dovednostech

Vlastní dovednosti mohou podporovat složitější scénáře, jako je rozpoznávání formulářů nebo detekce vlastních entit pomocí modelu, který zadáte, a zalomit ve [vlastním webovém rozhraní dovedností](cognitive-search-custom-skill-interface.md). Mezi příklady vlastních dovedností patří [rozpoznávání formulářů](/azure/cognitive-services/form-recognizer/overview), integrace [rozhraní API pro vyhledávání entit Bingu](https://docs.microsoft.com/azure/search/cognitive-search-create-custom-skill-example)a [rozpoznávání vlastních entit](https://github.com/Microsoft/SkillsExtractorCognitiveSearch).


## <a name="steps-in-an-enrichment-pipeline"></a>Kroky v kanálu obohacování

Kanál obohacení je založen na [*indexery*](search-indexer-overview.md). Indexery naplní index na základě mapování pole-pole mezi indexem a zdrojem dat pro listování dokumentu. Dovednosti, které jsou nyní připojeny k indexerům, zachycují a obohacují dokumenty podle sad dovedností, které definujete. Po indexování můžete přistupovat k obsahu prostřednictvím požadavků na vyhledávání prostřednictvím všech [typů dotazů podporovaných službou Azure Cognitive Search](search-query-overview.md).  Pokud jste novým indexery, tato část vás provede kroky.

### <a name="step-1-connection-and-document-cracking-phase"></a>Krok 1: Fáze propojování připojení a prolomení dokumentů

Na začátku kanálu máte nestrukturovaný textový nebo netextový obsah (například obrázky, naskenované dokumenty nebo soubory JPEG). Data musí existovat ve službě úložiště dat Azure, ke které má přístup indexer. Indexery mohou "rozlousknout" zdrojové dokumenty a extrahovat text ze zdrojových dat. Rozluštit dokumenty je proces extrahování nebo vytváření textového obsahu z netextových zdrojů během indexování.

![Fáze prolomení dokumentů](./media/cognitive-search-intro/document-cracking-phase-blowup.png "dokument praskání")

 Mezi podporované zdroje patří úložiště objektů blob Azure, úložiště tabulek Azure, Azure SQL Database a Azure Cosmos DB. Textový obsah lze extrahovat z následujících typů souborů: SOUBORY PDF, Word, PowerPoint, CSV soubory. Úplný seznam naleznete v tématu [Podporované formáty](search-howto-indexing-azure-blob-storage.md#supported-document-formats). Indexování nějakou dobu trvá, takže začněte s malou reprezentativní datovou sadou a pak ji postupně vytvářejte, jakmile vaše řešení zraje.

### <a name="step-2-cognitive-skills-and-enrichment-phase"></a>Krok 2: Kognitivní dovednosti a fáze obohacení

Obohacení se provádí s *kognitivními dovednostmi* provádějícími atomové operace. Například po popraskaném PDF můžete použít rozpoznávání entit, detekci jazyka nebo extrakci klíčových frází k vytvoření nových polí v indexu, která nejsou ve zdroji nativně k dispozici. Dohromady, sbírka dovedností používaných ve vašem potrubí se nazývá *skillset*.  

![Fáze obohacování](./media/cognitive-search-intro/enrichment-phase-blowup.png "fáze obohacování")

Sada dovedností je založena na [vestavěných kognitivních dovednostech](cognitive-search-predefined-skills.md) nebo [vlastních dovednostech,](cognitive-search-create-custom-skill-example.md) které poskytujete a připojujete se k dovednostní sadě. Skillset může být minimální nebo velmi složité a určuje nejen typ zpracování, ale také pořadí operací. Skillset plus mapování polí definované jako součást indexeru plně určuje kanál obohacení. Další informace o vytažení všech těchto částí dohromady naleznete [v tématu Definování sady dovedností](cognitive-search-defining-skillset.md).

Interně potrubí generuje kolekci obohacené dokumenty. Můžete se rozhodnout, které části obohacených dokumentů by měly být mapovány na indexovatelná pole v indexu vyhledávání. Pokud jste například použili extrakci klíčových frází a dovednosti rozpoznávání entit, tato nová pole by se stala součástí obohaceného dokumentu a mohla by být mapována na pole v indexu. Další informace o formacích vstupů a výstupů najdete [v tématu Poznámky.](cognitive-search-concept-annotations-syntax.md)

#### <a name="add-a-knowledgestore-element-to-save-enrichments"></a>Přidání prvku knowledgeStore pro uložení obohacení

[Hledání REST api-version=2019-05-06-Preview](search-api-preview.md) rozšiřuje skillsets s `knowledgeStore` definicí, která poskytuje připojení úložiště Azure a projekce, které popisují, jak jsou uloženy obohacení. To je navíc k indexu. Ve standardním kanálu AI jsou rozšířené dokumenty přechodné, používané pouze během indexování a poté zahozeny. Díky úložišti znalostí jsou zachovány obohacené dokumenty. Další informace naleznete v [tématu Knowledge Store (preview).](knowledge-store-concept-intro.md)

### <a name="step-3-search-index-and-query-based-access"></a>Krok 3: Index hledání a přístup založený na dotazech

Po dokončení zpracování máte index vyhledávání skládající se z obohacených dokumentů, plně prohledávatelný text v Azure Cognitive Search. [Dotazování indexu](search-query-overview.md) je způsob, jakým vývojáři a uživatelé přistupují k obohacenému obsahu generovanému kanálem. 

![Index s ikonou hledání](./media/cognitive-search-intro/search-phase-blowup.png "Index s ikonou hledání")

Index je jako každý jiný, který můžete vytvořit pro Azure Cognitive Search: můžete doplnit o vlastní analyzátory, vyvolat fuzzy vyhledávací dotazy, přidat filtrované vyhledávání nebo experimentovat s profily hodnocení změnit tvar výsledků hledání.

Indexy jsou generovány ze schématu indexu, který definuje pole, atributy a další konstrukce připojené k určitému indexu, jako jsou profily hodnocení a mapy synonym. Jakmile je index definován a naplněn, můžete indexovat postupně a vyzvednout nové a aktualizované zdrojové dokumenty. Některé úpravy vyžadují úplné obnovení. Měli byste použít malou sadu dat, dokud nebude návrh schématu stabilní. Další informace najdete v článku o tom, [jak znovu sestavit index](search-howto-reindex.md).

**Kontrolní seznam: Typický pracovní postup**

1. Podmnožinou zdrojových dat Azure do reprezentativní ukázky. Indexování nějakou dobu trvá, takže začněte s malou reprezentativní datovou sadou a pak ji postupně vytvářejte, jakmile vaše řešení zraje.

1. Vytvořte [objekt zdroje dat](https://docs.microsoft.com/rest/api/searchservice/create-data-source) v Azure Cognitive Search poskytnout připojovací řetězec pro načítání dat.

1. Vytvořte [sadu dovedností](https://docs.microsoft.com/rest/api/searchservice/create-skillset) s kroky obohacení.

1. Definujte [schéma indexu](https://docs.microsoft.com/rest/api/searchservice/create-index). Kolekce *Fields* obsahuje pole ze zdrojových dat. Měli byste také se zakázaným inzerováním z dalších polí pro uložení generovaných hodnot pro obsah vytvořený během obohacení.

1. Definujte [indexer](https://docs.microsoft.com/rest/api/searchservice/create-skillset) odkazující na zdroj dat, skillset a index.

1. V rámci indexeru přidejte *outputFieldMappings*. Tato část mapuje výstup z skillset (v kroku 3) na vstupní pole ve schématu indexu (v kroku 4).

1. Odeslat *požadavek vytvořit indexer,* který jste právě vytvořili (požadavek POST s definicí indexeru v těle požadavku) k vyjádření indexeru v Azure Cognitive Search. Tento krok je způsob spuštění indexeru s vyvoláním kanálu.

1. Spusťte dotazy k vyhodnocení výsledků a upravit kód pro aktualizaci skillsets, schéma nebo indexer konfigurace.

1. Před opětovnou sestavením kanálu [obnovte indexer.](search-howto-reindex.md)

## <a name="next-steps"></a>Další kroky

+ [Odkazy na dokumentaci k obohacení umělá ai.](cognitive-search-resources-documentation.md)
+ [Příklad: Vytvoření vlastní dovednosti pro obohacení umělou ai (C#)](cognitive-search-create-custom-skill-example.md)
+ [Úvodní příručka: Vyzkoušejte obohacení umělou ai v průchodu portálu](cognitive-search-quickstart-blob.md)
+ [Kurz: Další informace o api pro obohacení ai](cognitive-search-tutorial-blob.md)
+ [Knowledge Store (Preview)](knowledge-store-concept-intro.md)
+ [Vytvoření úložiště znalostí v REST](knowledge-store-create-rest.md)
+ [Rady pro řešení potíží](cognitive-search-concept-troubleshooting.md)
