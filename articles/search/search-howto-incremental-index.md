---
title: Konfigurace mezipaměti a přírůstkové obohacení (Preview)
titleSuffix: Azure Cognitive Search
description: Povolit ukládání do mezipaměti a zachovat stav obohaceného obsahu pro řízené zpracování v dovednostii rozpoznávání. Tato funkce je aktuálně ve verzi Public Preview.
author: vkurpad
manager: eladz
ms.author: vikurpad
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: a1b317b651b0e17c07eb17dbdb8a7c6657d39564
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "90971617"
---
# <a name="how-to-configure-caching-for-incremental-enrichment-in-azure-cognitive-search"></a>Jak nakonfigurovat ukládání do mezipaměti pro přírůstkové obohacení v Azure Kognitivní hledání

> [!IMPORTANT] 
> Přírůstkové obohacení je aktuálně ve verzi Public Preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 
> Tuto funkci poskytují [verze Preview pro REST API](search-api-preview.md) . V tuto chvíli není k dispozici žádný portál ani podpora sady .NET SDK.

V tomto článku se dozvíte, jak přidat do kanálu pro obohacení do mezipaměti, abyste mohli postupně upravovat kroky, aniž by bylo nutné pokaždé znovu sestavovat. Ve výchozím nastavení je dovednosti Bezstavová a změna jakékoli části jejího složení vyžaduje úplné spuštění indexeru. Pomocí přírůstkového obohacení může indexer určit, které části stromu dokumentů se musí aktualizovat na základě změn zjištěných v definicích dovednosti nebo indexer. Existující zpracovaný výstup se zachová a znovu použije tam, kde je to možné. 

Obsah uložený v mezipaměti je umístěný v Azure Storage pomocí informací o účtu, který zadáte. Kontejner s názvem `ms-az-search-indexercache-<alpha-numerc-string>` , je vytvořen při spuštění indexeru. Měl by se považovat za interní komponentu spravovanou vaší vyhledávací službou a nesmí se měnit.

Pokud nejste obeznámeni s nastavením indexerů, začněte nástrojem [indexer](search-indexer-overview.md) a potom pokračujte na [dovednosti](cognitive-search-working-with-skillsets.md) a získejte další informace o kanálech pro obohacení. Další informace o klíčových konceptech najdete v tématu [přírůstkové obohacení](cognitive-search-incremental-indexing-conceptual.md).

## <a name="enable-caching-on-an-existing-indexer"></a>Povolit ukládání do mezipaměti u stávajícího indexeru

Pokud máte stávajícího indexeru, který už má dovednosti, postupujte podle kroků v této části a přidejte ukládání do mezipaměti. V rámci jednorázové operace budete muset indexer resetovat a znovu spustit v plném rozsahu, než se může přírůstkové zpracování projevit.

> [!TIP]
> Jako koncept si můžete projít pomocí tohoto rychlého startu na [portálu](cognitive-search-quickstart-blob.md) a vytvořit potřebné objekty a pak použít post nebo portál k provedení aktualizací. Je možné, že budete chtít připojit Fakturovatelné Cognitive Services prostředku. Pokud chcete indexer spustit několikrát, vyčerpá bezplatné denní přidělení, než budete moct dokončit všechny kroky.

### <a name="step-1-get-the-indexer-definition"></a>Krok 1: získání definice indexeru

Začněte s platným existujícím indexerem, který má tyto komponenty: zdroj dat, dovednosti, index. Indexer by měl být spustitelný. 

Pomocí klienta rozhraní API Sestavte [požadavek GET indexeru](/rest/api/searchservice/get-indexer) , který získá aktuální konfiguraci indexeru. Při použití verze Preview rozhraní API k získání indexeru `cache` je do definic přidána vlastnost nastavená na hodnotu null.

