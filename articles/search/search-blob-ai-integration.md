---
title: Použití AI k pochopení dat objektů BLOB
titleSuffix: Azure Search
description: Pomocí kanálu obohacení AI v Azure Search přidejte do objektů blob Azure sémantické zpracování v přirozeném jazyce a analýzu obrázků.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: search
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: 2513825fcb275aeb3c4f0ca49ff5f2a6bd9441f0
ms.sourcegitcommit: bd4198a3f2a028f0ce0a63e5f479242f6a98cc04
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2019
ms.locfileid: "72303012"
---
# <a name="use-ai-to-understand-blob-data"></a>Použití AI k pochopení dat objektů BLOB

Data v úložišti objektů BLOB v Azure jsou často řadou nestrukturovaných obsahu, jako jsou obrázky, dlouhé texty, soubory PDF a dokumenty Office. Pomocí funkcí AI v Azure Search můžete v různých způsobech pochopit a extrahovat cenné informace z objektů BLOB. Mezi příklady použití AI na obsah objektu BLOB patří:

+ Extrakce textu z obrázků pomocí optického rozpoznávání znaků (OCR)
+ Vytvoření popisu nebo značek scény z fotky
+ Rozpoznávání jazyka a překlad textu do různých jazyků
+ Zpracování textu s použitím rozpoznávání pojmenovaných entit (NER) k vyhledání odkazů na lidi, kalendářní data, místa nebo organizace 

I když budete možná potřebovat jenom jednu z těchto funkcí AI, je běžné zkombinovat několik z nich do stejného kanálu (například extrakce textu ze skenovaného obrázku a hledání všech dat a míst, na které se odkazuje). 

Rozšíření AI vytvoří nové informace zachycené jako text, které jsou uloženy v polích. Po obohacení můžete k těmto informacím přistupovat z indexu hledání prostřednictvím fulltextového vyhledávání nebo posílat obohacené dokumenty zpátky do služby Azure Storage, abyste mohli zasílat nová prostředí aplikací, která zahrnují zkoumání dat pro scénáře zjišťování nebo analýzy. 

V tomto článku zobrazujeme obohacení AI prostřednictvím celé čočky, abyste mohli rychle poznat celý proces, od transformace nezpracovaných dat v objektech blob, aby se Queryable informace buď v indexu vyhledávání nebo ve znalostní bázi.

## <a name="what-it-means-to-enrich-blob-data"></a>Co znamená "obohacen" data objektů BLOB

*Rozšíření AI* je součástí architektury indexování Azure Search, která integruje integrovaný AI od společnosti Microsoft nebo vlastního AI, který zadáte. Pomůže vám implementovat ucelené scénáře, kdy potřebujete zpracovat objekty BLOB (obě existující a nové, když jsou v nástroji nebo aktualizované), trhliny otevřít všechny formáty souborů pro extrakci obrázků a textu, extrahovat požadované informace pomocí různých funkcí AI a Indexujte je v indexu Azure Search pro rychlé hledání, načítání a průzkum. 

Vstupy jsou vaše objekty BLOB v jednom kontejneru v úložišti objektů BLOB v Azure. Objekty blob můžou být skoro jakýkoli druh dat typu text nebo obrázek. 

Výstupem je vždycky Azure Search index, který se používá pro rychlé vyhledávání textu, načítání a průzkum v klientských aplikacích. Kromě toho může být výstup také *úložištěm znalostní báze* , které projekty rozšiřují do objektů blob Azure nebo tabulek Azure pro účely analýzy pro příjem dat v nástrojích, jako je Power BI nebo v úlohách v oblasti datových věd.

V mezi je samotná architektura kanálu. Kanál je založen na funkci *indexeru* , ke které můžete přiřadit *dovednosti*, který se skládá z jedné nebo více *dovedností* , které poskytují AI. Účelem tohoto kanálu je vytvořit *obohacené dokumenty* , které vstupují jako nezpracovaný obsah, ale při přesouvání přes kanál si můžete vybrat další strukturu, kontext a informace. Obohacené dokumenty se při indexování spotřebují k vytváření obrácených indexů a dalších struktur používaných při fulltextovém vyhledávání nebo průzkumu a analýzách.

## <a name="how-to-get-started"></a>Jak začít

Můžete začít přímo na stránce portálu účtu úložiště. Klikněte na **přidat Azure Search** a vytvořte novou službu Azure Search nebo vyberte některou z existujících. Pokud již máte v rámci stejného předplatného existující vyhledávací službu, kliknutím na tlačítko **přidat Azure Search** otevřete Průvodce importem dat, abyste mohli okamžitě Krokovat s indexováním, rozšířením a definicí indexu.

