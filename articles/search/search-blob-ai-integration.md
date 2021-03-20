---
title: Použití AI k obohacení obsahu objektu BLOB
titleSuffix: Azure Cognitive Search
description: Přečtěte si informace o funkcích přirozeného jazyka a analýz obrázků v Azure Kognitivní hledání a o tom, jak se tyto procesy vztahují k obsahu uloženému v objektech blob Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 3d427d80e502eed0825165e640acc0755515c5b0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99222044"
---
# <a name="use-ai-to-process-and-analyze-blob-content-in-azure-cognitive-search"></a>Použití AI ke zpracování a analýze obsahu objektů BLOB v Azure Kognitivní hledání

Obsah ve službě Azure Blob Storage, který se skládá z obrázků nebo dlouhý nerozlišený text, může projít analýzou hloubkového učení, aby se odhalily a mohly extrahovat cenné informace užitečné pro podřízené aplikace. Pomocí [rozšíření AI](cognitive-search-concept-intro.md)můžete:

+ Extrakce textu z obrázků pomocí optického rozpoznávání znaků (OCR)
+ Vytvoření popisu nebo značek scény z fotky
+ Rozpoznávání jazyka a překlad textu do různých jazyků
+ Odvodit strukturu prostřednictvím rozpoznávání entit hledáním odkazů na lidi, kalendářní data, místa nebo organizace

I když budete možná potřebovat jenom jednu z těchto funkcí AI, je běžné zkombinovat několik z nich do stejného kanálu (například extrakce textu ze skenovaného obrázku a hledání všech dat a míst, na které se odkazuje). Také je běžné zahrnout vlastní zpracování AI nebo strojového učení ve formě špičkových externích balíčků a modelů na pracovišti přizpůsobených vašim datům a vašim požadavkům.

I když můžete použít rozšíření AI na libovolný zdroj dat podporovaný indexerem vyhledávání, objekty blob jsou nejčastěji používané struktury v kanálu rozšíření. Výsledky se připravují do indexu vyhledávání pro fulltextové vyhledávání, nebo se přesměrují zpátky na Azure Storage, aby se nové prostředí aplikace, které zahrnuje zkoumání dat pro scénáře zjišťování nebo analýzy, přidalo. 

V tomto článku zobrazujeme obohacení AI prostřednictvím celé čočky, abyste mohli rychle poznat celý proces, od transformace nezpracovaných dat v objektech blob, aby se Queryable informace buď v indexu vyhledávání nebo ve znalostní bázi.

## <a name="what-it-means-to-enrich-blob-data-with-ai"></a>Co znamená "obohacení" dat objektů BLOB pomocí AI

*Rozšíření AI* je součástí architektury indexování služby Azure kognitivní hledání, která integruje modely strojového učení ze společnosti Microsoft nebo vlastních výukových modelů, které zadáte. Pomáhá implementovat ucelené scénáře, kdy potřebujete zpracovat objekty BLOB (jak existující, tak i nové, když jsou v nástroji nebo aktualizované), trhliny otevřou všechny formáty souborů pro extrakci obrázků a textu, extrakci požadovaných informací pomocí různých funkcí AI a jejich indexování v indexu vyhledávání pro rychlé hledání, načítání a průzkum. 

Vstupy jsou vaše objekty BLOB v jednom kontejneru v úložišti objektů BLOB v Azure. Objekty blob můžou být skoro jakýkoli druh dat typu text nebo obrázek. 

Výstupem je vždy index vyhledávání, který se používá pro rychlé vyhledávání textu, načítání a průzkum v klientských aplikacích. Kromě toho může být výstup také [*úložištěm znalostní báze*](knowledge-store-concept-intro.md) , které projekty rozšiřují do objektů blob Azure nebo tabulek Azure pro účely analýzy pro příjem dat v nástrojích, jako je Power BI nebo v úlohách v oblasti datových věd.

V mezi je samotná architektura kanálu. Kanál je založen na [*indexerech*](search-indexer-overview.md), ke kterým můžete přiřadit [*dovednosti*](cognitive-search-working-with-skillsets.md), který se skládá z jedné nebo více *dovedností* poskytujících AI. Účelem tohoto kanálu je vytvořit *obohacené dokumenty* , které zadávají kanál jako nezpracovaný obsah, ale při přesunu kanálu si můžete vybrat další strukturu, kontext a informace. Obohacené dokumenty se při indexování spotřebují k vytváření obrácených indexů a dalších struktur používaných při fulltextovém vyhledávání nebo průzkumu a analýzách.

## <a name="required-resources"></a>Požadované prostředky

Kromě služby Azure Blob Storage a Azure Kognitivní hledání potřebujete třetí službu nebo mechanismus, který poskytuje AI:

+ V případě integrovaných AI Kognitivní hledání integruje s rozhraními API Azure Cognitive Services Vision a přirozeného jazyka pro zpracování. [Prostředek Cognitive Services můžete připojit](cognitive-search-attach-cognitive-services.md) k přidání optického rozpoznávání znaků (OCR), analýzy obrázků nebo zpracování přirozeného jazyka (rozpoznávání jazyka, překlad textu, rozpoznávání entit, extrakce klíčových frází). 

