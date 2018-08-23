---
title: Volba správného klíč rozdělení do oddílů provést ve více oblastech zápisy a čtení | Dokumentace Microsoftu
description: Další informace o tom, jak navrhnout aplikačních architektur pomocí místní operací čtení a zápisu napříč několika geografickými oblastmi pomocí služby Azure Cosmos DB zvolit klíč oddílu.
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: conceptual
ms.date: 06/6/2018
ms.author: rimman
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3d38b7cd7d1f28f706e94782602926abc8fc11e3
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2018
ms.locfileid: "42060736"
---
# <a name="perform-multi-region-writes-and-reads-by-choosing-the-right-partitioning-key"></a>Volba správného klíč rozdělení do oddílů provést ve více oblastech zápisy a čtení

Azure Cosmos DB podporuje na klíč [globální replikace](distribute-data-globally.md), která umožňuje distribuovat data do několika oblastí s přístup s nízkou latencí kdekoli v úloze. Tento model se běžně používá pro úlohy vydavatel/konzumenta najdete níž se nachází zapisovač v jedné zeměpisné oblasti a globálně distribuovanou čtenáři v jiných oblastech (čtení). 

Podpora globální replikace služby Azure Cosmos DB můžete použít také k vytváření aplikací, ve kterých jsou globálně distribuované zapisovače a čtenáři. Tento dokument popisuje vzor, který umožňuje dosáhnout místní zápisu a čtení místní distribuované uživatelé vytvářející obsah pomocí služby Azure Cosmos DB.

## <a id="ExampleScenario"></a>Publikování obsahu – příklad scénáře
Podívejme se na reálném scénáři popisující, jak můžete globálně distribuované více region/více master čtení zápisu vzory pomocí služby Azure Cosmos DB. Zvažte možnost obsahu publikování platforma založená na službě Azure Cosmos DB. Tady jsou některé požadavky, které tuto platformu, musí splňovat pro skvělé uživatelské prostředí pro vydavatele i spotřebitele.

* Autoři a předplatitelé jsou rozdělené do světa 
* Autoři musíte publikovat články (zápis) pro místní oblasti (nejbližší)
* Autoři mají čtenáři/předplatitelé jejich článků, kteří jsou distribuovaných po celém světě. 
* Předplatitelé měli oznámení, když se publikují nové články.
* Předplatitelé musí být schopni číst články z místní oblasti. Musí být také možnost přidávat recenze na tyto články. 
* Každý, včetně autora článků by měl být moct zobrazit všechny revize připojená k články z místní oblasti. 

Miliony zákazníků a vydavatelé miliardy články, za předpokladu, že nejdříve musíme boji s problémy škálování – zaručující umístění přístup. Stejně jako u většiny problémy se škálovatelností, řešení spočívá v vhodné strategie dělení. V dalším kroku Podívejme se na tom, jak model články, revize a oznámení jako dokumenty, nakonfigurovat účty služby Azure Cosmos DB a implementovat vrstvy přístupu k datům. 

Pokud chcete získat další informace o vytváření oddílů a klíče oddílů, přečtěte si téma [dělení a škálování ve službě Azure Cosmos DB](partition-data.md).

## <a id="ModelingNotifications"></a>Modelování oznámení
Oznámení se, že data se předají specifické pro uživatele. Proto jsou vzorce přístupu pro dokumenty oznámení vždy v rámci jednoho uživatele. Například by "post oznámení pro uživatele" nebo "načíst všechna oznámení pro daného uživatele". Proto by optimální volbou dělení klíč pro tento typ `UserId`.

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

## <a id="ModelingSubscriptions"></a>Modelování předplatná
Odběry můžete vytvořit pro různá kritéria jako určitou kategorii články, které vás zajímají, nebo konkrétní vydavatele. Proto `SubscriptionFilter` je dobrou volbou pro klíč oddílu.

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

## <a id="ModelingArticles"></a>Modelování články
Jakmile článku je identifikován oznámení, následné dotazů jsou obvykle založené na `Article.Id`. Výběr `Article.Id` jako oddíl klíč tak poskytuje nejlepší distribuce pro ukládání články v kolekci Azure Cosmos DB. 

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

## <a id="ModelingReviews"></a>Modelování revize
Například články kontroly jsou většinou napsané a čtení v kontextu článku. Výběr `ArticleId` jako oddíl klíč poskytuje nejlepší distribuce a efektivní přístup kontroly související s článkem. 

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