Až přidáte Azure Search do svého účtu úložiště, můžete postupovat podle standardního procesu k obohacení dat v jakémkoli zdroji dat Azure. Za předpokladu, že už máte obsah objektu blob, můžete pomocí Průvodce importem dat v Azure Search snadno počáteční Úvod do rozšíření AI. Tento rychlý Start vysvětluje postup: [vytvoření kanálu rozšíření AI na portálu](cognitive-search-quickstart-blob.md). 

V následujících částech prozkoumáme více komponent a konceptů.

## <a name="use-blob-indexers"></a>Použití indexerů objektů BLOB

Rozšíření AI je doplněk k kanálu indexování a v Azure Search jsou tyto kanály postaveny nad *indexerem*. Indexer je podslužba s podporou zdrojů dat, která je vybavená interní logikou pro vzorkování dat, čtení dat metadat, načítání dat a serializaci dat z nativních formátů do dokumentů JSON pro následné importy. Indexery se často používají pro import, oddělené od AI, ale pokud chcete vytvořit kanál pro rozšíření AI, budete potřebovat indexer a dovednosti, abyste s ním mohli přejít. V této části se zaměříme na samotného indexeru.

Objekty BLOB v Azure Storage jsou indexovány pomocí [Azure Search indexer úložiště objektů BLOB](search-howto-indexing-azure-blob-storage.md). Tento indexer vyvoláte tak, že nastavíte typ a poskytnete informace o připojení, které zahrnují účet Azure Storage společně s kontejnerem objektů BLOB. Pokud jste objekty blob předtím neorganizovali do virtuálního adresáře, který pak můžete předat jako parametr, vyžádá indexer objektu BLOB z celého kontejneru.

Indexer provádí "trhliny dokumentů" a po připojení ke zdroji dat je prvním krokem v kanálu. V případě dat objektů BLOB se zjistí, kde jsou zjištěny soubory PDF, obrázky Office, image a další typy obsahu. Trhlina dokumentu s extrakcí textu je bezplatná. K extrakci dokumentů s extrakcí obrázků se účtují sazby, které najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/search/)Azure Search.

I když budou všechny dokumenty probíhat, k obohacení dojde pouze v případě, že tyto dovednosti výslovně zadáte. Pokud se například váš kanál skládá výhradně z analýzy textu, budou se všechny obrázky ve vašem kontejneru nebo dokumentech ignorovat.

Indexer objektů BLOB obsahuje konfigurační parametry a podporuje sledování změn, pokud podkladová data poskytují dostatek informací. Další informace o základních funkcích najdete v části [Azure Search indexer úložiště objektů BLOB](search-howto-indexing-azure-blob-storage.md).

## <a name="add-ai"></a>Přidat AI

*Dovednosti* jsou jednotlivé komponenty zpracování AI, které můžete použít samostatně nebo v kombinaci s dalšími dovednostmi pro sekvenční zpracování. 

+ Integrované dovednosti jsou zajištěny Cognitive Services s analýzou obrázků založenou na Počítačové zpracování obrazu a zpracováním přirozeného jazyka na základě Analýza textu. V několika příkladech jsou [optické rozpoznávání OCR](cognitive-search-skill-ocr.md), [rozpoznávání entit](cognitive-search-skill-entity-recognition.md)a [Analýza obrázků](cognitive-search-skill-image-analysis.md). Můžete si prohlédnout úplný seznam integrovaných dovedností v části [předdefinované dovednosti pro obohacení obsahu](cognitive-search-predefined-skills.md).

+ Vlastní dovednosti jsou vlastní kód, zabalený do definice rozhraní, který umožňuje integraci do kanálu. V zákaznických řešeních je běžné použití obou s vlastními dovednostmi, které poskytují moduly open-source, třetí strany nebo nástroje AI první strany.

*Dovednosti* je kolekce dovedností použitých v kanálu a vyvolá se poté, co je ve fázi pro trhliny dokumentů k dispozici obsah. Indexer může spotřebovat právě jeden dovednosti, ale dovednosti existuje nezávisle na indexeru, abyste ho mohli znovu použít v jiných scénářích.

