---
title: 'Rychlý Start: vytvoření dovednosti v Azure Portal'
titleSuffix: Azure Cognitive Search
description: Průvodce importem dat slouží k přidání dovedností rozpoznávání do kanálu indexování. K vnímání dovedností patří optické rozpoznávání znaků (OCR) a zpracování přirozeného jazyka.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/04/2019
ms.openlocfilehash: 2280b718fe949384bb67b1b606ab143ddca8e077
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113348"
---
# <a name="quickstart-create-an-azure-cognitive-search-cognitive-skillset-in-the-azure-portal"></a>Rychlý Start: vytvoření služby Azure Kognitivní hledání rozpoznávání dovednosti v Azure Portal

Dovednosti je funkce AI, která extrahuje informace a strukturu z velkých nerozlišených textových nebo obrázkových souborů a zpřístupňuje je pro fulltextové vyhledávací dotazy v Azure Kognitivní hledání. 

V tomto rychlém startu spojíte služby a data v cloudu Azure, abyste mohli vytvořit dovednosti. Jakmile bude vše na svém místě, spustíte průvodce **importem dat** na portálu, aby se všechno načetlo dohromady. Konečným výsledkem je index s možností vyhledávání naplněný daty vytvořeným zpracováním AI, na kterých se můžete dotazovat na portálu ([Průzkumník hledání](search-explorer.md)).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="create-services-and-load-data"></a>Vytváření služeb a načítání dat

Tento rychlý Start používá pro AI Azure Kognitivní hledání, Azure Blob Storage a [azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) . 

Vzhledem k tomu, že je zatížení tak malé, Cognitive Services na pozadí po dobu, po které se vyvolají z Azure Kognitivní hledání, po dobu až 20 transakcí denně zajišťovat bezplatné zpracování. Pokud používáte ukázková data, která poskytujeme, můžete přeskočit vytvoření nebo připojení prostředku Cognitive Services.

1. [Stáhněte si ukázková data](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) sestávající z malé sady souborů různých typů. Soubory rozbalte.

1. [Vytvořte si účet Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) nebo v rámci aktuálního předplatného [Najděte existující účet](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) . 

   Vyberte stejnou oblast jako Azure Kognitivní hledání. Vyberte typ účtu StorageV2 (pro obecné účely v2), pokud chcete vyzkoušet funkci Knowledge Store později v jiném návodu. V opačném případě vyberte libovolný typ.

1. Otevřete stránky služby BLOB Services a vytvořte kontejner. Můžete použít výchozí úroveň veřejného přístupu. 

1. V části kontejner klikněte na **Odeslat** a nahrajte ukázkové soubory, které jste stáhli v prvním kroku. Všimněte si, že máte široké spektrum typů obsahu včetně obrázků a souborů aplikací, které nejsou fulltextově prohledávatelné v jejich nativních formátech.

   ![Zdrojové soubory ve službě Azure Blob Storage](./media/cognitive-search-quickstart-blob/sample-data.png)