## <a id="DataAccessMethods"></a>Metody datového přístupu vrstvy
Nyní Podívejme se na hlavní data metody přístupu musíme implementovat. Tady je seznam metod, které `ContentPublishDatabase` potřebuje:

    class ContentPublishDatabase 
    { 
        public async Task CreateSubscriptionAsync(string userId, string category);
    
        public async Task<IEnumerable<Notification>> ReadNotificationFeedAsync(string userId);
    
        public async Task<Article> ReadArticleAsync(string articleId);
    
        public async Task WriteReviewAsync(string articleId, string userId, string reviewText, int rating);
    
        public async Task<IEnumerable<Review>> ReadReviewsAsync(string articleId); 
    }

## <a id="Architecture"></a>Konfigurace účtu Azure Cosmos DB
K zajištění, čtení a zápis jsme musí rozdělit na místní data ne jenom v oddílu klíče, ale také podle zeměpisné Přistupování do oblasti. Model využívá s geograficky replikovanou službu Azure Cosmos DB databázový účet pro každou oblast. Se dvěma oblastmi, zde je například nastavení pro více oblastí zápisu:

| Název účtu | Oblast zápisu | Oblast čtení |
| --- | --- | --- |
| `contentpubdatabase-usa.documents.azure.com` | `West US` |`North Europe` |
| `contentpubdatabase-europe.documents.azure.com` | `North Europe` |`West US` |

Následující diagram znázorňuje, jak provádět čtení a zápisy v typické aplikaci s tímto nastavením:

![Architektura služby Azure Cosmos DB několika hlavními databázemi](./media/multi-master-workaround/multi-master.png)

Tady je fragment kódu ukazuje, jak inicializovat klienty v DAL používané `West US` oblasti.
    
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

Předchozí nastavení vrstvy přístupu k datům může předávat všechny operace zápisu do místní účet, podle které se nasadí. Čtení provádějí čtení z oba účty, a získejte globální přehled o data. Tento přístup je možné rozšířit na jakýkoli počet oblastí podle potřeby. Například tady je nastavení pomocí tří geografických oblastech:

| Název účtu | Oblast zápisu | Oblast čtení 1 | Oblast čtení 2 |
| --- | --- | --- | --- |
| `contentpubdatabase-usa.documents.azure.com` | `West US` |`North Europe` |`Southeast Asia` |
| `contentpubdatabase-europe.documents.azure.com` | `North Europe` |`West US` |`Southeast Asia` |
| `contentpubdatabase-asia.documents.azure.com` | `Southeast Asia` |`North Europe` |`West US` |

## <a id="DataAccessImplementation"></a>Implementace vrstvy přístupu dat
Nyní Pojďme se podívat na provádění vrstvy přístupu k datům (DAL) pro aplikaci se dvěma oblastmi zapisovatelný. DAL musí implementovat následující kroky:

* Vytvoření víc instancí `DocumentClient` pro jednotlivé účty. Se dvěma oblastmi, každá instance vrstvy DAL má jeden `writeClient` a jeden `readClient`. 
* Podle oblasti nasazené aplikace, konfigurace koncových bodů pro `writeclient` a `readClient`. Například DAL nasazená v `West US` používá `contentpubdatabase-usa.documents.azure.com` pro provádění zápisy. DAL nasazené v `NorthEurope` používá `contentpubdatabase-europ.documents.azure.com` pro zápis.

V předchozí instalaci je možné implementovat metody datového přístupu. Zápis dál operace zápisu do odpovídající `writeClient`.

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

Pro čtení, oznámení a kontroly, si musíte přečíst z oblasti a sjednocení výsledky jak je znázorněno v následujícím fragmentu kódu:

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

Proto vyberete vhodný klíč dělení a statické, založené na účtu dělení, můžete dosáhnout více oblastí místní zápisy a čtení pomocí služby Azure Cosmos DB.

## <a id="NextSteps"></a>Další kroky
V tomto článku jsme popisuje použití vzorů globálně distribuované ve více oblastech čtení zápis s Azure Cosmos DB pomocí publikování obsahu jako ukázkový scénář.

* Další informace o tom, jak službu Azure Cosmos DB podporuje [globální distribuce](distribute-data-globally.md)
* Další informace o [automatického a ručního převzetí služeb při selhání ve službě Azure Cosmos DB](regional-failover.md)
* Další informace o [globální soudržnosti pomocí služby Azure Cosmos DB](consistency-levels.md)
* Vývoj s využitím více oblastí [Azure Cosmos DB – rozhraní SQL API](tutorial-global-distribution-sql-api.md)
* Vývoj s využitím více oblastí [Azure Cosmos DB – rozhraní API MongoDB](tutorial-global-distribution-MongoDB.md)
* Vývoj s využitím více oblastí [služby Azure Cosmos DB - Table API](tutorial-global-distribution-table.md)