+ Pro vlastní AI pomocí prostředků Azure můžete definovat vlastní dovednost, která zabalí externí funkci nebo model, který chcete použít. [Vlastní dovednosti](cognitive-search-custom-skill-interface.md) můžou používat kód poskytovaný Azure Functions, Azure Machine Learning, nástroj pro rozpoznávání formulářů Azure nebo jiný prostředek, který je dosažitelný přes protokol HTTPS.

+ U vlastních souborů AI mimo Azure musí být model nebo modul přístupný indexeru přes HTTP.

Pokud nemáte všechny služby snadno dostupné, začněte přímo na stránce portálu účtu úložiště. Na levé navigační stránce v části **BLOB Service** klikněte na **Přidat Azure kognitivní hledání** a vytvořte novou službu nebo vyberte existující. 

Až přidáte Azure Kognitivní hledání do svého účtu úložiště, můžete postupovat podle standardního procesu k obohacení dat v jakémkoli zdroji dat Azure. Průvodce **importem dat** v Azure kognitivní hledání doporučujeme, abyste mohli snadno úvodní Úvod do rozšíření AI. Během pracovního postupu můžete připojit prostředek Cognitive Services. Tento rychlý Start vás provede jednotlivými kroky: [vytvoření kanálu rozšíření AI na portálu](cognitive-search-quickstart-blob.md). 

V následujících částech se podrobněji podíváte na komponenty a pracovní postup.

## <a name="use-a-blob-indexer"></a>Použití indexeru objektů BLOB

Rozšíření AI je doplněk k kanálu indexování a v Azure Kognitivní hledání jsou tyto kanály postaveny na *indexeru*. Indexer je podslužba s podporou zdrojů dat, která je vybavená interní logikou pro vzorkování dat, čtení dat metadat, načítání dat a serializaci dat z nativních formátů do dokumentů JSON pro následné importy. Indexery se často používají pro import, oddělené od AI, ale pokud chcete vytvořit kanál pro rozšíření AI, budete potřebovat indexer a dovednosti, abyste s ním mohli přejít. V této části se zvýrazní indexer. Další oddíl se zaměřuje na dovednosti.

Objekty BLOB v Azure Storage jsou indexovány pomocí [indexeru objektů BLOB](search-howto-indexing-azure-blob-storage.md). Tento indexer můžete vyvolat pomocí průvodce **importem dat** , REST API nebo sady SDK. Indexer objektů BLOB se vyvolá v případě, že zdroj dat, který indexer používá, je kontejnerem objektů BLOB v Azure. Podmnožinu objektů blob můžete indexovat vytvořením virtuálního adresáře, který pak můžete předat jako parametr, nebo filtrováním přípony typu souboru.