```http
GET https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

Zkopírujte definici indexeru z odpovědi.

### <a name="step-2-modify-the-cache-property-in-the-indexer-definition"></a>Krok 2: Úprava vlastnosti cache v definici indexeru

Ve výchozím nastavení má `cache` vlastnost hodnotu null. Pro nastavení konfigurace mezipaměti použijte klienta rozhraní API (portál nepodporuje tuto aktualizaci částic). 

Upravte objekt mezipaměti tak, aby obsahoval následující povinné a volitelné vlastnosti: 

+ `storageConnectionString`Je vyžadován a musí být nastaven na připojovací řetězec úložiště Azure. 
+ `enableReprocessing`Vlastnost Boolean je volitelná ( `true` ve výchozím nastavení) a označuje, že je povoleno přírůstkové rozšíření. V případě potřeby ho můžete nastavit tak, aby `false` se pozastavilo přírůstkové zpracování, zatímco jiné operace náročné na prostředky, jako je indexování nových dokumentů, jsou v provozu a pak se Překlopí zpět na `true` později.

```json
{
    "name": "<YOUR-INDEXER-NAME>",
    "targetIndexName": "<YOUR-INDEX-NAME>",
    "dataSourceName": "<YOUR-DATASOURCE-NAME>",
    "skillsetName": "<YOUR-SKILLSET-NAME>",
    "cache" : {
        "storageConnectionString" : "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
}
```

### <a name="step-3-reset-the-indexer"></a>Krok 3: resetování indexeru

Při nastavování přírůstkového obohacení pro existující indexery se vyžaduje resetování indexeru, aby se zajistilo, že všechny dokumenty jsou v konzistentním stavu. Pro tuto úlohu můžete použít portál nebo klienta rozhraní API a [REST API resetování indexeru](/rest/api/searchservice/reset-indexer) .

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/reset?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

### <a name="step-4-save-the-updated-definition"></a>Krok 4: uložení aktualizované definice

[Aktualizace indexeru](/rest/api/searchservice/preview-api/update-indexer) s požadavkem Put by měl tělo žádosti obsahovat aktualizovanou definici indexeru, která má vlastnost cache. Pokud získáte 400, zkontrolujte definici indexeru a ujistěte se, že jsou splněné všechny požadavky (zdroj dat, dovednosti, index).

```http
PUT https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
{
    "name" : "<YOUR-INDEXER-NAME>",
    ...
    "cache": {
        "storageConnectionString": "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    }
}
```

Pokud teď v indexeru vydáte jinou žádost o získání, bude odpověď ze služby zahrnovat `ID` vlastnost v objektu mezipaměti. Alfanumerický řetězec je připojen k názvu kontejneru obsahujícímu všechny výsledky v mezipaměti a průběžný stav každého dokumentu zpracovaného tímto indexerem. ID se použije k jedinečnému pojmenování mezipaměti v úložišti objektů BLOB.

```http
    "cache": {
        "ID": "<ALPHA-NUMERIC STRING>",
        "enableReprocessing": true,
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-STORAGE-KEY>;EndpointSuffix=core.windows.net"
    }
```

### <a name="step-5-run-the-indexer"></a>Krok 5: spuštění indexeru

Indexer můžete spustit pomocí portálu nebo rozhraní API. Na portálu v seznamu indexery vyberte indexer a klikněte na **Spustit**. Jednou z výhod používání portálu je, že můžete monitorovat stav indexeru, poznamenat dobu trvání úlohy a počet zpracovávaných dokumentů. Stránky portálu se aktualizují každých několik minut.

Alternativně můžete použít REST ke [spuštění indexeru](/rest/api/searchservice/run-indexer):

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/run?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

Po spuštění indexeru můžete najít mezipaměť v úložišti objektů BLOB v Azure. Název kontejneru má následující formát: `ms-az-search-indexercache-<YOUR-CACHE-ID>`

> [!NOTE]
> Resetování a opakované spuštění indexeru má za následek úplné opětovné sestavení, aby se obsah mohl ukládat do mezipaměti. Všechna obohacení vnímání se znovu spustí u všech dokumentů.
>

### <a name="step-6-modify-a-skillset-and-confirm-incremental-enrichment"></a>Krok 6: Úprava dovednosti a potvrzení přírůstkového obohacení

Pokud chcete upravit dovednosti, můžete použít portál nebo rozhraní API. Například pokud používáte překlad textu, jednoduchá vložená změna z nástroje `en` na `es` nebo jiný jazyk je dostačující pro testování přírůstkového rozšíření při testování konceptu.

Spusťte indexer znovu. Aktualizují se jenom ty části obohaceného stromu dokumentů. Pokud jste jako zkušební verzi používali nástroj pro [rychlé zprovoznění portálu](cognitive-search-quickstart-blob.md) a měníte dovednost překladu textu na ES, všimnete si, že místo původních 14 se aktualizují jenom 8 dokumentů. Soubory imagí neovlivněné procesem překladu se znovu použijí z mezipaměti.

## <a name="enable-caching-on-new-indexers"></a>Povolit ukládání do mezipaměti pro nové indexery

Pokud chcete nastavit přírůstkové obohacení pro nového indexeru, stačí, `cache` když při volání metody [Create indexer (2020-06-30-Preview)](/rest/api/searchservice/preview-api/create-indexer)zahrnete do datové části definice indexeru vlastnost. 


```json
{
    "name": "<YOUR-INDEXER-NAME>",
    "targetIndexName": "<YOUR-INDEX-NAME>",
    "dataSourceName": "<YOUR-DATASOURCE-NAME>",
    "skillsetName": "<YOUR-SKILLSET-NAME>",
    "cache" : {
        "storageConnectionString" : "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
    }
}
```

## <a name="checking-for-cached-output"></a>Kontrola výstupu v mezipaměti

Mezipaměť je vytvořena, používána a spravována indexerem a její obsah není reprezentován ve formátu, který je čitelný pro člověka. Nejlepším způsobem, jak určit, jestli se mezipaměť používá, je spustit indexer a porovnat je s metrikami pro dobu provádění a počet dokumentů. 

Předpokládejme například, že dovednosti začíná s analýzou obrazu a optického rozpoznávání znaků (OCR) naskenovaných dokumentů následovaných analýzou pro výsledný text. Pokud upravíte dovednost textu pro příjem dat, indexer může načíst všechny dříve zpracované image a obsah OCR z mezipaměti, aktualizovat a zpracovávat pouze změny související s textem, které jsou označeny vašimi úpravami. Můžete očekávat, že se v počtu dokumentů zobrazí méně dokumentů (například 8/8 na rozdíl od 14/14 v původním běhu), kratší doba spuštění a méně poplatků za vaše vyúčtování.

## <a name="working-with-the-cache"></a>Práce s mezipamětí

Jakmile je mezipaměť funkční, indexery kontrolují mezipaměť vždy, když je volána rutina [Spustit indexer](/rest/api/searchservice/run-indexer) , aby bylo možné zjistit, které části stávajícího výstupu lze použít. 

Následující tabulka shrnuje, jak různá rozhraní API souvisí s mezipamětí:

| Rozhraní API           | Dopad mezipaměti     |
|---------------|------------------|
| [Vytvořit indexer (2020-06-30 – Preview)](/rest/api/searchservice/preview-api/create-indexer) | Vytvoří a spustí indexer při prvním použití, včetně vytvoření mezipaměti, pokud ho definuje definice indexeru. |
| [Spustit indexer](/rest/api/searchservice/run-indexer) | Spustí na vyžádání kanál pro obohacení. Toto rozhraní API načte z mezipaměti, pokud existuje, nebo vytvoří mezipaměť, pokud jste přidali do aktualizované definice indexeru ukládání do mezipaměti. Když spustíte indexer s povoleným ukládáním do mezipaměti, indexer vynechá kroky, pokud je možné použít výstup z mezipaměti. Můžete použít verzi rozhraní API všeobecně k dispozici nebo Preview.|
| [Resetovat indexer](/rest/api/searchservice/reset-indexer)| Vymaže indexer všech přírůstkových informací o indexování. Další indexer se spustí (buď na vyžádání, nebo podle plánu) zcela znovu od začátku, včetně opětovného spuštění všech dovedností a nové sestavení mezipaměti. Je funkčně ekvivalentní k odstranění indexeru a jeho opětovnému vytvoření. Můžete použít verzi rozhraní API všeobecně k dispozici nebo Preview.|
| [Resetovat dovednosti](/rest/api/searchservice/preview-api/reset-skills) | Určuje, které dovednosti se mají znovu spustit u dalšího indexeru, i když jste nezměnili žádnou dovednost. Mezipaměť se odpovídajícím způsobem aktualizuje. Výstupy, jako je znalostní báze úložiště nebo vyhledávací index, se aktualizují pomocí opakovaně použitelných dat z mezipaměti a nového obsahu na aktualizovanou dovednost. |

Další informace o řízení, co se stane s mezipamětí, najdete v tématu [Správa mezipaměti](cognitive-search-incremental-indexing-conceptual.md#cache-management).

## <a name="next-steps"></a>Další kroky

Přírůstkové obohacení se vztahuje na indexery, které obsahují dovednosti. V dalším kroku najdete dokumentaci k dovednosti, která vám pomůže pochopit koncepty a kompozici. 

Navíc po povolení mezipaměti budete chtít znát parametry a rozhraní API, které přiřadí do mezipaměti, včetně toho, jak přepsat nebo vynutit konkrétní chování. Další informace najdete na následujících odkazech.

+ [Dovednosti koncepty a složení](cognitive-search-working-with-skillsets.md)
+ [Vytvoření dovednosti](cognitive-search-defining-skillset.md)
+ [Úvod k přírůstkové obohacení a ukládání do mezipaměti](cognitive-search-incremental-indexing-conceptual.md)