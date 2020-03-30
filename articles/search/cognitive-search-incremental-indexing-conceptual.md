---
title: Přírůstkové obohacení (náhled)
titleSuffix: Azure Cognitive Search
description: Ukládat do mezipaměti zprostředkující obsah a přírůstkové změny z kanálu obohacení umělou ai ve službě Azure Storage, abyste zachovali investice do existujících zpracovaných dokumentů. Tato funkce je aktuálně ve verzi Public Preview.
manager: nitinme
author: Vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 09003c26ead9108d07ae339fcf64235c246474a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024139"
---
# <a name="introduction-to-incremental-enrichment-and-caching-in-azure-cognitive-search"></a>Úvod k přírůstkovému obohacení a ukládání do mezipaměti v Azure Cognitive Search

> [!IMPORTANT] 
> Přírůstkové obohacení je aktuálně ve verzi Public Preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [Rozhraní REST API verze 2019-05-06-Preview](search-api-preview.md) poskytuje tuto funkci. V tuto chvíli neexistuje žádná podpora portálu nebo sady .NET SDK.

Přírůstkové obohacení přidává ukládání do mezipaměti a stavu kanálu obohacení, zachování vašich investic do stávajícího výstupu, při změně pouze ty dokumenty ovlivněny konkrétní změny. Nejen, že to zachová vaše peněžní investice do zpracování (zejména OCR a zpracování obrazu), ale také to přispívá k efektivnějšímu systému. Při ukládání struktur a obsahu do mezipaměti, indexer můžete určit, které dovednosti se změnily a spustit pouze ty, které byly změněny, stejně jako všechny následné závislé dovednosti. 

## <a name="indexer-cache"></a>Mezipaměť indexeru

Přírůstkové obohacení přidá do kanálu obohacení mezipaměť. Indexer ukládá výsledky z dokumentu praskání plus výstupy každé dovednosti pro každý dokument. Při aktualizaci sady dovedností se znovu spustí pouze změněné nebo následné dovednosti. Aktualizované výsledky jsou zapsány do mezipaměti a dokument je aktualizován v indexu vyhledávání nebo v úložišti znalostí.

Fyzicky se mezipaměť uložených v kontejneru objektů blob ve vašem účtu Azure Storage. Mezipaměť také používá úložiště tabulky pro interní záznam zpracování aktualizací. Všechny indexy v rámci vyhledávací služby mohou sdílet stejný účet úložiště pro mezipaměť indexeru. Každému indexeru je přiřazen jedinečný a neměnný identifikátor mezipaměti kontejneru, který používá.

## <a name="cache-configuration"></a>Konfigurace mezipaměti

Budete muset nastavit `cache` vlastnost na indexeru začít těžit z přírůstkového obohacení. Následující příklad ilustruje indexer s povoleným ukládáním do mezipaměti. Konkrétní části této konfigurace jsou popsány v následujících částech. Další informace naleznete v [tématu Nastavení přírůstkového obohacení](search-howto-incremental-index.md).

```json
{
    "name": "myIndexerName",
    "targetIndexName": "myIndex",
    "dataSourceName": "myDatasource",
    "skillsetName": "mySkillset",
    "cache" : {
        "storageConnectionString" : "Your storage account connection string",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
}
```

Nastavení této vlastnosti na existující indexer bude vyžadovat obnovení a opětovné spuštění indexeru, což bude mít za následek všechny dokumenty ve zdroji dat jsou zpracovány znovu. Tento krok je nezbytný k odstranění všech dokumentů obohacených předchozími verzemi sady dovedností. 

## <a name="cache-management"></a>Správa mezipaměti

Životní cyklus mezipaměti je spravován indexerem. Pokud `cache` je vlastnost indexeru nastavena na hodnotu null nebo je změněn připojovací řetězec, existující mezipaměť je odstraněna při dalším spuštění indexeru. Životní cyklus mezipaměti je také vázána na životní cyklus indexeru. Pokud je indexer odstraněn, bude odstraněna také přidružená mezipaměť.

Zatímco přírůstkové obohacení je navrženo tak, aby detekovalo změny bez zásahu z vaší strany a reagovalo na ně, existují parametry, které můžete použít k přepsání výchozího chování:

+ Upřednostnění nových dokumentů
+ Obejít kontroly sady dovedností
+ Obejít kontroly zdroje dat
+ Vyhodnocení sady dovedností

### <a name="prioritize-new-documents"></a>Upřednostnění nových dokumentů

Nastavte `enableReprocessing` vlastnost pro řízení zpracování nad příchozí dokumenty již zastoupeny v mezipaměti. Když `true` (výchozí), dokumenty, které jsou již v mezipaměti jsou znovu zpracovány při opětovném spuštění indexeru, za předpokladu, že aktualizace dovedností ovlivní tento dokument. 

Pokud `false`nebudou existující dokumenty znovu zpracovány a efektivně upřednostní nový příchozí obsah před existujícím obsahem. Měli byste `enableReprocessing` nastavit `false` pouze dočasně. Aby byla zajištěna konzistentnost napříč korpusem, `enableReprocessing` měla by být `true` většinu času, aby se zajistilo, že všechny dokumenty, nové i stávající, jsou platné podle aktuální definice skillset.

### <a name="bypass-skillset-evaluation"></a>Obejít hodnocení sady dovedností

Úprava skillset a přepracování této skillset obvykle jdou ruku v ruce. Některé změny skillset by však neměly vést k opětovnému zpracování (například nasazení vlastní dovednosti do nového umístění nebo s novým přístupovým klíčem). S největší pravděpodobností se jedná o periferní modifikace, které nemají žádný skutečný dopad na podstatu samotného souboru dovedností. 

Pokud víte, že změna skillset je skutečně povrchní, měli byste `disableCacheReprocessingChangeDetection` přepsat `true`skillset hodnocení nastavením parametru na :

1. Volejte aktualizace skillset a upravit definici skillset.
1. Připojit `disableCacheReprocessingChangeDetection=true` parametr na požadavek.
1. Odešlete změnu.

Nastavení tohoto parametru zajišťuje, že jsou potvrzeny pouze aktualizace definice sady dovedností a změna není vyhodnocena pro účinky na existující korpus.

Následující příklad ukazuje požadavek aktualizace skillset s parametrem:

```http
PUT https://customerdemos.search.windows.net/skillsets/callcenter-text-skillset?api-version=2019-05-06-Preview&disableCacheReprocessingChangeDetection=true
```

### <a name="bypass-data-source-validation-checks"></a>Obejít kontroly ověření zdroje dat

Většina změn definice zdroje dat zruší platnost mezipaměti. Však pro scénáře, kde víte, že změna by neměla zneplatnit mezipaměť – například`ignoreResetRequirement` změna připojovacího řetězce nebo otočení klíče v účtu úložiště - připojit parametr na aktualizaci zdroje dat. Nastavení tohoto `true` parametru umožňuje potvrzení projít, bez spuštění obnovení podmínku, která by vedla k opětovnému sestavit a naplnit všechny objekty od začátku.

```http
PUT https://customerdemos.search.windows.net/datasources/callcenter-ds?api-version=2019-05-06-Preview&ignoreResetRequirement=true
```

### <a name="force-skillset-evaluation"></a>Vyhodnocení sady dovedností

Účelem mezipaměti je zabránit zbytečnému zpracování, ale předpokládejme, že provedete změnu dovednosti, kterou indexer nerozpozná (například změna v externím kódu, například vlastní dovednost).

V takovém případě můžete použít [obnovit dovednosti](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/reset-skills) vynutit přepracování určité dovednosti, včetně všech navazující dovednosti, které mají závislost na výstupu této dovednosti. Toto rozhraní API přijímá požadavek POST se seznamem dovedností, které by měly být zrušeny a označeny pro opětovné zpracování. Po obnovení dovednosti, spusťte indexer vyvolat potrubí.

## <a name="change-detection"></a>Změna detekce

Jakmile povolíte mezipaměť, indexer vyhodnotí změny ve složení kanálu, aby zjistil, který obsah lze znovu použít a který potřebuje opětovné zpracování. Tato část obsahuje výčet změn, které úplně zneplatní mezipaměť, následované změnami, které aktivují přírůstkové zpracování. 

