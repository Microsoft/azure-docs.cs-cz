---
title: Provést výběr správné klíč rozdělení do oddílů s více oblast zápisů a čtení | Microsoft Docs
description: Další informace o návrhu architektury aplikací s místní čtení a zápisu v různých geografických oblastech s Azure Cosmos DB výběrem klíč oddílu.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: conceptual
ms.date: 06/6/2018
ms.author: sngun
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 18f036a259bbec98382927ad1d9e8f654b56850b
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850357"
---
# <a name="perform-multi-region-writes-and-reads-by-choosing-the-right-partitioning-key"></a>Provádění více oblast zápisů a čtení tak, že zvolíte právo klíč rozdělení do oddílů

Podporuje Azure Cosmos DB připraveného [globální replikace](distribute-data-globally.md), která umožňuje distribuci dat do několika oblastí přístup s nízkou latencí kdekoli v zatížení. Tento model se často používá pro vydavatele nebo příjemce zatížení tam, kde je zapisovač v jedné zeměpisné oblasti a globálně distribuované čtečky v jiných oblastech (čtení). 

Podpora globální replikace databáze Cosmos Azure můžete taky vytvářet aplikace, ve kterých jsou globálně distribuované zapisovače a čtečky. Tento dokument popisuje vzor, který umožňuje dosažení místní zápisu a čtení místní pro distribuované zapisovače pomocí Azure Cosmos DB.

## <a id="ExampleScenario"></a>Publikování obsahu – příklad scénáře
Podívejme se na scénář skutečných popisují, jak můžete používat vzory globálně distribuované více region nebo více master čtení zápisu s Azure Cosmos DB. Vezměte v úvahu vytvořené v Azure Cosmos DB platforma pro publikování obsahu. Tady jsou některé požadavky, které tuto platformu musí splňovat pro vysoký výkon uživatele pro vydavatele a spotřebitelé.

* Autoři a Odběratelé, kteří jsou rozloženy na světě. 
* Autoři články (zápisu) musíte publikovat své místní oblast (nejbližší)
* Autoři mají čtečky nebo odběratele, jejich článků kteří distribuují po celém světě. 
* Odběratelé, kteří měli obdržet oznámení, při publikování nových článků.
* Odběratelé, kteří musí být možné číst články ze své místní oblast. Musí být také možné přidat recenze na tyto články. 
* Každý, kdo včetně autora články musí být možné zobrazit všechny recenze připojená k články z místní oblast. 

Za předpokladu, že miliony s až miliardy článků, vydavatelům a spotřebitelům brzy máme boji s problémy měřítka společně s zaručující polohu přístupu. Stejně jako u většiny problémů škálovatelnost řešení spočívá ve strategii je dobré rozdělení. V dalším kroku podíváme, jak model články, zkontrolujte a oznámení jako dokumenty, nakonfigurovat účty Azure Cosmos DB a implementovat vrstva přístupu k datům. 

Pokud vás zajímají další informace o vytváření oddílů a klíče oddílů, najdete v části [vytváření oddílů a škálování v Azure Cosmos DB](partition-data.md).

## <a id="ModelingNotifications"></a>Modelování oznámení
Oznámení jsou datových kanálů specifické pro uživatele. Proto přístupové vzorce pro dokumenty oznámení jsou vždy v rámci jednoho uživatele. Například by "post oznámení pro uživatele" nebo "načíst všechna oznámení pro daného uživatele". Ano, bude optimální volbou oddíly klíč pro tento typ `UserId`.

    class Notification 
    { 
        // Unique ID for Notification. 
        public string Id { get; set; }

        // The user Id for which notification is addressed to. 
        public string UserId { get; set; }

        // The partition Key for the resource. 
        public string PartitionKey 
        { 
            get 
            { 
                return this.UserId; 
            }
        }

        // Subscription for which this notification is raised. 
        public string SubscriptionFilter { get; set; }

        // Subject of the notification. 
        public string ArticleId { get; set; } 
    }

## <a id="ModelingSubscriptions"></a>Odběry modelování
Odběry můžete vytvořit pro různé kritéria jako určitou kategorii články zájmu nebo konkrétní vydavatele. Proto `SubscriptionFilter` je vhodná pro klíč oddílu.

    class Subscriptions 
    { 
        // Unique ID for Subscription 
        public string Id { get; set; }

        // Subscription source. Could be Author | Category etc. 
        public string SubscriptionFilter { get; set; }

        // subscribing User. 
        public string UserId { get; set; }

        public string PartitionKey 
        { 
            get 
            { 
                return this.SubscriptionFilter; 
            } 
        } 
    }

