---
title: Použití ai k pochopení dat úložiště objektů Blob
titleSuffix: Azure Cognitive Search
description: Přidejte sémantické zpracování přirozeného jazyka a analýzu image do objektů BLOB Azure pomocí kanálu obohacení umělou ai cítění v Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 591437eb3951164d53388b6164103948e9ad65e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73496442"
---
# <a name="use-ai-to-understand-blob-storage-data"></a>Použití ai k pochopení dat úložiště objektů Blob

Data v úložišti objektů Blob Azure jsou často různé nestrukturovaného obsahu, jako jsou obrázky, dlouhý text, PDF a dokumenty Office. Pomocí možností AI v Azure Cognitive Search, můžete pochopit a extrahovat cenné informace z objektů BLOB různými způsoby. Příklady použití AI na obsah objektů blob patří:

+ Extrahovat text z obrazů pomocí optického rozpoznávání znaků (OCR)
+ Vytvoření popisu scény nebo značek z fotografie
+ Zjišťování jazyka a překlad textu do různých jazyků
+ Zpracovat text s rozpoznáváním pojmenovaných entit (NER) za účelem vyhledání odkazů na osoby, data, místa nebo organizace 

I když možná budete potřebovat pouze jednu z těchto funkcí AI, je běžné kombinovat více z nich do stejného kanálu (například extrahování textu z naskenovaného obrázku a pak najít všechna data a místa, na které se odkazuje). 

Obohacení umělou ai vytváří nové informace, zachycené jako text, uložené v polích. Po obohacení můžete získat přístup k těmto informacím z indexu vyhledávání prostřednictvím fulltextového vyhledávání nebo odeslat rozšířené dokumenty zpět do úložiště Azure, abyste mohli napájet nová prostředí aplikací, která zahrnují zkoumání dat pro scénáře zjišťování nebo analýzy. 

V tomto článku se podíváme na obohacení ai prostřednictvím širokého objektivu, takže můžete rychle pochopit celý proces, od transformace nezpracovaných dat v blobch až po dotazovatelné informace v indexu vyhledávání nebo v úložišti znalostí.

## <a name="what-it-means-to-enrich-blob-data-with-ai"></a>Co to znamená "obohatit" data objektu blob s AI

*Obohacení umělou a id* je součástí architektury indexování Azure Cognitive Search, která integruje integrovanou umělou ai od Microsoftu nebo vlastní umělou ai, kterou poskytujete. Pomáhá implementovat komplexní scénáře, kde potřebujete zpracovat objekty BLOB (existující i nové, jakmile přijdou nebo jsou aktualizovány), otevřou všechny formáty souborů, aby extrahovaly obrázky a text, extrahovaly požadované informace pomocí různých funkcí AI a indexovat je v indexu vyhledávání pro rychlé vyhledávání, vyhledávání a průzkum. 

Vstupy jsou objekty BLOB v jednom kontejneru v úložišti objektů blob Azure. Objekty BLOB mohou být téměř jakýkoli druh textových nebo obrazových dat. 

Výstup je vždy index vyhledávání, který se používá pro rychlé vyhledávání textu, načítání a zkoumání v klientských aplikacích. Kromě toho může být výstupem také *úložiště znalostí,* které projekty obohacené dokumenty do objektů BLOB Azure nebo tabulek Azure pro následné analýzy v nástrojích, jako je Power BI nebo v úlohách datové vědy.

Mezi tím je samotná architektura kanálu. Kanál je založen na funkci *indexeru,* ke které můžete přiřadit *skillset*, který se skládá z jedné nebo více *dovedností* poskytujících AI. Účelem kanálu je vytvořit *obohacené dokumenty,* které zadávají jako nezpracovaný obsah, ale vyzvednout další strukturu, kontext a informace při procházení kanálu. Obohacené dokumenty jsou spotřebovány během indexování k vytvoření invertovaných indexů a dalších struktur používaných v fulltextovém vyhledávání nebo průzkumu a analýze.

## <a name="start-with-services"></a>Začněte se službami

Potřebujete Azure Cognitive Search a úložiště objektů blob Azure. V rámci úložiště objektů Blob potřebujete kontejner, který poskytuje zdrojový obsah.

Můžete začít přímo na stránce portálu účtu úložiště. Na levé navigační stránce klikněte v části **Blob service** na **Přidat Azure Cognitive Search** a vytvořte novou službu nebo vyberte existující službu. 

Jakmile přidáte Azure Cognitive Search do svého účtu úložiště, můžete postupovat podle standardního procesu a obohatit data v libovolném zdroji dat Azure. Doporučujeme průvodce **importem dat** v Azure Cognitive Search pro snadný počáteční úvod do obohacení AI. Tento rychlý start vás provede kroky: [Vytvořte kanál obohacení AI na portálu](cognitive-search-quickstart-blob.md). 

