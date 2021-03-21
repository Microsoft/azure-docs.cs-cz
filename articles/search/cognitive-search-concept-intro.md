---
title: Koncepce obohacení AI
titleSuffix: Azure Cognitive Search
description: Extrakce obsahu, zpracování přirozeného jazyka (NLP) a zpracování obrazu slouží k vytváření prohledávatelných obsahu ve službě Azure Kognitivní hledání indexy s předem definovanými dovednostmi a vlastními algoritmy AI.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/18/2020
ms.custom: references_regions
ms.openlocfilehash: 22d7c1bbe03d8b3c0e3b6026c9bac039f0651548
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102037247"
---
# <a name="ai-enrichment-in-azure-cognitive-search"></a>Obohacení AI v Azure Kognitivní hledání

Obohacení AI je rozšíření [indexerů](search-indexer-overview.md) , která se dají použít k extrakci textu z obrázků, objektů BLOB a dalších nestrukturovaných zdrojů dat. Obohacení a extrakce usnadňují hledání obsahu ve výstupních objektech indexeru, a to buď pomocí [indexu hledání](search-what-is-an-index.md) , nebo z [úložiště znalostí](knowledge-store-concept-intro.md). 

Extrakce a obohacení jsou implementovány pomocí *dovedností rozpoznávání* připojených k kanálu řízenému indexerem. Pomocí integrovaných dovedností z Microsoftu nebo můžete vložit externí zpracování do [*vlastní dovednosti*](cognitive-search-create-custom-skill-example.md) , kterou vytvoříte. Příkladem vlastní dovednosti může být modul vlastních entit nebo třídění dokumentů cílící na konkrétní doménu, jako je finance, vědecké publikace nebo lékařství.

Mezi integrované dovednosti patří tyto kategorie: 

+ Mezi dovednosti při **zpracování přirozeného jazyka** patří [rozpoznávání entit](cognitive-search-skill-entity-recognition.md), rozpoznávání [jazyka](cognitive-search-skill-language-detection.md), [extrakce klíčových frází](cognitive-search-skill-keyphrases.md), manipulace s textem, [detekce mínění](cognitive-search-skill-sentiment.md)a [zjišťování PII](cognitive-search-skill-pii-detection.md). S těmito dovednostmi jsou nestrukturované texty namapované jako prohledávatelný a filtrovatelné pole v indexu.

+ Mezi dovednosti **zpracování obrazu** patří [optické rozpoznávání znaků (OCR)](cognitive-search-skill-ocr.md) a identifikace [vizuálních funkcí](cognitive-search-skill-image-analysis.md), jako je detekce obličeje, interpretace obrázků, rozpoznávání obrázků (slavných lidi a orientačních bodů) nebo atributy, jako je například orientace obrázku. Tyto dovednosti vytvářejí textová reprezentace obsahu obrázku, takže je lze prohledávat pomocí možností dotazů v Azure Kognitivní hledání.

![Diagram kanálu obohacení](./media/cognitive-search-intro/cogsearch-architecture.png "Přehled kanálu obohacení")

Integrované dovednosti v Azure Kognitivní hledání jsou založené na předem vyškolených modelech strojového učení v rozhraní API služeb Cognitive Services: [počítačové zpracování obrazu](../cognitive-services/computer-vision/index.yml) a [Analýza textu](../cognitive-services/text-analytics/overview.md). Prostředek Cognitive Services můžete připojit, pokud chcete během zpracování obsahu tyto prostředky využít.

Během fáze příjmu dat se použije přirozený jazyk a zpracování obrazu s výsledky, které se stanou součástí kompozice dokumentu v prohledávatelných indexech v Azure Kognitivní hledání. Data se nastavují jako datová sada Azure a pak se provedou prostřednictvím kanálu indexování s použitím libovolných [integrovaných dovedností](cognitive-search-predefined-skills.md) , které potřebujete.  

## <a name="feature-availability"></a>Dostupnost funkcí

