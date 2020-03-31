---
title: Konfigurace mezipaměti a přírůstkového obohacení (náhled)
titleSuffix: Azure Cognitive Search
description: Povolit ukládání do mezipaměti a zachovat stav obohaceného obsahu pro řízené zpracování v kognitivní skillset. Tato funkce je aktuálně ve verzi Public Preview.
author: vkurpad
manager: eladz
ms.author: vikurpad
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 66bac2a063a3257a2101ca2f30e5946264adb9ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76989548"
---
# <a name="how-to-configure-caching-for-incremental-enrichment-in-azure-cognitive-search"></a>Jak nakonfigurovat ukládání do mezipaměti pro přírůstkové obohacení v Azure Cognitive Search

> [!IMPORTANT] 
> Přírůstkové obohacení je aktuálně ve verzi Public Preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [Rozhraní REST API verze 2019-05-06-Preview](search-api-preview.md) poskytuje tuto funkci. V tuto chvíli neexistuje žádná podpora portálu nebo sady .NET SDK.

Tento článek ukazuje, jak přidat ukládání do mezipaměti do kanálu obohacení, takže můžete postupně upravovat kroky bez nutnosti opětovného sestavení pokaždé. Ve výchozím nastavení skillset je bezstavové a změna libovolné části jeho složení vyžaduje úplné opakování indexeru. Při přírůstkovém obohacení může indexer určit, které části stromu dokumentů je třeba aktualizovat na základě změn zjištěných v definicích sady dovedností nebo indexeru. Stávající zpracovaný výstup je zachován a znovu použit, kdykoli je to možné. 

Obsah uložený v mezipaměti se umístí do Služby Azure Storage pomocí informací o účtu, které poskytnete. Kontejner s `ms-az-search-indexercache-<alpha-numerc-string>`názvem , je vytvořen při spuštění indexeru. Měla by být považována za interní součást spravovanou vyhledávací službou a nesmí být změněna.

Pokud nejste obeznámeni s nastavením indexery, začněte s [přehledem indexeru](search-indexer-overview.md) a pak [pokračujte na skillsets](cognitive-search-working-with-skillsets.md) se dozvíte o kanálech obohacení. Další informace o klíčových pojmech naleznete [v tématu přírůstkové obohacení](cognitive-search-incremental-indexing-conceptual.md).

## <a name="enable-caching-on-an-existing-indexer"></a>Povolení ukládání do mezipaměti na existujícím indexeru

Pokud máte existující indexer, který již má skillset, postupujte podle kroků v této části přidat ukládání do mezipaměti. Jako jednorázová operace budete muset obnovit a znovu spustit indexer v plném rozsahu, než se přírůstkové zpracování projeví.

> [!TIP]
> Jako proof-of-concept můžete spustit tento [portál rychlý start](cognitive-search-quickstart-blob.md) k vytvoření potřebné objekty a potom použijte Postman nebo portál, aby vaše aktualizace. Můžete chtít připojit fakturovatelný prostředek služeb Cognitive Services. Spuštění indexeru vícekrát vyčerpá volné denní přidělení před dokončením všech kroků.

### <a name="step-1-get-the-indexer-definition"></a>Krok 1: Získání definice indexeru

Začněte s platným, existující indexer, který má tyto součásti: zdroj dat, skillset, index. Indexer by měl být spustitelný. 

