---
title: Přidat přírůstkové indexování (Náhled)
titleSuffix: Azure Cognitive Search
description: Povolte sledování změn a zachovejte stav obohaceného obsahu pro řízené zpracování v dovednostii rozpoznávání. Tato funkce je aktuálně ve verzi Public Preview.
author: vkurpad
manager: eladz
ms.author: vikurpad
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 09defe9648208e2300594169add990d4bcbd7a39
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112573"
---
# <a name="how-to-set-up-incremental-indexing-of-enriched-documents-in-azure-cognitive-search"></a>Jak nastavit přírůstkové indexování obohacených dokumentů v Azure Kognitivní hledání

> [!IMPORTANT] 
> Přírůstkové indexování je aktuálně ve verzi Public Preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Tato funkce poskytuje [REST API verze 2019-05-06-Preview](search-api-preview.md) . V tuto chvíli není k dispozici žádný portál ani podpora sady .NET SDK.

V tomto článku se dozvíte, jak přidat stav a ukládání do mezipaměti pro obohacené dokumenty, které se pohybují prostřednictvím kanálu pro rozšíření Azure Kognitivní hledání, abyste mohli přírůstkově indexovat dokumenty z libovolného podporovaného zdroje dat. Ve výchozím nastavení je dovednosti Bezstavová a změna jakékoli části jejího složení vyžaduje úplné spuštění indexeru. V případě přírůstkového indexování může indexer určit, které části kanálu se změnily, znovu použít existující rozšíření pro nezměněné části a revidovat obohacení kroků, které se mění. Obsah uložený v mezipaměti je umístěný v Azure Storage.

Pokud nejste obeznámeni s nastavením indexerů, začněte nástrojem [indexer](search-indexer-overview.md) a potom pokračujte na [dovednosti](cognitive-search-working-with-skillsets.md) a získejte další informace o kanálech pro obohacení. Další informace o klíčových konceptech najdete v tématu [přírůstkové indexování](cognitive-search-incremental-indexing-conceptual.md).

## <a name="modify-an-existing-indexer"></a>Úprava existujícího indexeru

Pokud máte indexer, postupujte podle těchto kroků a povolte přírůstkové indexování.

### <a name="step-1-get-the-indexer"></a>Krok 1: získání indexeru

