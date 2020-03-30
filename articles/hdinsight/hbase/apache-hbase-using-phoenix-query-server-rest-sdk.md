---
title: Dotazovací server Phoenix REST SDK – Azure HDInsight
description: Nainstalujte a použijte sadku REST SDK pro dotazovací server Phoenix v Azure HDInsight.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/01/2020
ms.openlocfilehash: 84c2bad1004029fe61dcfc19321957a170284587
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75612253"
---
# <a name="apache-phoenix-query-server-rest-sdk"></a>Dotazovací server Apache Phoenix REST SDK

[Apache Phoenix](https://phoenix.apache.org/) je open source, masivně paralelní relační databázová vrstva nad [Apache HBase](apache-hbase-overview.md). Phoenix umožňuje používat SQL-jako dotazy s HBase prostřednictvím Nástrojů SSH, jako je [SQLLine](apache-hbase-query-with-phoenix.md). Phoenix také poskytuje http server s názvem Phoenix Query Server (PQS), tenký klient, který podporuje dva mechanismy přenosu pro komunikaci klienta: JSON a vyrovnávací paměti protokolu. Vyrovnávací paměti protokolu je výchozí mechanismus a nabízí efektivnější komunikaci než JSON.

Tento článek popisuje, jak pomocí sady PQS REST SDK vytvářet tabulky, upsert řádky jednotlivě a hromadně a vybrat data pomocí příkazů SQL. Příklady používají [ovladač Microsoft .NET pro dotazovací server Apache Phoenix](https://www.nuget.org/packages/Microsoft.Phoenix.Client). Tato sada SDK je postavena na api [Apache Calcite Avatica,](https://calcite.apache.org/avatica/) která používají výhradně vyrovnávací paměti protokolu pro formát serializace.

Další informace naleznete v [tématu Apache Calcite Avatica Protocol Buffers Reference](https://calcite.apache.org/avatica/docs/protobuf_reference.html).

## <a name="install-the-sdk"></a>Instalace sady SDK

Ovladač Microsoft .NET pro dotazovací server Apache Phoenix je k dispozici jako balíček NuGet, který lze nainstalovat z **konzoly Správce balíčků** sady Visual Studio NuGet pomocí následujícího příkazu:

    Install-Package Microsoft.Phoenix.Client

## <a name="instantiate-new-phoenixclient-object"></a>Vytvoření instance nového objektu PhoenixClient

Chcete-li začít používat knihovnu, `PhoenixClient` vytvořte instanci nového objektu a předejte `ClusterCredentials` `Uri` tak, že bude obsahovat cluster a uživatelské jméno a heslo apache hadoop clusteru.

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net/"), "USERNAME", "PASSWORD");
client = new PhoenixClient(credentials);
```

Nahraďte název clusteru HDInsight HBase názvem a username a password pověřeními Hadoop určenými při vytváření clusteru. Výchozí uživatelské jméno Hadoop je **admin**.

## <a name="generate-unique-connection-identifier"></a>Generovat jedinečný identifikátor připojení

Chcete-li odeslat jeden nebo více požadavků do služby PQS, je třeba zahrnout jedinečný identifikátor připojení, který přidruží požadavky k připojení.

```csharp
string connId = Guid.NewGuid().ToString();
```

Každý příklad nejprve provede `OpenConnectionRequestAsync` volání metody, předávání v jedinečný identifikátor připojení. Dále `ConnectionProperties` definujte `RequestOptions`a , předávací tyto `ConnectionSyncRequestAsync` objekty a vygenerovaný identifikátor připojení k metodě. `ConnectionSyncRequest` Objekt PQS pomáhá zajistit, že klient i server mají konzistentní zobrazení vlastností databáze.

## <a name="connectionsyncrequest-and-its-connectionproperties"></a>ConnectionSyncRequest a jeho vlastnosti ConnectionProperties

Chcete-li volat `ConnectionSyncRequestAsync` `ConnectionProperties` , předat objekt.

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

Zde jsou některé vlastnosti zájmu:

| Vlastnost | Popis |
| -- | -- |
| Autocommit | Logická hodnota označující, zda `autoCommit` je povolena pro transakce Phoenix. |
| ReadOnly | Logická hodnota označující, zda je připojení jen pro čtení. |
| Transakční izolace | Celé číslo označující úroveň izolace transakcí podle specifikace JDBC – viz následující tabulka.|
| Katalog | Název katalogu, který se má použít při načítání vlastností připojení. |
| Schéma | Název schématu, které má být používáno při načítání vlastností připojení. |
| Isdirty | Logická hodnota označující, zda byly vlastnosti změněny. |

Zde jsou `TransactionIsolation` hodnoty:

| Hodnota izolace | Popis |
| -- | -- |
| 0 | Transakce nejsou podporovány. |
| 1 | Může dojít k neopakovatelným čtením, neopakovatelným čtením a fiktivním čtením. |
| 2 | Neznečištěné čtení jsou zabráněno, ale může dojít k neopakovatelné čtení a fiktivní čtení. |
| 4 | Neznečištěné čtení a neopakovatelné čtení jsou zabráněno, ale může dojít k fiktivní čtení. |
| 8 | Neznečištěné čtení, neopakovatelné čtení a fiktivní čtení jsou zabráněno. |

## <a name="create-a-new-table"></a>Vytvoření nové tabulky

HBase, stejně jako všechny ostatní RDBMS, ukládá data v tabulkách. Phoenix používá standardní dotazy SQL k vytvoření nových tabulek při definování typů primárního klíče a sloupců.

Tento příklad a všechny pozdější příklady, `PhoenixClient` použijte instanciovaný objekt, jak je definováno v [Instanci nový phoenixclient objekt](#instantiate-new-phoenixclient-object).

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

Předchozí příklad vytvoří novou tabulku `Customers` s `IF NOT EXISTS` názvem pomocí možnosti. Volání `CreateStatementRequestAsync` vytvoří nový příkaz na serveru Avitica (PQS). Blok `finally` zavře vrácené `CreateStatementResponse` a `OpenConnectionResponse` objekty.

## <a name="insert-data-individually"></a>Vložení dat jednotlivě

Tento příklad ukazuje jednotlivé vkládání dat `List<string>` odkazující na kolekci zkratek amerického státu a území:

```csharp
var states = new List<string> { "AL", "AK", "AS", "AZ", "AR", "CA", "CO", "CT", "DE", "DC", "FM", "FL", "GA", "GU", "HI", "ID", "IL", "IN", "IA", "KS", "KY", "LA", "ME", "MH", "MD", "MA", "MI", "MN", "MS", "MO", "MT", "NE", "NV", "NH", "NJ", "NM", "NY", "NC", "ND", "MP", "OH", "OK", "OR", "PW", "PA", "PR", "RI", "SC", "SD", "TN", "TX", "UT", "VT", "VI", "VA", "WA", "WV", "WI", "WY" };
```

Hodnota `StateProvince` sloupce tabulky bude použita v pozdější operaci výběru.

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

Struktura pro provádění příkazu insert je podobná vytvoření nové tabulky. Na konci `try` bloku transakce je explicitně potvrzena. Tento příklad opakuje vložení transakce 300 krát. Následující příklad ukazuje efektivnější proces dávkového vkládání.

## <a name="batch-insert-data"></a>Data dávkového vložení

Následující kód je téměř totožný s kódem pro vkládání dat jednotlivě. Tento příklad `UpdateBatch` používá objekt ve `ExecuteBatchRequestAsync`volání , nikoli `ExecuteRequestAsync` opakovaně volání s připravený mj.

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

V jednom testovacím prostředí trvalo individuální vložení 300 nových záznamů téměř 2 minuty. Naproti tomu vložení 300 záznamů jako dávky vyžadovalo pouze 6 sekund.

## <a name="select-data"></a>Výběr dat

Tento příklad ukazuje, jak znovu použít jedno připojení ke spuštění více dotazů:

1. Vyberte všechny záznamy a poté načíst zbývající záznamy po vrácení výchozího maxima 100.
2. K načtení jednoho skalárního výsledku použijte příkaz select scelkového počtu řádků.
3. Proveďte příkaz select, který vrátí celkový počet zákazníků na stát nebo oblast.

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

Výstup emvětých `select` prohlášení by měl být následující výsledek:

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

## <a name="next-steps"></a>Další kroky

* [Apache Phoenix ve službě HDInsight](../hdinsight-phoenix-in-hdinsight.md)
* [Použití sady Apache HBase REST SDK](apache-hbase-rest-sdk.md)