Rozšíření AI je dostupné v oblastech, kde jsou dostupné taky služby Azure Cognitive Services.  Aktuální dostupnost rozšíření AI můžete kontrolovat na stránce [produkty Azure dostupné v oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=search) .  Rozšíření AI je k dispozici ve všech podporovaných oblastech kromě těchto:

+ Austrálie – jihovýchod
+ Čína – sever 2
+ Norsko – východ
+ Německo – středozápad

Pokud je vaše vyhledávací služba umístěná v jedné z těchto oblastí, nebudete moct vytvářet a používat dovednosti, ale všechny ostatní funkce služby Search jsou dostupné a plně podporované.

## <a name="when-to-use-ai-enrichment"></a>Kdy použít obohacení AI

Měli byste zvážit použití vestavěných schopností rozpoznávání, pokud je nezpracovaný obsah nestrukturovaný text, obsah obrázku nebo obsah, který potřebuje rozpoznání a překlad jazyka. Použití AI prostřednictvím integrovaných přístupných znalostí může tento obsah odemknout, což zvyšuje jeho hodnotu a nástroj ve vašich aplikacích pro vyhledávání a datovou vědu. 

Kromě toho můžete zvážit přidání vlastní dovednosti, pokud máte otevřený zdrojový kód, třetí stranu nebo kód první strany, který byste chtěli do kanálu integrovat. Modely klasifikace, které identifikují charakteristiky nejdůležitějšími různých typů dokumentů, spadají do této kategorie, ale mohly by se použít všechny balíčky, které přidají k obsahu hodnotu.

### <a name="more-about-built-in-skills"></a>Další informace o integrovaných dovednostech

[Dovednosti](cognitive-search-defining-skillset.md) , která je sestavená pomocí integrovaných dovedností, je vhodná pro následující scénáře aplikací:

+ Naskenované dokumenty (JPEG), u kterých chcete provádět fulltextové vyhledávání. Můžete připojit dovednost optického rozpoznávání znaků (OCR) k identifikaci, extrakci a přijímání textu ze souborů JPEG.

+ Soubory PDF s kombinovaným obrázkem a textem Text v souborech PDF lze extrahovat během indexování bez použití kroků obohacení, ale přidání image a zpracování přirozeného jazyka může často způsobit lepší výsledek, než poskytuje standardní indexování.

+ Vícejazyčný obsah, pro který chcete použít detekci jazyka a případně převod textu.

+ Nestrukturované nebo částečně strukturované dokumenty obsahující obsah, který má podstatný význam nebo kontext, který je ve větším dokumentu skrytý. 

  Objekty blob často obsahují velký obsah, který se balí do jediného "pole". Připojením obrazu a dovedností pro zpracování přirozeného jazyka k indexeru můžete vytvořit nové informace, které se Extant v nezpracovaném obsahu, ale ne jinak naplochit jako různá pole. Některé předem připravené schopnosti pro rozpoznávání řeči, které vám můžou pomáhat: extrakce klíčových frází, analýza mínění a rozpoznávání entit (lidé, organizace a umístění).

  Kromě toho je možné pomocí integrovaných dovedností také změnit strukturu obsahu prostřednictvím operací rozdělení textu, sloučení a tvaru.

### <a name="more-about-custom-skills"></a>Další informace o vlastních dovednostech

