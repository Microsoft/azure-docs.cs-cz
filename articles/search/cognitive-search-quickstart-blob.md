---
title: 'Úvodní příručka: Vytvoření sady dovedností na webu Azure Portal'
titleSuffix: Azure Cognitive Search
description: V tomto portálu rychlý start, zjistěte, jak pomocí průvodce importdat přidat kognitivní dovednosti do kanálu indexování v Azure Cognitive Search. Dovednosti zahrnují optické rozpoznávání znaků (OCR) a zpracování přirozeného jazyka.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 12/20/2019
ms.openlocfilehash: e2e17ba6af60fa495a03e7d46a07cfe6b66f4e68
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77472413"
---
# <a name="quickstart-create-an-azure-cognitive-search-cognitive-skillset-in-the-azure-portal"></a>Úvodní příručka: Vytvoření kognitivních dovedností Azure Cognitive Search na webu Azure Portal

Skillset je funkce AI, která extrahuje informace a strukturu z velkých nediferencovaných textových nebo obrazových souborů a umožňuje ji indexovat a prohledávat pro fulltextové vyhledávací dotazy v Azure Cognitive Search. 

V tomto rychlém startu budete kombinovat služby a data v cloudu Azure a vytvořit sadu dovedností. Jakmile je vše na svém místě, spustíte na portálu Průvodce **importem dat,** abyste to všechno spojili. Konečným výsledkem je index s vyhledatelný, který je naplněn daty vytvořenými zpracováním AI, na který se můžete dotazovat na portálu ([Průzkumník vyhledávání](search-explorer.md)).

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="create-services-and-load-data"></a>Vytváření služeb a načítání dat