1. [Vytvořte službu Azure kognitivní hledání](search-create-service-portal.md) nebo [Najděte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v rámci stejného předplatného. Pro tento rychlý Start můžete použít bezplatnou službu.

<!-- 1. You are almost done with this resource, but before you leave these pages, use a link on the left navigation pane to open the **Access Keys** page. In many tutorials, especially those that use the REST API, you will need a connection string to retrieve data from Blob storage. A connection string looks similar to the following example: `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net` -->

Nyní jste připraveni na přesun Průvodce importem dat.

## <a name="run-the-import-data-wizard"></a>Spuštění Průvodce importem dat

Na stránce Přehled služby Search klikněte na panelu příkazů na **importovat data** a nastavte obohacení vnímání ve čtyřech krocích.

  ![Příkaz pro import dat](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1-create-a-data-source"></a>Krok 1: Vytvoření zdroje dat

1. V části **připojit k vašim datům**vyberte **úložiště objektů BLOB v Azure**, vyberte účet úložiště a kontejner, který jste vytvořili. Zadejte název pro zdroj dat a u ostatních položek nechejte výchozí hodnoty. 

   ![Konfigurace služby Azure Blob](./media/cognitive-search-quickstart-blob/blob-datasource.png)

1. Pokračujte na další stránku.

### <a name="step-2-add-cognitive-skills"></a>Krok 2: Přidání kognitivních dovedností

Dále přidejte dovednosti rozpoznávání pro vyvolání zpracování přirozeného jazyka. Ukázková data se skládají z 12 souborů, takže bezplatné plnění 20 transakcí v Cognitive Services je pro tento rychlý Start dostatečné. Vzhledem k tomu, že nepoužíváte optické rozpoznávání znaků, budou se v tomto procesu počítat, prolomit a používat pouze soubory bez obrázku.

1. V tomto rychlém startu používáme **bezplatný** Cognitive Services prostředek.

   ![Připojení Cognitive Services](media/cognitive-search-quickstart-blob/cog-search-attach.png)

1. Rozbalte možnost **Přidat dovednosti** a vyberte dovednosti, které provádějí zpracování v přirozeném jazyce. Pro tento rychlý start zvolte rozpoznávání entit pro lidi, organizace a lokality.

   ![Připojení Cognitive Services](media/cognitive-search-quickstart-blob/skillset.png)

1. Přijměte výchozí pole zdroje: `content`. Může se zdát, že se jedná o malý cíl, ale u objektů blob Azure obsahuje pole `content` většinu dokumentu objektu BLOB (například aplikaci Word doc nebo PowerPoint), což má za cíl dobrý kandidát.

1. Pokračujte na další stránku.

> [!NOTE]
> Dovednosti zpracování přirozeného jazyka pracují s textovým obsahem v ukázkové sadě dat. Vzhledem k tomu, že jsme nevybrali možnost optického rozpoznávání znaků, soubory JPEG a PNG nalezené v ukázkové sadě dat nebudou zpracovávány v tomto rychlém startu. 

### <a name="step-3-configure-the-index"></a>Krok 3: Konfigurace indexu

V Azure Kognitivní hledání index obsahuje obsah s možností prohledávání a průvodce **importem dat** vám většinou může vytvořit schéma vzorkováním zdroje dat. V tomto kroku si prohlédněte vygenerované schéma a případně opravte všechna nastavení. Níže je uvedené výchozí schéma vytvořené pro ukázkovou datovou sadu objektů BLOB.

Pro tento rychlý start průvodce odvedl dobrou práci při nastavování rozumných výchozích hodnot: 

+ Výchozí název je *azureblobu-index* založený na typu zdroje dat. 

+ Výchozí pole jsou založena na původním zdrojovém datovém poli (`content`) a výstupní pole (`people`, `organizations`a `locations`) vytvořená dovednostmi rozpoznávání. Výchozí datové typy jsou odvozeny z metadat a vzorkování dat.

+ Výchozí klíč dokumentu je *metadata_storage_path* (vybráno, protože pole obsahuje jedinečné hodnoty).

+ Výchozí atributy lze **získat a** **Vyhledat** u těchto polí. **Prohledávatelné** signalizuje, že pole může být prohledáno. Možnost získatelné **znamená, že se dá vrátit** do výsledků. Průvodce předpokládá, že chcete tato pole získat a prohledávat, protože jste je vytvořili přes dovednosti.

  ![Pole indexu](media/cognitive-search-quickstart-blob/index-fields.png)

Všimněte si přeškrtnutí a otazníku **u atributu získat** pomocí pole `content`. Pro dokumenty objektů BLOB s vysokým textem obsahuje `content` pole hromadně soubor, který může běžet do tisíců řádků. Pokud potřebujete předat obsah souboru do klientského kódu, **Ujistěte se, že je** vybraná možnost načístelné. V opačném případě zvažte zrušení zaškrtnutí tohoto atributu u `content`, pokud jsou extrahované elementy (`people`, `organizations`a `locations`) pro vaše účely dostačující.

Označení **pole jako k dispozici neznamená,** že pole *musí* být ve výsledcích hledání. Složení výsledků hledání můžete přesně řídit pomocí parametru **$Select** dotazu k určení, která pole se mají zahrnout. Pro textová pole, jako je například `content`, **$Select** parametr je vaše řešení pro zajištění spravovatelných výsledků hledání pro lidské uživatele vaší aplikace, zatímco kód klienta má přístup ke všem informacím, které potřebuje, **prostřednictvím atributu získat** .
  
Pokračujte na další stránku.

### <a name="step-4-configure-the-indexer"></a>Krok 4: Konfigurace indexeru

Indexer je prostředek vysoké úrovně, který řídí proces indexování. Určuje název zdroje dat, cílový index a četnost provádění. Průvodce **importem dat** vytvoří několik objektů a je vždycky indexer, který můžete opakovaně spouštět.

1. Na stránce **indexer** můžete přijmout výchozí název a kliknout na možnost **jednou** naplánovat, aby se spustila hned. 

   ![Definice indexeru](media/cognitive-search-quickstart-blob/indexer-def.png)

1. Kliknutím na **Odeslat** vytvořte a souběžně spusťte indexer.

## <a name="monitor-status"></a>Stav monitorování

Indexování dovedností pro rozpoznávání trvá déle než typické indexování založené na textu. Pokud chcete sledovat průběh, přejděte na stránku Přehled a klikněte na **indexery** uprostřed stránky.

K tomuto upozornění dochází, když jsou soubory obrázků JPG a PNG ve zdroji dat a z tohoto kanálu jsme vynechali dovednost OCR. Také najdete oznámení o zkrácení. Extrakce je omezena na 32 000 znaků na úrovni Free.

  ![Oznámení služby Azure Kognitivní hledání](./media/cognitive-search-quickstart-blob/indexer-notification.png)

Indexování a rozšiřování může nějakou dobu trvat. To je důvod, proč se pro seznamování s touto službou doporučuje používat menší sady dat. 

V Azure Portal můžete také monitorovat protokol aktivit oznámení pro odkaz na stav **oznámení v Azure kognitivní hledání** . Provedení může trvat několik minut.

## <a name="query-in-search-explorer"></a>Dotaz v Průzkumníku služby Hledání

Až se index vytvoří, můžete odesílat dotazy, aby se z indexu vracely dokumenty. Na portálu použijte **Průzkumníka služby Hledání**, pomocí kterého spusťte dotazy a zobrazte si výsledky. 

1. Na stránce řídicího panelu služby Search klikněte na panelu příkazů na **Průzkumník služby Hledání**.

1. V horní části vyberte **Změnit index**, abyste mohli vybrat index, který jste vytvořili.

1. Zadejte hledaný řetězec pro dotazování indexu, například `search=Microsoft&searchFields=Organizations`.

Výsledky se vrátí ve formátu JSON, který může být dost podrobný a jen obtížně čitelný, a to hlavně v případě velkých dokumentů, které pocházejí z objektů blob Azure. Pokud není možné si výsledky snadno projít, můžete v dokumentech hledat pomocí CTRL+F. Pro tento dotaz můžete vyhledat konkrétní výrazy ve formátu JSON. 

CTRL+F vám navíc může pomoct určit, kolik dokumentů v sadě výsledků je. Pro objekty blob Azure portál volí jako klíč metada_storage_path, protože každá hodnota je v dokumentu jedinečná. Pomocí CTRL+F vyhledejte metadata_storage_path a získáte počet dokumentů. 

  ![Příkaz Průzkumníka služby Hledání](./media/cognitive-search-quickstart-blob/search-explorer.png)

## <a name="takeaways"></a>Shrnutí

Nyní jste vytvořili první dovednosti a zjistili jste důležité koncepty, které jsou užitečné pro vytváření prototypů obohaceného řešení hledání pomocí vlastních dat.

Mezi klíčové koncepty, které jste si snad osvojili, patří závislost na zdrojích dat Azure. Dovednosti je vázán na indexer a indexery jsou specifické pro Azure a zdroje. I když tento rychlý start používá službu Azure Blob Storage, je možné použít i jiné zdroje dat Azure. Další informace najdete v tématu [Indexery v Azure kognitivní hledání](search-indexer-overview.md).

Další důležitý koncept je to, že dovednosti pracují se vstupními poli. Na portálu si musíte zvolit jedno zdrojové pole pro všechny dovednosti. V kódu se jako vstup dají použít jiná pole nebo výstup z nadřazené dovednosti.

Výstup je směrován na index vyhledávání a existuje mapování mezi páry název-hodnota vytvořené během indexování a jednotlivých polí v indexu. Vnitřně portál nastaví [poznámky](cognitive-search-concept-annotations-syntax.md) a definuje [sadu dovedností](cognitive-search-defining-skillset.md), čímž určuje pořadí operací v obecném toku. Tyto kroky jsou na portálu skryté, ale až začnete psát kód, budou tyto koncepty důležité.

Nakonec jste se dozvěděli, že by bylo možné ověřit obsah pomocí dotazu na index. V tomto případě je to, co Azure Kognitivní hledání poskytuje, prohledávatelný index, pomocí kterého se můžete dotazovat pomocí [jednoduché](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) nebo [plně rozšířené syntaxe dotazu](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search). Index, který obsahuje rozšířená pole, se v ničem neliší od ostatních indexů. Pokud chcete zahrnout standardní nebo [vlastní analyzátory](search-analyzers.md), [profily vyhodnocování](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [synonyma](search-synonyms.md), [omezující filtry](search-filters-facets.md), geografické vyhledávání nebo jakékoli jiné funkce Azure kognitivní hledání, můžete to určitě udělat.

## <a name="clean-up"></a>Vyčištění

Pokud pracujete ve vlastním předplatném, je vhodné na konci projektu zjistit, zda stále potřebujete prostředky, které jste vytvořili. Prostředky, které se na něm zbývá, můžou mít náklady na peníze. Prostředky můžete odstranit jednotlivě nebo odstranit skupinu prostředků, abyste odstranili celou sadu prostředků.

Prostředky můžete najít a spravovat na portálu pomocí odkazu **všechny prostředky** nebo **skupiny prostředků** v levém navigačním podokně.

Pokud používáte bezplatnou službu, pamatujte na to, že jste omezeni na tři indexy, indexery a zdroje dat. Jednotlivé položky na portálu můžete odstranit, aby zůstaly pod limitem. 

## <a name="next-steps"></a>Další kroky

Dovednosti můžete vytvořit pomocí portálu, sady .NET SDK nebo REST API. Pokud chcete pokračovat ve své znalosti, zkuste REST API pomocí post a dalších ukázkových dat.

> [!div class="nextstepaction"]
> [Kurz: Přidání struktury do "nestrukturovaného obsahu" s obohacením AI](cognitive-search-tutorial-blob.md)

> [!Tip]
> Pokud chcete tento cvičení zopakovat nebo vyzkoušet jiný návod k rozšíření AI, odstraňte indexer na portálu. Odstranění indexeru resetuje čítač bezplatných denních transakcí zpět na nulu pro zpracování Cognitive Services.