---
title: Vytváření vlastních sad SDK pro digitální vlákna Azure s využitím AutoRest
titleSuffix: Azure Digital Twins
description: Podívejte se, jak vygenerovat vlastní sady SDK pro použití digitálních vláken Azure s jinými jazyky než C#.
author: baanders
ms.author: baanders
ms.date: 4/24/2020
ms.topic: how-to
ms.service: digital-twins
ROBOTS: NOINDEX, NOFOLLOW
ms.openlocfilehash: 3d6fa1660f5204137dbebeae36f6d06852d88412
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/12/2020
ms.locfileid: "84725796"
---
# <a name="create-custom-sdks-for-azure-digital-twins-with-autorest"></a>Vytváření vlastních sad SDK pro digitální vlákna Azure s využitím AutoRest

[!INCLUDE [Azure Digital Twins current preview status](../../includes/digital-twins-preview-status.md)]

Teď jediná publikovaná sada SDK pro datovou rovinu pro interakci s rozhraními API digitálních vláken Azure je určena pro .NET (C#). V tématu [Postupy: použití rozhraní API a sad SDK pro digitální vlákna Azure](how-to-use-apis-sdks.md)si můžete přečíst o sadě .NET SDK a obecně o rozhraních API. Pokud pracujete v jiném jazyce, v tomto článku se dozvíte, jak vygenerovat vlastní sadu SDK v jazyce podle vašeho výběru pomocí funkce AutoRest.

## <a name="set-up-the-sdk"></a>Nastavení sady SDK

K vygenerování sady SDK budete potřebovat:
* [AutoRest](https://github.com/Azure/autorest), verze 2.0.4413 (verze 3 není momentálně podporovaná)
* [Node.js](https://nodejs.org) jako předpoklad pro AutoRest
* [Soubor openapi digitálního vlákna (Swagger) pro Azure](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/preview/2020-03-01-preview/digitaltwins.json)

Pokud máte Node.js nainstalované, můžete spustit tento příkaz, abyste se ujistili, že máte nainstalovanou správnou verzi AutoRest:
```cmd/sh
npm install -g autorest@2.0.4413
```

Pokud chcete spustit AutoRest proti souboru Swagger digitálních vláken Azure, použijte následující postup:
1. Zkopírujte soubor Swagger digitálních vláken Azure do pracovního adresáře.
2. Na příkazovém řádku přepněte do tohoto pracovního adresáře.
3. Spusťte AutoRest pomocí následujícího příkazu.

```cmd/sh
autorest --input-file=adtApiSwagger.json --csharp --output-folder=ADTApi --add-credentials --azure-arm --namespace=ADTApi
```

V důsledku toho se v pracovním adresáři zobrazí nová složka s názvem *ADTApi* . Vygenerované soubory sady SDK budou mít *ADTApi*oboru názvů, který budete dál používat ve zbývajících příkladech.

AutoRest podporuje široké spektrum generátorů kódů jazyka.

## <a name="add-the-sdk-to-a-visual-studio-project"></a>Přidat sadu SDK do projektu sady Visual Studio

Můžete zahrnout soubory generované AutoRest přímo do řešení .NET. Stejně jako budete ale pravděpodobně potřebovat sadu SDK pro Azure Digital revlákens v několika samostatných projektech (klientské aplikace, Azure Functionsch aplikacích atd.), doporučujeme sestavit samostatný projekt (knihovny tříd .NET) ze generovaných souborů. Pak můžete zahrnout tento projekt knihovny tříd do jiných řešení jako odkaz na projekt.

V této části jsou uvedeny pokyny k sestavení sady SDK jako knihovny tříd, což je vlastní projekt a lze je zahrnout do jiných projektů. Postupujte takto:

1. Vytvoření nového řešení sady Visual Studio pro knihovnu tříd
2. Jako název projektu použijte název "ADTApi".
3. V Průzkumníku řešení klikněte pravým tlačítkem na projekt *ADTApi* vygenerovaného řešení a zvolte *Přidat > existující položku...*
4. Najděte složku, do které jste sadu SDK vygenerovali, a vyberte soubory na kořenové úrovni.
5. Stiskněte OK
6. Přidejte do projektu složku (kliknutím pravým tlačítkem vyberte projekt v Průzkumník řešení a zvolte *přidat > nová složka*).
7. Pojmenování složky "modely"
8. V Průzkumníku řešení klikněte pravým tlačítkem na složku *modely* a vyberte *Přidat > existující položka...*
9. Vyberte soubory ve složce *modely* VYGENEROVANÉ sady SDK a stiskněte OK.

K úspěšnému sestavení sady SDK bude projekt potřebovat tyto odkazy:
* `Microsoft.Rest.ClientRuntime`
* `Microsoft.Rest.ClientRuntime.Azure`

Pokud je chcete přidat, otevřete *nástroje > správce balíčků nuget > spravovat balíčky NuGet pro řešení...*.

1. Na panelu se ujistěte, že je vybraná karta *Procházet* .
2. Hledání *Microsoft. REST*
3. Vyberte balíčky *ClientRuntime* a *ClientRuntime. Azure* a přidejte je do svého řešení.

Nyní můžete sestavit projekt a zahrnout ho jako odkaz na projekt v libovolné aplikaci digitálního vlákna v Azure, kterou zapisujete.

## <a name="general-guidelines-for-generated-sdks"></a>Obecné pokyny pro vygenerované sady SDK

Tato část obsahuje obecné informace o nástroji a pokyny pro používání vygenerované sady SDK.

### <a name="synchronous-and-asynchronous-calls"></a>Synchronní a asynchronní volání

Všechny funkce sady SDK přicházejí v synchronních a asynchronních verzích.

### <a name="typed-and-untyped-data"></a>Zadaná a netypové data

REST API volání obecně vracejí objekty silného typu. Protože ale digitální vlákna Azure umožňuje uživatelům definovat vlastní typy pro vlákna, neexistuje žádný způsob, jak předem definovat statická návratová data pro mnoho volání digitálních vláken Azure. Místo toho rozhraní API vrátí typy obálky silného typu tam, kde je to možné, a vlastní data typu jsou v objektech Json.NET (používají se všude, kde se v podpisech rozhraní API zobrazuje datový typ "Object"). Tyto objekty lze v kódu vhodně přetypovat.

### <a name="error-handling"></a>Zpracování chyb

Pokaždé, když v sadě SDK dojde k chybě (včetně chyb HTTP, jako je 404), SDK vyvolá výjimku. Z tohoto důvodu je důležité zapouzdřit všechna volání rozhraní API v blocích try/catch.

Zde je fragment kódu, který se pokusí přidat dvojitou hodnotu a zachytí všechny chyby v tomto procesu:

```csharp
try
{
    await client.DigitalTwins.AddAsync(id, initData);
    Console.WriteLine($"Created a twin successfully: {id}");
}
catch (ErrorResponseException e)
{
    Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}"); 
}
```

### <a name="paging"></a>Stránkování

AutoRest generuje dva typy vzorů stránkování pro sadu SDK:
* Jedna pro všechna rozhraní API s výjimkou rozhraní API pro dotazy
* Jedno pro rozhraní API pro dotazování

Ve vzoru stránkování bez dotazu existují dvě verze každého volání:
* Verze, která provede počáteční volání (například `DigitalTwins.ListEdges()` )
* Verze, která získá následné stránky s příponou "Next" (například `DigitalTwins.ListEdgesNext()` )

Tady je fragment kódu, který ukazuje, jak načíst stránkovaný seznam odchozích vztahů z digitálních vláken Azure:
```csharp
try
{
    // List to hold the results in
    List<object> relList = new List<object>();
    // Enumerate the IPage object returned to get the results
    // ListAsync will throw if an error occurs
    IPage<object> relPage = await client.DigitalTwins.ListEdgesAsync(id);
    relList.AddRange(relPage);
    // If there are more pages, the NextPageLink in the page is set
    while (relPage.NextPageLink != null)
    {
        // Get more pages...
        relPage = await client.DigitalTwins.ListEdgesNextAsync(relPage.NextPageLink);
        relList.AddRange(relPage);
    }
    Console.WriteLine($"Found {relList.Count} relationships on {id}");
    // Do something with each object found
    // As relationships are custom types, they are JSON.Net types
    foreach (JObject r in relList)
    {
        string relId = r.Value<string>("$edgeId");
        string relName = r.Value<string>("$relationship");
        Console.WriteLine($"Found relationship {relId} from {id}");
    }
}
catch (ErrorResponseException e)
{
    Console.WriteLine($"*** Error retrieving relationships on {id}: {e.Response.StatusCode}");
}
```

Druhý vzor se generuje jenom pro rozhraní API dotazů. Používá `continuationToken` explicitně.

Tady je příklad s tímto vzorem:

```csharp
string query = "SELECT * FROM digitaltwins";
string conToken = null; // continuation token from the query
int page = 0;
try
{
    // Repeat the query while there are pages
    do
    {
        QuerySpecification spec = new QuerySpecification(query, conToken);
        QueryResult qr = await client.Query.QueryTwinsAsync(spec);
        page++;
        Console.WriteLine($"== Query results page {page}:");
        if (qr.Items != null)
        {
            // Query returns are JObjects
            foreach(JObject o in qr.Items)
            {
                string twinId = o.Value<string>("$dtId");
                Console.WriteLine($"  Found {twinId}");
            }
        }
        Console.WriteLine($"== End query results page {page}");
        conToken = qr.ContinuationToken;
    } while (conToken != null);
} catch (ErrorResponseException e)
{
    Console.WriteLine($"*** Error in twin query: ${e.Response.StatusCode}");
}
```

## <a name="next-steps"></a>Další kroky

Projděte si postup vytvoření klientské aplikace, kde můžete použít sadu SDK:
* [Kurz: vytvoření kódu klientské aplikace](tutorial-code.md)