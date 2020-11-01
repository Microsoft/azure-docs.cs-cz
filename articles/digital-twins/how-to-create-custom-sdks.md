---
title: Vytváření vlastních sad SDK pro digitální vlákna Azure s využitím AutoRest
titleSuffix: Azure Digital Twins
description: Podívejte se, jak vygenerovat vlastní sady SDK pro použití digitálních vláken Azure s jinými jazyky než C#.
author: baanders
ms.author: baanders
ms.date: 4/24/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: devx-track-js
ms.openlocfilehash: 158d22ffb3bc5486e0523c07cc2c022c49f2ee9c
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2020
ms.locfileid: "93145595"
---
# <a name="create-custom-sdks-for-azure-digital-twins-using-autorest"></a>Vytváření vlastních sad SDK pro digitální vlákna Azure pomocí AutoRest

V současné době jsou pro rozhraní .NET (C#), JavaScript a Java pouze sady SDK pro rovinu publikovaných dat pro interakci s rozhraními API digitálních vláken Azure. O těchto sadách SDK si můžete přečíst a obecně v tématu [*Postupy: použití rozhraní API a sad SDK pro digitální vlákna Azure*](how-to-use-apis-sdks.md). Pokud pracujete v jiném jazyce, v tomto článku se dozvíte, jak vygenerovat vlastní sadu SDK pro datovou rovinu v jazyce podle vašeho výběru pomocí funkce AutoRest.

>[!NOTE]
> Můžete také použít AutoRest k vygenerování sady SDK řídicí roviny, pokud byste chtěli. Provedete to tak, že provedete kroky v tomto článku pomocí nejnovějšího souboru Swagger (openapi) **řídicí** plochy ze [složky Swagger ovládacího prvku](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/) , nikoli z roviny dat.

## <a name="set-up-your-machine"></a>Nastavení počítače

K vygenerování sady SDK budete potřebovat:
* [AutoRest](https://github.com/Azure/autorest), verze 2.0.4413 (verze 3 není momentálně podporovaná)
* [Node.js](https://nodejs.org) jako předpoklad pro AutoRest
* Nejnovější soubor Swagger (openapi) **roviny dat** v Azure, ze [složky Swagger datové roviny](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins), a do doprovodné složky příkladů.  Stáhněte si soubor Swagger *digitaltwins.js* a jeho složku příkladů do svého místního počítače.

Jakmile je počítač vybavený vše ze seznamu výše, jste připraveni použít k vytvoření sady SDK sadu AutoRest.

## <a name="create-the-sdk-with-autorest"></a>Vytvoření sady SDK pomocí programu AutoRest 

Pokud máte Node.js nainstalované, můžete spustit tento příkaz, abyste se ujistili, že máte nainstalovanou správnou verzi AutoRest:
```cmd/sh
npm install -g autorest@2.0.4413
```

Pokud chcete spustit AutoRest proti souboru Swagger digitálních vláken Azure, použijte následující postup:
1. Zkopírujte soubor Swagger digitálních vláken Azure a jeho doprovodnou složku příkladů do pracovního adresáře.
2. Pomocí okna příkazového řádku přepněte do tohoto pracovního adresáře.
3. Spusťte AutoRest pomocí následujícího příkazu. `<language>`Zástupný text nahraďte vaším jazykem podle vlastního výběru: `python` , `java` , `go` a tak dále. (Můžete najít úplný seznam možností v [souboru Readme pro AutoRest](https://github.com/Azure/autorest).)

```cmd/sh
autorest --input-file=digitaltwins.json --<language> --output-folder=ADTApi --add-credentials --azure-arm --namespace=ADTApi
```

V důsledku toho se v pracovním adresáři zobrazí nová složka s názvem *ADTApi* . Vygenerované soubory sady SDK budou mít obor názvů *ADTApi* . Tento obor názvů budete používat i v ostatních příkladech použití v tomto článku.

AutoRest podporuje široké spektrum generátorů kódů jazyka.

## <a name="add-the-sdk-to-a-visual-studio-project"></a>Přidat sadu SDK do projektu sady Visual Studio

Můžete zahrnout soubory generované AutoRest přímo do řešení .NET. Je ale možné, že budete chtít zahrnout sadu Azure Digital SDK do několika samostatných projektů (klientské aplikace, Azure Functions aplikace atd.). Z tohoto důvodu může být užitečné sestavit samostatný projekt (knihovnu tříd .NET) ze generovaných souborů. Pak můžete zahrnout tento projekt knihovny tříd do několika řešení jako odkaz na projekt.

V této části jsou uvedeny pokyny k sestavení sady SDK jako knihovny tříd, což je vlastní projekt a lze je zahrnout do jiných projektů. Tyto kroky spoléhají na **Visual Studio** ( [tady](https://visualstudio.microsoft.com/downloads/)si můžete nainstalovat nejnovější verzi).

Postupujte takto:

1. Vytvoření nového řešení sady Visual Studio pro knihovnu tříd
2. Jako název projektu použijte *ADTApi*
3. V Průzkumníku řešení klikněte pravým tlačítkem na projekt *ADTApi* vygenerovaného řešení a zvolte *Přidat > existující položku...*
4. Najděte složku, do které jste sadu SDK vygenerovali, a vyberte soubory na kořenové úrovni.
5. Stiskněte OK
6. Přidejte do projektu složku (kliknutím pravým tlačítkem vyberte projekt v Průzkumník řešení a zvolte *přidat > nová složka* ).
7. Pojmenování *modelů* složek
8. V Průzkumníku řešení klikněte pravým tlačítkem na složku *modely* a vyberte *Přidat > existující položka...*
9. Vyberte soubory ve složce *modely* VYGENEROVANÉ sady SDK a stiskněte OK.

K úspěšnému sestavení sady SDK bude projekt potřebovat tyto odkazy:
* `Microsoft.Rest.ClientRuntime`
* `Microsoft.Rest.ClientRuntime.Azure`

Pokud je chcete přidat, otevřete *nástroje > správce balíčků nuget > spravovat balíčky NuGet pro řešení...* .

1. Na panelu se ujistěte, že je vybraná karta *Procházet* .
2. Hledání *Microsoft. REST*
3. Vyberte `ClientRuntime` balíčky a a `ClientRuntime.Azure` přidejte je do svého řešení.

Nyní můžete sestavit projekt a zahrnout ho jako odkaz na projekt v libovolné aplikaci digitálního vlákna v Azure, kterou zapisujete.

## <a name="general-guidelines-for-generated-sdks"></a>Obecné pokyny pro vygenerované sady SDK

Tato část obsahuje obecné informace a pokyny pro používání vygenerované sady SDK.

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

Ve vzoru stránkování bez dotazu je zde uveden fragment kódu, který ukazuje, jak načíst stránkovaný seznam odchozích vztahů z digitálních vláken Azure:

```csharp
 try 
 {
     // List the relationships.
    AsyncPageable<BasicRelationship> results = client.GetRelationshipsAsync<BasicRelationship>(srcId);
    Console.WriteLine($"Twin {srcId} is connected to:");
    // Iterate through the relationships found.
    int numberOfRelationships = 0;
    await foreach (string rel in results)
    {
         ++numberOfRelationships;
         // Do something with each relationship found
         Console.WriteLine($"Found relationship-{rel.Name}->{rel.TargetId}");
    }
    Console.WriteLine($"Found {numberOfRelationships} relationships on {srcId}");
} catch (RequestFailedException rex) {
    Console.WriteLine($"Relationship retrieval error: {rex.Status}:{rex.Message}");   
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
* [*Kurz: vytvoření kódu klientské aplikace*](tutorial-code.md)
