---
title: Phoenix Query Server REST SDK – Azure HDInsight
description: Instalace a použití sady Phoenix Query Server v Azure HDInsight REST SDK.
services: hdinsight
ms.service: hdinsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/04/2017
ms.openlocfilehash: f5be273401fa9fdfdc2c97296f5f2265dbb3c447
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43042373"
---
# <a name="phoenix-query-server-rest-sdk"></a>Phoenix Query Server REST SDK

[Apache Phoenix](http://phoenix.apache.org/) je open source, vrstva masivně paralelní relační databáze nad [HBase](apache-hbase-overview.md). Phoenix umožňuje použít dotazy na podobném SQL s HBase SSH pomocí nástrojů, jako [SQLLine](apache-hbase-phoenix-squirrel-linux.md). Phoenix také poskytuje server HTTP volá Phoenix Query Server (PQS), tenkým klientům, který podporuje dva mechanismy přenosu pro komunikaci klienta: JSON a Protocol Buffers. Protocol Buffers je výchozího mechanismu a nabízí mnohem efektivnější komunikaci než pro JSON.

Tento článek popisuje způsob použití PQS REST SDK k vytváření tabulek, řádky upsert jednotlivě a hromadně a výběr dat pomocí příkazů jazyka SQL. V příkladech se používá [ovladač rozhraní Microsoft .NET pro Apache Phoenix Query Server](https://www.nuget.org/packages/Microsoft.Phoenix.Client). Tato sada SDK je postavená na [Apache Calcite Avatica](https://calcite.apache.org/avatica/) rozhraní API, která používají Protocol Buffers výlučně pro formát serializace.

Další informace najdete v tématu [referenční informace o Apache Calcite Avatica protokolu vyrovnávací paměti](https://calcite.apache.org/avatica/docs/protobuf_reference.html).

## <a name="install-the-sdk"></a>Instalace sady SDK

Ovladač Microsoft .NET pro Apache Phoenix Query Server k dispozici jako balíček NuGet, který si můžete nainstalovat pomocí sady Visual Studio **Konzola správce balíčků NuGet** pomocí následujícího příkazu:

    Install-Package Microsoft.Phoenix.Client

## <a name="instantiate-new-phoenixclient-object"></a>Vytvoření instance nového objektu PhoenixClient

Pokud chcete začít používat knihovny, vytvoření instance nového `PhoenixClient` objekt předávání v `ClusterCredentials` obsahující `Uri` do clusteru a clusteru Hadoop uživatelské jméno a heslo.

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net/"), "USERNAME", "PASSWORD");
client = new PhoenixClient(credentials);
```

Nahraďte název clusteru s názvem clusteru HDInsight HBase a uživatelské jméno a heslo zadané při vytvoření clusteru Hadoop přihlašovací údaje. Je výchozí uživatelské jméno Hadoop **správce**.

## <a name="generate-unique-connection-identifier"></a>Generovat jedinečný identifikátor připojení

K odesílání požadavků na jeden nebo více PQS, budete muset zahrnout jedinečný identifikátor připojení k přidružení žádosti o připojení.

```csharp
string connId = Guid.NewGuid().ToString();
```

Každý příklad nejprve provede volání `OpenConnectionRequestAsync` metodu jedinečný identifikátor připojení. Dále definujte `ConnectionProperties` a `RequestOptions`, předávání těchto objektů a připojení vygenerovaný identifikátor, který `ConnectionSyncRequestAsync` metody. Společnosti PQS `ConnectionSyncRequest` objekt pomáhá zajistit, že klient i server mají konzistentní zobrazení vlastností databáze.

## <a name="connectionsyncrequest-and-its-connectionproperties"></a>ConnectionSyncRequest a jeho ConnectionProperties

Pro volání `ConnectionSyncRequestAsync`, předejte `ConnectionProperties` objektu.

```csharp
ConnectionProperties connProperties = new ConnectionProperties
{
    HasAutoCommit = true,
    AutoCommit = true,
    HasReadOnly = true,
    ReadOnly = false,
    TransactionIsolation = 0,
    Catalog = "",
    Schema = "",
    IsDirty = true
};
await client.ConnectionSyncRequestAsync(connId, connProperties, options);
```

Tady jsou některé vlastnosti, které vás zajímají:

| Vlastnost | Popis |
| -- | -- |
| Automatický zápis | Boolean označující, zda `autoCommit` je povolená pro Phoenix transakce. |
| ReadOnly | Logická hodnota označující, zda je připojení jen pro čtení. |
| TransactionIsolation | Celé číslo představující úroveň izolace transakce podle specifikace JDBC – viz následující tabulka.|
| Katalog | Název katalogu pro použití při načítání vlastnosti připojení. |
| Schéma | Název schématu pro použití při načítání vlastnosti připojení. |
| IsDirty | Logická hodnota označující, zda byla změněna vlastnosti. |

Tady jsou `TransactionIsolation` hodnoty:

| Hodnotu izolace | Popis |
| -- | -- |
| 0 | Transakce nejsou podporovány. |
| 1 | Může dojít k nepřesné, – a opakovatelné operace čtení a fiktivní čtení. |
| 2 | Nepřesné zabráníte tím zdvojnásobení, ale může dojít, – a opakovatelné operace čtení a fiktivní čtení. |
| 4 | Nemají nepřesné a -opakovatelné operace čtení, ale může dojít k fiktivní čtení. |
| 8 | Nepřesné, – a opakovatelné operace čtení a fiktivní čtení všech nemají. |

## <a name="create-a-new-table"></a>Vytvořit novou tabulku

HBase, stejně jako jakékoli jiné relační databázový systém a ukládá data v tabulkách. Phoenix využívá standardní dotazy SQL k vytvoření nových tabulek, při definování typů primární klíč a ve sloupci.

V tomto příkladu a všechny další příklady použití instance `PhoenixClient` objektu, jak jsou definovány v [vytvoření instance nového objektu PhoenixClient](#instantiate-new-phoenixclient-object).

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();

// You can set certain request options, such as timeout in milliseconds:
options.TimeoutMillis = 300000;

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
CreateStatementResponse createStatementResponse = null;
OpenConnectionResponse openConnResponse = null;

try
{
    // Opening connection
    var info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);

    // Create the statement
    createStatementResponse = client.CreateStatementRequestAsync(connId, options).Result;
    
    // Create the table if it does not exist
    string sql = "CREATE TABLE IF NOT EXISTS Customers (Id varchar(20) PRIMARY KEY, FirstName varchar(50), " +
        "LastName varchar(100), StateProvince char(2), Email varchar(255), Phone varchar(15))";
    await client.PrepareAndExecuteRequestAsync(connId, sql, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);

    Console.WriteLine($"Table \"Customers\" created.");
}
catch (Exception e)
{
    Console.WriteLine(e);
    throw;
}
finally
{
    if (createStatementResponse != null)
    {
        client.CloseStatementRequestAsync(connId, createStatementResponse.StatementId, options).Wait();
        createStatementResponse = null;
    }

    if (openConnResponse != null)
    {
        client.CloseConnectionRequestAsync(connId, options).Wait();
        openConnResponse = null;
    }
}
```

V předchozím příkladu se vytvoří novou tabulku s názvem `Customers` pomocí `IF NOT EXISTS` možnost. `CreateStatementRequestAsync` Volání vytvoří nové prohlášení Avitica (PQS) serveru. `finally` Blok ukončí vráceného `CreateStatementResponse` a `OpenConnectionResponse` objekty.

## <a name="insert-data-individually"></a>Vkládání dat jednotlivě

Tento příklad ukazuje jednotlivé datové vložit, odkazující `List<string>` kolekce American stavu a území zkratek:

```csharp
var states = new List<string> { "AL", "AK", "AS", "AZ", "AR", "CA", "CO", "CT", "DE", "DC", "FM", "FL", "GA", "GU", "HI", "ID", "IL", "IN", "IA", "KS", "KY", "LA", "ME", "MH", "MD", "MA", "MI", "MN", "MS", "MO", "MT", "NE", "NV", "NH", "NJ", "NM", "NY", "NC", "ND", "MP", "OH", "OK", "OR", "PW", "PA", "PR", "RI", "SC", "SD", "TN", "TX", "UT", "VT", "VI", "VA", "WA", "WV", "WI", "WY" };
```

V tabulce `StateProvince` hodnota ve sloupci se použijí v následné operace select.

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();
options.TimeoutMillis = 300000;

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
OpenConnectionResponse openConnResponse = null;
StatementHandle statementHandle = null;
try
{
    // Opening connection
    pbc::MapField<string, string> info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);

    string sql = "UPSERT INTO Customers VALUES (?,?,?,?,?,?)";
    PrepareResponse prepareResponse = await client.PrepareRequestAsync(connId, sql, 100, options);
    statementHandle = prepareResponse.Statement;
    
    var r = new Random();

    // Insert 300 rows
    for (int i = 0; i < 300; i++)
    {
        var list = new pbc.RepeatedField<TypedValue>();
        var id = new TypedValue
        {
            StringValue = "id" + i,
            Type = Rep.String
        };
        var firstName = new TypedValue
        {
            StringValue = "first" + i,
            Type = Rep.String
        };
        var lastName = new TypedValue
        {
            StringValue = "last" + i,
            Type = Rep.String
        };
        var state = new TypedValue
        {
            StringValue = states.ElementAt(r.Next(0, 49)),
            Type = Rep.String
        };
        var email = new TypedValue
        {
            StringValue = $"email{1}@junkemail.com",
            Type = Rep.String
        };
        var phone = new TypedValue
        {
            StringValue = $"555-229-341{i.ToString().Substring(0,1)}",
            Type = Rep.String
        };
        list.Add(id);
        list.Add(firstName);
        list.Add(lastName);
        list.Add(state);
        list.Add(email);
        list.Add(phone);

        Console.WriteLine("Inserting customer " + i);

        await client.ExecuteRequestAsync(statementHandle, list, long.MaxValue, true, options);
    }

    await client.CommitRequestAsync(connId, options);

    Console.WriteLine("Upserted customer data");

}
catch (Exception ex)
{

}
finally
{
    if (statementHandle != null)
    {
        await client.CloseStatementRequestAsync(connId, statementHandle.Id, options);
        statementHandle = null;
    }
    if (openConnResponse != null)
    {
        await client.CloseConnectionRequestAsync(connId, options);
        openConnResponse = null;
    }
}
```

Struktury pro provádění příkazu insert je podobné jako vytvoření nové tabulky. Všimněte si, že na konci `try` blok, transakce je explicitně potvrdit. V tomto příkladu se opakuje transakci vložit 300 časy. Následující příklad ukazuje mnohem efektivnější dávkového vložení procesu.

## <a name="batch-insert-data"></a>Dávkové vkládání dat

Následující kód je téměř stejný jako kód pro vložení dat samostatně. V tomto příkladu `UpdateBatch` objektu ve volání `ExecuteBatchRequestAsync`, namísto opakovaného volání `ExecuteRequestAsync` pomocí připraveného příkazu.

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();
options.TimeoutMillis = 300000;

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
OpenConnectionResponse openConnResponse = null;
CreateStatementResponse createStatementResponse = null;
try
{
    // Opening connection
    pbc::MapField<string, string> info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);

    // Creating statement
    createStatementResponse = await client.CreateStatementRequestAsync(connId, options);

    string sql = "UPSERT INTO Customers VALUES (?,?,?,?,?,?)";
    PrepareResponse prepareResponse = client.PrepareRequestAsync(connId, sql, long.MaxValue, options).Result;
    var statementHandle = prepareResponse.Statement;
    var updates = new pbc.RepeatedField<UpdateBatch>();

    var r = new Random();

    // Insert 300 rows
    for (int i = 300; i < 600; i++)
    {
        var list = new pbc.RepeatedField<TypedValue>();
        var id = new TypedValue
        {
            StringValue = "id" + i,
            Type = Rep.String
        };
        var firstName = new TypedValue
        {
            StringValue = "first" + i,
            Type = Rep.String
        };
        var lastName = new TypedValue
        {
            StringValue = "last" + i,
            Type = Rep.String
        };
        var state = new TypedValue
        {
            StringValue = states.ElementAt(r.Next(0, 49)),
            Type = Rep.String
        };
        var email = new TypedValue
        {
            StringValue = $"email{1}@junkemail.com",
            Type = Rep.String
        };
        var phone = new TypedValue
        {
            StringValue = $"555-229-341{i.ToString().Substring(0, 1)}",
            Type = Rep.String
        };
        list.Add(id);
        list.Add(firstName);
        list.Add(lastName);
        list.Add(state);
        list.Add(email);
        list.Add(phone);

        var batch = new UpdateBatch
        {
            ParameterValues = list
        };
        updates.Add(batch);

        Console.WriteLine($"Added customer {i} to batch");
    }

    var executeBatchResponse = await client.ExecuteBatchRequestAsync(connId, statementHandle.Id, updates, options);

    Console.WriteLine("Batch upserted customer data");

}
catch (Exception ex)
{

}
finally
{
    if (openConnResponse != null)
    {
        await client.CloseConnectionRequestAsync(connId, options);
        openConnResponse = null;
    }
}
```

V jednom testovacím prostředí jednotlivě vkládání nových záznamů 300 trvalo téměř 2 minut. Naproti tomu vkládání 300 záznamů v dávce vyžaduje jenom 6 sekund.

## <a name="select-data"></a>Výběr dat

Tento příklad ukazuje, jak znovu použít jedno připojení ke spuštění více dotazů:

1. Vyberte všechny záznamy a pak načíst zbývající záznamy poté, co vrátí výchozí maximální hodnoty 100.
2. K načtení výsledku jednu skalární použijte příkaz select počet celkový počet řádků.
3. Spusťte příkaz select, který vrátí celkový počet zákazníků za stát nebo území.

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
OpenConnectionResponse openConnResponse = null;
StatementHandle statementHandle = null;

try
{
    // Opening connection
    pbc::MapField<string, string> info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);
    var createStatementResponse = await client.CreateStatementRequestAsync(connId, options);

    string sql = "SELECT * FROM Customers";
    ExecuteResponse executeResponse = await client.PrepareAndExecuteRequestAsync(connId, sql, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);

    pbc::RepeatedField<Row> rows = executeResponse.Results[0].FirstFrame.Rows;
    // Loop through all of the returned rows and display the first two columns
    for (int i = 0; i < rows.Count; i++)
    {
        Row row = rows[i];
        Console.WriteLine(row.Value[0].ScalarValue.StringValue + " " + row.Value[1].ScalarValue.StringValue);
    }
    
    // 100 is hard-coded on the server side as the default firstframe size
    // FetchRequestAsync is called to get any remaining rows
    Console.WriteLine("");
    Console.WriteLine($"Number of rows: {rows.Count}");

    // Fetch remaining rows, offset is not used, simply set to 0
    // When FetchResponse.Frame.Done is true, all rows were fetched
    FetchResponse fetchResponse = await client.FetchRequestAsync(connId, createStatementResponse.StatementId, 0, int.MaxValue, options);
    Console.WriteLine($"Frame row count: {fetchResponse.Frame.Rows.Count}");
    Console.WriteLine($"Fetch response is done: {fetchResponse.Frame.Done}");
    Console.WriteLine("");

    // Running query 2
    string sql2 = "select count(*) from Customers";
    ExecuteResponse countResponse = await client.PrepareAndExecuteRequestAsync(connId, sql2, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);
    long count = countResponse.Results[0].FirstFrame.Rows[0].Value[0].ScalarValue.NumberValue;

    Console.WriteLine($"Total customer records: {count}");
    Console.WriteLine("");

    // Running query 3
    string sql3 = "select StateProvince, count(*) as Number from Customers group by StateProvince order by Number desc";
    ExecuteResponse groupByResponse = await client.PrepareAndExecuteRequestAsync(connId, sql3, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);

    pbc::RepeatedField<Row> stateRows = groupByResponse.Results[0].FirstFrame.Rows;
    for (int i = 0; i < stateRows.Count; i++)
    {
        Row row = stateRows[i];
        Console.WriteLine(row.Value[0].ScalarValue.StringValue + ": " + row.Value[1].ScalarValue.NumberValue);
    }
}
catch (Exception ex)
{

}
finally
{
    if (statementHandle != null)
    {
        await client.CloseStatementRequestAsync(connId, statementHandle.Id, options);
        statementHandle = null;
    }
    if (openConnResponse != null)
    {
        await client.CloseConnectionRequestAsync(connId, options);
        openConnResponse = null;
    }
}
```

Výstup `select` příkazy by měl být následující výsledek:

```
id0 first0
id1 first1
id10 first10
id100 first100
id101 first101
id102 first102
. . .
id185 first185
id186 first186
id187 first187
id188 first188

Number of rows: 100
Frame row count: 500
Fetch response is done: True

Total customer records: 600

NJ: 21
CA: 19
GU: 17
NC: 16
IN: 16
MA: 16
AZ: 16
ME: 16
IL: 15
OR: 15
. . . 
MO: 10
HI: 10
GA: 10
DC: 9
NM: 9
MD: 9
MP: 9
SC: 7
AR: 7
MH: 6
FM: 5
```

## <a name="next-steps"></a>Další postup 

* [Phoenix v HDInsight](../hdinsight-phoenix-in-hdinsight.md)
* [Pomocí sady SDK REST HBase](apache-hbase-rest-sdk.md)