V následujících částech prozkoumáme další součásti a koncepty.

## <a name="use-a-blob-indexer"></a>Použití indexeru objektů blob

Obohacení ai je doplněk k kanálu indexování a v Azure Cognitive Search, tyto kanály jsou postavené na vrcholu *indexeru*. Indexer je dílčí služba podporující zdroj dat vybavená interní logikou pro vzorkování dat, čtení dat metadat, načítání dat a serializaci dat z nativních formátů do dokumentů JSON pro následný import. Indexery jsou často používány samy o sobě pro import, odděleně od AI, ale pokud chcete vytvořit kanál obohacení AI, budete potřebovat indexer a skillset jít s ním. Tato část zvýrazní indexer; další část se zaměřuje na dovednosti.

Objekty BLOB ve službě Azure Storage se indexují pomocí [indexeru úložiště objektů blob Azure Cognitive Search](search-howto-indexing-azure-blob-storage.md). Tento indexer můžete vyvolat pomocí Průvodce **importem dat,** rozhraní REST API nebo sady .NET SDK. V kódu použijete tento indexer nastavením typu a poskytnutím informací o připojení, které zahrnuje účet Azure Storage spolu s kontejnerem objektů blob. Objekty BLOB můžete podmnožinou vytvořením virtuálního adresáře, který pak můžete předat jako parametr, nebo filtrováním přípony typu souboru.

Indexer provádí "dokument praskání", otevření objektu blob pro kontrolu obsahu. Po připojení ke zdroji dat je to první krok v kanálu. U dat objektů blob se zjistí pdf, kancelářské dokumenty, obrázek a další typy obsahu. Popraskání dokumentů s extrakcí textu je zdarma. Popraskání dokumentů s extrakcí obrazu se účtuje podle sazeb, které najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/search/).

I když budou všechny dokumenty popraskané, obohacení nastane pouze v případě, že výslovně poskytnete dovednosti, abyste tak učinili. Pokud například kanál obsahuje výhradně analýzu obrázků, text v kontejneru nebo dokumentech se ignoruje.

Indexer objektů blob je dodáván s parametry konfigurace a podporuje sledování změn, pokud podkladová data poskytují dostatečné informace. Další informace o základních funkcích najdete v [indexeru úložiště objektů blob Azure Cognitive Search](search-howto-indexing-azure-blob-storage.md).

## <a name="add-ai-components"></a>Přidání komponent AI

Obohacení ai odkazuje na moduly, které hledají vzory nebo charakteristiky a pak provést operaci odpovídajícím způsobem. Rozpoznání obličeje na fotografiích, textové popisy fotografií, detekce klíčových frází v dokumentu a Rozpoznávání OCR (nebo rozpoznávání tištěného nebo ručně psaného textu v binárních souborech) jsou ilustrativními příklady.

V Azure Cognitive Search *dovednosti* jsou jednotlivé součásti zpracování AI, které můžete použít samostatně nebo v kombinaci s jinými dovednostmi. 

+ Integrované dovednosti jsou podpořeny službami Cognitive Services, s analýzou obrazu založenou na počítačovém vidění a zpracováním přirozeného jazyka na základě analýzy textu. Úplný seznam najdete [v tématu Integrované dovednosti pro obohacení obsahu](cognitive-search-predefined-skills.md).

+ Vlastní dovednosti jsou vlastní kód, zabalené v [definici rozhraní,](cognitive-search-custom-skill-interface.md) která umožňuje integraci do kanálu. V zákaznických řešeních je běžnou praxí používat obojí, s vlastními dovednostmi poskytujícími moduly ai s otevřeným zdrojovým kódem, třetích stran nebo první strany.

*Skillset* je kolekce dovedností používaných v kanálu a je vyvolána po fázi prolomení dokumentu zpřístupní obsah. Indexer může spotřebovat přesně jednu sadu dovedností, ale tato sada dovedností existuje nezávisle na indexeru, takže jej můžete znovu použít v jiných scénářích.

