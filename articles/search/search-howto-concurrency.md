---
title: Správa souběžných zápisů do prostředků
titleSuffix: Azure Cognitive Search
description: Pomocí optimistické souběžnosti se vyhnete kolizím ve vzduchu při aktualizacích nebo odstranění indexů Azure Cognitive Search, indexerů, zdrojů dat.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: edfb2fe5cc37a00335ca7b5be851a88825b03eb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792217"
---
# <a name="how-to-manage-concurrency-in-azure-cognitive-search"></a>Jak spravovat souběžnost v Azure Cognitive Search

Při správě prostředků Azure Cognitive Search, jako jsou indexy a zdroje dat, je důležité aktualizovat prostředky bezpečně, zejména pokud prostředky jsou přístupné současně různými součástmi vaší aplikace. Pokud dva klienti současně aktualizují prostředek bez koordinace, jsou možné podmínky časování. Chcete-li tomu zabránit, Azure Cognitive Search nabízí *optimistický model souběžnosti*. Neexistují žádné zámky na prostředek. Místo toho je eTag pro každý prostředek, který identifikuje verzi prostředku, takže můžete řemesla požadavky, které se vyhýbají náhodné přepsání.

> [!Tip]
> Koncepční kód v [ukázkovém řešení Jazyka C#](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer) vysvětluje, jak funguje řízení souběžnosti v Azure Cognitive Search. Kód vytvoří podmínky, které vyvolávají řízení souběžnosti. Čtení [fragmentu kódu níže](#samplecode) je pravděpodobně dostačující pro většinu vývojářů, ale pokud chcete spustit, upravte appsettings.json přidat název služby a admin api-key. Vzhledem k `http://myservice.search.windows.net`tomu, adresa `myservice`URL služby , název služby je .

## <a name="how-it-works"></a>Jak to funguje

Optimistická souběžnost je implementována prostřednictvím kontroly podmínek přístupu ve volání rozhraní API, které zapisují indexy, indexery, zdroje dat a synonymaMap.

Všechny prostředky mají [*značku entity (ETag),*](https://en.wikipedia.org/wiki/HTTP_ETag) která poskytuje informace o verzi objektu. Kontrolou eTag první, můžete se vyhnout souběžné aktualizace v typické workflow (získat, upravit místně, aktualizovat) tím, že zajistí prostředek ETag odpovídá místní kopii.

+ Rozhraní REST API používá [etag](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) v hlavičce požadavku.
+ Sada .NET SDK nastaví eTag prostřednictvím accessCondition objektu, nastavení [If-Match | If-Match-None záhlaví](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) na prostředek. Libovolný objekt dědící z [IResourceWithETag (.NET SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.iresourcewithetag) má accessCondition objekt.

Pokaždé, když aktualizujete prostředek, jeho ETag se automaticky změní. Při implementaci správy souběžnosti, vše, co děláte, je uvedení předběžnou podmínku na požadavek na aktualizaci, která vyžaduje, aby vzdálený prostředek mít stejné eTag jako kopie prostředku, který jste upravili na straně klienta. Pokud souběžný proces již změnil vzdálený prostředek, eTag nebude odpovídat předpokladu a požadavek se nezdaří s HTTP 412. Pokud používáte .NET SDK, to manifesty `CloudException` jako `IsAccessConditionFailed()` kde metoda rozšíření vrátí true.

> [!Note]
> Existuje pouze jeden mechanismus pro souběžnost. Vždy se používá bez ohledu na to, které rozhraní API se používá pro aktualizace prostředků.

<a name="samplecode"></a>
## <a name="use-cases-and-sample-code"></a>Případy použití a ukázkový kód

Následující kód ukazuje kontroly accessCondition pro operace aktualizace klíče:

+ Pokud prostředek již neexistuje, nepodaří se mu aktualizace.
+ Selhání aktualizace, pokud se změní verze prostředku

### <a name="sample-code-from-dotnetetagsexplainer-program"></a>Ukázkový kód z [programu DotNetETagsExplainer](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer)

```
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

## <a name="design-pattern"></a>Návrhový vzor

Návrhový vzor pro implementaci optimistické souběžnosti by měl obsahovat smyčku, která opakuje kontrolu podmínky přístupu, test podmínky přístupu a volitelně načte aktualizovaný prostředek před pokusem o opětovné použití změn.

Tento fragment kódu ilustruje přidání synonymMap do indexu, který již existuje. Tento kód je z [příkladu synonyma C# pro Azure Cognitive Search](search-synonyms-tutorial-sdk.md).

Výstřižek získá index "hotels", zkontroluje verzi objektu při operaci aktualizace, vyvolá výjimku, pokud se podmínka nezdaří, a pak opakuje operaci (až třikrát) počínaje načítáním indexu ze serveru, aby získal nejnovější verzi.

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


## <a name="next-steps"></a>Další kroky

Zkontrolujte [synonyma C# ukázka](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms) pro další kontext o tom, jak bezpečně aktualizovat existující index.

Zkuste upravit některý z následujících ukázek zahrnout ETags nebo AccessCondition objekty.

+ [Ukázka rozhraní REST API na GitHubu](https://github.com/Azure-Samples/search-rest-api-getting-started)
+ [Ukázka sady .NET SDK na GitHubu](https://github.com/Azure-Samples/search-dotnet-getting-started). Toto řešení zahrnuje projekt "DotNetEtagsExplainer", který obsahuje kód uvedený v tomto článku.

## <a name="see-also"></a>Viz také

[Běžné http požadavky a hlavičky](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search)
odpovědí[HTTP stavové kódy](https://docs.microsoft.com/rest/api/searchservice/http-status-codes)
[Index operace (REST API)](https://docs.microsoft.com/rest/api/searchservice/index-operations)