Začněte s platným existujícím indexerem, který má požadovaný zdroj dat a index již definován. Indexer by měl být spustitelný. Pomocí klienta rozhraní API Sestavte [požadavek GET](https://docs.microsoft.com/rest/api/searchservice/get-indexer) , který získá aktuální konfiguraci indexeru, ke kterému chcete přidat přírůstkové indexování.

```http
GET https://[service name].search.windows.net/indexers/[your indexer name]?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [admin key]
```

### <a name="step-2-add-the-cache-property"></a>Krok 2: Přidání vlastnosti cache

Upravte odpověď z požadavku GET a přidejte do indexeru vlastnost `cache`. Objekt mezipaměti vyžaduje pouze jednu vlastnost, která je připojovacím řetězcem k účtu Azure Storage.

```json
    "cache": {
        "storageConnectionString": "[your storage connection string]"
    }
```

### <a name="step-3-reset-the-indexer"></a>Krok 3: resetování indexeru

> [!NOTE]
> Resetování indexeru způsobí, že se znovu zpracují všechny dokumenty ve zdroji dat, aby se obsah mohla ukládat do mezipaměti. Všechna obohacení vnímání se spustí znovu ve všech dokumentech.
>

Při nastavování přírůstkového indexování pro existující indexery se vyžaduje resetování indexeru, aby se zajistilo, že všechny dokumenty jsou v konzistentním stavu. Resetujte indexer pomocí [REST API](https://docs.microsoft.com/rest/api/searchservice/reset-indexer).

```http
POST https://[service name].search.windows.net/indexers/[your indexer name]/reset?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [admin key]
```

### <a name="step-4-save-the-updated-definition"></a>Krok 4: uložení aktualizované definice

Aktualizujte definici indexeru s požadavkem PUT, tělo požadavku by mělo obsahovat aktualizovanou definici indexeru.

```http
PUT https://[service name].search.windows.net/indexers/[your indexer name]/reset?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [admin key]
{
    "name" : "your indexer name",
    ...
    "cache": {
        "storageConnectionString": "[your storage connection string]",
        "enableReprocessing": true
    }
}
```

Pokud teď v indexeru vydáte jinou žádost o získání, bude odpověď ze služby zahrnovat vlastnost `cacheId` v objektu mezipaměti. `cacheId` je název kontejneru, který bude obsahovat všechny výsledky v mezipaměti a průběžný stav každého dokumentu zpracovaného tímto indexerem.

## <a name="enable-incremental-indexing-on-new-indexers"></a>Povolit přírůstkové indexování u nových indexerů

Pokud chcete nastavit přírůstkové indexování pro nového indexeru, zahrňte do datové části definice indexeru vlastnost `cache`. Ujistěte se, že používáte `2019-05-06-Preview` verzi rozhraní API.

## <a name="overriding-incremental-indexing"></a>Přepsání přírůstkového indexování

Když se nakonfiguruje, přírůstkové indexování sleduje změny v rámci vašeho kanálu indexování a v dokumentech zjišťuje konzistenci napříč vaším indexem a výčnělky. V některých případech budete muset toto chování přepsat, abyste zajistili, že indexovací člen neprovede další práci v důsledku aktualizace kanálu indexování. Například aktualizace připojovacího řetězce DataSource bude vyžadovat obnovení indexeru a nové indexování všech dokumentů, protože zdroj dat se změnil. Pokud ale aktualizujete připojovací řetězec jenom pomocí nového klíče, nechcete, aby tato změna způsobila aktualizace stávajících dokumentů. V opačném případě může indexer zrušit platnost mezipaměti a rozšířit dokumenty i v případě, že nejsou provedeny žádné změny kanálu indexování. Například můžete chtít indexer deověřit, pokud byste chtěli znovu nasadit vlastní dovednost s novým modelem a chtěli byste se pokusit znovu spustit u všech vašich dokumentů.

### <a name="override-reset-requirement"></a>Přepsat požadavek na resetování

Při provádění změn v kanálu indexování vyžaduje všechny změny, které mají za následek neplatnost mezipaměti, resetování indexeru. Pokud provedete změnu kanálu indexeru a nechcete, aby sledování změn zrušilo platnost mezipaměti, budete muset nastavit parametr `ignoreResetRequirement` QueryString na `true` pro operace indexeru nebo zdroje dat.

### <a name="override-change-detection"></a>Přepsat detekci změn

Při provádění aktualizací dovednosti, jejichž výsledkem je označení dokumentů jako nekonzistentní, například aktualizace vlastní adresy URL dovedností při opětovném nasazení dovednosti, nastavte parametr řetězce dotazu `disableCacheReprocessingChangeDetection` tak, aby `true` na aktualizace dovednosti.

### <a name="force-change-detection"></a>Vynutit detekci změn

Instance: Pokud chcete, aby kanál indexování rozpoznal změnu externí entity, jako je třeba nasazení nové verze vlastní dovednosti, budete muset aktualizovat dovednosti a "dotykové" konkrétní dovednost úpravou definice dovednosti, konkrétně adresy URL, která se má vynutit. Změňte detekci a neověří mezipaměť této dovednosti.

## <a name="next-steps"></a>Další kroky

Tento článek popisuje přírůstkové indexování pro indexery, které zahrnují dovednosti. Pokud chcete dál vymezit své znalosti, Projděte si články o opětovném indexování obecně použitelné pro všechny scénáře indexování v Azure Kognitivní hledání.

+ [Postup opětovného sestavení indexu služby Azure kognitivní hledání](search-howto-reindex.md). 
+ [Indexování velkých datových sad v Azure kognitivní hledání](search-howto-large-index.md). 