Tento rychlý start používá Azure Cognitive Search, [Azure Blob storage](https://docs.microsoft.com/azure/storage/blobs/)a [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) pro UI. 

Vzhledem k tomu, že zatížení je tak malé, služby Cognitive Services jsou poklepány na pozadí, aby poskytovaly bezplatné zpracování až pro 20 transakcí. Pro tak malou sadu dat můžete přeskočit vytváření nebo připojení prostředek služby Cognitive Services.

1. [Stáhněte si ukázková data](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) sestávající z malé sady souborů různých typů. Rozbalte soubory.

1. [Vytvořte si účet úložiště Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) nebo [najděte existující účet](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). 

   Zvolte stejnou oblast jako Azure Cognitive Search, abyste se vyhnuli poplatkům za šířku pásma. 
   
   Zvolte typ účtu StorageV2 (pro obecné účely V2), pokud chcete později vyzkoušet funkci úložiště znalostí v jiném návodu. V opačném případě zvolte libovolný typ.

1. Otevřete stránky služby blob a vytvořte kontejner. Můžete použít výchozí úroveň veřejného přístupu. 

1. V kontejneru klikněte na **Nahrát** a nahrajte ukázkové soubory, které jste stáhli v prvním kroku. Všimněte si, že máte širokou škálu typů obsahu, včetně obrázků a souborů aplikací, které nejsou fulltextové prohledávatelné v nativních formátech.

   ![Zdrojové soubory ve službě Azure Blob Storage](./media/cognitive-search-quickstart-blob/sample-data.png)

1. [Vytvořte službu Azure Cognitive Search](search-create-service-portal.md) nebo [najděte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Pro tento rychlý start můžete využít bezplatnou službu.

Nyní jste připraveni přejít v průvodci importem dat.

## <a name="run-the-import-data-wizard"></a>Spuštění Průvodce importem dat

Na stránce Přehled vyhledávací služby klikněte na **tlačítko Importovat data** na panelu příkazů a nastavte kognitivní obohacení ve čtyřech krocích.

  ![Příkaz pro import dat](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1---create-a-data-source"></a>Krok 1 – Vytvoření zdroje dat

1. V **části Připojit k datům**zvolte Úložiště objektů **blob Azure**, vyberte účet úložiště a kontejner, který jste vytvořili. Zadejte název pro zdroj dat a u ostatních položek nechejte výchozí hodnoty. 

   ![Konfigurace služby Azure Blob](./media/cognitive-search-quickstart-blob/blob-datasource.png)

    Pokračujte na další stránku.

### <a name="step-2---add-cognitive-skills"></a>Krok 2 - Přidání kognitivních dovedností

Dále nakonfigurujte obohacení AI tak, aby vyvolávač OCR, analýzu obrázků a zpracování přirozeného jazyka. 

1. Pro tento rychlý start používáme prostředek **bezplatných** kognitivních služeb. Ukázková data se skládají ze 14 souborů, takže pro tento rychlý start je dostačující volné přidělení 20 transakcí ve službách Cognitive Services. 

   ![Připojení Cognitive Services](media/cognitive-search-quickstart-blob/cog-search-attach.png)

1. Rozbalte **Přidat obohacení** a proveďte čtyři výběry. 

   Povolte rozpoznávání OCR a přidejte dovednosti analýzy obrázků na stránku průvodce.

   Nastavte rozlišovací schopnost na Stránky, abyste rozdělili text na menší bloky. Několik textových dovedností je omezeno na vstupy 5 KB.

   Zvolte rozpoznávání entit (osoby, organizace, lokality) a dovednosti analýzy obrázků.

   ![Připojení Cognitive Services](media/cognitive-search-quickstart-blob/skillset.png)

   Pokračujte na další stránku.

### <a name="step-3---configure-the-index"></a>Krok 3 – Konfigurace indexu

Index obsahuje prohledávatelný obsah a Průvodce **importem dat** může obvykle vytvořit schéma vzorkováním zdroje dat. V tomto kroku zkontrolujte generované schéma a potenciálně revidovat všechna nastavení. Níže je výchozí schéma vytvořené pro ukázkovou datovou sadu objektů Blob.

Pro tento rychlý start průvodce odvedl dobrou práci při nastavování rozumných výchozích hodnot:  

+ Výchozí pole jsou založena na vlastnostech existujících objektů BLOB a `people`na `organizations` `locations`nových polích, která obsahují výstup obohacení (například , , ). Datové typy jsou odvozeny z metadat a vzorkování dat.

+ Výchozí klíč dokumentu je *metadata_storage_path* (vybráno, protože pole obsahuje jedinečné hodnoty).

+ Výchozí atributy jsou **retrievable** a **Searchable**. **Prohledávatelné** umožňuje fulltextové vyhledávání pole. **Načístelné** znamená, že hodnoty polí mohou být vráceny ve výsledcích. Průvodce předpokládá, že chcete, aby tato pole bylo možné vyhledávat a prohledávat, protože jste je vytvořili pomocí sady dovedností.

  ![Pole indexu](media/cognitive-search-quickstart-blob/index-fields.png)

Všimněte si přeškrtnutého a otazníku na **retrievable** atribut pole. `content` U dokumentů objektů blob `content` s velkým textem obsahuje pole většinu souboru, který může být spuštěn na tisíce řádků. Pole, jako je toto, je ve výsledcích vyhledávání nepraktický a měli byste ho vyloučit pro tuto ukázku. 

Pokud však potřebujete předat obsah souboru do kódu klienta, ujistěte se, že **načítání** zůstane vybráno. V opačném případě zvažte vymazání tohoto atributu `content` na v případě, že extrahované prvky (například `people`, `organizations`, `locations`a tak dále) jsou dostatečné.

Označení pole jako **načístelné** neznamená, že pole *musí* být přítomno ve výsledcích hledání. Složení výsledků hledání můžete přesně řídit pomocí parametru **$select** dotazu a určit, která pole chcete zahrnout. Pro text-těžká `content`pole, jako je **$select** parametr je vaše řešení pro poskytování spravovatelných výsledků hledání pro lidské uživatele aplikace, při zajištění klientský kód má přístup ke všem informacím, které potřebuje prostřednictvím **Retrievable** atribut.
  
Pokračujte na další stránku.

### <a name="step-4---configure-the-indexer"></a>Krok 4 – Konfigurace indexeru

Indexer je prostředek vysoké úrovně, který řídí proces indexování. Určuje název zdroje dat, cílový index a četnost provádění. Průvodce **importem dat** vytvoří několik objektů a z nich je vždy indexer, který lze spustit opakovaně.

1. Na stránce **Indexer** můžete přijmout výchozí název a kliknutím na možnost **Jednou** naplánovat jej okamžitě spustit. 

   ![Definice indexeru](media/cognitive-search-quickstart-blob/indexer-def.png)

1. Chcete-li indexer vytvořit a současně spustit, klepněte na **tlačítko Odeslat.**

## <a name="monitor-status"></a>Sledování stavu

Indexování kognitivních dovedností trvá déle než typické textové indexování, zejména Rozpoznávání OCR a analýza obrázků. Chcete-li sledovat průběh, přejděte na stránku Přehled a uprostřed stránky klikněte na **Indexery.**

  ![Oznámení azure kognitivního vyhledávání](./media/cognitive-search-quickstart-blob/indexer-notification.png)

Upozornění jsou normální vzhledem k široké škále typů obsahu. Některé typy obsahu nejsou platné pro určité dovednosti a na nižších úrovních je běžné, že se setkávají s [limity indexeru](search-limits-quotas-capacity.md#indexer-limits). Například oznámení zkrácení 32 000 znaků jsou limit indexeru na úrovni Free. Pokud jste spustili tuto ukázku na vyšší úrovni, mnoho upozornění na zkrácení zmizí.

Chcete-li zkontrolovat upozornění nebo chyby, klikněte na stav Upozornění v seznamu Indexery a otevřete stránku Historie spuštění.

Na této stránce znovu klikněte na Stav upozornění, chcete-li zobrazit seznam upozornění podobný tomu, který je uveden níže. 

  ![Výstražný seznam indexeru](./media/cognitive-search-quickstart-blob/indexer-warnings.png)

Podrobnosti se zobrazí po klepnutí na určitý stavový řádek. Toto upozornění říká, že sloučení se zastavilo po dosažení maximální prahové hodnoty (tento konkrétní PDF je velký).

  ![Podrobnosti upozornění](./media/cognitive-search-quickstart-blob/warning-detail.png)

## <a name="query-in-search-explorer"></a>Dotaz v Průzkumníku služby Hledání

Po vytvoření indexu můžete spustit dotazy a vrátit výsledky. Na portálu použijte **průzkumníka hledání** pro tento úkol. 

1. Na stránce řídicího panelu služby Search klikněte na panelu příkazů na **Průzkumník služby Hledání**.

1. V horní části vyberte **Změnit index**, abyste mohli vybrat index, který jste vytvořili.

1. Zadejte vyhledávací řetězec pro dotaz `search=Microsoft&$select=people,organizations,locations,imageTags`na index, například .

Výsledky jsou vráceny jako JSON, které mohou být podrobné a těžko čitelné, zejména ve velkých dokumentech pocházejících z objektů BLOB Azure. Několik tipů pro vyhledávání v tomto nástroji patří následující techniky:

+ Chcete-li určit, `$select` která pole mají být zahrnuta do výsledků. 
+ Pomocí kombinace kláves CTRL-F vyhledejte v rámci jsonu konkrétní vlastnosti nebo termíny.

Řetězce dotazu rozlišují malá a velká písmena, takže pokud se zobrazí zpráva "neznámé pole", zkontrolujte **pole** nebo **definici indexu (JSON)** a ověřte název a případ. 

  ![Příkaz Průzkumníka služby Hledání](./media/cognitive-search-quickstart-blob/search-explorer.png)

## <a name="takeaways"></a>Shrnutí

Nyní jste vytvořili svůj první skillset a naučildůležité koncepty užitečné pro vytváření prototypů obohacené řešení vyhledávání pomocí vlastních dat.

Mezi klíčové koncepty, které jste si snad osvojili, patří závislost na zdrojích dat Azure. Skillset je vázán na indexer u indexeru a indexery jsou Azure a specifické pro zdroj. I když tento rychlý start používá službu Azure Blob Storage, je možné použít i jiné zdroje dat Azure. Další informace najdete [v tématu Indexery v Azure Cognitive Search](search-indexer-overview.md). 

Dalším důležitým konceptem je, že dovednosti fungují nad typy obsahu a při práci s heterogenním obsahem budou některé vstupy přeskočeny. Velké soubory nebo pole mohou také překročit limity indexeru vaší úrovně služeb. Je normální zobrazit upozornění, když dojde k těmto událostem. 

Výstup je směrován na index vyhledávání a existuje mapování mezi páry název-hodnota vytvořené během indexování a jednotlivá pole v indexu. Vnitřně portál nastaví [poznámky](cognitive-search-concept-annotations-syntax.md) a definuje [sadu dovedností](cognitive-search-defining-skillset.md), čímž určuje pořadí operací v obecném toku. Tyto kroky jsou na portálu skryté, ale až začnete psát kód, budou tyto koncepty důležité.

Nakonec jste se dozvěděli, že můžete ověřit obsah dotazem na index. Nakonec azure cognitive search poskytuje index, který lze prohledávat, který můžete dotazovat pomocí [jednoduché](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) nebo [plně rozšířené syntaxe dotazu](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search). Index, který obsahuje rozšířená pole, se v ničem neliší od ostatních indexů. Pokud chcete začlenit standardní nebo [vlastní analyzátory](search-analyzers.md), [vyhodnocování profilů](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [synonyma](search-synonyms.md), [fazetované filtry](search-filters-facets.md), geografické vyhledávání nebo jakoukoli jinou funkci Azure Cognitive Search, určitě tak můžete učinit.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud pracujete s vlastním předplatným, je vhodné vždy na konci projektu zkontrolovat, jestli budete vytvořené prostředky ještě potřebovat. Prostředky, které necháte běžet, vás můžou stát peníze. Prostředky můžete odstraňovat jednotlivě nebo můžete odstranit skupinu prostředků, a odstranit tak celou sadu prostředků najednou.

Můžete najít a spravovat prostředky na portálu pomocí odkazu **Všechny prostředky** nebo **skupiny prostředků** v levém navigačním podokně.

Pokud používáte bezplatnou službu, nezapomeňte, že jste omezeni na tři indexy, indexery a zdroje dat. Můžete odstranit jednotlivé položky na portálu, abyste zůstali pod limitem. 

## <a name="next-steps"></a>Další kroky

Dovednosti můžete vytvořit pomocí portálu, sady .NET SDK nebo rozhraní REST API. Chcete-li dále své znalosti, zkuste ROZHRANÍ REST API pomocí Postman a další ukázková data.

> [!div class="nextstepaction"]
> [Kurz: Extrahujte text a strukturu z objektů BLOB JSON pomocí api REST API](cognitive-search-tutorial-blob.md)

> [!Tip]
> Pokud chcete toto cvičení zopakovat nebo zkusit jiný návod k obohacení ai, odstraňte indexer na portálu. Odstranění množení obnoví počítadlo transakcí zdarma denní zpět na nulu pro zpracování cognitive services.