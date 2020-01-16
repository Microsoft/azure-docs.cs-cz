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
ms.openlocfilehash: 285b3608bc57d88ca2e81ed14355923436ed9d8d
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028510"
---
# <a name="introduction-to-incremental-enrichment-and-caching-in-azure-cognitive-search"></a>Úvod k přírůstkové obohacení a ukládání do mezipaměti v Azure Kognitivní hledání

> [!IMPORTANT] 
> Přírůstkové obohacení je aktuálně ve verzi Public Preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Tato funkce poskytuje [REST API verze 2019-05-06-Preview](search-api-preview.md) . V tuto chvíli není k dispozici žádný portál ani podpora sady .NET SDK.

Přírůstkové obohacení přidává do kanálu pro rozšíření mezipaměť a stav a zachovává vaši investici do stávajícího výstupu a zároveň mění pouze ty dokumenty, které mají vliv na konkrétní úpravu. To nedělá jenom to, aby se zajistila vaše peněžní investice do zpracování (zejména optické rozpoznávání znaků a zpracování obrázků), ale také to umožňuje efektivnější systém. Když jsou struktury a obsah uložené v mezipaměti, může indexer určit, které dovednosti se změnily a spustí jenom ty, které se změnily, a také všechny závislé dovednosti. 

## <a name="indexer-cache"></a>Mezipaměť indexeru

Přírůstkové obohacení přidá do kanálu pro rozšíření mezipaměť. Indexer ukládá výsledky z odhalujícího dokumentu do mezipaměti a také výstupy jednotlivých dovedností pro každý dokument. Jakmile se dovednosti aktualizuje, budou se znovu spouštět jenom změněné nebo podřízené. Aktualizované výsledky se zapisují do mezipaměti a dokument se aktualizuje v indexu vyhledávání nebo ve znalostní bázi Knowledge Store.

Mezipaměť je fyzicky uložená v kontejneru objektů BLOB v účtu Azure Storage. Všechny indexy v rámci vyhledávací služby můžou sdílet stejný účet úložiště pro mezipaměť indexeru. Každému indexeru je přiřazen jedinečný a neproměnlivý identifikátor mezipaměti pro kontejner, který používá.

## <a name="cache-configuration"></a>Konfigurace mezipaměti

V indexeru budete muset nastavit vlastnost `cache` tak, aby využívání z přírůstkového obohacení. Následující příklad znázorňuje indexer s povoleným ukládáním do mezipaměti. Konkrétní části této konfigurace jsou popsány v následujících oddílech. Další informace najdete v tématu [Nastavení přírůstkového obohacení](search-howto-incremental-index.md).

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

Životní cyklus mezipaměti spravuje indexer. Pokud je vlastnost `cache` v indexeru nastavená na hodnotu null nebo dojde ke změně připojovacího řetězce, při příštím spuštění indexeru se odstraní existující mezipaměť. Životní cyklus mezipaměti je také svázán s životním cyklem indexeru. Pokud se indexer odstraní, odstraní se i přidružená mezipaměť.

I když je přírůstkové obohacení určené ke zjištění a reakci na změny bez zásahu vaší strany, existují parametry, pomocí kterých můžete přepsat výchozí chování:

+ Určení priorit nových dokumentů
+ Obejít dovednosti kontroly
+ Obejít kontrolu zdroje dat
+ Vynutit vyhodnocování dovednosti

### <a name="prioritize-new-documents"></a>Určení priorit nových dokumentů

Nastavte vlastnost `enableReprocessing` pro řízení zpracování příchozích dokumentů, které jsou již v mezipaměti zastoupeny. Při `true` (výchozí nastavení) se při opětovném spuštění indexeru znovu zpracovávají dokumenty v mezipaměti, za předpokladu, že se aktualizace dovedností projeví i v tomto dokumentu. 

Když `false`, existující dokumenty se nezpracovávají a efektivně se tak budou upřednostňovat nové a příchozí obsahy přes existující obsah. Měli byste nastavit jenom `enableReprocessing`, aby `false` na dočasné bázi. Aby se zajistila konzistence napříč corpus, `enableReprocessing` by se měla `true` většinu času a zajistit, aby všechny dokumenty, obě nové i existující, byly platné pro aktuální definici dovednosti.

### <a name="bypass-skillset-evaluation"></a>Obejít vyhodnocování dovednosti

Změna dovednosti a redovednostií tohoto u se obvykle dostanou rukou. Nicméně některé změny dovednosti by neměly mít za následek rezpracování (například nasazení vlastní dovednosti do nového umístění nebo pomocí nového přístupového klíče). Nejpravděpodobnější je to, že jde o periferní úpravy, které nemají žádný skutečný dopad na obsah samotné dovednostiy. 

Pokud víte, že změna dovednosti je skutečně povrchová, měli byste přepsat vyhodnocení dovednosti nastavením parametru `disableCacheReprocessingChangeDetection` na `true`:

1. Zavolejte dovednosti Update a upravte definici dovednosti.
1. Do žádosti přidejte parametr `disableCacheReprocessingChangeDetection=true`.
1. Odešlete změnu.

Nastavením tohoto parametru zajistíte, že se potvrdí jenom aktualizace definice dovednosti a změna se Nevyhodnocení pro účinky na existující corpus.

Následující příklad ukazuje požadavek Update dovednosti s parametrem:

```http
PUT https://customerdemos.search.windows.net/skillsets/callcenter-text-skillset?api-version=2019-05-06-Preview&disableCacheReprocessingChangeDetection=true
```

### <a name="bypass-data-source-validation-checks"></a>Obejít kontrolu ověřování zdroje dat

