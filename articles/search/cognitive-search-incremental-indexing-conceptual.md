---
title: Přírůstkové obohacení (Preview)
titleSuffix: Azure Cognitive Search
description: Ukládání mezilehlého obsahu do mezipaměti a přírůstkových změn z kanálu obohacení AI v Azure Storage, aby se zachovaly investice do stávajících zpracovaných dokumentů. Tato funkce je aktuálně ve verzi Public Preview.
manager: nitinme
author: Vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 09003c26ead9108d07ae339fcf64235c246474a4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77024139"
---
# <a name="introduction-to-incremental-enrichment-and-caching-in-azure-cognitive-search"></a>Úvod k přírůstkové obohacení a ukládání do mezipaměti v Azure Kognitivní hledání

> [!IMPORTANT] 
> Přírůstkové obohacení je aktuálně ve verzi Public Preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Tato funkce poskytuje [REST API verze 2019-05-06-Preview](search-api-preview.md) . V tuto chvíli není k dispozici žádný portál ani podpora sady .NET SDK.

Přírůstkové obohacení přidává do kanálu pro rozšíření mezipaměť a stav a zachovává vaši investici do stávajícího výstupu a zároveň mění pouze ty dokumenty, které mají vliv na konkrétní úpravu. To nedělá jenom to, aby se zajistila vaše peněžní investice do zpracování (zejména optické rozpoznávání znaků a zpracování obrázků), ale také to umožňuje efektivnější systém. Když jsou struktury a obsah uložené v mezipaměti, může indexer určit, které dovednosti se změnily a spustí jenom ty, které se změnily, a také všechny závislé dovednosti. 

## <a name="indexer-cache"></a>Mezipaměť indexeru

Přírůstkové obohacení přidá do kanálu pro rozšíření mezipaměť. Indexer ukládá výsledky z odhalujícího dokumentu do mezipaměti a také výstupy jednotlivých dovedností pro každý dokument. Jakmile se dovednosti aktualizuje, budou se znovu spouštět jenom změněné nebo podřízené. Aktualizované výsledky se zapisují do mezipaměti a dokument se aktualizuje v indexu vyhledávání nebo ve znalostní bázi Knowledge Store.

Mezipaměť je fyzicky uložená v kontejneru objektů BLOB v účtu Azure Storage. Mezipaměť také používá úložiště tabulek pro interní záznam aktualizací zpracování. Všechny indexy v rámci vyhledávací služby můžou sdílet stejný účet úložiště pro mezipaměť indexeru. Každému indexeru je přiřazen jedinečný a neproměnlivý identifikátor mezipaměti pro kontejner, který používá.

## <a name="cache-configuration"></a>Konfigurace mezipaměti

Je potřeba nastavit `cache` vlastnost indexeru tak, aby využívání z přírůstkového obohacení. Následující příklad znázorňuje indexer s povoleným ukládáním do mezipaměti. Konkrétní části této konfigurace jsou popsány v následujících oddílech. Další informace najdete v tématu [Nastavení přírůstkového obohacení](search-howto-incremental-index.md).

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

Nastavení této vlastnosti u stávajícího indexeru bude vyžadovat, abyste indexer obnovili a znovu znovu provedli, což způsobí, že se všechny dokumenty ve zdroji dat zpracovávají znovu. Tento krok je nezbytný pro odstranění všech dokumentů obohacených předchozími verzemi dovednosti. 

## <a name="cache-management"></a>Správa mezipaměti

Životní cyklus mezipaměti spravuje indexer. Pokud je `cache` vlastnost indexeru nastavená na hodnotu null nebo dojde ke změně připojovacího řetězce, při příštím spuštění indexeru se odstraní existující mezipaměť. Životní cyklus mezipaměti je také svázán s životním cyklem indexeru. Pokud se indexer odstraní, odstraní se i přidružená mezipaměť.

I když je přírůstkové obohacení určené ke zjištění a reakci na změny bez zásahu vaší strany, existují parametry, pomocí kterých můžete přepsat výchozí chování:

+ Určení priorit nových dokumentů
+ Obejít dovednosti kontroly
+ Obejít kontrolu zdroje dat
+ Vynutit vyhodnocování dovednosti