Vlastní dovednosti můžou podporovat složitější scénáře, jako je rozpoznávání formulářů nebo detekce vlastních entit pomocí modelu, který zadáte a zabalíte do [vlastního webového rozhraní s dovednostmi](cognitive-search-custom-skill-interface.md). Mezi vlastní dovednosti patří i [Nástroj pro rozpoznávání formulářů](../cognitive-services/form-recognizer/overview.md), integraci [rozhraní API Bingu pro vyhledávání entit](./cognitive-search-create-custom-skill-example.md)a [rozpoznávání vlastních entit](https://github.com/Microsoft/SkillsExtractorCognitiveSearch).

## <a name="steps-in-an-enrichment-pipeline"></a>Kroky v kanálu rozšíření <a name="enrichment-steps"></a>

Kanál pro rozšíření je založený na [*indexerech*](search-indexer-overview.md). Indexery naplní index založený na mapování polí mezi indexem a zdrojem dat pro trhliny dokumentů. Dovednosti, teď připojené k indexerům, zachytit a rozšířit dokumenty podle definovaných dovednostiů. Po indexování můžete k obsahu přistupovat pomocí požadavků na hledání prostřednictvím všech [typů dotazů podporovaných službou Azure kognitivní hledání](search-query-overview.md).  Pokud s indexery začínáte, Tato část vás provede jednotlivými kroky.

### <a name="step-1-connection-and-document-cracking-phase"></a>Krok 1: fáze připojení a odhalující dokumentu

Na začátku kanálu máte nestrukturovaný text nebo netextový obsah (například obrázky, naskenované dokumenty nebo soubory JPEG). Data musí existovat ve službě úložiště dat Azure, ke které je možné přistupovat indexerem. Indexery mohou "prolomené" zdrojové dokumenty pro extrakci textu ze zdrojových dat. Odhalení dokumentů je proces extrakce nebo vytvoření textového obsahu z netextových zdrojů během indexování.

![Fáze pro trhliny dokumentů](./media/cognitive-search-intro/document-cracking-phase-blowup.png "trhliny dokumentů")

 Mezi podporované zdroje patří Azure Blob Storage, Azure Table Storage, Azure SQL Database a Azure Cosmos DB. Textový obsah se dá extrahovat z následujících typů souborů: PDF, Word, PowerPoint, soubory CSV. Úplný seznam najdete v tématu [podporované formáty](search-howto-indexing-azure-blob-storage.md#SupportedFormats). Indexování trvá čas, takže začne používat malou reprezentativní datovou sadu a pak ji vytvoří přírůstkově jako vaše řešení.

### <a name="step-2-cognitive-skills-and-enrichment-phase"></a>Krok 2: vnímání dovedností a fáze obohacení

Obohacení se provede *rozpoznáváním dovedností* , které provádějí atomické operace. Když například rozložili PDF, můžete použít rozpoznávání entit, rozpoznávání jazyka nebo extrakci klíčových frází a vytvořit tak nová pole v indexu, která nejsou nativně dostupná ve zdroji. Kolekce dovedností použité ve vašem kanálu se zcela nazývá *dovednosti*.  

![Fáze obohacení](./media/cognitive-search-intro/enrichment-phase-blowup.png "fáze obohacení")

Dovednosti je založen na [integrovaných dovednostech rozpoznávání](cognitive-search-predefined-skills.md) nebo na [vlastních dovednostech](cognitive-search-create-custom-skill-example.md) , které poskytnete a připojujete se k dovednosti. Dovednosti může být minimální nebo velmi složitá a určuje nejen typ zpracování, ale také pořadí operací. Dovednosti plus mapování polí definovaná jako součást indexeru plně určuje kanál rozšíření. Další informace o tom, jak všechny tyto části pohromadě získat, najdete v tématu [definice dovednosti](cognitive-search-defining-skillset.md).

Kanál interně vytvoří kolekci obohacených dokumentů. Můžete určit, které části obohacených dokumentů by měly být namapovány na indexovaná pole v indexu vyhledávání. Pokud jste například použili extrakci klíčových frází a dovednosti pro rozpoznávání entit, tato nová pole se stanou součástí obohaceného dokumentu a mohou být mapována na pole v indexu. Další informace o vstupních/výstupních formách najdete v tématu [poznámky](cognitive-search-concept-annotations-syntax.md) .

#### <a name="add-a-knowledgestore-element-to-save-enrichments"></a>Přidat element knowledgeStore pro uložení obohacení

[Search REST API-Version = 2020-06-30](/rest/api/searchservice/) rozšiřuje dovednosti s `knowledgeStore` definicí, která poskytuje připojení a projekce úložiště Azure, které popisují, jak jsou rozšíření uložená. To je navíc k vašemu indexu. V standardním kanálu AI jsou obohacené dokumenty přechodné, používané jenom při indexování a pak se zahodí. Díky znalostnímu obchodu jsou rozšířené dokumenty zachované. Další informace najdete v [článku znalostní báze Knowledge Store](knowledge-store-concept-intro.md).

### <a name="step-3-search-index-and-query-based-access"></a>Krok 3: hledání indexu a přístupu na základě dotazů

Po dokončení zpracování máte index vyhledávání sestávající z obohacených dokumentů, což je plně náročné na hledání textu v Azure Kognitivní hledání. [Dotazování indexu](search-query-overview.md) je způsob, jakým vývojáři a uživatelé přistupují k obohacenému obsahu vygenerovanému kanálem. 

![Index s ikonou hledání](./media/cognitive-search-intro/search-phase-blowup.png "Index s ikonou hledání")

Index je stejný jako jiný, který můžete pro Azure Kognitivní hledání vytvořit: můžete doplnit vlastní analyzátory, vyvolat přibližné vyhledávací dotazy, přidat filtrované hledání nebo experimentovat s profily vyhodnocování, abyste mohli změnit tvar výsledků hledání.

Indexy jsou generovány z schématu indexu definující pole, atributy a další konstrukce připojené ke konkrétnímu indexu, například profily vyhodnocování a mapy synonym. Po definování a naplnění indexu můžete postupně indexovat a vybírat nové a aktualizované zdrojové dokumenty. Některé změny vyžadují úplné opětovné sestavení. Měli byste použít malou datovou sadu, dokud nebude návrh schématu stabilní. Další informace najdete v článku o tom, [jak znovu sestavit index](search-howto-reindex.md).

**Kontrolní seznam: Typický pracovní postup**

1. Oddělit vaše zdrojová data Azure do reprezentativního vzorku. Indexování trvá čas, takže začne používat malou reprezentativní datovou sadu a pak ji vytvoří přírůstkově jako vaše řešení.

1. Vytvoření [objektu zdroje dat](/rest/api/searchservice/create-data-source) v Azure kognitivní hledání k zadání připojovacího řetězce pro načtení dat.

1. Vytvořte [dovednosti](/rest/api/searchservice/create-skillset) s kroky obohacení.

1. Definujte [schéma indexu](/rest/api/searchservice/create-index). Kolekce *polí* obsahuje pole ze zdrojových dat. Měli byste také odhlásit další pole, která budou obsahovat vygenerované hodnoty pro obsah vytvořený během obohacení.

1. Definujte [indexer](/rest/api/searchservice/create-indexer) , který odkazuje na zdroj dat, dovednosti a index.

1. V indexeru přidejte *outputFieldMappings*. Tento oddíl mapuje výstup z dovednosti (v kroku 3) na pole vstupy ve schématu indexu (v kroku 4).

1. Odešlete žádost o *Vytvoření indexeru* , kterou jste právě vytvořili (požadavek post s definicí indexeru v textu požadavku), který zařadí indexer do Azure kognitivní hledání. Tímto krokem spustíte indexer, vyvoláte kanál.

1. Spusťte dotazy pro vyhodnocení výsledků a upravte kód pro aktualizaci dovednosti, schématu nebo konfigurace indexeru.

1. Před opětovným sestavením kanálu [resetujte indexer](search-howto-reindex.md) .

## <a name="next-steps"></a>Další kroky

+ [Odkazy na dokumentaci pro obohacení AI](cognitive-search-resources-documentation.md)
+ [Příklad: Vytvoření vlastní dovednosti pro rozšíření AI (C#)](cognitive-search-create-custom-skill-example.md)
+ [Rychlý Start: Vyzkoušejte obohacení AI na portálu procházením](cognitive-search-quickstart-blob.md)
+ [Kurz: informace o rozhraních API pro obohacení AI](cognitive-search-tutorial-blob.md)
+ [Knowledge Store](knowledge-store-concept-intro.md)
+ [Vytvoření úložiště znalostí v REST](knowledge-store-create-rest.md)
+ [Tipy pro řešení potíží](cognitive-search-concept-troubleshooting.md)