Většina změn definice zdroje dat zruší platnost mezipaměti. Nicméně u scénářů, kde víte, že změna by neměla mít za následek zrušení platnosti mezipaměti – například změna připojovacího řetězce nebo otočení klíče v účtu úložiště – připojíte parametr`ignoreResetRequirement` pro aktualizaci zdroje dat. Nastavením tohoto parametru na `true` umožníte, aby potvrzení procházelo, aniž by se aktivovala podmínka resetování, která by způsobila, že se všechny objekty znovu sestaví a vyplní od začátku.

```http
PUT https://customerdemos.search.windows.net/datasources/callcenter-ds?api-version=2019-05-06-Preview&ignoreResetRequirement=true
```

### <a name="force-skillset-evaluation"></a>Vynutit vyhodnocování dovednosti

Účelem mezipaměti je vyhnout se zbytečnému zpracování, ale Předpokládejme, že provedete změnu dovednosti, kterou indexer nedetekuje (například změna nějakého externího kódu, jako je třeba vlastní dovednost).

V takovém případě můžete použít [dovednost obnovení](preview-api-resetskills.md) k vynucení přepracování konkrétní dovednosti, včetně všech dovedností, které jsou závislé na výstupu této dovednosti. Toto rozhraní API přijme požadavek POST se seznamem dovedností, které by měly být neověřené a označené k novému zpracování. Po resetování dovedností spusťte indexer k vyvolání kanálu.

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
* Aktualizace dovedností mají za následek odlišné původ, došlo ke změně kvalifikačního řetězu, tj. Vstupy dovedností
* Případná neplatnost všech nadřazených dovedností, pokud se aktualizuje dovednost, která poskytuje vstup této dovednosti
* Aktualizace umístění projekce ve znalostní bázi Store má za následek reprojekci dokumentů.
* Změny v projekcích ve znalostní bázi Store mají za následek reprojekci dokumentů.
* Mapování polí výstupu změněné u indexeru má za následek reprodukci dokumentů do indexu.

## <a name="api-reference-content-for-incremental-enrichment"></a>Referenční obsah rozhraní API pro přírůstkové obohacení

REST `api-version=2019-05-06-Preview` poskytuje rozhraní API pro přírůstkové obohacení s dodatky k indexerům, dovednosti a zdroji dat. [Oficiální Referenční dokumentace](https://docs.microsoft.com/rest/api/searchservice/) je pro všeobecně dostupná rozhraní API a nezabývá se funkcemi verze Preview. V následující části najdete referenční obsah pro ovlivněná rozhraní API.

Informace o využití a příklady najdete v v [konfiguraci ukládání do mezipaměti pro přírůstkové obohacení](search-howto-incremental-index.md).

### <a name="indexers"></a>Indexovací moduly

[Vytvoření indexeru](https://docs.microsoft.com/rest/api/searchservice/create-indexer) a [aktualizace indexeru](https://docs.microsoft.com/rest/api/searchservice/update-indexer) teď budou zveřejňovat nové vlastnosti týkající se mezipaměti:

+ `StorageAccountConnectionString`: připojovací řetězec k účtu úložiště, který se použije k ukládání mezilehlých výsledků do mezipaměti.

+ `EnableReprocessing`: ve výchozím nastavení se nastaví na `true`, když se nastaví na `false`, dokumenty se budou do mezipaměti zapisovat, ale žádné existující dokumenty se nebudou znovu zpracovávat na základě dat mezipaměti.

+ `ID` (jen pro čtení): `ID` je identifikátor kontejneru v rámci `annotationCache` účtu úložiště, který se použije jako mezipaměť pro tento indexer. Tato mezipaměť bude pro tohoto indexer jedinečná a pokud se indexer odstraní a znovu vytvoří se stejným názvem, `ID` se znovu vygeneruje. `ID` nelze nastavit, je vždy vygenerována službou.

### <a name="skillsets"></a>Sady dovedností

+ [Aktualizace dovednosti](https://docs.microsoft.com/rest/api/searchservice/update-skillset) podporuje nový parametr v požadavku: `disableCacheReprocessingChangeDetection`, který by měl být nastaven na hodnotu `true`, pokud chcete, aby na základě aktuální akce nebyly žádné aktualizace stávajících dokumentů.

+ [Resetování dovedností](preview-api-resetskills.md) je nová operace, která se používá k devalidaci dovednosti.

### <a name="datasources"></a>Zdroje dat

+ Některé indexery načítají data prostřednictvím dotazů. U dotazů, které načítají data, [aktualizuje zdroj dat](https://docs.microsoft.com/rest/api/searchservice/update-data-source) v `ignoreResetRequirement`žádosti nový parametr, který by měl být nastaven na hodnotu `true`, pokud by vaše akce aktualizace neměla mít za následek zrušení platnosti mezipaměti.

`ignoreResetRequirement` můžete použít zřídka, protože by mohlo vést k nezamýšlené nekonzistenci vašich dat, která se nezjistí snadno.

## <a name="next-steps"></a>Další kroky

Přírůstkové obohacení je výkonná funkce, která rozšiřuje sledování změn na rozšíření dovednosti a AI. Jak je to dovednosti, přírůstkové rozšíření zajišťuje co nejmenší možné práci a přitom stále chrání vaše dokumenty v konečném důsledku konzistence.

Začněte přidáním mezipaměti do stávajícího indexeru nebo při definování nového indexeru přidejte mezipaměť.

> [!div class="nextstepaction"]
> [Konfigurace ukládání do mezipaměti pro přírůstkové obohacení](search-howto-incremental-index.md)
