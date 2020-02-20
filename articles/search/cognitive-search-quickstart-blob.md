---
title: 'Rychlý Start: vytvoření dovednosti v Azure Portal'
titleSuffix: Azure Cognitive Search
description: V tomto rychlém startu na portálu se naučíte používat Průvodce importem dat k přidání dovedností rozpoznávání do kanálu indexování v Azure Kognitivní hledání. Mezi dovednosti patří optické rozpoznávání znaků (OCR) a zpracování přirozeného jazyka.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 12/20/2019
ms.openlocfilehash: e2e17ba6af60fa495a03e7d46a07cfe6b66f4e68
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472413"
---
# <a name="quickstart-create-an-azure-cognitive-search-cognitive-skillset-in-the-azure-portal"></a>Rychlý Start: vytvoření služby Azure Kognitivní hledání rozpoznávání dovednosti v Azure Portal

Dovednosti je funkce AI, která extrahuje informace a strukturu z velkých nerozlišených textových nebo obrázkových souborů a zpřístupňuje je pro fulltextové vyhledávací dotazy v Azure Kognitivní hledání. 

V tomto rychlém startu spojíte služby a data v cloudu Azure, abyste mohli vytvořit dovednosti. Jakmile bude vše na svém místě, spustíte průvodce **importem dat** na portálu, aby se všechno načetlo dohromady. Konečným výsledkem je index s možností vyhledávání naplněný daty vytvořeným zpracováním AI, na kterých se můžete dotazovat na portálu ([Průzkumník hledání](search-explorer.md)).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="create-services-and-load-data"></a>Vytváření služeb a načítání dat