## <a id="ModelingArticles"></a>Články modelování
Jakmile článek identifikuje prostřednictvím oznámení, další dotazy jsou obvykle založené na `Article.Id`. Výběr `Article.Id` jako oddíl klíč tak poskytuje nejlepší distribuce pro ukládání články v kolekci Azure Cosmos DB. 

    class Article 
    { 
        // Unique ID for Article 
        public string Id { get; set; }
        
        public string PartitionKey 
        { 
            get 
            { 
                return this.Id; 
            } 
        }
        
        // Author of the article
        public string Author { get; set; }

        // Category/genre of the article
        public string Category { get; set; }

        // Tags associated with the article
        public string[] Tags { get; set; }

        // Title of the article
        public string Title { get; set; }
        
        //... 
    }

## <a id="ModelingReviews"></a>Zkontroluje modelování
Jako články jsou recenze většinou zapisovat a číst v kontextu článku. Výběr `ArticleId` jako oddíl klíč poskytuje nejlepší distribuce a efektivní přístup recenze přidružené článku. 

    class Review 
    { 
        // Unique ID for Review 
        public string Id { get; set; }

        // Article Id of the review 
        public string ArticleId { get; set; }

        public string PartitionKey 
        { 
            get 
            { 
                return this.ArticleId; 
            } 
        }
        
        //Reviewer Id 
        public string UserId { get; set; }
        public string ReviewText { get; set; }
        
        public int Rating { get; set; } }
    }

## <a id="DataAccessMethods"></a>Metody pro přístup k vrstvě
Nyní Podíváme se na hlavní data musíme implementovat metody přístupu. Tady je seznam metod, `ContentPublishDatabase` musí:

    class ContentPublishDatabase 
    { 
        public async Task CreateSubscriptionAsync(string userId, string category);
    
        public async Task<IEnumerable<Notification>> ReadNotificationFeedAsync(string userId);
    
        public async Task<Article> ReadArticleAsync(string articleId);
    
        public async Task WriteReviewAsync(string articleId, string userId, string reviewText, int rating);
    
        public async Task<IEnumerable<Review>> ReadReviewsAsync(string articleId); 
    }

## <a id="Architecture"></a>Konfigurace účtu Azure Cosmos DB
Zaručit místní čte a zapisuje, jsme musí oddílu data nejen v oddílu klíče, ale také podle vzoru zeměpisné přístup do oblasti. Model spoléhá na nutnosti geograficky replikované Azure Cosmos DB databázového účtu pro každou oblast. Například se dvěma oblastmi, zde je instalace s pro zápisy více oblasti:

| Název účtu | Oblast zápisu | Oblast čtení |
| --- | --- | --- |
| `contentpubdatabase-usa.documents.azure.com` | `West US` |`North Europe` |
| `contentpubdatabase-europe.documents.azure.com` | `North Europe` |`West US` |

Následující diagram znázorňuje, jak provádět čtení a zápisu v typické aplikaci s tímto nastavením:

![Azure Cosmos DB více hlavních architektura](./media/multi-master-workaround/multi-master.png)

Zde je fragment kódu znázorňující k chybě při inicializaci klienty v DAL, spuštěné v `West US` oblast.
    
    ConnectionPolicy writeClientPolicy = new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp };
    writeClientPolicy.PreferredLocations.Add(LocationNames.WestUS);
    writeClientPolicy.PreferredLocations.Add(LocationNames.NorthEurope);

    DocumentClient writeClient = new DocumentClient(
        new Uri("https://contentpubdatabase-usa.documents.azure.com"), 
        writeRegionAuthKey,
        writeClientPolicy);

    ConnectionPolicy readClientPolicy = new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp };
    readClientPolicy.PreferredLocations.Add(LocationNames.NorthEurope);
    readClientPolicy.PreferredLocations.Add(LocationNames.WestUS);

    DocumentClient readClient = new DocumentClient(
        new Uri("https://contentpubdatabase-europe.documents.azure.com"),
        readRegionAuthKey,
        readClientPolicy);

V předchozí instalaci může předat vrstva přístupu k datům všech zápisů místní účet, podle které se nasadí. Čtení ze oba účty, a získat globální zobrazení dat provádí čtení. Tuto metodu lze rozšířit na jako v mnoha oblastech podle potřeby. Zde je ukázka, instalační program s tři zeměpisné oblasti:

| Název účtu | Oblast zápisu | Oblast pro čtení 1 | Přečtěte si oblasti 2 |
| --- | --- | --- | --- |
| `contentpubdatabase-usa.documents.azure.com` | `West US` |`North Europe` |`Southeast Asia` |
| `contentpubdatabase-europe.documents.azure.com` | `North Europe` |`West US` |`Southeast Asia` |
| `contentpubdatabase-asia.documents.azure.com` | `Southeast Asia` |`North Europe` |`West US` |

## <a id="DataAccessImplementation"></a>Data access layer implementace
Nyní Podíváme se na provádění vrstva přístupu k datům (DAL) pro aplikaci se dvěma oblastmi s možností zápisu. DAL musí implementovat následující kroky:

