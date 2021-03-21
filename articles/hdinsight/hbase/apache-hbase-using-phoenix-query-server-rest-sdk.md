---
title: Phoenix Query Server REST SDK – Azure HDInsight
description: Nainstalujte a použijte sadu REST SDK pro Phoenix Query Server ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 01/01/2020
ms.openlocfilehash: 5ce459e2820cfd2ea5cacb036f5d6885c9c5afa0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98933366"
---
# <a name="apache-phoenix-query-server-rest-sdk"></a>Sada SDK REST sady Apache Phoenix dotazů serveru

[Apache Phoenix](https://phoenix.apache.org/) je otevřená zdrojová a rozsáhlá paralelní relační databázová vrstva nad [Apache HBA](apache-hbase-overview.md). Phoenix umožňuje používat dotazy podobné SQL s adaptéry HBA prostřednictvím nástrojů SSH, jako je [SQLLine](apache-hbase-query-with-phoenix.md). Phoenix také poskytuje HTTP server s názvem Phoenix Query Server (PQS), tenký klient, který podporuje dva mechanismy přenosu pro komunikaci klientů: vyrovnávací paměti JSON a protokolu. Vyrovnávací paměti protokolu jsou výchozím mechanismem a nabízejí efektivnější komunikaci než JSON.

Tento článek popisuje, jak pomocí sady PQS REST SDK vytvářet tabulky, Upsert řádky jednotlivě a hromadně a vybírat data pomocí příkazů SQL. V příkladech se používá [ovladač Microsoft .NET pro Apache Phoenix dotazový Server](https://www.nuget.org/packages/Microsoft.Phoenix.Client). Tato sada SDK je postavená na rozhraních API [Avatica pro Apache Calcite](https://calcite.apache.org/avatica/) , která výhradně využívají vyrovnávací paměti protokolu pro formát serializace.

Další informace najdete v referenčních informacích o [vyrovnávací paměti protokolu Apache Calcite Avatica](https://calcite.apache.org/avatica/docs/protobuf_reference.html).

## <a name="install-the-sdk"></a>Instalace sady SDK

Ovladač Microsoft .NET pro Apache Phoenix dotazový Server je k dispozici jako balíček NuGet, který se dá nainstalovat z **konzoly Správce balíčků NuGet** sady Visual Studio pomocí následujícího příkazu:

```console
Install-Package Microsoft.Phoenix.Client
```

## <a name="instantiate-new-phoenixclient-object"></a>Vytvoření instance nového objektu PhoenixClient

Pokud chcete začít používat knihovnu, vytvořte instanci nového `PhoenixClient` objektu a předejte ji `ClusterCredentials` `Uri` do svého clusteru a Apache Hadoop uživatelské jméno a heslo v clusteru.

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net/"), "USERNAME", "PASSWORD");
client = new PhoenixClient(credentials);
```

Položku název_clusteru nahraďte názvem clusteru HDInsight HBA a uživatelské jméno a heslo s přihlašovacími údaji Hadoop zadaným při vytváření clusteru. Výchozí uživatelské jméno Hadoop je **admin**.

## <a name="generate-unique-connection-identifier"></a>Generovat jedinečný identifikátor připojení

Chcete-li odeslat jednu nebo více požadavků na PQS, je třeba zahrnout jedinečný identifikátor připojení pro přidružení požadavků k tomuto připojení.

```csharp
string connId = Guid.NewGuid().ToString();
```

Každý příklad nejprve provede volání `OpenConnectionRequestAsync` metody a předání jedinečného identifikátoru připojení. Dále definujte `ConnectionProperties` a `RequestOptions` předejte tyto objekty a generovaný identifikátor připojení k `ConnectionSyncRequestAsync` metodě. Objekt PQS `ConnectionSyncRequest` pomáhá zajistit, aby měl klient i server konzistentní zobrazení vlastností databáze.

## <a name="connectionsyncrequest-and-its-connectionproperties"></a>ConnectionSyncRequest a jeho ConnectionProperties

Pro volání  `ConnectionSyncRequestAsync` , předání `ConnectionProperties` objektu.

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
| Automatický zápis | Logická hodnota, která označuje, jestli `autoCommit` je pro transakce v Phoenixu povolený. |
| ReadOnly | Logická hodnota, která označuje, zda je připojení jen pro čtení. |
| TransactionIsolation | Celé číslo, které označuje úroveň izolace transakce podle specifikace JDBC – viz následující tabulka.|
| Katalog | Název katalogu, který se má použít při načítání vlastností připojení. |
| Schéma | Název schématu, který má být použit při načítání vlastností připojení. |
| IsDirty | Logická hodnota, která označuje, zda byly vlastnosti změněny. |

Tady jsou `TransactionIsolation` hodnoty:

| Hodnota izolace | Popis |
| -- | -- |
| 0 | Transakce nejsou podporovány. |
| 1 | Může dojít k nezměněnému čtení, čtení bez opakování a k fiktivnímu čtení. |
| 2 | Nezměněné čtení se znemožňuje, ale může dojít k neopakovaným čtením a k operacím typu Fantom. |
| 4 | Znemožňují se čtení neopakujících se čtením, ale může dojít k fiktivnímu čtení. |
| 8 | Nezměněné čtení, čtení bez opakování a čtení s fiktivními záznamy jsou zabráněno. |

## <a name="create-a-new-table"></a>Vytvoření nové tabulky

HBA, stejně jako jakékoli jiné RDBMS, ukládají data v tabulkách. Phoenix používá standardní dotazy SQL k vytváření nových tabulek a při definování primárních typů klíčů a sloupců.

V tomto příkladu a všech pozdějších příkladech použijte instanci `PhoenixClient` objektu, jak je definováno v části [Vytvoření nového objektu PhoenixClient](#instantiate-new-phoenixclient-object).

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

Předchozí příklad vytvoří novou tabulku s názvem `Customers` pomocí `IF NOT EXISTS` Možnosti. `CreateStatementRequestAsync`Volání vytvoří nový příkaz na serveru Avitica (PQS). `finally`Blok ukončí vrácenou `CreateStatementResponse` a `OpenConnectionResponse` objekty.

## <a name="insert-data-individually"></a>Vkládat data jednotlivě

V tomto příkladu se zobrazuje jednotlivá data vložení, odkazování na `List<string>` kolekci zkratek států a oblastí:

```csharp
var states = new List<string> { "AL", "AK", "AS", "AZ", "AR", "CA", "CO", "CT", "DE", "DC", "FM", "FL", "GA", "GU", "HI", "ID", "IL", "IN", "IA", "KS", "KY", "LA", "ME", "MH", "MD", "MA", "MI", "MN", "MS", "MO", "MT", "NE", "NV", "NH", "NJ", "NM", "NY", "NC", "ND", "MP", "OH", "OK", "OR", "PW", "PA", "PR", "RI", "SC", "SD", "TN", "TX", "UT", "VT", "VI", "VA", "WA", "WV", "WI", "WY" };
```

`StateProvince`Hodnota sloupce tabulky bude použita v pozdější operaci výběru.

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

Struktura pro provedení příkazu INSERT je podobná vytvoření nové tabulky. Na konci `try` bloku je transakce explicitně potvrzena. V tomto příkladu se opakuje operace INSERT Transaction 300 krát. Následující příklad ukazuje efektivnější proces dávkového vkládání.

## <a name="batch-insert-data"></a>Dávková vložení dat

Následující kód je téměř totožný s kódem pro vkládání dat jednotlivě. V tomto příkladu se použije `UpdateBatch` objekt ve volání na `ExecuteBatchRequestAsync` místo opakovaného volání `ExecuteRequestAsync` s připraveným příkazem.

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

V jednom testovacím prostředí trvalo vkládání 300 nových záznamů téměř 2 minuty. Naproti tomu vkládání 300 záznamů do dávky se vyžaduje jenom 6 sekund.

## <a name="select-data"></a>Výběr dat

Tento příklad ukazuje, jak znovu použít jedno připojení pro spuštění více dotazů:

1. Výběr všech záznamů a načtení zbývajících záznamů po vrácení výchozího maxima 100.
2. K načtení jednoho skalárního výsledku použijte příkaz SELECT s celkovým počtem řádků.
3. Spusťte příkaz SELECT, který vrátí celkový počet zákazníků na stav nebo oblast.

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

Výstup `select` příkazů by měl být následující výsledek:

```output
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
* [Použití sady REST SDK pro Apache HBA](apache-hbase-rest-sdk.md)
