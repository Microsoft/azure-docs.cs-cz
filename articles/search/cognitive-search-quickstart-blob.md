---
title: Vytvoření dovednosti v Azure Portal
titleSuffix: Azure Cognitive Search
description: V tomto rychlém startu na portálu se naučíte používat Průvodce importem dat k přidání dovedností rozpoznávání do kanálu indexování v Azure Kognitivní hledání. Mezi dovednosti patří optické rozpoznávání znaků (OCR) a zpracování přirozeného jazyka.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 03/21/2021
ms.openlocfilehash: d3e1b73789d6bd4df3dfe9a0e05048f9bbbb25bb
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104770967"
---
# <a name="quickstart-create-an-azure-cognitive-search-cognitive-skillset-in-the-azure-portal"></a>Rychlý Start: vytvoření služby Azure Kognitivní hledání rozpoznávání dovednosti v Azure Portal

Tento rychlý Start ukazuje podporu dovednosti na portálu, která ukazuje, jak optické rozpoznávání znaků (OCR) a rozpoznávání entit lze použít k vytvoření vyhledávaného textového obsahu z obrázků a souborů aplikace.

V rámci přípravy vytvoříte několik prostředků a nahrajete ukázkové obrázky a soubory obsahu aplikace. Jakmile bude vše na svém místě, spustíte průvodce **importem dat** v Azure Portal, aby se všechno načetlo dohromady. Konečným výsledkem je index s možností vyhledávání naplněný daty vytvořeným zpracováním AI, na kterých se můžete dotazovat na portálu ([Průzkumník hledání](search-explorer.md)).

Chcete začít s kódem? Viz [kurz: použití Rest a AI ke generování vyhledávaného obsahu z objektů blob Azure](cognitive-search-tutorial-blob.md) nebo [kurzu: k vygenerování vyhledávaného obsahu z objektů blob Azure použijte .NET a AI](cognitive-search-tutorial-blob-dotnet.md) .

## <a name="prerequisites"></a>Předpoklady

Než začnete, vydejte tyto požadavky:

+ Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/).

+ Služba Azure Kognitivní hledání. [Vytvořte službu](search-create-service-portal.md) nebo [vyhledejte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v rámci aktuálního předplatného. Pro tento rychlý Start můžete použít bezplatnou službu. 

+ Účet Azure Storage s [úložištěm objektů BLOB](../storage/blobs/index.yml).

> [!NOTE]
> V tomto rychlém startu se také pro AI používá [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) . Vzhledem k tomu, že úloha je tak malá, Cognitive Services po dobu bezplatného zpracování po dobu až 20 transakcí klepnuli na pozadí. To znamená, že můžete dokončit toto cvičení, aniž byste museli vytvořit další prostředek Cognitive Services.

## <a name="set-up-your-data"></a>Nastavení dat

V následujících krocích nastavte kontejner objektů BLOB v Azure Storage, aby se ukládaly heterogenní soubory obsahu.

1. [Stáhněte si ukázková data](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) sestávající z malé sady souborů různých typů. Soubory rozbalte.

1. [Vytvořte si účet Azure Storage](../storage/common/storage-account-create.md?tabs=azure-portal) nebo [Najděte existující účet](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). 

   + Vyberte stejnou oblast jako Azure Kognitivní hledání, abyste se vyhnuli poplatkům za šířku pásma. 

   + Vyberte StorageV2 (obecné účely v2).

1. Otevřete stránky služby BLOB Services a vytvořte kontejner. Můžete použít výchozí úroveň veřejného přístupu. 

1. V části kontejner klikněte na **Odeslat** a nahrajte ukázkové soubory, které jste stáhli v prvním kroku. Všimněte si, že máte široké spektrum typů obsahu včetně obrázků a souborů aplikací, které nejsou fulltextově prohledávatelné v jejich nativních formátech.

   :::image type="content" source="media/cognitive-search-quickstart-blob/sample-data.png" alt-text="Zdrojové soubory ve službě Azure Blob Storage" border="false":::

Nyní jste připraveni na přesun Průvodce importem dat.

## <a name="run-the-import-data-wizard"></a>Spuštění Průvodce importem dat

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) pomocí svého účtu Azure.