### <a name="prioritize-new-documents"></a>Určení priorit nových dokumentů

Nastavte `enableReprocessing` vlastnost pro řízení zpracování příchozích dokumentů, které už jsou v mezipaměti reprezentované. Když `true` (výchozí), dokumenty, které jsou už v mezipaměti, se znovu zpracovávají při opětovném spuštění indexeru, za předpokladu, že se aktualizace dovedností týká tohoto dokumentu. 

Když `false`nedojde k novému zpracování stávajících dokumentů, efektivní určení priorit nového a příchozího obsahu prostřednictvím stávajícího obsahu. Nastavení byste měli nastavit `enableReprocessing` jenom `false` na dočasný základ. Aby se zajistila konzistence napříč `enableReprocessing` corpus, `true` měla by být většina času, aby se zajistilo, že všechny dokumenty, obě nové i existující jsou platné pro aktuální definici dovednosti.

### <a name="bypass-skillset-evaluation"></a>Obejít vyhodnocování dovednosti

Změna dovednosti a redovednostií tohoto u se obvykle dostanou rukou. Nicméně některé změny dovednosti by neměly mít za následek rezpracování (například nasazení vlastní dovednosti do nového umístění nebo pomocí nového přístupového klíče). Nejpravděpodobnější je to, že jde o periferní úpravy, které nemají žádný skutečný dopad na obsah samotné dovednostiy. 

Pokud víte, že změna dovednosti je skutečně povrchová, měli byste přepsat vyhodnocení dovednosti nastavením `disableCacheReprocessingChangeDetection` parametru na: `true`

1. Zavolejte dovednosti Update a upravte definici dovednosti.
1. Přidejte `disableCacheReprocessingChangeDetection=true` parametr na žádost.
1. Odešlete změnu.

Nastavením tohoto parametru zajistíte, že se potvrdí jenom aktualizace definice dovednosti a změna se Nevyhodnocení pro účinky na existující corpus.

Následující příklad ukazuje požadavek Update dovednosti s parametrem:

```http
PUT https://customerdemos.search.windows.net/skillsets/callcenter-text-skillset?api-version=2019-05-06-Preview&disableCacheReprocessingChangeDetection=true
```

### <a name="bypass-data-source-validation-checks"></a>Obejít kontrolu ověřování zdroje dat

Většina změn definice zdroje dat zruší platnost mezipaměti. Nicméně u scénářů, kde víte, že změna by neměla mít za následek zrušení platnosti mezipaměti – například změna připojovacího řetězce nebo otočení klíče v účtu úložiště – připojí`ignoreResetRequirement` parametr na aktualizaci zdroje dat. Nastavením tohoto parametru `true` umožníte, aby potvrzení procházelo, aniž by se aktivovala podmínka resetování, která by způsobila, že se všechny objekty znovu sestaví a vyplní od začátku.

```http
PUT https://customerdemos.search.windows.net/datasources/callcenter-ds?api-version=2019-05-06-Preview&ignoreResetRequirement=true
```

### <a name="force-skillset-evaluation"></a>Vynutit vyhodnocování dovednosti

Účelem mezipaměti je vyhnout se zbytečnému zpracování, ale Předpokládejme, že provedete změnu dovednosti, kterou indexer nedetekuje (například změna nějakého externího kódu, jako je třeba vlastní dovednost).

V takovém případě můžete použít [dovednost obnovení](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/reset-skills) k vynucení přepracování konkrétní dovednosti, včetně všech dovedností, které jsou závislé na výstupu této dovednosti. Toto rozhraní API přijme požadavek POST se seznamem dovedností, které by měly být neověřené a označené k novému zpracování. Po resetování dovedností spusťte indexer k vyvolání kanálu.

## <a name="change-detection"></a>Zjišťování změn

Jakmile povolíte mezipaměť, indexer vyhodnotí změny ve vašem kompozici kanálu a určí, který obsah se může znovu použít a které požadavky se mají znovu zpracovat. V této části jsou vyhodnoceny změny, které mezipaměť neověřují, následované změnami, které aktivují přírůstkové zpracování. 

