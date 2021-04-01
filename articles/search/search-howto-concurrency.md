---
title: Jak spravovat souběžné zápisy do prostředků
titleSuffix: Azure Cognitive Search
description: Použijte optimistickou souběžnost, abyste zabránili kolizím středního proudění při aktualizacích nebo odstraňování do Azure Kognitivní hledání indexů, indexerů a datových zdrojů.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/26/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 52260facc1a458a3ef18b067e6d52e189f5d329c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98881750"
---
# <a name="how-to-manage-concurrency-in-azure-cognitive-search"></a>Jak spravovat souběžnost v Azure Kognitivní hledání

Při správě prostředků Azure Kognitivní hledání, jako jsou indexy a zdroje dat, je důležité aktualizovat prostředky bezpečně, zejména v případě, že se k prostředkům přistupoval současně pomocí různých komponent aplikace. Když dva klienti souběžně aktualizují prostředek bez koordinace, jsou možné situace časování. Aby k tomu nedocházelo, Azure Kognitivní hledání nabízí *optimistický model souběžnosti*. U prostředku neexistují žádné zámky. Místo toho je k dispozici značka ETag pro každý prostředek, který identifikuje verzi prostředku, abyste mohli formulovat žádosti, které zabraňují náhodnému přepsání.

> [!Tip]
> Koncepční kód v [ukázkovém řešení jazyka C#](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer) vysvětluje, jak řízení souběžnosti funguje v Azure kognitivní hledání. Kód vytvoří podmínky, které vyvolávají řízení souběžnosti. Čtení [fragmentu kódu níže](#samplecode) může být pro většinu vývojářů dostačující, ale pokud ho chcete spustit, upravte appsettings.jsna a přidejte název služby a klíč rozhraní API pro správu. Název služby je dán adresou URL služby `http://myservice.search.windows.net` `myservice` .

## <a name="how-it-works"></a>Jak to funguje

Optimistická souběžnost je implementována prostřednictvím kontrol podmínek přístupu v volání rozhraní API při psaní do indexů, indexerů, zdrojů dat, dovednosti a prostředků synonymMap.

Všechny prostředky mají [*značku entity (ETag)*](https://en.wikipedia.org/wiki/HTTP_ETag) , která poskytuje informace o verzi objektu. Zaškrtnutím ETag First (získat, upravit místně, aktualizovat) se můžete vyhnout souběžným aktualizacím, a to tak, že zaručíte, že značka ETag prostředku odpovídá vaší místní kopii.

+ REST API používá [ETag](/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) v hlavičce požadavku.

+ Sada .NET SDK nastaví značku ETag prostřednictvím objektu accessCondition, nastavení [If-Match | Záhlaví If-Match-None](/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) u prostředku Objekty, které používají ETag, například [SynonymMap. ETag](/dotnet/api/azure.search.documents.indexes.models.synonymmap.etag) a [SearchIndex. ETag](/dotnet/api/azure.search.documents.indexes.models.searchindex.etag), mají objekt accessCondition.

Pokaždé, když aktualizujete prostředek, jeho značka ETag se automaticky změní. Když implementujete řízení souběžnosti, vše, co provádíte, je předběžnou podmínkou pro žádost o aktualizaci, která vyžaduje, aby vzdálený prostředek měl stejnou značku ETag jako kopie prostředku, který jste změnili v klientovi. Pokud již souběžný proces změnil vzdálený prostředek, značka ETag se neshoduje s podmínkou a požadavek selže s protokolem HTTP 412. Pokud používáte sadu .NET SDK, tento manifest jako `CloudException` `IsAccessConditionFailed()` metoda rozšíření vrátí hodnotu true.

> [!Note]
> Existuje pouze jeden mechanismus pro souběžnost. Vždycky se používá bez ohledu na to, jaké rozhraní API se používá pro aktualizace prostředků.

<a name="samplecode"></a>
## <a name="use-cases-and-sample-code"></a>Případy použití a ukázka kódu

Následující kód ukazuje accessCondition kontroly operací aktualizace klíčů:

+ Neúspěšná aktualizace, pokud prostředek už neexistuje
+ Neúspěšná aktualizace, pokud se změní verze prostředku

### <a name="sample-code-from-dotnetetagsexplainer-program"></a>Ukázkový kód z [DotNetETagsExplainer programu](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer)

```csharp
class Program
{
    // This sample shows how ETags work by performing conditional updates and deletes
    // on an Azure Cognitive Search index.
    static void Main(string[] args)
    {
        IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
        IConfigurationRoot configuration = builder.Build();

        SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

        Console.WriteLine("Deleting index...\n");
        DeleteTestIndexIfExists(serviceClient);

        // Every top-level resource in Azure Cognitive Search has an associated ETag that keeps track of which version
        // of the resource you're working on. When you first create a resource such as an index, its ETag is
        // empty.
        Index index = DefineTestIndex();
        Console.WriteLine(
            $"Test index hasn't been created yet, so its ETag should be blank. ETag: '{index.ETag}'");

        // Once the resource exists in Azure Cognitive Search, its ETag will be populated. Make sure to use the object
        // returned by the SearchServiceClient! Otherwise, you will still have the old object with the
        // blank ETag.
        Console.WriteLine("Creating index...\n");
        index = serviceClient.Indexes.Create(index);
        
        Console.WriteLine($"Test index created; Its ETag should be populated. ETag: '{index.ETag}'");

        // ETags let you do some useful things you couldn't do otherwise. For example, by using an If-Match
        // condition, we can update an index using CreateOrUpdate and be guaranteed that the update will only
        // succeed if the index already exists.
        index.Fields.Add(new Field("name", AnalyzerName.EnMicrosoft));
        index =
            serviceClient.Indexes.CreateOrUpdate(
                index,
                accessCondition: AccessCondition.GenerateIfExistsCondition());

        Console.WriteLine(
            $"Test index updated; Its ETag should have changed since it was created. ETag: '{index.ETag}'");

        // More importantly, ETags protect you from concurrent updates to the same resource. If another
        // client tries to update the resource, it will fail as long as all clients are using the right
        // access conditions.
        Index indexForClient1 = index;
        Index indexForClient2 = serviceClient.Indexes.Get("test");

        Console.WriteLine("Simulating concurrent update. To start, both clients see the same ETag.");
        Console.WriteLine($"Client 1 ETag: '{indexForClient1.ETag}' Client 2 ETag: '{indexForClient2.ETag}'");

        // Client 1 successfully updates the index.
        indexForClient1.Fields.Add(new Field("a", DataType.Int32));
        indexForClient1 =
            serviceClient.Indexes.CreateOrUpdate(
                indexForClient1,
                accessCondition: AccessCondition.IfNotChanged(indexForClient1));

        Console.WriteLine($"Test index updated by client 1; ETag: '{indexForClient1.ETag}'");

        // Client 2 tries to update the index, but fails, thanks to the ETag check.
        try
        {
            indexForClient2.Fields.Add(new Field("b", DataType.Boolean));
            serviceClient.Indexes.CreateOrUpdate(
                indexForClient2,
                accessCondition: AccessCondition.IfNotChanged(indexForClient2));

            Console.WriteLine("Whoops; This shouldn't happen");
            Environment.Exit(1);
        }
        catch (CloudException e) when (e.IsAccessConditionFailed())
        {
            Console.WriteLine("Client 2 failed to update the index, as expected.");
        }

        // You can also use access conditions with Delete operations. For example, you can implement an
        // atomic version of the DeleteTestIndexIfExists method from this sample like this:
        Console.WriteLine("Deleting index...\n");
        serviceClient.Indexes.Delete("test", accessCondition: AccessCondition.GenerateIfExistsCondition());

        // This is slightly better than using the Exists method since it makes only one round trip to
        // Azure Cognitive Search instead of potentially two. It also avoids an extra Delete request in cases where
        // the resource is deleted concurrently, but this doesn't matter much since resource deletion in
        // Azure Cognitive Search is idempotent.

        // And we're done! Bye!
        Console.WriteLine("Complete.  Press any key to end application...\n");
        Console.ReadKey();
    }

    private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
    {
        string searchServiceName = configuration["SearchServiceName"];
        string adminApiKey = configuration["SearchServiceAdminApiKey"];

        SearchServiceClient serviceClient =
            new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
        return serviceClient;
    }

    private static void DeleteTestIndexIfExists(SearchServiceClient serviceClient)
    {
        if (serviceClient.Indexes.Exists("test"))
        {
            serviceClient.Indexes.Delete("test");
        }
    }

    private static Index DefineTestIndex() =>
        new Index()
        {
            Name = "test",
            Fields = new[] { new Field("id", DataType.String) { IsKey = true } }
        };
    }
}
```

## <a name="design-pattern"></a>Vzor návrhu

Vzor návrhu pro implementaci optimistického řízení souběžnosti by měl zahrnovat smyčku, která opakuje kontrolu podmínky přístupu, otestuje podmínku přístupu a volitelně načte aktualizovaný prostředek před pokusem o opětovné použití změn.

Tento fragment kódu ilustruje přidání synonymMap k indexu, který již existuje. Tento kód pochází z [příkladu synonym jazyka C# pro Azure kognitivní hledání](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/v10/DotNetHowToSynonyms).

Fragment Načte index "hotely", zkontroluje verzi objektu u operace Update, vyvolá výjimku, pokud se podmínka nezdařila, a poté opakuje operaci (až třikrát), počínaje načítáním indexu ze serveru za účelem získání nejnovější verze.

```csharp
private static void EnableSynonymsInHotelsIndexSafely(SearchServiceClient serviceClient)
{
    int MaxNumTries = 3;

    for (int i = 0; i < MaxNumTries; ++i)
    {
        try
        {
            Index index = serviceClient.Indexes.Get("hotels");
            index = AddSynonymMapsToFields(index);

            // The IfNotChanged condition ensures that the index is updated only if the ETags match.
            serviceClient.Indexes.CreateOrUpdate(index, accessCondition: AccessCondition.IfNotChanged(index));

            Console.WriteLine("Updated the index successfully.\n");
            break;
        }
        catch (CloudException e) when (e.IsAccessConditionFailed())
        {
            Console.WriteLine($"Index update failed : {e.Message}. Attempt({i}/{MaxNumTries}).\n");
        }
    }
}

private static Index AddSynonymMapsToFields(Index index)
{
    index.Fields.First(f => f.Name == "category").SynonymMaps = new[] { "desc-synonymmap" };
    index.Fields.First(f => f.Name == "tags").SynonymMaps = new[] { "desc-synonymmap" };
    return index;
}
```

## <a name="next-steps"></a>Další kroky

Zkuste upravit další vzorky, abyste mohli uplatnit značky ETag nebo AccessCondition objekty.

+ [Search-dotnet-Začínáme na GitHubu](https://github.com/Azure-Samples/search-dotnet-getting-started). Toto úložiště obsahuje projekt "DotNetEtagsExplainer".

+ [Azure-Search-dotnet-ukázky na GitHubu](https://github.com/Azure-Samples/azure-search-dotnet-samples) obsahují další ukázky C#.

## <a name="see-also"></a>Viz také

+ [Běžné hlavičky požadavků a odpovědí protokolu HTTP](/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search)
+ [Stavové kódy HTTP](/rest/api/searchservice/http-status-codes)
+ [Operace s indexem (REST API)](/rest/api/searchservice/index-operations)