1. [Vyhledejte vyhledávací službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) a na stránce Přehled klikněte na tlačítko **importovat data** na panelu příkazů a nastavte obohacení vnímání ve čtyřech krocích.

   :::image type="content" source="media/cognitive-search-quickstart-blob/import-data-cmd2.png" alt-text="Příkaz pro import dat" border="false":::

### <a name="step-1---create-a-data-source"></a>Krok 1 – Vytvoření zdroje dat

1. V části **připojit k vašim datům** vyberte **úložiště objektů BLOB v Azure**, vyberte účet úložiště a kontejner, který jste vytvořili. Zadejte název pro zdroj dat a u ostatních položek nechejte výchozí hodnoty. 

   :::image type="content" source="media/cognitive-search-quickstart-blob/blob-datasource.png" alt-text="Konfigurace služby Azure Blob" border="false":::

    Pokračujte na další stránku.

### <a name="step-2---add-cognitive-skills"></a>Krok 2 – Přidání dovedností rozpoznávání

Dále nakonfigurujte rozšíření AI tak, aby se vyvolalo rozpoznávání OCR, analýza obrázků a zpracování přirozeného jazyka. 

1. V tomto rychlém startu používáme **bezplatný** Cognitive Services prostředek. Ukázková data se skládají ze 14 souborů, takže bezplatné plnění 20 transakcí v Cognitive Services je pro tento rychlý Start dostatečné. 

   :::image type="content" source="media/cognitive-search-quickstart-blob/cog-search-attach.png" alt-text="Připojit Cognitive Services připojit základní službu" border="false":::

1. Rozbalte položku **Přidat rozšíření** a proveďte čtyři výběry. 

   Povolte rozpoznávání OCR pro přidání dovedností pro analýzu obrázků na stránku průvodce.

   Nastavte členitost na stránky pro rozdělení textu do menších bloků dat. Několik textových dovedností je omezeno na 5 až KB.

   Vyberte možnost rozpoznávání entit (osoby, organizace, umístění) a dovednosti při analýze obrázků.

   :::image type="content" source="media/cognitive-search-quickstart-blob/skillset.png" alt-text="Připojit Cognitive Services vybrat služby pro dovednosti" border="false":::

   Pokračujte na další stránku.

### <a name="step-3---configure-the-index"></a>Krok 3 – konfigurace indexu

Index obsahuje obsah, který se dá prohledávat, a průvodce **importem dat** vám většinou může vytvořit schéma vzorkováním zdroje dat. V tomto kroku si prohlédněte vygenerované schéma a případně opravte všechna nastavení. Níže je uvedené výchozí schéma vytvořené pro ukázkovou datovou sadu objektů BLOB.

Pro tento rychlý start průvodce odvedl dobrou práci při nastavování rozumných výchozích hodnot:  

+ Výchozí pole jsou založena na vlastnostech existujících objektů BLOB a nových polích, která obsahují výstup obohacení (například, `people` , `organizations` `locations` ). Datové typy jsou odvozeny z metadat a vzorkování dat.

+ Výchozí klíč dokumentu je *metadata_storage_path* (vybráno, protože pole obsahuje jedinečné hodnoty).

+ Výchozí atributy lze **získat a** **prohledávatelné**. **Prohledávatelné** umožňuje fulltextové hledání pole. Možnost získatelné **znamená,** že hodnoty polí lze vracet ve výsledcích. Průvodce předpokládá, že chcete tato pole získat a prohledávat, protože jste je vytvořili přes dovednosti.

  :::image type="content" source="media/cognitive-search-quickstart-blob/index-fields.png" alt-text="Pole indexu" border="false":::

Všimněte si lomítka a otazníku **u atributu k** disnáročnému v `content` poli. Pro dokumenty objektů BLOB s velkým textem `content` obsahuje pole hromadně soubor, který je potenciálně spuštěný v tisících řádků. Toto pole je nepraktický ve výsledcích hledání a mělo by se pro tuto ukázku vyloučit. 

Pokud však potřebujete předat obsah souboru do klientského kódu, **Ujistěte se,** že je vybrána možnost získatelné zůstává. V opačném případě zvažte zrušení zaškrtnutí tohoto atributu, `content` Pokud jsou extrahované elementy (například `people` ,, `organizations` `locations` a tak dále) dostatečné.

