---
title: Přírůstkové indexování (Náhled)
titleSuffix: Azure Cognitive Search
description: Nakonfigurujte svůj kanál pro obohacení AI tak, aby bylo možné zajistit, aby se vaše data zpracovávala na jakékoli aktualizace dovedností, dovednosti, indexerů nebo zdrojů dat. Tato funkce je aktuálně ve verzi Public Preview.
manager: nitinme
author: Vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 76ab8784f04f3c67e4ea8062505931783048dea1
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113597"
---
# <a name="what-is-incremental-indexing-in-azure-cognitive-search"></a>Co je přírůstkové indexování v Azure Kognitivní hledání?

> [!IMPORTANT] 
> Přírůstkové indexování je aktuálně ve verzi Public Preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Tato funkce poskytuje [REST API verze 2019-05-06-Preview](search-api-preview.md) . V tuto chvíli není k dispozici žádný portál ani podpora sady .NET SDK.

Přírůstkové indexování je nová funkce služby Azure Kognitivní hledání, která přidává ukládání do mezipaměti a stav do obohaceného obsahu v dovednostiu, což vám umožní řídit zpracování a opětovné zpracování jednotlivých kroků v kanálu rozšíření. To neplatí jenom za tím, že zachovává vaše peněžní investice do zpracování, ale také poskytuje efektivnější systém. Když jsou struktury a obsah uložené v mezipaměti, může indexer určit, které dovednosti se změnily a spustí jenom ty, které se změnily, a také všechny závislé dovednosti. 

V případě přírůstkového indexování představuje aktuální verze kanálu rozšíření nejmenší množství práce, která zaručuje konzistenci všech dokumentů v indexu. U scénářů, kde chcete úplné řízení, můžete použít jemně odstupňované ovládací prvky pro přepsání očekávaného chování. Další informace o konfiguraci najdete v tématu [Nastavení přírůstkového indexování](search-howto-incremental-index.md).

## <a name="indexer-cache"></a>Mezipaměť indexeru

Přírůstkové indexování přidá do kanálu pro rozšíření mezipaměť indexerů. Indexer ukládá výsledky z trhlin dokumentů a výstupy jednotlivých dovedností pro každý dokument. Jakmile se dovednosti aktualizuje, budou se znovu spouštět jenom změněné nebo podřízené. Aktualizované výsledky se zapisují do mezipaměti a dokument se aktualizuje v indexu a v úložišti znalostí.

Mezipaměť je fyzicky účet úložiště. Všechny indexy v rámci vyhledávací služby můžou sdílet stejný účet úložiště pro mezipaměť indexeru. Každému indexeru je přiřazen jedinečný a neproměnlivý identifikátor mezipaměti.

### <a name="cache-configuration"></a>Konfigurace mezipaměti

V indexeru budete muset nastavit vlastnost `cache` tak, aby využívání z přírůstkového indexování. Následující příklad znázorňuje indexer s povoleným ukládáním do mezipaměti. Konkrétní části této konfigurace jsou popsány v následujících oddílech.

```json
{
    "name": "myIndexerName",
    "targetIndexName": "myIndex",
    "dataSourceName": "myDatasource",
    "skillsetName": "mySkillset",
    "cache" : {
        "storageConnectionString" : "Your storage account connection string",
        "enableReprocessing": true,
        "id" : "Auto generated Id you do not need to set"
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters":{}
}
```

Při prvním nastavení této vlastnosti v existujícím indexeru budete vyžadovat, abyste ji také obnovili, což způsobí, že se všechny dokumenty ve zdroji dat zpracovávají znovu. Cílem přírůstkového indexování je učinit dokumenty v indexu konzistentní s vaším zdrojem dat a aktuální verzí vaší dovednosti. Resetování indexu je prvním krokem k této konzistenci, protože eliminuje všechny dokumenty obohacené předchozími verzemi dovednosti. Indexer se musí resetovat, aby se začínaly konzistentním směrným plánem.

### <a name="cache-lifecycle"></a>Životní cyklus mezipaměti

Životní cyklus mezipaměti spravuje indexer. Pokud je vlastnost `cache` v indexeru nastavená na hodnotu null nebo došlo ke změně připojovacího řetězce, odstraní se stávající mezipaměť. Životní cyklus mezipaměti je také svázán s životním cyklem indexeru. Pokud se indexer odstraní, odstraní se i přidružená mezipaměť.

### <a name="indexer-cache-mode"></a>Režim mezipaměti indexeru

