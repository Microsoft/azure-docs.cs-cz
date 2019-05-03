---
title: 'Rychlý start: Sestavení indexu obohaceného AI na webu Azure portal – Azure Search'
description: Extrakce dat, přirozeného jazyka a dovednosti na portálu Azure Search indexování pro zpracování obrázků pomocí webu Azure portal a ukázková data.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: cb029530d2c6cdac82fd0d257e10717386eebf0e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65022101"
---
# <a name="quickstart-create-an-ai-indexing-pipeline-using-cognitive-skills-in-azure-search"></a>Rychlý start: Vytvořit kanál indexování AI využitím kognitivních dovedností v Azure Search

Služba Azure Search se integruje s [služeb Cognitive Services](https://azure.microsoft.com/services/cognitive-services/), přidávání obsahu extrakce, zpracování přirozeného jazyka (NLP) a dovednosti zpracování obrázků na kanál indexování Azure Search, vytváření obsahu unsearchable nebo nestrukturovaných informace prohledávatelné. 

Mnoho prostředků služeb Cognitive Services – například [OCR](cognitive-search-skill-ocr.md), [rozpoznávání jazyka](cognitive-search-skill-language-detection.md), [rozpoznávání entit](cognitive-search-skill-entity-recognition.md) pár – je možné připojit k indexovacím procesem. Algoritmy AI služeb Cognitive Services se používají k vyhledání vzorců, funkce a vlastnosti ve zdrojových datech, vrátí struktury a textový obsah, který lze použít v fulltextové vyhledávání řešení založená na Azure Search.

V tomto rychlém startu vytvořte svůj první kanál v rozšíření [webu Azure portal](https://portal.azure.com) před napsat jediný řádek kódu:

> [!div class="checklist"]
> * Začnete s ukázkovými daty ve službě Azure Blob Storage.
> * Konfigurace [ **importovat data** ](search-import-data-portal.md) Průvodce pro cognitive indexování a obohacení 
> * Spustíte průvodce (dovednost entity zjistí lidi, lokality a organizace).
> * Použití [ **Průzkumníka služby Search** ](search-explorer.md) dotazy na data bohatších možností

V tomto rychlém startu běží na bezplatné služby, ale počet transakcí zdarma je omezený na 20 dokumenty za den. Pokud chcete spustit v tomto rychlém startu více než jednou denně, použít menší soubor nastavit tak, aby se vejde do více spuštění.

> [!NOTE]
> Jak můžete rozšířit rozsah zvýšení četnosti zpracování, přidání více dokumentů nebo přidání další algoritmy AI, budete muset [připojení účtovaných prostředku služeb Cognitive Services](cognitive-search-attach-cognitive-services.md). Poplatky se účtují při volání rozhraní API ve službě Cognitive Services a extrakci image jako součást fáze hádání dokumentu ve službě Azure Search. Neúčtují žádné poplatky pro extrakci textu z dokumentů.
>
> Provádění předdefinované dovednosti, se účtuje za stávající [přejít ceny služeb Cognitive Services, platit jako můžete](https://azure.microsoft.com/pricing/details/cognitive-services/). Ceny za extrakce Image je popsaný na [stránce s cenami Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

[Vytvoření služby Azure Search](search-create-service-portal.md) nebo [najít existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) pod vaším aktuálním předplatným. Můžete použít bezplatnou službou pro tento rychlý start.

[Služby cognitive Services](https://azure.microsoft.com/services/cognitive-services/) poskytuje AI. Tento rychlý Start obsahuje kroky pro přidání těchto prostředků v řádku, při určování kanálu. Není nutné předem nastavení účtů.

Služby Azure se vyžaduje zadání vstupů na kanál indexování. Můžete použít libovolný zdroj dat nepodporuje [indexerů Azure Search](search-indexer-overview.md) s výjimkou Azure Table Storage, což není podporováno pro indexování AI. Tento rychlý start využívá [úložiště objektů Blob v Azure](https://azure.microsoft.com/services/storage/blobs/) jako kontejner pro zdroj dat souborů. 

### <a name="set-up-azure-blob-service-and-load-sample-data"></a>Nastavení služby Azure Blob a načtení ukázkových dat

1. [Stáhněte si ukázková data](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) sestávající z malé sady souborů různých typů. 

1. [Zaregistrovat do Azure Blob storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal), vytvořit účet úložiště, otevřete stránky služby objektů Blob a vytvořte kontejner.  Vytvořte účet úložiště ve stejné oblasti jako Azure Search.

1. V kontejneru, který jste vytvořili, klikněte na tlačítko **nahrát** k nahrání ukázkových souborů, které jste si stáhli v předchozím kroku.

   ![Zdrojové soubory ve službě Azure Blob Storage](./media/cognitive-search-quickstart-blob/sample-data.png)

## <a name="create-the-enrichment-pipeline"></a>Vytvoření kanálu pro rozšiřování

Vraťte se na stránce řídicího panelu služby Azure Search a klikněte na tlačítko **importovat data** na panelu příkazů a nastavte si kognitivní rozšíření ve čtyřech krocích.

  ![Příkaz pro import dat](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1-create-a-data-source"></a>Krok 1: Vytvoření zdroje dat

V **připojit ke svým datům**, zvolte **úložiště objektů Blob v Azure**, vyberte účet a kontejner vytvoříte. Zadejte název pro zdroj dat a u ostatních položek nechejte výchozí hodnoty. 

  ![Konfigurace služby Azure Blob](./media/cognitive-search-quickstart-blob/blob-datasource.png)

Přejít na další stránku.

  ![Tlačítko Další stránky pro kognitivního vyhledávání](media/cognitive-search-quickstart-blob/next-button-add-cog-search.png)

### <a name="step-2-add-cognitive-skills"></a>Krok 2: Přidat kognitivní dovednosti

Jako další krok přidáte do kanálu indexování kroky pro rozšiřování. Pokud nemáte prostředku služeb Cognitive Services, můžete zaregistrovat pro bezplatnou verzi, která poskytuje 20 transakcí každý den. Ukázková data se skládá z 14 souborů, takže denní přidělení se většinou použít po spuštění tohoto průvodce.

1. Rozbalte **připojení služeb Cognitive Services** zobrazení možností pro resourcing rozhraní API služeb Cognitive Services. Pro účely tohoto kurzu můžete použít **Free** prostředků.

   ![Připojit Cognitive Services](media/cognitive-search-quickstart-blob/cog-search-attach.png)

2. Rozbalte **přidat obohacení** a vyberte znalosti, které provádějí zpracování přirozeného jazyka. Pro tento rychlý start zvolte rozpoznávání entit pro lidi, organizace a lokality.

   ![Připojit Cognitive Services](media/cognitive-search-quickstart-blob/skillset.png)

   Tento portál nabízí integrované znalosti pro zpracování a text analysis optické rozpoznávání znaků. Na portálu sada dovedností pracuje s jedním polem zdroje. To může vypadat jako malý cíl, ale pro objekty blob služby Azure pole `content` obsahuje většinu dokumentu objektu blob (např. wordový dokument nebo powerpointovou sadu karet). Z toho důvodu je toto pole ideální vstup, protože je v něm veškerý obsah objektu blob.

3. Přejít na další stránku.

   ![Další stránka přizpůsobení indexů](media/cognitive-search-quickstart-blob/next-button-customize-index.png)

> [!NOTE]
> Dovednosti zpracování přirozeného jazyka pracují s textovým obsahem v ukázkové sadě dat. Protože jsme neměli možnost OCR, nezpracují JPEG a PNG souborů a složek nalezených v datové sadě ukázka v tomto rychlém startu. 

### <a name="step-3-configure-the-index"></a>Krok 3: Konfigurace indexu

Obvykle dokáže Průvodce odvodit výchozí index. V tomto kroku můžete zobrazit schéma vygenerovaný index a potenciálně opravit všechna nastavení. Níže se výchozí index vytvoří pro datovou sadu Blob ukázku.

Pro tento rychlý start průvodce odvedl dobrou práci při nastavování rozumných výchozích hodnot: 

+ Výchozí název je *azureblob index* na základě datového typu zdroje. 

+ Výchozí pole jsou založena na původní zdrojové pole dat (`content`), a navíc výstupních polí (`people`, `organizations`, a `locations`) vytvořené pomocí cognitive kanálu. Výchozí datové typy jsou odvozeny z metadat a dat vzorkování.

+ Výchozí klíč je *metadata_storage_path* (Toto pole obsahuje jedinečné hodnoty).

+ Výchozí atributy jsou **Retrievable** a **Searchable** těchto polí. **Prohledávání** určuje pole lze prohledávat. **Retrievable** znamená, že mohou být vráceny ve výsledcích. V Průvodci se předpokládá, že chcete tato pole bude retrievable a prohledávatelné, protože jste vytvořili pomocí dovedností.

  ![Pole indexu](media/cognitive-search-quickstart-blob/index-fields.png)

Všimněte si, přeškrtnutí a otazník na **Retrievable** atribut podle `content` pole. Pro objekt blob hodně textu dokumenty `content` pole obsahuje větší část souboru, potenciálně narazíte na tisíce řádků. Pokud je potřeba předat obsah souboru kódu klienta, ujistěte se, že **Retrievable** zůstane vybrali. V opačném případě zvažte vymazání tohoto atributu na `content` Pokud extrahovaných elementů (`people`, `organizations`, a `locations`) jsou dostačující pro účely.

Označení polí jako **Retrievable** neznamená, že pole *musí* nacházet ve výsledcích hledání. Složení výsledky hledání můžete přesně řídit pomocí **$select** parametr k určení pole, která chcete zahrnout dotazu. Například pole hodně textu pro `content`, **$select** parametr je vaše řešení pro poskytující výsledky hledání spravovatelné lidské uživatelům vaší aplikace, zatímco je zajištěna klientský kód má přístup ke všem informacím, které potřebuje prostřednictvím **Retrievable** atribut.
  
Přejít na další stránku.

  ![Další stránka vytvořit indexer](media/cognitive-search-quickstart-blob/next-button-create-indexer.png)

### <a name="step-4-configure-the-indexer"></a>Krok 4: Konfigurace indexeru

Indexer je prostředek vysoké úrovně, který řídí proces indexování. Určuje název datového zdroje, cílový index a četnosti provádění. Konečným výsledkem **Průvodce importem dat** je vždy indexer, který můžete opakovaně spouštět.

V **Indexer** stránky, můžete přijmout výchozí název a použít **spustit jednou** naplánovat možnost spustit okamžitě. 

  ![Definice indexeru](media/cognitive-search-quickstart-blob/indexer-def.png)

Klikněte na tlačítko **odeslat** vytvořit a současně spustit indexer.

## <a name="monitor-indexing"></a>Monitorování indexování

Postup rozšíření trvat déle než typické založený na textu indexování. Průvodce by měla otevření seznamu Indexer na stránce Přehled tak, aby mohl sledovat průběh. Navigace v samostatné, najdete v přehledu stránky a klikněte na tlačítko **indexery**.

Upozornění dochází, protože jsou soubory obrázků JPG a PNG soubory, a jsme tento parametr vynechán OCR dovednosti z tohoto kanálu. Naleznete zde také zkrácení oznámení. Služba Azure Search omezuje extrakce 32 000 znaků na úrovni Free.

  ![Oznámení služby Azure Search](./media/cognitive-search-quickstart-blob/indexer-notification.png)

Indexování a rozšiřování může nějakou dobu trvat. To je důvod, proč se pro seznamování s touto službou doporučuje používat menší sady dat. 

## <a name="query-in-search-explorer"></a>Dotaz v Průzkumníku služby Hledání

Až se index vytvoří, můžete odesílat dotazy, aby se z indexu vracely dokumenty. Na portálu použijte **Průzkumníka služby Hledání**, pomocí kterého spusťte dotazy a zobrazte si výsledky. 

1. Na stránce řídicího panelu služby Search klikněte na panelu příkazů na **Průzkumník služby Hledání**.

1. V horní části vyberte **Změnit index**, abyste mohli vybrat index, který jste vytvořili.

1. Zadejte hledaný řetězec pro dotazování indexu, jako například `search=Microsoft&searchFields=organizations`.

Výsledky se vrátí ve formátu JSON, který může být dost podrobný a jen obtížně čitelný, a to hlavně v případě velkých dokumentů, které pocházejí z objektů blob Azure. Pokud není možné si výsledky snadno projít, můžete v dokumentech hledat pomocí CTRL+F. Pro tento dotaz můžete vyhledat v rámci JSON pro konkrétní podmínky. 

CTRL+F vám navíc může pomoct určit, kolik dokumentů v sadě výsledků je. Pro objekty blob Azure portál volí jako klíč metada_storage_path, protože každá hodnota je v dokumentu jedinečná. Pomocí CTRL+F vyhledejte metadata_storage_path a získáte počet dokumentů. 

  ![Příkaz Průzkumníka služby Hledání](./media/cognitive-search-quickstart-blob/search-explorer.png)

## <a name="takeaways"></a>Shrnutí

Právě jste dokončili vaší první cognitive obohacené indexování cvičení. Účelem tohoto rychlého startu bylo představit důležité koncepty a projít s vámi průvodce, abyste mohli rychle vytvořit prototyp řešení kognitivního hledání pomocí svých vlastních dat.

Mezi klíčové koncepty, které jste si snad osvojili, patří závislost na zdrojích dat Azure. Rozšiřování pomocí kognitivního hledání se váže na indexery a indexery pracují s konkrétním zdrojem a službou Azure. I když tento rychlý start používá službu Azure Blob Storage, je možné použít i jiné zdroje dat Azure. Další informace najdete v článku o [indexerech ve službě Azure Search](search-indexer-overview.md).

Další důležitý koncept je to, že dovednosti pracují se vstupními poli. Na portálu si musíte zvolit jedno zdrojové pole pro všechny dovednosti. V kódu se jako vstup dají použít jiná pole nebo výstup z nadřazené dovednosti.

 Vstupy dovednosti se mapují na výstupní pole v indexu. Vnitřně portál nastaví [poznámky](cognitive-search-concept-annotations-syntax.md) a definuje [sadu dovedností](cognitive-search-defining-skillset.md), čímž určuje pořadí operací v obecném toku. Tyto kroky jsou na portálu skryté, ale až začnete psát kód, budou tyto koncepty důležité.

Nakonec jste zjistili, že k zobrazení výsledků slouží dotazy na index. Azure Search vlastně poskytuje prohledávatelný index, na který můžete poslat dotaz buď pomocí [jednoduché](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search), nebo [plně rozšířené syntaxe dotazů](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search). Index, který obsahuje rozšířená pole, se v ničem neliší od ostatních indexů. Pokud chcete začlenit standardní nebo [vlastní analyzátory](search-analyzers.md), [profily vyhodnocování](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [synonyma](search-synonyms.md), [filtry s charakteristikou](search-filters-facets.md), vyhledávání na základě geografických dat nebo jakoukoli jinou funkci služby Azure Search, nic tomu nebrání.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už jste všechno dostatečně prozkoumali, nejrychlejší způsob, jak vše vyčistit, je odstranit skupinu prostředků, která obsahuje službu Azure Search a službu Azure Blob.  

Za předpokladu, že jste vložili obě služby do stejné skupiny, odstraňte skupinu prostředků. Tím se trvale odstraní všechno, co v ní je, včetně služeb a veškerého uloženého obsahu, který jste pro toto cvičení vytvořili. Na portálu najdete název skupiny prostředků na stránce Přehled jednotlivých služeb.

## <a name="next-steps"></a>Další postup

V závislosti na tom, jak jste zřídili prostředku služeb Cognitive Services můžete experimentovat s indexování a rozšiřování opětovným spuštěním průvodce, jiné dovednosti a pole datového zdroje. Pokud chcete postup opakovat, odstraňte index a indexer a pak znovu indexer vytvořte s tím, že vyberete novou kombinaci možností.

+ V části **Přehled** > **Indexy** vyberte index, který jste vytvořili, a klikněte na **Odstranit**.

+ V části **Přehled** klikněte dvakrát na dlaždici **Indexery**. Najděte indexer, který jste vytvořili, a odstraňte ho.

Alternativně můžete znovu použít ukázková data a služby, které jste vytvořili, a v dalším kurzu se naučit dělat totéž programově. 

> [!div class="nextstepaction"]
> [Kurz: Přečtěte si, že že kognitivní vyhledávání rozhraní REST API](cognitive-search-tutorial-blob.md)