Indexer provádí "trhliny dokumentů", otevírání objektu BLOB pro kontrolu obsahu. Po připojení ke zdroji dat je prvním krokem v kanálu. V případě dat objektů BLOB se zjistí, kde jsou zjištěny soubory PDF, obrázky Office, image a další typy obsahu. Trhlina dokumentu s extrakcí textu je bezplatná. Na [stránce s cenami](https://azure.microsoft.com/pricing/details/search/)se účtují poplatky za extrakci dokumentů s extrakcí obrázků.

I když budou všechny dokumenty probíhat, k obohacení dojde pouze v případě, že tyto dovednosti výslovně zadáte. Pokud se například váš kanál skládá výhradně z analýzy obrázků, text ve vašem kontejneru nebo dokumentech se ignoruje.

Indexer objektů BLOB obsahuje konfigurační parametry a podporuje sledování změn, pokud podkladová data poskytují dostatek informací. Další informace najdete v tématu [jak nakonfigurovat indexer objektů BLOB](search-howto-indexing-azure-blob-storage.md).

## <a name="add-ai-components"></a>Přidat komponenty AI

Rozšíření AI odkazuje na moduly, které hledají vzory nebo charakteristiky, a pak provede operaci odpovídajícím způsobem. Rozpoznávání obličeje na fotografiích, textové popisy fotek, detekce klíčových frází v dokumentu a rozpoznávání OCR (nebo rozpoznávání vytištěného textu nebo ručně psaného textu v binárních souborech) jsou příklady příkladů.

V Azure Kognitivní hledání *dovednosti* jsou jednotlivé komponenty zpracování AI, které můžete použít samostatně, nebo v kombinaci s dalšími dovednostmi. 

+ Integrované dovednosti jsou zajištěny Cognitive Services s analýzou obrázků založenou na Počítačové zpracování obrazu a zpracováním přirozeného jazyka na základě Analýza textu. Úplný seznam najdete v tématu [integrované dovednosti pro rozšíření obsahu](cognitive-search-predefined-skills.md).

+ Vlastní dovednosti jsou vlastní kód, zabalený do [definice rozhraní](cognitive-search-custom-skill-interface.md) , který umožňuje integraci do kanálu. V zákaznických řešeních je běžné použití obou s vlastními dovednostmi, které poskytují moduly open-source, třetí strany nebo nástroje AI první strany.

*Dovednosti* je kolekce dovedností použitých v kanálu a vyvolá se poté, co je ve fázi pro trhliny dokumentů k dispozici obsah. Indexer může spotřebovat právě jeden dovednosti, ale dovednosti existuje nezávisle na indexeru, abyste ho mohli znovu použít v jiných scénářích.

Vlastní dovednosti můžou být složité, ale můžou být jednoduché a jednoduché v souvislosti s implementací. Pokud máte existující balíčky, které poskytují model porovnávání nebo klasifikace modelů, obsah, který extrahujete z objektů blob, se může předat těmto modelům ke zpracování. Vzhledem k tomu, že rozšíření AI je založené na Azure, váš model by měl být také v Azure. Mezi běžné metody hostování patří použití [Azure Functions](cognitive-search-create-custom-skill-example.md) nebo [kontejnerů](https://github.com/Microsoft/SkillsExtractorCognitiveSearch).

Integrované dovednosti, které poskytuje Cognitive Services, vyžadují klíč předplatného [Cognitive Services](cognitive-search-attach-cognitive-services.md) All-in-One, který vám poskytne přístup k tomuto prostředku. Klíč vše v jednom poskytuje analýzu obrázků, detekci jazyka, překlad textu a analýzu textu. Další integrované dovednosti jsou funkce služby Azure Kognitivní hledání a nevyžadují žádnou další službu ani klíč. Shaper textu, rozdělovač a fúze jsou příklady pomocných dovedností, které jsou někdy nezbytné při navrhování kanálu.

Pokud používáte pouze vlastní dovednosti a integrované obslužné pomůcky, neexistují žádné závislosti ani náklady související s Cognitive Services.

## <a name="consume-ai-enriched-output-in-downstream-solutions"></a>Využití výstupu AI v navazujících řešeních

Výstupem rozšíření AI je index vyhledávání v Azure Kognitivní hledání nebo [znalostní báze](knowledge-store-concept-intro.md) v Azure Storage.

V Azure Kognitivní hledání se index vyhledávání používá pro interaktivní zkoumání pomocí bezplatného textu a filtrovaných dotazů v klientské aplikaci. Obohacené dokumenty vytvořené prostřednictvím AI jsou ve formátu JSON a indexované stejným způsobem jako všechny dokumenty indexované v Azure Kognitivní hledání a využívají všechny výhody, které indexer poskytuje. Například při indexování odkazuje indexer objektů blob na konfigurační parametry a nastavení, aby bylo možné využít všechna mapování polí nebo změnu logiky detekce. Tato nastavení jsou plně dostupná pro pravidelné indexování a rozšířené úlohy AI. Po indexování se po uložení obsahu na Azure Kognitivní hledání dají vytvářet bohatá dotaz a filtrovat výrazy, abyste pochopili svůj obsah.

V Azure Storage má znalostní báze dva manifesty: kontejner objektů BLOB nebo tabulky v úložišti tabulek. 

+ Kontejner objektů BLOB zachycuje obohacené dokumenty v celém rozsahu, což je užitečné, pokud chcete předávat data do jiných procesů. 

+ Na rozdíl od toho může tabulka úložiště přizpůsobit fyzické projekce obohacených dokumentů. Můžete vytvořit řezy nebo vrstvy obohacených dokumentů, které obsahují nebo vylučují určité části. Pro účely analýzy v Power BI se tabulky ve službě Azure Table Storage stanou zdrojem dat pro další vizualizaci a průzkum.

Obohacený dokument na konci kanálu se od původní vstupní verze liší přítomností dalších polí obsahujících nové informace, které byly extrahovány nebo generovány během obohacení. V takovém případě můžete pracovat s kombinací původního a vytvořeného obsahu bez ohledu na to, jakou výstupní strukturu používáte.

## <a name="next-steps"></a>Další kroky

Díky obohacení AI se můžete dostat na maximum, abyste mohli co nejvíc využít data v Azure Storage, včetně kombinování Cognitive Services různých způsobů a vytváření vlastních dovedností pro případy, kdy pro daný scénář neexistuje žádná stávající služba pro rozpoznávání. Další informace najdete na následujících odkazech.

+ [Nahrávání, stahování a výpis objektů BLOB pomocí Azure Portal (úložiště objektů BLOB v Azure)](../storage/blobs/storage-quickstart-blobs-portal.md)
+ [Nastavení indexeru objektů BLOB (Azure Kognitivní hledání)](search-howto-indexing-azure-blob-storage.md) 
+ [Přehled rozšíření AI (Azure Kognitivní hledání)](cognitive-search-concept-intro.md) 
+ [Vytvoření dovednosti (Azure Kognitivní hledání)](cognitive-search-defining-skillset.md)
+ [Mapování uzlů ve stromu poznámek (Azure Kognitivní hledání)](cognitive-search-output-field-mapping.md)