Mezipaměť indexeru může fungovat v režimech, kdy se data zapisují do mezipaměti, nebo se data zapisují do mezipaměti a používají se k opětovnému rozšíření dokumentů.  Přírůstkové obohacení můžete dočasně pozastavit nastavením vlastnosti `enableReprocessing` v mezipaměti na `false`a později pokračovat v přírůstkovém obohacení a tím, že ji nastavíte na `true`. Tento ovládací prvek je zvláště užitečný, pokud chcete určit prioritu indexování nových dokumentů s cílem zajistit konzistenci napříč corpus dokumentů.

## <a name="change-detection-override"></a>Přepsání detekce změn

Přírůstkové indexování vám poskytne podrobnější kontrolu nad všemi aspekty kanálu rozšíření. Tento ovládací prvek vám umožní zabývat se situacemi, kdy změna může mít nezamýšlené důsledky. Například úprava dovednosti a aktualizace adresy URL pro vlastní dovednosti způsobí, že indexer neověřuje výsledky v mezipaměti pro danou dovednost. Pokud přesunete koncový bod jenom na jiný virtuální počítač nebo znovu nasadíte vaši dovednost pomocí nového přístupového klíče, nechcete, aby se všechny existující dokumenty znovu nezpracovaly.

Aby bylo zajištěno, že indexer provede pouze obohacení, které je výslovně nutné, aktualizace dovednosti mohou volitelně nastavit parametr `disableCacheReprocessingChangeDetection` QueryString na `true`. Při nastavení tento parametr zajistí, že budou potvrzeny pouze aktualizace dovednosti, a změna není vyhodnocována pro účinky na existující corpus.

Následující příklad znázorňuje použití řetězce dotazu. Je součástí žádosti s páry klíč-hodnota s oddělovači &. 

```http
PUT https://customerdemos.search.windows.net/skillsets/callcenter-text-skillset?api-version=2019-05-06-Preview&disableCacheReprocessingChangeDetection=true
```

## <a name="cache-invalidation"></a>Neplatnost mezipaměti

