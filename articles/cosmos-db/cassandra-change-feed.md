---
title: Změna kanálu v rozhraní Azure Cosmos DB API pro Cassandra
description: Naučte se, jak pomocí služby Change feed v rozhraní Azure Cosmos DB API pro Cassandra získat změny provedené ve vašich datech.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 11/25/2019
ms.author: thvankra
ms.openlocfilehash: 5252993c560b30836c65bf2d395173513993c307
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93092394"
---
# <a name="change-feed-in-the-azure-cosmos-db-api-for-cassandra"></a>Změna kanálu v rozhraní Azure Cosmos DB API pro Cassandra
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Podpora [kanálu změny](change-feed.md) v rozhraní Azure Cosmos DB API pro Cassandra je k dispozici prostřednictvím predikátů dotazu v CQL (Cassandra Query Language). Pomocí těchto podmínek predikátu se můžete dotazovat na rozhraní API Change feed. Aplikace mohou získat změny provedené v tabulce pomocí primárního klíče (označovaného také jako klíč oddílu), jak je požadováno v CQL. Na základě výsledků pak můžete provést další akce. Změny v řádcích v tabulce jsou zachyceny v pořadí času změny a v pořadí řazení na klíč oddílu.

Následující příklad ukazuje, jak získat kanál změn na všech řádcích v tabulce rozhraní API Cassandraho prostoru klíčů s použitím rozhraní .NET. Predikát COSMOS_CHANGEFEED_START_TIME () se používá přímo v rámci CQL k dotazování na položky v kanálu změn od zadaného počátečního času (v tomto případě aktuální datum a čas). Úplnou ukázku, pro C# [tady](/samples/azure-samples/azure-cosmos-db-cassandra-change-feed/cassandra-change-feed/) a pro Java si můžete stáhnout [tady](https://github.com/Azure-Samples/cosmos-changefeed-cassandra-java).

V každé iteraci pokračuje dotaz u poslední změny bodu pomocí stavu stránkování. Průběžný Stream pro nové změny v tabulce se zobrazí v prostoru. Uvidíme změny v řádcích, které jsou vložené nebo aktualizované. Sledování operací odstranění pomocí kanálu změn v rozhraní API Cassandra aktuálně není podporováno.

# <a name="java"></a>[Java](#tab/java)

```java
        Session cassandraSession = utils.getSession();

        try {
              DateTimeFormatter dtf = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");  
               LocalDateTime now = LocalDateTime.now().minusHours(6).minusMinutes(30);  
               String query="SELECT * FROM uprofile.user where COSMOS_CHANGEFEED_START_TIME()='" 
                    + dtf.format(now)+ "'";
               
             byte[] token=null; 
             System.out.println(query); 
             while(true)
             {
                 SimpleStatement st=new  SimpleStatement(query);
                 st.setFetchSize(100);
                 if(token!=null)
                     st.setPagingStateUnsafe(token);
                 
                 ResultSet result=cassandraSession.execute(st) ;
                 token=result.getExecutionInfo().getPagingState().toBytes();
                 
                 for(Row row:result)
                 {
                     System.out.println(row.getString("user_name"));
                 }
             }
                    

        } finally {
            utils.close();
            LOGGER.info("Please delete your table after verifying the presence of the data in portal or from CQL");
        }

```

# <a name="c"></a>[C#](#tab/csharp)

```C#
    //set initial start time for pulling the change feed
     DateTime timeBegin = DateTime.UtcNow;

    //initialise variable to store the continuation token
    byte[] pageState = null;
    while (true)
    {
        try
        {

            //Return the latest change for all rows in 'user' table    
            IStatement changeFeedQueryStatement = new SimpleStatement(
            $"SELECT * FROM uprofile.user where COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");
            if (pageState != null)
            {
                changeFeedQueryStatement = changeFeedQueryStatement.SetPagingState(pageState);
            }
            Console.WriteLine("getting records from change feed at last page state....");
            RowSet rowSet = session.Execute(changeFeedQueryStatement);

            //store the continuation token here
            pageState = rowSet.PagingState;

            List<Row> rowList = rowSet.ToList();
            if (rowList.Count != 0)
            {
                for (int i = 0; i < rowList.Count; i++)
                {
                    string value = rowList[i].GetValue<string>("user_name");
                    int key = rowList[i].GetValue<int>("user_id");
                    // do something with the data - e.g. compute, forward to another event, function, etc.
                    // here, we just print the user name field
                    Console.WriteLine("user_name: " + value);
                }
            }
            else
            {
                Console.WriteLine("zero documents read");
            }
        }
        catch (Exception e)
        {
            Console.WriteLine("Exception " + e);
        }
    }

```
---

Chcete-li získat změny v jednom řádku podle primárního klíče, můžete v dotazu přidat primární klíč. Následující příklad ukazuje, jak sledovat změny řádku, kde "user_id = 1"

# <a name="c"></a>[C#](#tab/csharp)

```C#
    //Return the latest change for all row in 'user' table where user_id = 1
    IStatement changeFeedQueryStatement = new SimpleStatement(
    $"SELECT * FROM uprofile.user where user_id = 1 AND COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");

```

# <a name="java"></a>[Java](#tab/java)

```java
    String query="SELECT * FROM uprofile.user where user_id=1 and COSMOS_CHANGEFEED_START_TIME()='" 
                    + dtf.format(now)+ "'";
    SimpleStatement st=new  SimpleStatement(query);
```
---
## <a name="current-limitations"></a>Aktuální omezení

Při použití kanálu Change s rozhraní API Cassandra platí následující omezení:

* V tuto chvíli se podporuje vkládání a aktualizace. Operace odstranění ještě není podporovaná. Jako alternativní řešení můžete přidat měkké označení na řádky, které se odstraňují. Přidejte například pole do řádku s názvem "Deleted" a nastavte jej na hodnotu "true".
* Poslední aktualizace je trvalá, protože v základní službě SQL API nejsou k dispozici průběžné aktualizace pro danou entitu.


## <a name="error-handling"></a>Zpracování chyb

Při použití kanálu Change v rozhraní API Cassandra jsou podporovány následující chybové kódy a zprávy:

* **Kód chyby HTTP 429** – Pokud je přenosový kanál změn omezený, vrátí prázdnou stránku.

## <a name="next-steps"></a>Další kroky

* [Správa prostředků Azure Cosmos DB rozhraní API Cassandra pomocí šablon Azure Resource Manager](./templates-samples-cassandra.md)