Označení **pole jako k dispozici neznamená,** že pole *musí* být ve výsledcích hledání. Složení výsledků hledání můžete přesně řídit pomocí parametru **$Select** dotazu k určení, která pole se mají zahrnout. U textových polí, jako je `content` například parametr **$Select** vaše řešení pro zajištění spravovatelných výsledků hledání pro lidské uživatele vaší aplikace, zatímco kód klienta má přístup ke všem informacím, které potřebuje, prostřednictvím atributu získat. 
  
Pokračujte na další stránku.

### <a name="step-4---configure-the-indexer"></a>Krok 4 – Konfigurace indexeru

Indexer je prostředek vysoké úrovně, který řídí proces indexování. Určuje název zdroje dat, cílový index a četnost provádění. Průvodce **importem dat** vytvoří několik objektů a je vždycky indexer, který můžete opakovaně spouštět.

1. Na stránce **indexer** můžete přijmout výchozí název a kliknout na možnost **jednou** naplánovat, aby se spustila hned. 

   :::image type="content" source="media/cognitive-search-quickstart-blob/indexer-def.png" alt-text="Definice indexeru" border="false":::

1. Kliknutím na **Odeslat** vytvořte a souběžně spusťte indexer.

## <a name="monitor-status"></a>Stav monitorování

Pro rozpoznávání dovedností se indexování trvá déle než typické indexování založené na textu, zejména optické rozpoznávání OCR a obrazové analýzy. Pokud chcete sledovat průběh, přejděte na stránku Přehled a klikněte na **indexery** uprostřed stránky.

  :::image type="content" source="media/cognitive-search-quickstart-blob/indexer-notification.png" alt-text="Oznámení služby Azure Kognitivní hledání" border="false":::

