---
title: Jak spravovat souběžných zapíše k prostředkům ve službě Azure Search
description: Zabrání se tím kolizím střední letecké na aktualizace nebo odstranění indexů Azure Search, indexery, zdroje dat pomocí optimistickou metodu souběžného.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/21/2017
ms.author: heidist
ms.openlocfilehash: f5fa495c1266c847cabc0eb4e35b85132550bc3c
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="how-to-manage-concurrency-in-azure-search"></a>Jak spravovat souběžnosti ve službě Azure Search

Při správě prostředků Azure Search, jako jsou indexy a zdroje dat, je důležité aktualizace zdroje, bezpečně, zejména v případě, že prostředkům přistupuje souběžně různých komponent vaší aplikace. Když se dvě klientů současně aktualizace prostředku bez spolupráce, je možné konflikty časování. Chcete-li tomu zabránit, Azure Search nabízí *optimistickou metodu souběžného modelu*. Neexistují žádné zámky na prostředek. Místo toho není značku ETag pro každý prostředek, který identifikuje prostředek verze tak, aby může vytvořit požadavků, které vyhnout náhodných přepíše.

> [!Tip]
> Koncepční kód [ukázkové C# řešení](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer) vysvětluje, jak funguje řízení souběžnosti ve službě Azure Search. Kód vytvoří podmínky, které vyvolají Kontrola souběžnosti. Čtení [fragment kódu níže](#samplecode) je pravděpodobně dostatečná Většina vývojářů, ale pokud chcete spustit, upravit appSettings.JSON určený k přidání názvu služby a rozhraní api – klíč správce. Danou adresu URL služby `http://myservice.search.windows.net`, název služby je `myservice`.

## <a name="how-it-works"></a>Jak to funguje

Optimistické, že je implementováno souběžnosti prostřednictvím přístupu podmínka kontroluje při voláních rozhraní API zápisu do indexy, indexery, zdrojů dat a synonymMap prostředky. 

Všechny prostředky mít [ *entity tag (značka ETag)* ](https://en.wikipedia.org/wiki/HTTP_ETag) poskytující informace o verzi objektu. Kontrolou nejprve značku ETag, se můžete vyhnout Souběžná aktualizace v obvyklý pracovní postup (získat, upravte místně, aktualizujte) zajištěním prostředku značka ETag odpovídá vaší místní kopie. 

+ Používá rozhraní API REST [značka ETag](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) v hlavičce žádosti.
+ .NET SDK nastaví značku ETag prostřednictvím objektu accessCondition, nastavení [If-Match | Záhlaví If-Match-žádný](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) u daného prostředku. Libovolného objektu, která dědí z [IResourceWithETag (.NET SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.iresourcewithetag) má accessCondition objekt.

Při každé aktualizaci prostředek automaticky změní jeho značka ETag. Při implementaci správy souběžnosti všechny, kdy provádíte je uvedení předběžné podmínky na žádost o aktualizaci, která vyžaduje vzdálený prostředek tak, aby měl stejné ETag jako kopii prostředku, který změnil na straně klienta. Pokud již souběžných procesů změnila vzdálený prostředek, značku ETag nebude odpovídat předběžnou podmínku a požadavek selže s HTTP 412. Pokud používáte sadu .NET SDK, manifesty jako `CloudException` kde `IsAccessConditionFailed()` rozšíření metoda vrátí hodnotu true.

> [!Note]
> Existuje pouze jeden mechanismus pro souběžnosti. Používá se vždy, bez ohledu na to, který se používá rozhraní API pro aktualizace prostředků. 

<a name="samplecode"></a>
## <a name="use-cases-and-sample-code"></a>Případy použití a ukázkový kód

Následující kód ukazuje, že accessCondition vyhledává operace klíče aktualizace:

+ Aktualizace nezdaří, pokud prostředek už existuje.
+ Aktualizace nezdaří, pokud se změní verze

### <a name="sample-code-from-dotnetetagsexplainer-programhttpsgithubcomazure-samplessearch-dotnet-getting-startedtreemasterdotnetetagsexplainer"></a>Ukázkový kód z [DotNetETagsExplainer programu](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer)

```
    class Program
    {
        // This sample shows how ETags work by performing conditional updates and deletes
        // on an Azure Search index.
        static void Main(string[] args)
        {
            IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
            IConfigurationRoot configuration = builder.Build();

            SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

            Console.WriteLine("Deleting index...\n");
            DeleteTestIndexIfExists(serviceClient);

            // Every top-level resource in Azure Search has an associated ETag that keeps track of which version
            // of the resource you're working on. When you first create a resource such as an index, its ETag is
            // empty.
            Index index = DefineTestIndex();
            Console.WriteLine(
                $"Test index hasn't been created yet, so its ETag should be blank. ETag: '{index.ETag}'");

            // Once the resource exists in Azure Search, its ETag will be populated. Make sure to use the object
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
            // Azure Search instead of potentially two. It also avoids an extra Delete request in cases where
            // the resource is deleted concurrently, but this doesn't matter much since resource deletion in
            // Azure Search is idempotent.

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

Vzoru návrhu pro implementaci optimistickou metodu souběžného zpracování by měl obsahovat smyčku opakování podmínka přístupu zkontrolujte testu pro podmínku přístup a volitelně načte aktualizovaný prostředek dřív, než se znovu použít změny. 

Tento fragment kódu ukazuje přidání synonymMap na index, který již existuje. Tento kód je z [synonymum (preview) C# – tutoriál pro službu Azure Search](https://docs.microsoft.com/azure/search/search-synonyms-tutorial-sdk). 

Fragment získá index "hotels", zkontroluje verzi objektu na operaci aktualizace, vyvolá výjimku, pokud se nezdaří podmínku a poté operaci zopakují (maximálně třikrát), od indexu načtení ze serveru získat nejnovější verzi.

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


## <a name="next-steps"></a>Další postup

Zkontrolujte [synonyma C# ukázka](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms) pro další kontext na tom, jak bezpečně aktualizovat existující index.

Zkuste upravit některou z následujících ukázek o značky etag binárním rozsáhlým nebo AccessCondition objekty.

+ [Rozhraní REST API ukázce na Githubu](https://github.com/Azure-Samples/search-rest-api-getting-started) 
+ [Ukázka sady .NET SDK na Githubu](https://github.com/Azure-Samples/search-dotnet-getting-started). Toto řešení zahrnuje "DotNetEtagsExplainer" projekt obsahující kód uvedený v tomto článku.

## <a name="see-also"></a>Další informace najdete v tématech

  [Společné hlavičky požadavku a odpovědi HTTP](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search)    
  [Stavové kódy HTTP](https://docs.microsoft.com/rest/api/searchservice/http-status-codes) [indexu operací (REST API)](https://docs.microsoft.com/\rest/api/searchservice/index-operations)