### <a name="changes-that-invalidate-the-cache"></a>Změny, které neověřuje mezipaměť

Změna neplatného je taková, kde už celá mezipaměť není platná. Například změna neplatného je jedna, kde se Váš zdroj dat aktualizoval. Tady je úplný seznam změn, které by způsobily neplatnost vaší mezipaměti:

* Změnit na typ zdroje dat
* Změnit na kontejner zdroje dat
* Přihlašovací údaje ke zdroji dat
* Zásady detekce změn zdroje dat
* Zásady detekce odstranění zdrojů dat
* Mapování polí indexeru
* Parametry indexeru
    * Režim analýzy
    * Vyloučené přípony názvů souborů
    * Přípony názvů indexovaných souborů
    * Metadata úložiště indexu jenom pro dokumenty s příliš velikostí
    * Záhlaví s oddělovači textu
    * Oddělovač textu s oddělovači
    * Kořen dokumentu
    * Akce obrázku (změny při extrakci imagí)

### <a name="changes-that-trigger-incremental-processing"></a>Změny, které aktivují přírůstkové zpracování

Přírůstkové zpracování vyhodnocuje vaši definici dovednosti a určuje, které dovednosti se mají znovu spustit, selektivně aktualizujte ovlivněné části stromu dokumentu. Tady je úplný seznam změn, které mají za následek přírůstkové obohacení:

* Dovednost v dovednosti má jiný typ. Typ OData dovednosti je aktualizovaný.
* Parametry specifické pro dovednost se aktualizovaly, například adresa URL, výchozí hodnoty nebo jiné parametry.
* Změny ve výstupech dovedností se změní, dovednosti vrátí další nebo různé výstupy.
* Aktualizace dovedností mají za následek odlišné původ, došlo ke změně kvalifikačního řetězu, tj. vstupy dovedností
* Případná neplatnost všech nadřazených dovedností, pokud se aktualizuje dovednost, která poskytuje vstup této dovednosti
* Aktualizace umístění projekce ve znalostní bázi Store má za následek reprojekci dokumentů.
* Změny v projekcích ve znalostní bázi Store mají za následek reprojekci dokumentů.
* Mapování polí výstupu změněné u indexeru má za následek reprodukci dokumentů do indexu.

## <a name="api-reference"></a>referenční dokumentace k rozhraní API

REST API verze `2019-05-06-Preview` poskytuje přírůstkové rozšíření prostřednictvím dalších vlastností indexerů, dovednosti a zdrojů dat. Kromě referenční dokumentace najdete další informace o volání rozhraní API v tématu [Konfigurace ukládání do mezipaměti pro přírůstkové obohacení](search-howto-incremental-index.md) .

+ [Create indexer (API-Version = 2019-05 -06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-indexer) 

+ [Aktualizace indexeru (API-Version = 2019-05 -06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-indexer) 

+ [Update dovednosti (API-Version = 2019-05 -06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-skillset) (nový parametr URI na žádosti)

+ [Reset Skills (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/reset-skills)

+ Indexery databází (Azure SQL, Cosmos DB). Některé indexery načítají data prostřednictvím dotazů. U dotazů, které načítají data, [aktualizuje zdroj dat](https://docs.microsoft.com/rest/api/searchservice/update-data-source) v žádosti **ignoreResetRequirement**nový parametr, který by měl být nastaven na `true` hodnotu, pokud by vaše akce aktualizace neměla mít za následek zrušení platnosti mezipaměti. 

  **IgnoreResetRequirement** můžete použít zřídka, protože by mohlo vést k nezamýšlené nekonzistenci vašich dat, která se nezjistí snadno.

## <a name="next-steps"></a>Další kroky

Přírůstkové obohacení je výkonná funkce, která rozšiřuje sledování změn na rozšíření dovednosti a AI. Rozšíření AIncremental umožňuje opakované použití existujícího zpracovávaného obsahu při iteraci během návrhu dovednosti.

V dalším kroku Povolte ukládání do mezipaměti pro existující indexer nebo přidání mezipaměti při definování nového indexeru.

> [!div class="nextstepaction"]
> [Konfigurace ukládání do mezipaměti pro přírůstkové obohacení](search-howto-incremental-index.md)