Upozornění jsou normální vzhledem k široké škále typů obsahu. Některé typy obsahu nejsou platné pro určité dovednosti a na nižších úrovních je běžné, že mají [omezení indexerů](search-limits-quotas-capacity.md#indexer-limits). Například oznámení o zkracování 32 000 znaků jsou omezením indexeru na úrovni Free. Pokud jste tuto ukázku spustili na vyšší úrovni, mnoho upozornění na zkracování by se nezobrazovalo.

Chcete-li kontrolovat upozornění nebo chyby, otevřete stránku historie spouštění kliknutím na stav upozornění v seznamu indexery.

Na této stránce klikněte znovu na stav upozornění, abyste zobrazili seznam upozornění, která jsou podobná uvedenému. 

  :::image type="content" source="media/cognitive-search-quickstart-blob/indexer-warnings.png" alt-text="Seznam upozornění indexeru" border="false":::

Podrobnosti se zobrazí po kliknutí na konkrétní stavový řádek. Toto upozornění znamená, že sloučení bylo zastaveno po dosažení maximální prahové hodnoty (Tento konkrétní PDF je velký).

  :::image type="content" source="media/cognitive-search-quickstart-blob/warning-detail.png" alt-text="Podrobnosti upozornění" border="false":::

## <a name="query-in-search-explorer"></a>Dotaz v Průzkumníku služby Hledání

Po vytvoření indexu můžete spustit dotazy a vracet výsledky. Na portálu použijte Průzkumníka služby **Search** pro tuto úlohu. 

1. Na stránce řídicího panelu služby Search klikněte na panelu příkazů na **Průzkumník služby Hledání**.

1. V horní části vyberte **Změnit index**, abyste mohli vybrat index, který jste vytvořili.

1. Zadejte hledaný řetězec pro dotazování indexu, například `search=Microsoft&$select=people,organizations,locations,imageTags` .

Výsledky jsou vraceny jako JSON, které mohou být podrobné a těžko čitelný, zejména ve velkých dokumentech pocházejících z objektů blob Azure. Několik tipů pro hledání v tomto nástroji zahrnuje následující postupy:

+ Připojit `$select` a určete, která pole se mají zahrnout do výsledků. 
+ Pomocí kombinace kláves CTRL-F můžete vyhledat konkrétní vlastnosti nebo výrazy ve formátu JSON.

V řetězcích dotazů se rozlišují malá a velká písmena, takže pokud obdržíte zprávu "neznámé pole", zkontrolujte **pole** nebo **definici indexu (JSON)** a ověřte název a velikost písmen. 

  :::image type="content" source="media/cognitive-search-quickstart-blob/search-explorer.png" alt-text="Příkaz Průzkumníka služby Hledání" border="false":::

## <a name="takeaways"></a>Shrnutí

Nyní jste vytvořili první dovednosti a zjistili jste důležité koncepty, které jsou užitečné pro vytváření prototypů obohaceného řešení hledání pomocí vlastních dat.

Mezi klíčové koncepty, které jste si snad osvojili, patří závislost na zdrojích dat Azure. Dovednosti je vázán na indexer a indexery jsou specifické pro Azure a zdroje. I když tento rychlý start používá službu Azure Blob Storage, je možné použít i jiné zdroje dat Azure. Další informace najdete v tématu [Indexery v Azure kognitivní hledání](search-indexer-overview.md). 

Dalším důležitým konceptem je, že dovednosti působí nad typy obsahu a při práci s heterogenním obsahem se některé vstupy přeskočí. Také velké soubory nebo pole mohou překročit omezení indexeru vaší úrovně služby. Při výskytu těchto událostí je běžné zobrazovat upozornění. 

Výstup je směrován na index vyhledávání a existuje mapování mezi páry název-hodnota vytvořené během indexování a jednotlivých polí v indexu. Vnitřně portál nastaví [poznámky](cognitive-search-concept-annotations-syntax.md) a definuje [sadu dovedností](cognitive-search-defining-skillset.md), čímž určuje pořadí operací v obecném toku. Tyto kroky jsou na portálu skryté, ale až začnete psát kód, budou tyto koncepty důležité.

Nakonec jste se dozvěděli, že by bylo možné ověřit obsah pomocí dotazu na index. V tomto případě je to, co Azure Kognitivní hledání poskytuje, prohledávatelný index, pomocí kterého se můžete dotazovat pomocí [jednoduché](/rest/api/searchservice/simple-query-syntax-in-azure-search) nebo [plně rozšířené syntaxe dotazu](/rest/api/searchservice/lucene-query-syntax-in-azure-search). Index, který obsahuje rozšířená pole, se v ničem neliší od ostatních indexů. Pokud chcete zahrnout standardní nebo [vlastní analyzátory](search-analyzers.md), [profily vyhodnocování](/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [synonyma](search-synonyms.md), [omezující filtry](search-filters-facets.md), geografické vyhledávání nebo jakékoli jiné funkce Azure kognitivní hledání, můžete to určitě udělat.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud pracujete s vlastním předplatným, je vhodné vždy na konci projektu zkontrolovat, jestli budete vytvořené prostředky ještě potřebovat. Prostředky, které necháte běžet, vás stojí peníze. Můžete odstraňovat prostředky jednotlivě nebo odstraněním skupiny prostředků odstranit celou sadu prostředků najednou.

Prostředky můžete najít a spravovat na portálu pomocí odkazu **všechny prostředky** nebo **skupiny prostředků** v levém navigačním podokně.

Pokud používáte bezplatnou službu, pamatujte na to, že jste omezeni na tři indexy, indexery a zdroje dat. Jednotlivé položky na portálu můžete odstranit, aby zůstaly pod limitem. 

## <a name="next-steps"></a>Další kroky

Dovednosti můžete vytvořit pomocí portálu, sady .NET SDK nebo REST API. Pokud chcete pokračovat ve své znalosti, zkuste REST API pomocí post a dalších ukázkových dat.

> [!div class="nextstepaction"]
> [Kurz: extrakce textu a struktury z objektů BLOB JSON pomocí rozhraní REST API ](cognitive-search-tutorial-blob.md)

> [!Tip]
> Pokud chcete tento cvičení zopakovat nebo vyzkoušet jiný návod k rozšíření AI, odstraňte indexer na portálu. Odstranění indexeru resetuje čítač bezplatných denních transakcí zpět na nulu pro zpracování Cognitive Services.