Tento rychlý Start používá pro AI Azure Kognitivní hledání, Azure [BLOB Storage](https://docs.microsoft.com/azure/storage/blobs/)a [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) . 

Vzhledem k tomu, že je zatížení tak malé, Cognitive Services po pozadí klepněte na pozadí, aby bylo možné zajistit bezplatné zpracování až pro 20 transakcí. Pro takovou malou datovou sadu můžete přeskočit vytvoření nebo připojení prostředku Cognitive Services.

1. [Stáhněte si ukázková data](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) sestávající z malé sady souborů různých typů. Soubory rozbalte.

1. [Vytvořte si účet Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) nebo [Najděte existující účet](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). 

   Vyberte stejnou oblast jako Azure Kognitivní hledání, abyste se vyhnuli poplatkům za šířku pásma. 
   
   Vyberte typ účtu StorageV2 (pro obecné účely v2), pokud chcete vyzkoušet funkci Knowledge Store později v jiném návodu. V opačném případě vyberte libovolný typ.

1. Otevřete stránky služby BLOB Services a vytvořte kontejner. Můžete použít výchozí úroveň veřejného přístupu. 

1. V části kontejner klikněte na **Odeslat** a nahrajte ukázkové soubory, které jste stáhli v prvním kroku. Všimněte si, že máte široké spektrum typů obsahu včetně obrázků a souborů aplikací, které nejsou fulltextově prohledávatelné v jejich nativních formátech.

   ![Zdrojové soubory ve službě Azure Blob Storage](./media/cognitive-search-quickstart-blob/sample-data.png)

1. [Vytvořte službu Azure kognitivní hledání](search-create-service-portal.md) nebo [Najděte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Pro tento rychlý Start můžete použít bezplatnou službu.

Nyní jste připraveni na přesun Průvodce importem dat.

## <a name="run-the-import-data-wizard"></a>Spuštění Průvodce importem dat

Na stránce Přehled služby Search klikněte na panelu příkazů na **importovat data** a nastavte obohacení vnímání ve čtyřech krocích.

  ![Příkaz pro import dat](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1---create-a-data-source"></a>Krok 1 – Vytvoření zdroje dat

1. V části **připojit k vašim datům**vyberte **úložiště objektů BLOB v Azure**, vyberte účet úložiště a kontejner, který jste vytvořili. Zadejte název pro zdroj dat a u ostatních položek nechejte výchozí hodnoty. 

   ![Konfigurace služby Azure Blob](./media/cognitive-search-quickstart-blob/blob-datasource.png)

    Pokračujte na další stránku.

### <a name="step-2---add-cognitive-skills"></a>Krok 2 – Přidání dovedností rozpoznávání

Dále nakonfigurujte rozšíření AI tak, aby se vyvolalo rozpoznávání OCR, analýza obrázků a zpracování přirozeného jazyka. 

1. V tomto rychlém startu používáme **bezplatný** Cognitive Services prostředek. Ukázková data se skládají ze 14 souborů, takže bezplatné plnění 20 transakcí v Cognitive Services je pro tento rychlý Start dostatečné. 

   ![Připojení Cognitive Services](media/cognitive-search-quickstart-blob/cog-search-attach.png)

1. Rozbalte položku **Přidat rozšíření** a proveďte čtyři výběry. 

   Povolte rozpoznávání OCR pro přidání dovedností pro analýzu obrázků na stránku průvodce.

   Nastavte členitost na stránky pro rozdělení textu do menších bloků dat. Několik textových dovedností je omezeno na 5 až KB.

   Vyberte možnost rozpoznávání entit (osoby, organizace, umístění) a dovednosti při analýze obrázků.

   ![Připojení Cognitive Services](media/cognitive-search-quickstart-blob/skillset.png)

   Pokračujte na další stránku.

### <a name="step-3---configure-the-index"></a>Krok 3 – konfigurace indexu

Index obsahuje obsah, který se dá prohledávat, a průvodce **importem dat** vám většinou může vytvořit schéma vzorkováním zdroje dat. V tomto kroku si prohlédněte vygenerované schéma a případně opravte všechna nastavení. Níže je uvedené výchozí schéma vytvořené pro ukázkovou datovou sadu objektů BLOB.

Pro tento rychlý start průvodce odvedl dobrou práci při nastavování rozumných výchozích hodnot:  

+ Výchozí pole jsou založena na vlastnostech existujících objektů BLOB a nových polích, která obsahují výstup rozšíření (například `people``organizations``locations`). Datové typy jsou odvozeny z metadat a vzorkování dat.

+ Výchozí klíč dokumentu je *metadata_storage_path* (vybráno, protože pole obsahuje jedinečné hodnoty).

+ Výchozí atributy lze **získat a** **prohledávatelné**. **Prohledávatelné** umožňuje fulltextové hledání pole. Možnost získatelné **znamená,** že hodnoty polí lze vracet ve výsledcích. Průvodce předpokládá, že chcete tato pole získat a prohledávat, protože jste je vytvořili přes dovednosti.

  ![Pole indexu](media/cognitive-search-quickstart-blob/index-fields.png)

Všimněte si přeškrtnutí a otazníku **u atributu získat** pomocí pole `content`. Pro dokumenty objektů BLOB s vysokým textem obsahuje `content` pole hromadně soubor, který může běžet do tisíců řádků. Toto pole je nepraktický ve výsledcích hledání a mělo by se pro tuto ukázku vyloučit. 

Pokud však potřebujete předat obsah souboru do klientského kódu, **Ujistěte se,** že je vybrána možnost získatelné zůstává. V opačném případě zvažte zrušení zaškrtnutí tohoto atributu u `content`, pokud jsou extrahované elementy (například `people`, `organizations`, `locations`a tak dále) dostatečné.

Označení **pole jako k dispozici neznamená,** že pole *musí* být ve výsledcích hledání. Složení výsledků hledání můžete přesně řídit pomocí parametru **$Select** dotazu k určení, která pole se mají zahrnout. Pro textová pole, jako je například `content`, **$Select** parametr je vaše řešení pro zajištění spravovatelných výsledků hledání pro lidské uživatele vaší aplikace, zatímco kód klienta má přístup ke všem informacím, které potřebuje, **prostřednictvím atributu získat** .
  
Pokračujte na další stránku.

### <a name="step-4---configure-the-indexer"></a>Krok 4 – Konfigurace indexeru

Indexer je prostředek vysoké úrovně, který řídí proces indexování. Určuje název zdroje dat, cílový index a četnost provádění. Průvodce **importem dat** vytvoří několik objektů a je vždycky indexer, který můžete opakovaně spouštět.

1. Na stránce **indexer** můžete přijmout výchozí název a kliknout na možnost **jednou** naplánovat, aby se spustila hned. 

   ![Definice indexeru](media/cognitive-search-quickstart-blob/indexer-def.png)

1. Kliknutím na **Odeslat** vytvořte a souběžně spusťte indexer.

## <a name="monitor-status"></a>Stav monitorování

Pro rozpoznávání dovedností se indexování trvá déle než typické indexování založené na textu, zejména optické rozpoznávání OCR a obrazové analýzy. Pokud chcete sledovat průběh, přejděte na stránku Přehled a klikněte na **indexery** uprostřed stránky.

  ![Oznámení služby Azure Kognitivní hledání](./media/cognitive-search-quickstart-blob/indexer-notification.png)

Upozornění jsou normální vzhledem k široké škále typů obsahu. Některé typy obsahu nejsou platné pro určité dovednosti a na nižších úrovních, na kterých se běžně setkáte s [omezeními pro indexer](search-limits-quotas-capacity.md#indexer-limits). Například oznámení o zkracování 32 000 znaků jsou omezením indexeru na úrovni Free. Pokud jste tuto ukázku spustili na vyšší úrovni, mnoho upozornění na zkracování by se nezobrazovalo.

Chcete-li kontrolovat upozornění nebo chyby, otevřete stránku historie spouštění kliknutím na stav upozornění v seznamu indexery.

Na této stránce klikněte znovu na stav upozornění, abyste zobrazili seznam upozornění, která jsou podobná uvedenému. 

  ![Seznam upozornění indexeru](./media/cognitive-search-quickstart-blob/indexer-warnings.png)

Podrobnosti se zobrazí po kliknutí na konkrétní stavový řádek. Toto upozornění znamená, že sloučení bylo zastaveno po dosažení maximální prahové hodnoty (Tento konkrétní PDF je velký).

  ![Podrobnosti upozornění](./media/cognitive-search-quickstart-blob/warning-detail.png)

## <a name="query-in-search-explorer"></a>Dotaz v Průzkumníku služby Hledání

Po vytvoření indexu můžete spustit dotazy a vracet výsledky. Na portálu použijte Průzkumníka služby **Search** pro tuto úlohu. 

1. Na stránce řídicího panelu služby Search klikněte na panelu příkazů na **Průzkumník služby Hledání**.

1. V horní části vyberte **Změnit index**, abyste mohli vybrat index, který jste vytvořili.

1. Zadejte hledaný řetězec pro dotazování indexu, například `search=Microsoft&$select=people,organizations,locations,imageTags`.

Výsledky jsou vraceny jako JSON, které mohou být podrobné a těžko čitelný, zejména ve velkých dokumentech pocházejících z objektů blob Azure. Několik tipů pro hledání v tomto nástroji zahrnuje následující postupy:

+ Přidejte `$select` k určení, která pole se mají zahrnout do výsledků. 
+ Pomocí kombinace kláves CTRL-F můžete vyhledat konkrétní vlastnosti nebo výrazy ve formátu JSON.

V řetězcích dotazů se rozlišují malá a velká písmena, takže pokud obdržíte zprávu "neznámé pole", zkontrolujte **pole** nebo **definici indexu (JSON)** a ověřte název a velikost písmen. 

  ![Příkaz Průzkumníka služby Hledání](./media/cognitive-search-quickstart-blob/search-explorer.png)

## <a name="takeaways"></a>Shrnutí

Nyní jste vytvořili první dovednosti a zjistili jste důležité koncepty, které jsou užitečné pro vytváření prototypů obohaceného řešení hledání pomocí vlastních dat.

Mezi klíčové koncepty, které jste si snad osvojili, patří závislost na zdrojích dat Azure. Dovednosti je vázán na indexer a indexery jsou specifické pro Azure a zdroje. I když tento rychlý start používá službu Azure Blob Storage, je možné použít i jiné zdroje dat Azure. Další informace najdete v tématu [Indexery v Azure kognitivní hledání](search-indexer-overview.md). 

Dalším důležitým konceptem je, že dovednosti působí nad typy obsahu a při práci s heterogenním obsahem se některé vstupy přeskočí. Také velké soubory nebo pole mohou překročit omezení indexeru vaší úrovně služby. Při výskytu těchto událostí je běžné zobrazovat upozornění. 

Výstup je směrován na index vyhledávání a existuje mapování mezi páry název-hodnota vytvořené během indexování a jednotlivých polí v indexu. Vnitřně portál nastaví [poznámky](cognitive-search-concept-annotations-syntax.md) a definuje [sadu dovedností](cognitive-search-defining-skillset.md), čímž určuje pořadí operací v obecném toku. Tyto kroky jsou na portálu skryté, ale až začnete psát kód, budou tyto koncepty důležité.

Nakonec jste se dozvěděli, že by bylo možné ověřit obsah pomocí dotazu na index. V tomto případě je to, co Azure Kognitivní hledání poskytuje, prohledávatelný index, pomocí kterého se můžete dotazovat pomocí [jednoduché](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) nebo [plně rozšířené syntaxe dotazu](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search). Index, který obsahuje rozšířená pole, se v ničem neliší od ostatních indexů. Pokud chcete zahrnout standardní nebo [vlastní analyzátory](search-analyzers.md), [profily vyhodnocování](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [synonyma](search-synonyms.md), [omezující filtry](search-filters-facets.md), geografické vyhledávání nebo jakékoli jiné funkce Azure kognitivní hledání, můžete to určitě udělat.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud pracujete ve vlastním předplatném, je vhodné na konci projektu zjistit, zda stále potřebujete prostředky, které jste vytvořili. Prostředky, které se na něm zbývá, můžou mít náklady na peníze. Prostředky můžete odstranit jednotlivě nebo odstranit skupinu prostředků, abyste odstranili celou sadu prostředků.

Prostředky můžete najít a spravovat na portálu pomocí odkazu **všechny prostředky** nebo **skupiny prostředků** v levém navigačním podokně.

Pokud používáte bezplatnou službu, pamatujte na to, že jste omezeni na tři indexy, indexery a zdroje dat. Jednotlivé položky na portálu můžete odstranit, aby zůstaly pod limitem. 

## <a name="next-steps"></a>Další kroky

Dovednosti můžete vytvořit pomocí portálu, sady .NET SDK nebo REST API. Pokud chcete pokračovat ve své znalosti, zkuste REST API pomocí post a dalších ukázkových dat.

> [!div class="nextstepaction"]
> [Kurz: extrakce textu a struktury z objektů BLOB JSON pomocí rozhraní REST API](cognitive-search-tutorial-blob.md)

> [!Tip]
> Pokud chcete tento cvičení zopakovat nebo vyzkoušet jiný návod k rozšíření AI, odstraňte indexer na portálu. Odstranění indexeru resetuje čítač bezplatných denních transakcí zpět na nulu pro zpracování Cognitive Services.