Pomocí klienta rozhraní API vytvořte [požadavek GET Indexer,](https://docs.microsoft.com/rest/api/searchservice/get-indexer) abyste získali aktuální konfiguraci indexeru. Při použití verze rozhraní PREVIEW rozhraní API get `cache` indexer, vlastnost nastavena na null je přidán do definice.

```http
GET https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

Zkopírujte definici indexeru z odpovědi.

### <a name="step-2-modify-the-cache-property-in-the-indexer-definition"></a>Krok 2: Změna vlastnosti mezipaměti v definici indexeru

Ve výchozím `cache` nastavení je vlastnost null. Pomocí klienta rozhraní API nastavte konfiguraci mezipaměti (portál tuto aktualizaci částic nepodporuje). 

Upravte objekt mezipaměti tak, aby obsahoval následující povinné a volitelné vlastnosti: 

+ Je `storageConnectionString` povinný a musí být nastavenna na připojovací řetězec úložiště Azure. 
+ Logická `enableReprocessing` vlastnost je`true` volitelná (ve výchozím nastavení) a označuje, že je povoleno přírůstkové obohacení. V případě potřeby můžete `false` nastavit, aby bylo pozastaveno přírůstkové zpracování, zatímco probíhají jiné operace náročné na `true` prostředky, například indexování nových dokumentů, a potom je překlopit zpět na později.

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

### <a name="step-3-reset-the-indexer"></a>Krok 3: Obnovení indexeru

Obnovení indexeru je vyžadováno při nastavování přírůstkového obohacení pro existující indexery, aby bylo zajištěno, že všechny dokumenty jsou v konzistentním stavu. Pro tuto úlohu můžete použít portál nebo klienta rozhraní API a [rozhraní REST API resetování indexeru.](https://docs.microsoft.com/rest/api/searchservice/reset-indexer)

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/reset?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

### <a name="step-4-save-the-updated-definition"></a>Krok 4: Uložení aktualizované definice

[Aktualizujte indexer](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-indexer) požadavkem PUT, tělo požadavku by mělo obsahovat aktualizovanou definici indexeru, která má vlastnost mezipaměti. Pokud získáte 400, zkontrolujte definici indexeru a ujistěte se, že jsou splněny všechny požadavky (zdroj dat, skillset, index).

```http
PUT https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2019-05-06-Preview
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

Pokud nyní vydáte další požadavek GET na indexeru, `ID` odpověď ze služby bude obsahovat vlastnost v objektu mezipaměti. Alfanumerický řetězec je připojen k názvu kontejneru obsahující všechny výsledky uložené v mezipaměti a zprostředkující stav každého dokumentu zpracovaného tímto indexerem. ID se použije k jedinečnému pojmenování mezipaměti v úložišti objektů Blob.

    "cache": {
        "ID": "<ALPHA-NUMERIC STRING>",
        "enableReprocessing": true,
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-STORAGE-KEY>;EndpointSuffix=core.windows.net"
    }

### <a name="step-5-run-the-indexer"></a>Krok 5: Spuštění indexeru

Chcete-li spustit indexer, můžete použít portál nebo rozhraní API. Na portálu vyberte ze seznamu indexerů indexer a klepněte na tlačítko **Spustit**. Jednou z výhod použití portálu je, že můžete sledovat stav indexeru, poznamenejte si dobu trvání úlohy a počet zpracovaných dokumentů. Portálové stránky se aktualizují každých několik minut.

Alternativně můžete použít REST ke [spuštění indexeru](https://docs.microsoft.com/rest/api/searchservice/run-indexer):

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/run?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

Po spuštění indexeru najdete mezipaměť v úložišti objektů Blob Azure. Název kontejneru je v následujícím formátu:`ms-az-search-indexercache-<YOUR-CACHE-ID>`

> [!NOTE]
> Obnovení a opětovné spuštění indexeru má za následek úplné sestavení, aby bylo možné uložit obsah do mezipaměti. Všechna kognitivní obohacení budou znovu spuštěna ve všech dokumentech.
>

### <a name="step-6-modify-a-skillset-and-confirm-incremental-enrichment"></a>Krok 6: Úprava sady dovedností a potvrzení přírůstkového obohacení

Chcete-li upravit skillset, můžete použít portál nebo rozhraní API. Například pokud používáte překlad textu, jednoduchý vřádkový přechod z `en` `es` nebo jiný jazyk je dostačující pro testování proof-of-concept přírůstkového obohacení.

Spusťte indexer znovu. Aktualizovány jsou pouze ty části stromu obohaceného dokumentu. Pokud jste použili [portál rychlý start](cognitive-search-quickstart-blob.md) jako proof-of-concept, úpravou dovednosti překladu textu na 'es', zjistíte, že pouze 8 dokumenty jsou aktualizovány místo původních 14. Obrazové soubory, které nejsou ovlivněny procesem překladu, jsou znovu použity z mezipaměti.

## <a name="enable-caching-on-new-indexers"></a>Povolení ukládání do mezipaměti u nových indexerů

Chcete-li nastavit přírůstkové obohacení pro nový indexer, `cache` stačí zahrnout vlastnost do datové části definice indexeru při volání [Create Indexer (2019-05-06-Preview).](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-indexer) 


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

## <a name="checking-for-cached-output"></a>Kontrola výstupu uloženého v mezipaměti

Mezipaměť je vytvořena, používáa a spravována indexerem a její obsah není reprezentován ve formátu, který je čitelný pro člověka. Nejlepší způsob, jak zjistit, zda se používá mezipaměť je spuštěním indexeru a porovnat před a po metriky pro čas spuštění a počet dokumentů. 

Předpokládejme například skillset, který začíná analýzou obrazu a optickým rozpoznáváním znaků (OCR) naskenovaných dokumentů, následovaným následnou analýzou výsledného textu. Pokud změníte přeplovací textovou dovednost, může indexer načíst veškerý dříve zpracovaný obraz a obsah OCR z mezipaměti, aktualizovat a zpracovávat pouze změny související s textem, které jsou označeny vašimi úpravami. Můžete očekávat, že v počtu dokumentů se zobrazí méně dokumentů (například 8/8 oproti 14/14 v původním běhu), kratší doby provádění a méně poplatků na účtu.

## <a name="working-with-the-cache"></a>Práce s mezipamětí

Jakmile je mezipaměť funkční, indexery zkontrolovat mezipaměť vždy, když je volána [Spustit Indexer,](https://docs.microsoft.com/rest/api/searchservice/run-indexer) chcete-li zjistit, které části existujícího výstupu lze použít. 

Následující tabulka shrnuje, jak různá api souvisejí s mezipamětí:

| rozhraní API           | Dopad mezipaměti     |
|---------------|------------------|
| [Vytvořit indexer (2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-indexer) | Vytvoří a spustí indexer při prvním použití, včetně vytvoření mezipaměti, pokud ji určuje definice indexeru. |
| [Spustit indexer](https://docs.microsoft.com/rest/api/searchservice/run-indexer) | Provede kanál obohacení na vyžádání. Toto rozhraní API čte z mezipaměti, pokud existuje, nebo vytvoří mezipaměť, pokud jste přidali ukládání do mezipaměti do aktualizované definice indexeru. Spustíte-li indexer, který má ukládání do mezipaměti povoleno, indexer vynese kroky, pokud lze použít výstup uložený v mezipaměti. Můžete použít obecně dostupnou verzi rozhraní API nebo verzi rozhraní API preview tohoto rozhraní API.|
| [Obnovit indexer](https://docs.microsoft.com/rest/api/searchservice/reset-indexer)| Vymaže indexer všechny informace o přírůstkové indexování. Další indexer spustit (buď na vyžádání nebo plán) je úplné přepracování od začátku, včetně opětovného spuštění všech dovedností a opětovné sestavení mezipaměti. Je funkčně ekvivalentní k odstranění indexeru a jeho opětovnému vytvoření. Můžete použít obecně dostupnou verzi rozhraní API nebo verzi rozhraní API preview tohoto rozhraní API.|
| [Obnovit dovednosti (2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/reset-skills) | Určuje, které dovednosti se mají znovu spustit při dalším spuštění indexeru, i když jste žádné dovednosti nezměnili. Mezipaměť je odpovídajícím způsobem aktualizována. Výstupy, jako je úložiště znalostí nebo index vyhledávání, se aktualizují pomocí opakovaně použitelných dat z mezipaměti a nového obsahu podle aktualizované dovednosti. |

Další informace o řízení toho, co se stane s mezipamětí, naleznete v [tématu Správa mezipaměti](cognitive-search-incremental-indexing-conceptual.md#cache-management).

## <a name="next-steps"></a>Další kroky

Přírůstkové obohacení je použitelné na indexery, které obsahují skillsets. Jako další krok navštivte dokumentaci skillset pochopit pojmy a složení. 

Navíc po povolení mezipaměti, budete chtít vědět o parametry a rozhraní API, které faktor do ukládání do mezipaměti, včetně jak přepsat nebo vynutit konkrétní chování. Další informace naleznete v následujících odkazech.

+ [Koncepce a složení sady dovedností](cognitive-search-working-with-skillsets.md)
+ [Jak vytvořit sadu dovedností](cognitive-search-defining-skillset.md)
+ [Úvod do postupného obohacování a ukládání do mezipaměti](cognitive-search-incremental-indexing-conceptual.md)
