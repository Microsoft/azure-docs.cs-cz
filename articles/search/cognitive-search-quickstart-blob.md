---
title: Vytvoření kanálu kognitivního vyhledávání na webu Azure portal – Azure Search s využitím AI indexování
description: Ukázka dovedností extrakce dat a zpracování přirozeného jazyka a obrázku na portálu Azure Portal s ukázkovými daty
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: quickstart
ms.date: 01/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 191cff21cdaa6a4e94358ed0b9c63cd942f71a6e
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55564557"
---
# <a name="quickstart-create-a-cognitive-search-pipeline-using-skills-and-sample-data"></a>Rychlý start: Vytvoření kanálu kognitivního vyhledávání využitím dovedností a ukázkových dat

Cognitive Search (Preview) přidává dovednosti extrakce dat, zpracování přirozeného jazyka (NLP) a zpracování obrazu do kanálu pro indexaci služby Azure Search, díky čemuž je možné v neprohledávatelném a nestrukturovaném obsahu snadněji vyhledávat. 

Kognitivní vyhledávání kanálu integruje [prostředků služeb Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) – například [OCR](cognitive-search-skill-ocr.md), [rozpoznávání jazyka](cognitive-search-skill-language-detection.md), [rozpoznávání entit](cognitive-search-skill-entity-recognition.md)– do indexovacím procesem. Algoritmy AI služeb Cognitive Services se používají k vyhledání vzorců, funkce a vlastnosti ve zdrojových datech, vrátí struktury a textový obsah, který lze použít v fulltextové vyhledávání řešení založená na Azure Search.

V tomto rychlém startu vytvořte svůj první kanál v rozšíření [webu Azure portal](https://portal.azure.com) před napsat jediný řádek kódu:

> [!div class="checklist"]
> * Začnete s ukázkovými daty ve službě Azure Blob Storage.
> * Konfigurace [ **importovat data** ](search-import-data-portal.md) Průvodce pro cognitive indexování a obohacení 
> * Spustíte průvodce (dovednost entity zjistí lidi, lokality a organizace).
> * Použití [ **Průzkumníka služby Search** ](search-explorer.md) dotazy na data bohatších možností

## <a name="supported-regions"></a> Podporované oblasti

Kognitivní hledání si můžete vyzkoušet ve službě Azure Search vytvořené v následujících oblastech:

* Západní střed USA
* Středojižní USA
* USA – východ
* Východní USA 2
* Západní USA 2
* Kanada – střed
* Západní Evropa
* Velká Británie – jih
* Severní Evropa
* Brazílie – jih
* Jihovýchodní Asie
* Střed Indie
* Austrálie – východ

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

> [!NOTE]
> Od 21. prosince 2018 se budou moct přidružit dovednosti Azure Search prostředku služeb Cognitive Services. To vám umožní spouštění poplatků za využití jeho dovedností. K tomuto datu také začneme pro extrakci image jako součást fáze hádání dokumentu. Extrakce textu z dokumentů se bude dál nabízet bez dalších poplatků.
>
> Provádění předdefinované dovednosti budou účtovat stávající [přejít ceny služeb Cognitive Services, platit jako můžete](https://azure.microsoft.com/pricing/details/cognitive-services/) . Ceny za extrakce Image se bude účtovat ceny verze preview a je popsaný na [stránce s cenami Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400). Přečtěte si [Další](cognitive-search-attach-cognitive-services.md).

## <a name="prerequisites"></a>Požadavky

Článek [Co je kognitivní hledání?](cognitive-search-concept-intro.md) nabízí základní informace o architektuře a součástech rozšiřování. 

V tomto scénáři se používají výhradně služby Azure. Během přípravy se vytvářejí potřebné služby.

+ [Azure Blob storage](https://azure.microsoft.com/services/storage/blobs/) poskytuje zdroj dat
+ [Služby cognitive Services](https://azure.microsoft.com/services/cognitive-services/) poskytuje AI (tyto prostředky v řádku, můžete vytvořit při zadávání kanál)
+ [Služba Azure Search](https://azure.microsoft.com/services/search/) poskytuje bohatších možností kanál indexování a bohaté volnou formou možnosti vyhledávání textu pro použití ve vlastních aplikací

### <a name="set-up-azure-search"></a>Nastavení služby Azure Search

Nejdříve se do služby Azure Search přihlaste. 

1. Přejděte na [Azure Portal](https://portal.azure.com) a přihlaste se pomocí svého účtu Azure.

1. Klikněte na **Vytvořit prostředek**, vyhledejte Azure Search a klikněte na **Vytvořit**. Pokud službu Search nastavujete poprvé a potřebujete ještě pomoc, přečtěte si informace v článku [Vytvoření služby Azure Search na portálu](search-create-service-portal.md).

  ![Portál řídicího panelu](./media/cognitive-search-tutorial-blob/create-search-service-full-portal.png "Vytvoření služby Azure Search na portálu")

1. Pro skupinu prostředků vytvořte novou skupinu prostředků obsahující všechny prostředky, které vytvoříte v tomto rychlém startu. To usnadní čištění prostředků, až rychlý start dokončíte.

1. Pro umístění, zvolte jednu z [podporované oblasti](#supported-regions) pro kognitivního vyhledávání.

1. V části Cenová úroveň můžete pro účely kurzů a rychlých startů vytvořit bezplatnou (**Free**) službu. Pokud chcete důkladněji zkoumat svá vlastní data, vytvořte si [placenou službu](https://azure.microsoft.com/pricing/details/search/), např. **Basic** nebo **Standard**. 

  Bezplatná služba je omezená na 3 indexy, 16 MB maximální velikosti objektu blob a 2 minuty indexování, což pro úplné využití funkcí kognitivního hledání nestačí. Pokud se chcete podívat na limity pro jednotlivé úrovně, najdete je v článku o [limitech služeb](search-limits-quotas-capacity.md).

  ![Stránka definice služby na portálu](./media/cognitive-search-tutorial-blob/create-search-service2.png "Stránka definice služby na portálu")

  > [!NOTE]
  > Cognitive Search je ve veřejné verzi Preview. Spouštění sad dovedností je v tuto chvíli k dispozici na všech úrovních, včetně bezplatné. Bude moct provádět omezené obohacení bez přiřazení placené prostředku služeb Cognitive Services. Přečtěte si [Další](cognitive-search-attach-cognitive-services.md).

1. Pokud chcete mít k informacím o službě rychlý přístup, připněte si službu na řídicí panel.

  ![Stránka definice služby na portálu](./media/cognitive-search-tutorial-blob/create-search-service3.png "Stránka definice služby na portálu")

### <a name="set-up-azure-blob-service-and-load-sample-data"></a>Nastavení služby Azure Blob a načtení ukázkových dat

Kanál pro rozšiřování načítá data ze zdrojů dat Azure podporovaných [indexery služby Azure Search](search-indexer-overview.md). Mějte prosím na paměti, že Azure Table Storage se nepodporuje pro kognitivního vyhledávání. Při tomto cvičení použijeme službu Blob Storage, na které ukážeme několik typů obsahu.

1. [Stáhněte si ukázková data](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) sestávající z malé sady souborů různých typů. 

1. Zaregistrovat do Azure Blob storage, vytvořte účet úložiště, otevřete stránek služby objektů Blob a vytvořit kontejner. V kontejneru, nastavte úroveň pro veřejný přístup **kontejneru**. Další informace najdete v tématu [části "Vytvoření kontejneru"](../storage/blobs/storage-unstructured-search.md#create-a-container) v *prohledávání nestrukturovaných dat* kurzu.

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