### <a name="changes-that-invalidate-the-cache"></a>Změny, které zneplatňují mezipaměť

Neplatná změna je změna, kde celá mezipaměť již není platná. Příkladem neplatné změny je změna, ve které je aktualizován zdroj dat. Zde je úplný seznam změn, které by zneplatnily vaši mezipaměť:

* Změna typu zdroje dat
* Změna kontejneru zdroje dat
* Přihlašovací údaje ke zdroji dat
* Zásady zjišťování změn zdroje dat
* Zásady odstranění zdrojů dat
* Mapování polí indexeru
* Parametry indexeru
    * Režim analýzy
    * Vyloučené přípony názvů souborů
    * Indexované přípony názvů souborů
    * Metadata úložiště indexu pouze pro dokumenty nadměrné velikosti
    * Záhlaví textu s oddělovači
    * Oddělovač oddělovač oddělovač e-textu
    * Kořen dokumentu
    * Akce obrázku (Změny způsobu extrahování obrazů)

### <a name="changes-that-trigger-incremental-processing"></a>Změny, které aktivují přírůstkové zpracování

Přírůstkové zpracování vyhodnotí definici sady dovedností a určí, které dovednosti se mají znovu spustit, a selektivně aktualizuje ovlivněné části stromu dokumentů. Zde je kompletní seznam změn, které vedou k postupnému obohacení:

* Dovednost v skillset má jiný typ. Typ odat dovednosti je aktualizován
* Aktualizovány parametry specifické pro dovednosti, například adresa URL, výchozí hodnoty nebo jiné parametry
* Skill výstupy změny, dovednost vrátí další nebo různé výstupy
* Aktualizace dovedností výsledné je jiný původ, dovednosti řetězení se změnilo tj vstupy dovedností
* Jakékoli zneplatnění dovedností proti proudu, pokud je dovednost, která poskytuje vstup do této dovednosti, aktualizována
* Aktualizace umístění projekce úložiště znalostí, výsledkem je přepromítání dokumentů
* Změny v projekcích úložiště znalostí, výsledky při přepromítání dokumentů
* Mapování výstupního pole změněno na indexeru má za následek přehodnocení dokumentů do indexu

## <a name="api-reference"></a>referenční dokumentace k rozhraní API

VERZE ROZHRANÍ `2019-05-06-Preview` REST API poskytuje přírůstkové obohacení prostřednictvím dalších vlastností na indexery, dovednosti a zdroje dat. Kromě referenční dokumentace naleznete v [tématu Konfigurace ukládání do mezipaměti pro přírůstkové obohacení](search-howto-incremental-index.md) podrobnosti o tom, jak volat rozhraní API.

+ [Vytvořit indexer (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-indexer) 

+ [Aktualizovat Indexer (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-indexer) 

+ [Aktualizovat skillset (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-skillset) (Nový parametr URI na vyžádání)

+ [Reset Skills (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/reset-skills)

+ Indexery databází (Azure SQL, Cosmos DB). Některé indexery načítají data prostřednictvím dotazů. U dotazů, které načítají data, [podporuje aktualizace zdroje dat](https://docs.microsoft.com/rest/api/searchservice/update-data-source) nový parametr v požadavku **ignoreResetRequirement**, který by měl být nastaven na `true` chvíli, kdy by akce aktualizace neměla zneplatnit mezipaměť. 

  Používejte **ignoreResetRequirement** střídmě, protože by to mohlo vést k neúmyslné nekonzistenci dat, které nebudou snadno rozpoznány.

## <a name="next-steps"></a>Další kroky

Přírůstkové obohacení je výkonná funkce, která rozšiřuje sledování změn na dovednosti a obohacení AI. APřírůstkové obohacení umožňuje opakované použití existujícího zpracovaného obsahu při iterování nad návrhem sady dovedností.

Jako další krok povolte ukládání do mezipaměti na existující indexer nebo přidat mezipaměť při definování nového indexeru.

> [!div class="nextstepaction"]
> [Konfigurace ukládání do mezipaměti pro přírůstkové obohacení](search-howto-incremental-index.md)
