---
title: 'Rychlý start: Sestavení indexu obohaceného AI v Azure Portal-Azure Search'
description: Data extrakce, přirozeného jazyka a zpracování obrazu na portálu Azure Search indexování pomocí Azure Portal a ukázkových dat.
manager: nitinme
author: HeidiSteen
services: search
ms.service: search
ms.subservice: cognitive-search
ms.topic: quickstart
ms.date: 07/09/2019
ms.author: heidist
ms.openlocfilehash: 72546e6327fc3286455482943dcaedbd5a8e2943
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70744736"
---
# <a name="quickstart-create-an-ai-indexing-pipeline-using-cognitive-skills-in-azure-search"></a>Rychlý start: Vytvoření kanálu indexování AI pomocí dovedností rozpoznávání v Azure Search

Azure Search se integruje s [Cognitive Services](https://azure.microsoft.com/services/cognitive-services/), přidáním extrakce obsahu, zpracováním přirozeného jazyka (NLP) a dovedností pro zpracování obrázků do kanálu indexování Azure Search, aby bylo možné prohledávat neprohledávatelné nebo nestrukturovaný obsah. 

Mnoho prostředků Cognitive Services – například [optické rozpoznávání znaků (OCR](cognitive-search-skill-ocr.md)), [rozpoznání jazyka](cognitive-search-skill-language-detection.md), [rozpoznávání entit](cognitive-search-skill-entity-recognition.md) pro pojmenování může být připojeno k procesu indexování. Algoritmy AI Cognitive Services se používají k hledání vzorů, funkcí a vlastností ve zdrojových datech, vracení struktur a textových obsahu, které se dají používat v řešeních fulltextového vyhledávání založeném na Azure Search.

V tomto rychlém startu vytvořte svůj první kanál pro rozšíření v [Azure Portal](https://portal.azure.com) před zápisem jediného řádku kódu:

> [!div class="checklist"]
> * Začnete s ukázkovými daty ve službě Azure Blob Storage.
> * Konfigurace Průvodce [**importem dat**](search-import-data-portal.md) pro rozpoznávání indexování a obohacení 
> * Spustíte průvodce (dovednost entity zjistí lidi, lokality a organizace).
> * Použití [**Průzkumníka hledání**](search-explorer.md) k dotazování obohacených dat

Tento rychlý Start běží na bezplatné službě, ale počet bezplatných transakcí je omezený na 20 dokumentů za den. Pokud chcete spustit tento rychlý Start více než jednou denně, použijte menší sadu souborů, abyste se mohli přizpůsobit více spuštění.

> [!NOTE]
> Když rozbalíte rozsah zvýšením četnosti zpracování, přidáním dalších dokumentů nebo přidáním dalších algoritmů AI, budete muset [připojit fakturovatelné Cognitive Services prostředku](cognitive-search-attach-cognitive-services.md). Poplatky se účtují při volání rozhraní API v Cognitive Services a pro extrakci obrázků jako součást fáze pro vystavování dokumentů v Azure Search. Pro extrakci textu z dokumentů se neúčtují žádné poplatky.
>
> Při provádění integrovaných dovedností se účtují poplatky za stávající [Cognitive Services průběžných plateb](https://azure.microsoft.com/pricing/details/cognitive-services/). Ceny za extrakci obrázků jsou popsány na [stránce s cenami Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

[Vytvořte službu Azure Search](search-create-service-portal.md) nebo [Najděte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v rámci aktuálního předplatného. Pro tento rychlý Start můžete použít bezplatnou službu.

[Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) poskytuje AI. Tento rychlý Start obsahuje kroky pro přidání těchto prostředků do řádku při zadávání kanálu. Není nutné nastavovat účty předem.

Pro poskytování vstupů do kanálu indexování se vyžadují služby Azure. Můžete použít libovolný zdroj dat podporovaný nástrojem [Azure Search indexery](search-indexer-overview.md). Tento rychlý Start využívá [úložiště objektů BLOB v Azure](https://azure.microsoft.com/services/storage/blobs/) jako kontejner pro zdrojové datové soubory. 

### <a name="set-up-azure-blob-service-and-load-sample-data"></a>Nastavení služby Azure Blob a načtení ukázkových dat

1. [Stáhněte si ukázková data](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) sestávající z malé sady souborů různých typů. 

1. [Zaregistrujte se do Azure Blob Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal), vytvořte účet úložiště, otevřete stránky služby BLOB Services a vytvořte kontejner.  Vytvořte účet úložiště ve stejné oblasti jako Azure Search.

1. V kontejneru, který jste vytvořili, klikněte na **nahrát** a nahrajte ukázkové soubory, které jste stáhli v předchozím kroku.

   ![Zdrojové soubory ve službě Azure Blob Storage](./media/cognitive-search-quickstart-blob/sample-data.png)

## <a name="create-the-enrichment-pipeline"></a>Vytvoření kanálu pro rozšiřování

Vraťte se na stránku řídicí panel služby Azure Search a kliknutím na **importovat data** na panelu příkazů nastavte obohacení vnímání ve čtyřech krocích.

  ![Příkaz pro import dat](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1-create-a-data-source"></a>Krok 1: Vytvoření zdroje dat

V části **připojit k vašim datům**vyberte **úložiště objektů BLOB v Azure**, vyberte účet a kontejner, který jste vytvořili. Zadejte název pro zdroj dat a u ostatních položek nechejte výchozí hodnoty. 

  ![Konfigurace služby Azure Blob](./media/cognitive-search-quickstart-blob/blob-datasource.png)

Pokračujte na další stránku.

  ![Tlačítko Další stránka pro hledání rozpoznávání](media/cognitive-search-quickstart-blob/next-button-add-cog-search.png)

### <a name="step-2-add-cognitive-skills"></a>Krok 2: Přidání dovedností rozpoznávání

Jako další krok přidáte do kanálu indexování kroky pro rozšiřování. Pokud nemáte Cognitive Services prostředek, můžete si zaregistrovat bezplatnou verzi, která vám poskytne 20 transakcí denně. Ukázková data se skládají ze 14 souborů, takže denní přidělení se použije hlavně po spuštění tohoto průvodce.

1. Rozbalte možnost **připojit Cognitive Services** pro zobrazení možností pro rerozhraní API služeb Cognitive Services. Pro účely tohoto kurzu můžete použít **bezplatný** prostředek.

   ![Připojit Cognitive Services](media/cognitive-search-quickstart-blob/cog-search-attach.png)

2. Rozbalte položku **Přidat rozšíření** a vyberte dovednosti, které provádějí zpracování přirozeného jazyka. Pro tento rychlý start zvolte rozpoznávání entit pro lidi, organizace a lokality.

   ![Připojit Cognitive Services](media/cognitive-search-quickstart-blob/skillset.png)

   Portál nabízí integrované dovednosti pro zpracování a analýzu textu v OCR. Na portálu sada dovedností pracuje s jedním polem zdroje. To může vypadat jako malý cíl, ale pro objekty blob služby Azure pole `content` obsahuje většinu dokumentu objektu blob (např. wordový dokument nebo powerpointovou sadu karet). Z toho důvodu je toto pole ideální vstup, protože je v něm veškerý obsah objektu blob.

3. Pokračujte na další stránku.

   ![Přizpůsobení indexu pro další stránku](media/cognitive-search-quickstart-blob/next-button-customize-index.png)

> [!NOTE]
> Dovednosti zpracování přirozeného jazyka pracují s textovým obsahem v ukázkové sadě dat. Vzhledem k tomu, že jsme nevybrali možnost optického rozpoznávání znaků, soubory JPEG a PNG nalezené v ukázkové sadě dat nebudou zpracovávány v tomto rychlém startu. 

### <a name="step-3-configure-the-index"></a>Krok 3: Konfigurace indexu

Průvodce může obvykle odvodit výchozí index. V tomto kroku můžete zobrazit vygenerované schéma indexu a potenciálně upravit nastavení. Níže je uveden výchozí index vytvořený pro ukázkovou datovou sadu objektů BLOB.

Pro tento rychlý start průvodce odvedl dobrou práci při nastavování rozumných výchozích hodnot: 

+ Výchozí název je *azureblobu-index* založený na typu zdroje dat. 

+ Výchozí pole jsou založena na původním zdrojovém datovém poli`content`() a výstupní pole (`people`, `organizations`a `locations`) vytvořená pomocí kanálu pro rozpoznávání. Výchozí datové typy jsou odvozeny z metadat a vzorkování dat.

+ Výchozí klíč je *metadata_storage_path* (Toto pole obsahuje jedinečné hodnoty).

+ Výchozí atributy lze **získat a** **Vyhledat** u těchto polí. **Prohledávatelné** signalizuje, že pole může být prohledáno. Možnost získatelné **znamená, že se dá vrátit** do výsledků. Průvodce předpokládá, že chcete tato pole získat a prohledávat, protože jste je vytvořili přes dovednosti.

  ![Pole indexu](media/cognitive-search-quickstart-blob/index-fields.png)

Všimněte si přeškrtnutí a otazníku **u atributu získat** pomocí `content` pole. Pro dokumenty `content` objektů BLOB s velkým textem obsahuje pole hromadně soubor, který je potenciálně spuštěný v tisících řádků. Pokud potřebujete předat obsah souboru do klientského kódu, **Ujistěte se, že je** vybraná možnost načístelné. V opačném případě zvažte zrušení zaškrtnutí `content` tohoto atributu, pokud jsou`people`extrahované prvky `locations`(, `organizations`, a) pro vaše účely dostačující.

Označení **pole jako k dispozici neznamená,** že pole *musí* být ve výsledcích hledání. Složení výsledků hledání můžete přesně řídit pomocí parametru **$Select** dotazu k určení, která pole se mají zahrnout. Pro textová pole jako `content`je parametr **$Select** vaše řešení pro zajištění spravovatelných výsledků hledání pro lidské uživatele vaší aplikace, zatímco kód klienta má přístup ke všem informacím, které potřebuje přes  **Atribut s** vlastností nelze načíst.
  
Pokračujte na další stránku.

  ![Další stránka – vytvořit indexer](media/cognitive-search-quickstart-blob/next-button-create-indexer.png)

### <a name="step-4-configure-the-indexer"></a>Krok 4: Konfigurace indexeru

Indexer je prostředek vysoké úrovně, který řídí proces indexování. Určuje název zdroje dat, cílový index a četnost provádění. Konečným výsledkem **Průvodce importem dat** je vždy indexer, který můžete opakovaně spouštět.

Na stránce **indexer** můžete přijmout výchozí název a pomocí možnosti **Spustit jednou** naplánovat ji okamžitě spustit. 

  ![Definice indexeru](media/cognitive-search-quickstart-blob/indexer-def.png)

Kliknutím na **Odeslat** vytvořte a souběžně spusťte indexer.

## <a name="monitor-indexing"></a>Monitorování indexování

Dokončení kroků rozšíření trvá déle, než je obvyklé indexování založené na textu. Průvodce by měl otevřít seznam indexerů na stránce Přehled, abyste mohli sledovat průběh. V případě samoobslužné navigace přejděte na stránku Přehled a klikněte na **indexery**.

K varování dochází, protože soubory JPG a PNG jsou soubory obrázků a z tohoto kanálu jsme vynechali dovednost OCR. Také najdete oznámení o zkrácení. Azure Search omezí extrakci na 32 000 znaků na úrovni Free.

  ![Oznámení služby Azure Search](./media/cognitive-search-quickstart-blob/indexer-notification.png)

Indexování a rozšiřování může nějakou dobu trvat. To je důvod, proč se pro seznamování s touto službou doporučuje používat menší sady dat. 

## <a name="query-in-search-explorer"></a>Dotaz v Průzkumníku služby Hledání

Až se index vytvoří, můžete odesílat dotazy, aby se z indexu vracely dokumenty. Na portálu použijte **Průzkumníka služby Hledání**, pomocí kterého spusťte dotazy a zobrazte si výsledky. 

1. Na stránce řídicího panelu služby Search klikněte na panelu příkazů na **Průzkumník služby Hledání**.

1. V horní části vyberte **Změnit index**, abyste mohli vybrat index, který jste vytvořili.

1. Zadejte hledaný řetězec pro dotazování indexu, například `search=Microsoft&searchFields=organizations`.

Výsledky se vrátí ve formátu JSON, který může být dost podrobný a jen obtížně čitelný, a to hlavně v případě velkých dokumentů, které pocházejí z objektů blob Azure. Pokud není možné si výsledky snadno projít, můžete v dokumentech hledat pomocí CTRL+F. Pro tento dotaz můžete vyhledat konkrétní výrazy ve formátu JSON. 

CTRL+F vám navíc může pomoct určit, kolik dokumentů v sadě výsledků je. Pro objekty blob Azure portál volí jako klíč metada_storage_path, protože každá hodnota je v dokumentu jedinečná. Pomocí CTRL+F vyhledejte metadata_storage_path a získáte počet dokumentů. 

  ![Příkaz Průzkumníka služby Hledání](./media/cognitive-search-quickstart-blob/search-explorer.png)

## <a name="takeaways"></a>Shrnutí

Právě jste dokončili své první prostředí pro indexování s obohaceným rozpoznáváním. Účelem tohoto rychlého startu bylo představit důležité koncepty a projít s vámi průvodce, abyste mohli rychle vytvořit prototyp řešení kognitivního hledání pomocí svých vlastních dat.

Mezi klíčové koncepty, které jste si snad osvojili, patří závislost na zdrojích dat Azure. Rozšiřování pomocí kognitivního hledání se váže na indexery a indexery pracují s konkrétním zdrojem a službou Azure. I když tento rychlý start používá službu Azure Blob Storage, je možné použít i jiné zdroje dat Azure. Další informace najdete v článku o [indexerech ve službě Azure Search](search-indexer-overview.md).

Další důležitý koncept je to, že dovednosti pracují se vstupními poli. Na portálu si musíte zvolit jedno zdrojové pole pro všechny dovednosti. V kódu se jako vstup dají použít jiná pole nebo výstup z nadřazené dovednosti.

 Vstupy dovednosti se mapují na výstupní pole v indexu. Vnitřně portál nastaví [poznámky](cognitive-search-concept-annotations-syntax.md) a definuje [sadu dovedností](cognitive-search-defining-skillset.md), čímž určuje pořadí operací v obecném toku. Tyto kroky jsou na portálu skryté, ale až začnete psát kód, budou tyto koncepty důležité.

Nakonec jste zjistili, že k zobrazení výsledků slouží dotazy na index. Azure Search vlastně poskytuje prohledávatelný index, na který můžete poslat dotaz buď pomocí [jednoduché](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search), nebo [plně rozšířené syntaxe dotazů](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search). Index, který obsahuje rozšířená pole, se v ničem neliší od ostatních indexů. Pokud chcete začlenit standardní nebo [vlastní analyzátory](search-analyzers.md), [profily vyhodnocování](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [synonyma](search-synonyms.md), [filtry s charakteristikou](search-filters-facets.md), vyhledávání na základě geografických dat nebo jakoukoli jinou funkci služby Azure Search, nic tomu nebrání.

## <a name="clean-up"></a>Vyčištění

Pokud pracujete ve vlastním předplatném, je vhodné na konci projektu zjistit, zda stále potřebujete prostředky, které jste vytvořili. Prostředky, které se na něm zbývá, můžou mít náklady na peníze. Prostředky můžete odstranit jednotlivě nebo odstranit skupinu prostředků, abyste odstranili celou sadu prostředků.

Prostředky můžete najít a spravovat na portálu pomocí odkazu **všechny prostředky** nebo **skupiny prostředků** v levém navigačním podokně.

Pokud používáte bezplatnou službu, pamatujte na to, že jste omezeni na tři indexy, indexery a zdroje dat. Jednotlivé položky na portálu můžete odstranit, aby zůstaly pod limitem. 

## <a name="next-steps"></a>Další kroky

V závislosti na tom, jak jste zřídili Cognitive Services prostředek, můžete experimentovat s indexováním a obohacením tak, že znovu spustíte Průvodce s různými dovednostmi a zdrojovými datovými poli. Pokud chcete postup opakovat, odstraňte index a indexer a pak znovu indexer vytvořte s tím, že vyberete novou kombinaci možností.

+ V části **Přehled** > **Indexy** vyberte index, který jste vytvořili, a klikněte na **Odstranit**.

+ V části **Přehled** klikněte dvakrát na dlaždici **Indexery**. Najděte indexer, který jste vytvořili, a odstraňte ho.

Alternativně můžete znovu použít ukázková data a služby, které jste vytvořili, a v dalším kurzu se naučit dělat totéž programově. 

> [!div class="nextstepaction"]
> [Kurz: Seznamte se s rozhraními REST API pro vyhledávání rozpoznávání](cognitive-search-tutorial-blob.md)