Vlastní dovednosti můžou být složité, ale můžou být jednoduché a jednoduché v souvislosti s implementací. Pokud máte existující balíčky, které poskytují model porovnávání nebo klasifikace modelů, obsah, který extrahujete z objektů blob, se může předat těmto modelům ke zpracování. Vzhledem k tomu, že rozšíření AI je založené na Azure, váš model by měl být také v Azure. Mezi běžné metody hostování patří použití [Azure Functions](cognitive-search-create-custom-skill-example.md) nebo [kontejnerů](https://github.com/Microsoft/SkillsExtractorCognitiveSearch).

Integrované dovednosti, které poskytuje Cognitive Services, vyžadují klíč předplatného [Cognitive Services](cognitive-search-attach-cognitive-services.md) All-in-One, který vám poskytne přístup k tomuto prostředku. Klíč vše v jednom poskytuje analýzu obrázků, detekci jazyka, překlad textu a analýzu textu. Další integrované dovednosti jsou funkce Azure Search a nevyžadují žádnou další službu ani klíč. Shaper textu, rozdělovač a fúze jsou příklady pomocných dovedností, které jsou někdy nezbytné při navrhování kanálu.

Pokud používáte pouze vlastní dovednosti a integrované obslužné pomůcky, neexistují žádné závislosti ani náklady související s Cognitive Services.

## <a name="order-of-operations"></a>Pořadí operací

Nyní jsme pokryli indexery, extrakci obsahu a dovednosti, můžeme se blíže podívat na mechanismy kanálu a pořadí operací.

Dovednosti je složení jedné nebo více dovedností. V případě, že je zapojeno více dovedností, dovednosti funguje jako sekvenční kanál a vytváření grafů závislostí, kde výstup z jedné dovednosti se zadává do jiného. 

Například vzhledem k velkému objektu BLOB nestrukturovaného textu může ukázkové pořadí operací pro text Analytics být následující:

1. K rozdělení objektu BLOB na menší části použijte rozdělovač textu.
1. Pomocí Rozpoznávání jazyka můžete zjistit, jestli je obsah v angličtině nebo v jiném jazyce.
1. Pomocí překladače textu můžete získat veškerý text do společného jazyka.
1. Spusťte rozpoznávání entit, Extrakce klíčových frází nebo Analýza mínění na blocích textu. V tomto kroku se vytvoří a naplní nová pole. Entitami mohou být umístění, lidé, organizace a kalendářní data. Klíčové fráze jsou krátké kombinace slov, která se jeví jako patřící dohromady. Mínění Skore je hodnocení pro Continuum negativu (0) do kladného (1) mínění.
1. Pomocí fúze textu můžete dokument znovu vytvořit z menších bloků dat.


## <a name="outputs-and-use-cases"></a>Výstupy a případy použití

Obohacený dokument na konci kanálu se od původní vstupní verze liší přítomností dalších polí obsahujících nové informace, které byly extrahovány nebo generovány během obohacení. V takovém případě můžete pracovat s kombinací původní a vytvořené hodnoty několika způsoby.

Výstupní formuláře jsou vyhledávací index v Azure Search nebo znalostní báze v Azure Storage.

V Azure Search jsou obohacené dokumenty ve formátu JSON a dají se indexovat stejným způsobem jako všechny dokumenty, a to s výhodami, které indexer poskytuje. Pole z obohacených dokumentů jsou mapována na schéma indexu. Při indexování odkazuje indexer objektů blob na konfigurační parametry a nastavení, aby bylo možné využít všechna mapování polí, nebo změnit logiku detekce, kterou jste určili. Po indexování, když je obsah uložený v Azure Search, můžete vytvářet bohatý dotazy a výrazy filtru, které vám pomůžou pochopit obsah.

V Azure Storage má znalostní báze dva manifesty: kontejner objektů BLOB nebo tabulky v úložišti tabulek. Kontejner objektů BLOB zachycuje obohacené dokumenty v celém rozsahu, což je užitečné, pokud chcete předávat data do jiných procesů. Na rozdíl od toho může tabulka úložiště přizpůsobit fyzické projekce obohacených dokumentů. Můžete vytvořit řezy nebo vrstvy obohacených dokumentů, které obsahují nebo vylučují určité části. Pro účely analýzy v Power BI se tabulky ve službě Azure Table Storage stanou zdrojem dat pro další vizualizaci a průzkum.

## <a name="next-steps"></a>Další kroky

Díky obohacení AI se můžete dostat na maximum, abyste mohli co nejvíc využít data v Azure Storage, včetně kombinování Cognitive Services různých způsobů a vytváření vlastních dovedností pro případy, kdy pro daný scénář neexistuje žádná stávající služba pro rozpoznávání. Další informace najdete na následujících odkazech.

> [!div class="nextstepaction"]
> [Přehled rozšíření AI](cognitive-search-concept-intro.md) 
> [vytvoření](cognitive-search-defining-skillset.md)uzlů map 
> [ve stromu poznámek](cognitive-search-output-field-mapping.md)