* Vytvoření více instancí `DocumentClient` pro jednotlivé účty. Se dvěma oblastmi jeden má každá instance vrstvy DAL `writeClient` a jeden `readClient`. 
* Podle oblasti nasazené aplikace, konfigurace koncových bodů pro `writeclient` a `readClient`. Například DAL nasazené v `West US` používá `contentpubdatabase-usa.documents.azure.com` pro provádění zápisy. DAL nasazené v `NorthEurope` používá `contentpubdatabase-europ.documents.azure.com` pro zápis.

V předchozí instalaci se dá implementovat datové metody přístupu. Zápis předávat operace zápisu do odpovídajících `writeClient`.

    public async Task CreateSubscriptionAsync(string userId, string category)
    {
        await this.writeClient.CreateDocumentAsync(this.contentCollection, new Subscriptions
        {
            UserId = userId,
            SubscriptionFilter = category
        });
    }

    public async Task WriteReviewAsync(string articleId, string userId, string reviewText, int rating)
    {
        await this.writeClient.CreateDocumentAsync(this.contentCollection, new Review
        {
            UserId = userId,
            ArticleId = articleId,
            ReviewText = reviewText,
            Rating = rating
        });
    }

Pro čtení, oznámení a recenze, můžete musí číst od oblasti a sjednocení výsledky jak je znázorněno v následujícím fragmentu kódu:

    public async Task<IEnumerable<Notification>> ReadNotificationFeedAsync(string userId)
    {
        IDocumentQuery<Notification> writeAccountNotification = (
            from notification in this.writeClient.CreateDocumentQuery<Notification>(this.contentCollection) 
            where notification.UserId == userId 
            select notification).AsDocumentQuery();
        
        IDocumentQuery<Notification> readAccountNotification = (
            from notification in this.readClient.CreateDocumentQuery<Notification>(this.contentCollection) 
            where notification.UserId == userId 
            select notification).AsDocumentQuery();

        List<Notification> notifications = new List<Notification>();

        while (writeAccountNotification.HasMoreResults || readAccountNotification.HasMoreResults)
        {
            IList<Task<FeedResponse<Notification>>> results = new List<Task<FeedResponse<Notification>>>();

            if (writeAccountNotification.HasMoreResults)
            {
                results.Add(writeAccountNotification.ExecuteNextAsync<Notification>());
            }

            if (readAccountNotification.HasMoreResults)
            {
                results.Add(readAccountNotification.ExecuteNextAsync<Notification>());
            }

            IList<FeedResponse<Notification>> notificationFeedResult = await Task.WhenAll(results);

            foreach (FeedResponse<Notification> feed in notificationFeedResult)
            {
                notifications.AddRange(feed);
            }
        }
        return notifications;
    }

    public async Task<IEnumerable<Review>> ReadReviewsAsync(string articleId)
    {
        IDocumentQuery<Review> writeAccountReviews = (
            from review in this.writeClient.CreateDocumentQuery<Review>(this.contentCollection) 
            where review.ArticleId == articleId 
            select review).AsDocumentQuery();
        
        IDocumentQuery<Review> readAccountReviews = (
            from review in this.readClient.CreateDocumentQuery<Review>(this.contentCollection) 
            where review.ArticleId == articleId 
            select review).AsDocumentQuery();

        List<Review> reviews = new List<Review>();
        
        while (writeAccountReviews.HasMoreResults || readAccountReviews.HasMoreResults)
        {
            IList<Task<FeedResponse<Review>>> results = new List<Task<FeedResponse<Review>>>();

            if (writeAccountReviews.HasMoreResults)
            {
                results.Add(writeAccountReviews.ExecuteNextAsync<Review>());
            }

            if (readAccountReviews.HasMoreResults)
            {
                results.Add(readAccountReviews.ExecuteNextAsync<Review>());
            }

            IList<FeedResponse<Review>> notificationFeedResult = await Task.WhenAll(results);

            foreach (FeedResponse<Review> feed in notificationFeedResult)
            {
                reviews.AddRange(feed);
            }
        }

        return reviews;
    }

Proto výběr dobrý klíč rozdělení a statické dělení založené na účet, můžete dosáhnout více oblast místní zápisů a čtení pomocí Azure Cosmos DB.

## <a id="NextSteps"></a>Další kroky
V tomto článku jsme popsané, jak je používat vzory globálně distribuované čtení zápisu více oblasti s Azure DB Cosmos jako vzorový scénář pomocí publikování obsahu.

* Další informace o tom, jak Azure Cosmos DB podporuje [globální distribuční](distribute-data-globally.md)
* Další informace o [automatickou a ruční převzetí služeb při selhání v Azure Cosmos DB](regional-failover.md)
* Další informace o [globální konzistence s Azure Cosmos DB](consistency-levels.md)
* Vývoj s více oblastí pomocí [Azure Cosmos DB - rozhraní SQL API](tutorial-global-distribution-sql-api.md)
* Vývoj s více oblastí pomocí [Azure Cosmos DB - MongoDB rozhraní API](tutorial-global-distribution-MongoDB.md)
* Vývoj s více oblastí pomocí [Azure Cosmos DB - API tabulky](tutorial-global-distribution-table.md)