Vlastní dovednosti mohou znít složitě, ale mohou být jednoduché a přímočaré, pokud jde o implementaci. Pokud máte existující balíčky, které poskytují vzorky odpovídající nebo klasifikační modely, obsah, který extrahujete z objektů BLOB, může být předán těmto modelům pro zpracování. Vzhledem k tomu, že obohacení umělou aicí je založené na Azure, váš model by měl být také v Azure. Některé běžné metodiky hostování zahrnují použití [azure funkce](cognitive-search-create-custom-skill-example.md) nebo [kontejnery](https://github.com/Microsoft/SkillsExtractorCognitiveSearch).

Integrované dovednosti podporované službami Cognitive Services vyžadují připojený klíč předplatného [služby Cognitive Services](cognitive-search-attach-cognitive-services.md) all-in-one, který vám umožní přístup k prostředku. Klíč vše v jednom vám poskytuje analýzu obrázků, detekci jazyka, překlad textu a analýzu textu. Další integrované dovednosti jsou funkce Azure Cognitive Search a nevyžadují žádné další služby nebo klíč. Tvarovač textu, rozdělovač a fúze jsou příklady pomocných dovedností, které jsou někdy nezbytné při navrhování kanálu.

Pokud používáte pouze vlastní dovednosti a integrované dovednosti nástrojů, neexistuje žádná závislost nebo náklady související se službami Cognitive Services.

<!-- ## Order of operations

Now we've covered indexers, content extraction, and skills, we can take a closer look at pipeline mechanisms and order of operations.

A skillset is a composition of one or more skills. When multiple skills are involved, the skillset operates as sequential pipeline, producing dependency graphs, where output from one skill becomes input to another. 

For example, given a large blob of unstructured text, a sample order of operations for text analytics might be as follows:

1. Use Text Splitter to break the blob into smaller parts.
1. Use Language Detection to determine if content is English or another language.
1. Use Text Translator to get all text into a common language.
1. Run Entity Recognition, Key Phrase Extraction, or Sentiment Analysis on chunks of text. In this step, new fields are created and populated. Entities might be location, people, organization, dates. Key phrases are short combinations of words that appear to belong together. Sentiment score is a rating on continuum of negative (0) to positive (1) sentiment.
1. Use Text Merger to reconstitute the document from the smaller chunks. -->

## <a name="consume-ai-enriched-output-in-downstream-solutions"></a>Spotřebovávat výstup obohacený o umělou aivu v následných řešeních

Výstup obohacení umělou ai je index vyhledávání v Azure Cognitive Search nebo [úložiště znalostí](knowledge-store-concept-intro.md) ve službě Azure Storage.

V Azure Cognitive Search se index vyhledávání používá pro interaktivní průzkum pomocí volného textu a filtrovaných dotazů v klientské aplikaci. Obohacené dokumenty vytvořené prostřednictvím ai jsou formátovány v JSON a indexovány stejným způsobem, jako všechny dokumenty jsou indexovány v Azure Cognitive Search, s využitím všech výhod, které nabízí indexer. Například během indexování indexer objektů blob odkazuje na parametry konfigurace a nastavení využít mapování polí nebo logiku zjišťování změn. Tato nastavení jsou plně k dispozici pro pravidelné indexování a ai obohacené úlohy. Po indexování, když je obsah uložený v Azure Cognitive Search, můžete vytvářet bohaté dotazy a filtrovat výrazy, abyste porozuměli obsahu.

Ve službě Azure Storage má úložiště znalostí dva projevy: kontejner objektů blob nebo tabulky v úložišti tabulek. 

+ Kontejner objektů blob zachycuje rozšířené dokumenty v plném rozsahu, což je užitečné, pokud chcete krmit do jiných procesů. 

+ Naproti tomu úložiště tabulky může pojmout fyzické projekce obohacených dokumentů. Můžete vytvořit řezy nebo vrstvy obohacených dokumentů, které obsahují nebo vylučují určité součásti. Pro analýzu v Power BI se tabulky v úložišti Azure Table stávají zdrojem dat pro další vizualizaci a průzkum.

Obohacený dokument na konci kanálu se liší od původní vstupní verze přítomností dalších polí obsahujících nové informace, které byly extrahovány nebo generovány během obohacení. V důsledku toho můžete pracovat s kombinací původního a vytvořeného obsahu bez ohledu na to, kterou výstupní strukturu používáte.

## <a name="next-steps"></a>Další kroky

Existuje mnohem víc, co můžete udělat s obohacením AI, abyste získali co nejvíce z vašich dat ve službě Azure Storage, včetně kombinování služeb Cognitive Services různými způsoby a vytváření vlastních dovedností pro případy, kdy neexistuje žádná existující kognitivní služba pro scénář. Můžete se dozvědět více pomocí níže uvedených odkazů.

+ [Nahrávání, stahování a seznam objektů BLOB pomocí portálu Azure (azure blob storage)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
+ [Nastavení indexeru objektů blob (Azure Cognitive Search)](search-howto-indexing-azure-blob-storage.md) 
+ [Přehled obohacení umělou ai (Azure Cognitive Search)](cognitive-search-concept-intro.md) 
+ [Vytvoření sady dovedností (Azure Cognitive Search)](cognitive-search-defining-skillset.md)
+ [Mapování uzlů ve stromu poznámky (Azure Cognitive Search)](cognitive-search-output-field-mapping.md)
