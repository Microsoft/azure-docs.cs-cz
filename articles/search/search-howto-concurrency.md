---
title: Jak spravovat souběžných zápisů do prostředků ve službě Azure Search
description: Pro zabránění kolizím uprostřed air na aktualizace nebo odstranění indexů Azure Search, indexery a zdroje dat použijte optimistické řízení souběžnosti.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/21/2017
ms.author: heidist
ms.openlocfilehash: 764c39b6619f42c5b765d53af4cbfbe43f1d8799
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52967493"
---
# <a name="how-to-manage-concurrency-in-azure-search"></a>Správa souběžnosti ve službě Azure Search

Při správě prostředků Azure Search, jako jsou indexy a zdroje dat, je potřeba aktualizovat prostředky bez obav, zejména v případě, že prostředky jsou současně přistupuje různých komponent vaší aplikace. Když dva klienti souběžně aktualizace prostředku bez koordinace, je možné ke konfliktům časování. Chcete-li tomu zabránit, nabízí Azure Search *optimistického řízení souběžnosti modelu*. Nejsou žádné zámky prostředku. Místo toho je značka ETag pro každý prostředek, který identifikuje verzi prostředků, takže můžete vytvářet žádosti, které se zabránilo nechtěnému přepíše.

> [!Tip]
> Koncepční kód [ukázka C# řešení](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer) vysvětluje, jak funguje řízení souběžnosti ve službě Azure Search. Kód vytvoří podmínky, které vyvolají řízení souběžnosti. Čtení [fragment kódu níže](#samplecode) je pravděpodobně dostatečné pro většinu vývojářů, ale pokud chcete spustit, upravit appsettings.json přidáte název služby a api-key správce. Zadané adresy URL služby z `http://myservice.search.windows.net`, název služby je `myservice`.

## <a name="how-it-works"></a>Jak to funguje

Optimistická souběžnost je implementováno prostřednictvím přístupu podmínka zkontroluje v zápisu na indexy, indexery, zdroje dat a prostředků synonymMap volání rozhraní API. 

Všechny prostředky měly [ *značka entity (značka ETag)* ](https://en.wikipedia.org/wiki/HTTP_ETag) , který poskytuje informace o verzi objektu. Značka ETag nejdřív zkontrolovat, se můžete vyhnout souběžných aktualizací v obvyklém pracovním postupu (získat, upravovat místně, aktualizujte) tím, že zajišťuje prostředku značka ETag odpovídá místní kopii. 

+ Používá rozhraní REST API [ETag](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) v hlavičce požadavku.
+ Sady .NET SDK nastaví značku ETag prostřednictvím objektu, který accessCondition, nastavení [If-Match | Záhlaví If-Match-žádný](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) u daného prostředku. Libovolný objekt odvozený od [IResourceWithETag (sadu .NET SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.iresourcewithetag) má accessCondition objektu.

Pokaždé, když aktualizujete zdroj, automaticky změní jeho značky ETag. Při implementaci řízení souběžnosti všechno, co děláte nastavuje předběžné podmínky na žádost o aktualizaci, která vyžaduje vzdálený prostředek, který chcete mít stejnou značku ETag jako kopie prostředku, kterou jste změnili na straně klienta. Pokud souběžných procesů došlo ke změně vzdálený prostředek již, značka ETag nebudou odpovídat předpoklad a požadavek selže s HTTP 412. Pokud při použití sady .NET SDK, manifesty jako `CloudException` kde `IsAccessConditionFailed()` rozšiřující metoda vrátí hodnotu true.

> [!Note]
> Existuje pouze jeden mechanismus pro souběžnost. Používá se vždy, bez ohledu na to, který se používá rozhraní API pro aktualizace prostředků. 

<a name="samplecode"></a>
## <a name="use-cases-and-sample-code"></a>Případy použití a ukázky kódu

Následující kód ukazuje, že kontroluje accessCondition operace aktualizace klíče:

+ Aktualizace nezdaří, pokud už existuje prostředek
+ Aktualizace nezdaří, pokud se změní verze prostředků

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

## <a name="design-pattern"></a>vzor návrhu

Návrhový vzor pro implementace optimistického řízení souběžnosti by měl obsahovat smyčku, která se pokusí znovu podmínka přístupu zkontrolujte testovací podmínku přístup a volitelně načte aktualizované prostředků než se pokusíte znovu použít změny. 

Tento fragment kódu ukazuje přidání synonymMap na index, který již existuje. Tento kód je z [Synonym (preview) C# kurz pro Azure Search](https://docs.microsoft.com/azure/search/search-synonyms-tutorial-sdk). 

Fragment kódu získá index "hotels", verze objektu na operaci aktualizace zkontroluje, vyvolá výjimku, pokud podmínka selže a potom opakovat operaci (až třikrát), počínaje indexem načtení ze serveru získat nejnovější verzi.

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

Zkontrolujte [synonyma C# ukázka](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms) pro podrobnější přehled o tom, jak bezpečně aktualizovat existující index.

Zkuste upravit některou z následujících ukázek zahrnout značek ETag nebo AccessCondition objektů.

+ [Rozhraní REST API ukázka na Githubu](https://github.com/Azure-Samples/search-rest-api-getting-started) 
+ [Ukázka sady .NET SDK na Githubu](https://github.com/Azure-Samples/search-dotnet-getting-started). Toto řešení zahrnuje "DotNetEtagsExplainer" projekt, který obsahuje kód uvedený v tomto článku.

## <a name="see-also"></a>Další informace najdete v tématech

  [Společné hlavičky HTTP požadavku a odpovědi](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search)    
  [Stavové kódy HTTP](https://docs.microsoft.com/rest/api/searchservice/http-status-codes) [indexu (REST API) operations](https://docs.microsoft.com/rest/api/searchservice/index-operations)