V tomto scénáři je to, kde můžete nasadit novou verzi vlastní dovednosti, nic v kanálu obohacení se nezmění, ale budete potřebovat neověřené konkrétní dovednosti a všechny ovlivněné dokumenty by odrážely výhody aktualizovaného modelu. V takových instancích můžete zavolat operaci zrušení platnosti dovedností na dovednosti. Rozhraní API pro resetování dovedností přijme požadavek POST se seznamem výstupů dovedností v mezipaměti, který by měl být neověřený. Další informace o rozhraní API pro resetování dovedností najdete v tématu [resetování indexeru (Search REST API)](https://docs.microsoft.com/rest/api/searchservice/reset-indexer).

## <a name="bi-directional-change-detection"></a>Zjištění obousměrné změny

Indexery nepřesouvá pouze vpřed a zpracovávají nové dokumenty, ale nyní mohou přesunout zpět a vyřídit dříve zpracované dokumenty na konzistenci. Díky této nové funkci je důležité pochopit, jakým způsobem mají změny komponent kanálu pro rozšíření výsledek práci s indexerem. Indexer zařadí do fronty práci, která se má provést, když identifikuje změnu, která se buď neověřuje, nebo nekonzistentní vzhledem k obsahu v mezipaměti.

### <a name="invalidating-changes"></a>Zrušení platnosti změn

Změny v invalidu jsou vzácné, ale mají významný vliv na stav kanálu rozšíření. Změna neplatného je taková, kde už celá mezipaměť není platná. Například změna neplatného je jedna, kde se Váš zdroj dat aktualizoval. Pokud víte, že změna by neměla mít za následek zrušení platnosti mezipaměti, jako je například otočení klíče v účtu úložiště, parametr `ignoreResetRequirement` QueryString by měl být nastaven na hodnotu `true` na operaci aktualizace konkrétního prostředku, aby bylo zajištěno, že operace bude neodmítnuto.

Tady je úplný seznam změn, které by způsobily neplatnost vaší mezipaměti:

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

### <a name="inconsistent-changes"></a>Nekonzistentní změny

Příkladem nekonzistentní změny je aktualizace vašeho dovednostiu, která mění dovednost. Úprava může udělat část mezipaměti jako nekonzistentní. Indexer bude identifikovat práci, aby se akce znovu shodovala.  

Úplný seznam změn, které mají za následek nekonzistenci mezipaměti:

* Dovednost v dovednosti má jiný typ. Typ OData dovednosti je aktualizovaný.
* Parametry specifické pro dovednost se aktualizovaly, například adresa URL, výchozí hodnoty nebo jiné parametry.
* Změny ve výstupech dovedností se změní, dovednosti vrátí další nebo různé výstupy.
* Aktualizace dovedností mají za následek odlišné původ, došlo ke změně kvalifikačního řetězu, tj. Vstupy dovedností
* Případná neplatnost všech nadřazených dovedností, pokud se aktualizuje dovednost, která poskytuje vstup této dovednosti
* Aktualizace umístění projekce ve znalostní bázi Store má za následek reprojekci dokumentů.
* Změny v projekcích ve znalostní bázi Store mají za následek reprojekci dokumentů.
* Mapování polí výstupu změněné u indexeru má za následek reprodukci dokumentů do indexu.

## <a name="rest-api-reference-for-incremental-indexing"></a>Reference REST API pro přírůstkové indexování

REST `api-version=2019-05-06-Preview` poskytuje rozhraní API pro přírůstkové indexování s přídavky indexerů, dovednosti a zdrojů dat. Referenční dokumentace aktuálně neobsahuje tyto doplňky. V následující části jsou popsány změny rozhraní API.

### <a name="indexers"></a>Indexery

[Vytvoření indexeru](https://docs.microsoft.com/rest/api/searchservice/create-indexer) a [aktualizace indexeru](https://docs.microsoft.com/rest/api/searchservice/update-indexer) teď budou zveřejňovat nové vlastnosti týkající se mezipaměti:

* `StorageAccountConnectionString`: připojovací řetězec k účtu úložiště, který se použije k ukládání mezilehlých výsledků do mezipaměti.

* `CacheId`: `cacheId` je identifikátor kontejneru v rámci `annotationCache` účtu úložiště, který se použije jako mezipaměť pro tento indexer. Tato mezipaměť bude pro tohoto indexer jedinečná a pokud se indexer odstraní a znovu vytvoří se stejným názvem, `cacheId` se znovu vygeneruje. `cacheId` nelze nastavit, je vždy vygenerována službou.

* `EnableReprocessing`: ve výchozím nastavení se nastaví na `true`, když se nastaví na `false`, dokumenty se budou do mezipaměti zapisovat, ale žádné existující dokumenty se nebudou znovu zpracovávat na základě dat mezipaměti.

Některé indexery (prostřednictvím [zdrojů dat](https://docs.microsoft.com/rest/api/searchservice/create-data-source)) načítají data prostřednictvím dotazů. Pro dotazy, které načítají data, indexer budou podporovat také nový parametr řetězce dotazu: `ignoreResetRequirement` by měl být nastaven na hodnotu `true`, pokud by vaše akce aktualizace neměla zrušit platnost mezipaměti.

### <a name="skillsets"></a>Sady dovedností

Dovednosti nebude podporovat žádné nové operace, ale bude podporovat nový parametr QueryString: `disableCacheReprocessingChangeDetection` by měl být nastaven na `true`, pokud chcete, aby na základě aktuální akce nebyly žádné aktualizace stávajících dokumentů.

### <a name="datasources"></a>Zdroje dat

Zdroje dat nebudou podporovat žádné nové operace, ale budou podporovat nový parametr QueryString: `ignoreResetRequirement` by měl být nastaven na hodnotu `true`, pokud by vaše akce aktualizace neměla zrušit platnost mezipaměti.

## <a name="best-practices"></a>Osvědčené postupy

Doporučený postup pro použití přírůstkového indexování je konfigurace přírůstkového indexování tím, že nastaví vlastnost cache na nového indexeru nebo obnoví existující indexer a nastaví vlastnost cache.

`ignoreResetRequirement` můžete použít zřídka, protože by mohlo vést k nezamýšlené nekonzistenci vašich dat, která se nezjistí snadno.

## <a name="takeaways"></a>Shrnutí

Přírůstkové indexování je výkonná funkce, která rozšiřuje sledování změn ze zdroje dat na všechny aspekty kanálu rozšíření, včetně zdroje dat, aktuální verze dovednosti a indexeru. Vzhledem k tomu, že se vaše dovednosti, dovednostié nebo obohacení zvyšují, zajišťuje kanál pro obohacení nejnižší možnou práci a přitom stále chrání vaše dokumenty s konečnou konzistencí.

## <a name="next-steps"></a>Další kroky

Začněte s přírůstkovým indexováním přidáním mezipaměti do existujícího indexeru nebo při definování nového indexeru přidejte mezipaměť.

> [!div class="nextstepaction"]
> [Nastavení přírůstkového indexování](search-howto-incremental-index.md)
