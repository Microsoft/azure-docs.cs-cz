---
title: Změna informačního kanálu v rozhraní API Azure Cosmos DB pro Cassandru
description: Zjistěte, jak používat kanál změn v rozhraní API Azure Cosmos DB pro Cassandra získat změny provedené ve vašich datech.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 11/25/2019
ms.author: thvankra
ms.openlocfilehash: c2c695608653130b97bf29cc9ce48e2fbb429209
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74694620"
---
# <a name="change-feed-in-the-azure-cosmos-db-api-for-cassandra"></a>Změna informačního kanálu v rozhraní API Azure Cosmos DB pro Cassandru

[Podpora kanálu změn](change-feed.md) v rozhraní API Azure Cosmos DB pro Cassandru je dostupná prostřednictvím predikátů dotazů v dotazovacím jazyce Cassandra (CQL). Pomocí těchto podmínek predikátu můžete zadat dotaz na rozhraní API kanálu změn. Aplikace mohou získat změny provedené v tabulce pomocí primárního klíče (označovaného také jako klíč oddílu), jak je požadováno v CQL. Potom můžete provést další akce na základě výsledků. Změny řádků v tabulce jsou zachyceny v pořadí podle doby jejich úpravy a pořadí řazení je zaručeno na klíč oddílu.

Následující příklad ukazuje, jak získat informační kanál změn na všech řádcích v tabulce Keyspace rozhraní CASSANDRA API pomocí rozhraní .NET. Predikát COSMOS_CHANGEFEED_START_TIME() se používá přímo v rámci CQL k dotazování položek v kanálu změn od zadaného počátečního času (v tomto případě aktuální datetime). Celý vzorek si můžete stáhnout [zde](https://docs.microsoft.com/samples/azure-samples/azure-cosmos-db-cassandra-change-feed/cassandra-change-feed/).

V každé iteraci dotaz pokračuje v posledním bodě změny byly přečteny, pomocí stránkovacího stavu. Můžeme vidět nepřetržitý proud nových změn v tabulce v Keyspace. Uvidíme změny řádků, které jsou vloženy nebo aktualizovány. Sledování operací odstranění pomocí kanálu změn v rozhraní CASSANDRA API není aktuálně podporováno. 

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

Chcete-li získat změny na jeden řádek podle primárního klíče, můžete přidat primární klíč v dotazu. Následující příklad ukazuje, jak sledovat změny pro řádek, kde "user_id = 1"

```C#
    //Return the latest change for all row in 'user' table where user_id = 1
    IStatement changeFeedQueryStatement = new SimpleStatement(
    $"SELECT * FROM uprofile.user where user_id = 1 AND COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");

```

## <a name="current-limitations"></a>Aktuální omezení

Následující omezení platí při použití kanálu změn s rozhraním CASSANDRA API:

* Vložení a aktualizace jsou aktuálně podporovány. Operace odstranění ještě není podporována. Jako alternativní řešení můžete přidat měkkou značku na řádky, které jsou mazány. Například přidejte pole do řádku s názvem "odstraněno" a nastavte ho na hodnotu "true".
* Poslední aktualizace je trvalá jako v základním rozhraní SQL API a zprostředkující aktualizace entity nejsou k dispozici.


## <a name="error-handling"></a>Zpracování chyb

Při použití kanálu změn v rozhraní CASSANDRA API jsou podporovány následující kódy chyb a zprávy:

* **Kód chyby HTTP 429** - Pokud je kanál změn omezen, vrátí prázdnou stránku.

## <a name="next-steps"></a>Další kroky

* [Správa prostředků rozhraní API Azure Cosmos DB Cassandra pomocí šablon Azure Resource Manager](manage-cassandra-with-resource-manager.md)
