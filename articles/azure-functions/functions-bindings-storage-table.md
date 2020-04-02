---
title: Vazby úložiště Azure Table pro funkce Azure
description: Zjistěte, jak používat vazby úložiště Azure Table ve funkcích Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.openlocfilehash: 1aa3537679ee37cbc6085344d2f31ae4043d32bb
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520673"
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Vazby úložiště Azure Table pro funkce Azure

Tento článek vysvětluje, jak pracovat s vazbami úložiště Azure Table v Azure Functions. Funkce Azure podporuje vstupní a výstupní vazby pro úložiště Azure Table.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Balíčky - Funkce 1.x

Vazby úložiště tabulky jsou k dispozici v balíčku [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet verze 2.x. Zdrojový kód pro balíček je v úložišti [GitHub azure-webjobs-sdk.](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Table)

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x-and-higher"></a>Balíčky - Funkce 2.x a vyšší

Vazby úložiště tabulky jsou k dispozici v balíčku [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet verze 3.x. Zdrojový kód pro balíček je v úložišti [GitHub azure-webjobs-sdk.](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables)

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="input"></a>Vstup

Ke čtení tabulky v účtu Azure Storage použijte vstupní vazbu úložiště Tabulky Azure.

# <a name="c"></a>[C#](#tab/csharp)

### <a name="one-entity"></a>Jedna entita

Následující příklad ukazuje [c# funkce,](functions-dotnet-class-library.md) která čte jeden řádek tabulky. Pro každý záznam vložený do tabulky se spustí funkce.

Hodnota klíče řádku {queueTrigger}označuje, že klíč řádku pochází z řetězce zprávy fronty.

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition", "{queueTrigger}")] MyPoco poco, 
        ILogger log)
    {
        log.LogInformation($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
    }
}
```

### <a name="iqueryable"></a>IQueryable

Následující příklad ukazuje [funkci C#,](functions-dotnet-class-library.md) která čte `MyPoco` více řádků `TableEntity`tabulky, kde je třída odvozena od .

```csharp
public class TableStorage
{
    public class MyPoco : TableEntity
    {
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition")] IQueryable<MyPoco> pocos, 
        ILogger log)
    {
        foreach (MyPoco poco in pocos)
        {
            log.LogInformation($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
        }
    }
}
```

### <a name="cloudtable"></a>Cloudtable

`IQueryable`není podporována v [modulu runtime Funkce v2](functions-versions.md). Alternativou je použití `CloudTable` parametru metody ke čtení tabulky pomocí sady Azure Storage SDK. Tady je příklad funkce, která se dotazuje na tabulku protokolu Azure Functions:

```csharp
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using Microsoft.WindowsAzure.Storage.Table;
using System;
using System.Threading.Tasks;

namespace FunctionAppCloudTable2
{
    public class LogEntity : TableEntity
    {
        public string OriginalName { get; set; }
    }
    public static class CloudTableDemo
    {
        [FunctionName("CloudTableDemo")]
        public static async Task Run(
            [TimerTrigger("0 */1 * * * *")] TimerInfo myTimer, 
            [Table("AzureWebJobsHostLogscommon")] CloudTable cloudTable,
            ILogger log)
        {
            log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");

            TableQuery<LogEntity> rangeQuery = new TableQuery<LogEntity>().Where(
                TableQuery.CombineFilters(
                    TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, 
                        "FD2"),
                    TableOperators.And,
                    TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, 
                        "t")));

            // Execute the query and loop through the results
            foreach (LogEntity entity in 
                await cloudTable.ExecuteQuerySegmentedAsync(rangeQuery, null))
            {
                log.LogInformation(
                    $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
            }
        }
    }
}
```

Další informace o tom, jak používat CloudTable, najdete [v tématu Začínáme s úložištěm tabulek Azure](../cosmos-db/table-storage-how-to-use-dotnet.md).

Pokud se pokusíte `CloudTable` vytvořit vazbu a zobrazí se chybová zpráva, ujistěte se, že máte odkaz na [správnou verzi sady Storage SDK](#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

### <a name="one-entity"></a>Jedna entita

Následující příklad ukazuje vstupní vazbu tabulky v souboru *function.json* a kódu [skriptu Jazyka C#,](functions-reference-csharp.md) který vazbu používá. Funkce používá aktivační událost fronty ke čtení jednoho řádku tabulky. 

Soubor *function.json* určuje `partitionKey` a `rowKey`a . Hodnota `rowKey` {queueTrigger}označuje, že klíč řádku pochází z řetězce zprávy fronty.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

[Konfigurační](#input---configuration) část vysvětluje tyto vlastnosti.

Zde je kód skriptu C#:

```csharp
public static void Run(string myQueueItem, Person personEntity, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    log.LogInformation($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

### <a name="iqueryable"></a>IQueryable

Následující příklad ukazuje vstupní vazbu tabulky v souboru *function.json* a kódu [skriptu Jazyka C#,](functions-reference-csharp.md) který vazbu používá. Funkce čte entity pro klíč oddílu, který je určen ve zprávě fronty.

Zde je *soubor function.json:*

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnectionAppSetting",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

[Konfigurační](#input---configuration) část vysvětluje tyto vlastnosti.

Kód skriptu Jazyka C# přidá odkaz na sadu Azure Storage `TableEntity`SDK tak, aby typ entity můžete odvodit z :

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;
using Microsoft.Extensions.Logging;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.LogInformation($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
```

### <a name="cloudtable"></a>Cloudtable

`IQueryable`není podporována v modulu runtime Funkce pro [verze 2.x a vyšší)](functions-versions.md). Alternativou je použití `CloudTable` parametru metody ke čtení tabulky pomocí sady Azure Storage SDK. Tady je příklad funkce, která se dotazuje na tabulku protokolu Azure Functions:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 */1 * * * *"
    },
    {
      "name": "cloudTable",
      "type": "table",
      "connection": "AzureWebJobsStorage",
      "tableName": "AzureWebJobsHostLogscommon",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;
using System;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static async Task Run(TimerInfo myTimer, CloudTable cloudTable, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");

    TableQuery<LogEntity> rangeQuery = new TableQuery<LogEntity>().Where(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, 
            "FD2"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, 
            "a")));

    // Execute the query and loop through the results
    foreach (LogEntity entity in 
    await cloudTable.ExecuteQuerySegmentedAsync(rangeQuery, null))
    {
        log.LogInformation(
            $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
    }
}

public class LogEntity : TableEntity
{
    public string OriginalName { get; set; }
}
```

Další informace o tom, jak používat CloudTable, najdete [v tématu Začínáme s úložištěm tabulek Azure](../cosmos-db/table-storage-how-to-use-dotnet.md).

Pokud se pokusíte `CloudTable` vytvořit vazbu a zobrazí se chybová zpráva, ujistěte se, že máte odkaz na [správnou verzi sady Storage SDK](#azure-storage-sdk-version-in-functions-1x).


# <a name="javascript"></a>[JavaScript](#tab/javascript)

Následující příklad ukazuje vstupní vazbu tabulky v souboru *function.json* a [kód Jazyka JavaScript,](functions-reference-node.md) který vazbu používá. Funkce používá aktivační událost fronty ke čtení jednoho řádku tabulky. 

Soubor *function.json* určuje `partitionKey` a `rowKey`a . Hodnota `rowKey` {queueTrigger}označuje, že klíč řádku pochází z řetězce zprávy fronty.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

[Konfigurační](#input---configuration) část vysvětluje tyto vlastnosti.

Zde je kód JavaScript:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Řádek jedné tabulky 

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "messageJSON",
      "type": "table",
      "tableName": "messages",
      "partitionKey": "message",
      "rowKey": "{id}",
      "connection": "AzureWebJobsStorage",
      "direction": "in"
    },
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ],
      "route": "messages/{id}"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ],
  "disabled": false
}
```

```python
import json

import azure.functions as func

def main(req: func.HttpRequest, messageJSON) -> func.HttpResponse:

    message = json.loads(messageJSON)
    return func.HttpResponse(f"Table row: {messageJSON}")
```

# <a name="java"></a>[Java](#tab/java)

Následující příklad ukazuje funkci aktivovanou protokolem HTTP, která vrací seznam objektů, které jsou v zadaném oddílu v úložišti tabulky. V příkladu je klíč oddílu extrahován z trasy http a tableName a připojení jsou z nastavení funkce. 

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() { return this.PartitionKey; }
    public void setPartitionKey(String key) { this.PartitionKey = key; }
    public String getRowKey() { return this.RowKey; }
    public void setRowKey(String key) { this.RowKey = key; }
    public String getName() { return this.Name; }
    public void setName(String name) { this.Name = name; }
}

@FunctionName("getPersonsByPartitionKey")
public Person[] get(
        @HttpTrigger(name = "getPersons", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="persons/{partitionKey}") HttpRequestMessage<Optional<String>> request,
        @BindingName("partitionKey") String partitionKey,
        @TableInput(name="persons", partitionKey="{partitionKey}", tableName="%MyTableName%", connection="MyConnectionString") Person[] persons,
        final ExecutionContext context) {

    context.getLogger().info("Got query for person related to persons with partition key: " + partitionKey);

    return persons;
}
```

TableInput anotace můžete také extrahovat vazby z těla json požadavku, jako je následující příklad ukazuje.

```java
@FunctionName("GetPersonsByKeysFromRequest")
public HttpResponseMessage get(
        @HttpTrigger(name = "getPerson", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="query") HttpRequestMessage<Optional<String>> request,
        @TableInput(name="persons", partitionKey="{partitionKey}", rowKey = "{rowKey}", tableName="%MyTableName%", connection="MyConnectionString") Person person,
        final ExecutionContext context) {

    if (person == null) {
        return request.createResponseBuilder(HttpStatus.NOT_FOUND)
                    .body("Person not found.")
                    .build();
    }

    return request.createResponseBuilder(HttpStatus.OK)
                    .header("Content-Type", "application/json")
                    .body(person)
                    .build();
}
```

Následující příklady používá Filtr k dotazování na osoby s určitým názvem v tabulce Azure a omezuje počet možných shod na 10 výsledků.

```java
@FunctionName("getPersonsByName")
public Person[] get(
        @HttpTrigger(name = "getPersons", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="filter/{name}") HttpRequestMessage<Optional<String>> request,
        @BindingName("name") String name,
        @TableInput(name="persons", filter="Name eq '{name}'", take = "10", tableName="%MyTableName%", connection="MyConnectionString") Person[] persons,
        final ExecutionContext context) {

    context.getLogger().info("Got query for person related to persons with name: " + name);

    return persons;
}
```

---

## <a name="input---attributes-and-annotations"></a>Vstup - atributy a poznámky

# <a name="c"></a>[C#](#tab/csharp)

 V [knihovnách tříd jazyka C#](functions-dotnet-class-library.md)použijte ke konfiguraci vstupní vazby tabulky následující atributy:

* [Atribut tableattribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)

  Konstruktor atributu přebírá název tabulky, klíč oddílu a klíč řádku. Atribut lze použít na `out` parametr nebo na vrácenou hodnotu funkce, jak je znázorněno v následujícím příkladu:

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco, 
      ILogger log)
  {
      ...
  }
  ```

  Vlastnost můžete `Connection` nastavit tak, aby určila účet úložiště, který se má použít, jak je znázorněno v následujícím příkladu:

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}", Connection = "StorageConnectionAppSetting")] MyPoco poco, 
      ILogger log)
  {
      ...
  }
  ```

  Úplný příklad naleznete v tématu Input - C# příklad.

* [Atribut účtu úložiště](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Poskytuje další způsob, jak zadat účet úložiště, který se má použít. Konstruktor přebírá název nastavení aplikace, která obsahuje připojovací řetězec úložiště. Atribut lze použít na úrovni parametru, metody nebo třídy. Následující příklad ukazuje úroveň třídy a úroveň metody:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("TableInput")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

Účet úložiště, který se má použít, se určuje v následujícím pořadí:

* Vlastnost `Table` atributu. `Connection`
* Atribut `StorageAccount` použitý na stejný parametr `Table` jako atribut.
* Atribut `StorageAccount` použitý pro funkci.
* Atribut `StorageAccount` použitý pro třídu.
* Výchozí účet úložiště pro aplikaci funkce ("AzureWebJobsStorage" nastavení aplikace).

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Atributy nejsou podporovány skriptem jazyka C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atributy nejsou podporovány javascriptem.

# <a name="python"></a>[Python](#tab/python)

Atributy nejsou podporovány Pythonem.

# <a name="java"></a>[Java](#tab/java)

V [knihovně runtime funkcí Javy](/java/api/overview/azure/functions/runtime)použijte poznámku `@TableInput` k parametrům, jejichž hodnota by pocházela z úložiště tabulek.  Tuto poznámku lze použít s nativními typy Jazyka Java, `Optional<T>`POJOnebo hodnotami s možnou hodnotou s hodnotou s nulou pomocí .

---

## <a name="input---configuration"></a>Vstup - konfigurace

Následující tabulka vysvětluje vlastnosti konfigurace vazby, které jste nastavili `Table` v souboru *function.json* a atributu.

|vlastnost function.json | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**Typ** | neuvedeno | Musí být `table`nastavena na . Tato vlastnost se nastaví automaticky při vytváření vazby na webu Azure Portal.|
|**direction** | neuvedeno | Musí být `in`nastavena na . Tato vlastnost se nastaví automaticky při vytváření vazby na webu Azure Portal. |
|**Jméno** | neuvedeno | Název proměnné, která představuje tabulku nebo entitu v kódu funkce. | 
|**Tablename** | **TableName** | Název tabulky.| 
|**partitionKey** | **PartitionKey** |Nepovinný parametr. Klíč oddílu entity tabulky ke čtení. Pokyny k použití této vlastnosti najdete v části [použití.](#input---usage)| 
|**řádekKlíč** |**RowKey** | Nepovinný parametr. Klíč řádku entity tabulky ke čtení. Pokyny k použití této vlastnosti najdete v části [použití.](#input---usage)| 
|**vzít** |**Take** | Nepovinný parametr. Maximální počet entit ke čtení v jazyce JavaScript. Pokyny k použití této vlastnosti najdete v části [použití.](#input---usage)| 
|**Filtr** |**Filtr** | Nepovinný parametr. Výraz filtru OData pro vstup tabulky v Jazyce JavaScript. Pokyny k použití této vlastnosti najdete v části [použití.](#input---usage)| 
|**Připojení** |**Připojení** | Název nastavení aplikace, která obsahuje připojovací řetězec úložiště pro tuto vazbu. Nastavení může být název nastavení předponou aplikace "AzureWebJobs" nebo název připojovacího řetězce. Například pokud je název nastavení "AzureWebJobsMyStorage", můžete zadat "MyStorage" zde. Functions runtime bude automaticky hledat nastavení aplikace s názvem "AzureWebJobsMyStorage". Pokud necháte `connection` prázdné, spustí se s funkcí, která použije výchozí `AzureWebJobsStorage`připojovací řetězec úložiště v nastavení aplikace s názvem .|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Vstup - využití

# <a name="c"></a>[C#](#tab/csharp)

* **Čtení jednoho řádku**

  Nastavit `partitionKey` `rowKey`a . Přístup k datům tabulky `T <paramName>`pomocí parametru metody . Ve skriptu `paramName` jazyka C# je `name` hodnota zadaná ve vlastnosti *function.json*. `T`je obvykle typ, který `ITableEntity` implementuje `TableEntity`nebo je odvozen od . `filter` Vlastnosti `take` a nejsou použity v tomto scénáři.

* **Čtení jednoho nebo více řádků**

  Přístup k datům tabulky `IQueryable<T> <paramName>`pomocí parametru metody . Ve skriptu `paramName` jazyka C# je `name` hodnota zadaná ve vlastnosti *function.json*. `T`musí být typ, `ITableEntity` který implementuje nebo je odvozen od `TableEntity`. Můžete použít `IQueryable` metody k provedení jakékoli filtrování požadované. V `partitionKey`tomto scénáři se nepoužívají vlastnosti , `take` a . `rowKey` `filter`  

  > [!NOTE]
  > `IQueryable`není podporována v [modulu runtime Funkce v2](functions-versions.md). Alternativou je [použití parametru metody CloudTable paramName](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) ke čtení tabulky pomocí sady Azure Storage SDK. Pokud se pokusíte `CloudTable` vytvořit vazbu a zobrazí se chybová zpráva, ujistěte se, že máte odkaz na [správnou verzi sady Storage SDK](#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

* **Čtení jednoho řádku**

  Nastavit `partitionKey` `rowKey`a . Přístup k datům tabulky `T <paramName>`pomocí parametru metody . Ve skriptu `paramName` jazyka C# je `name` hodnota zadaná ve vlastnosti *function.json*. `T`je obvykle typ, který `ITableEntity` implementuje `TableEntity`nebo je odvozen od . `filter` Vlastnosti `take` a nejsou použity v tomto scénáři.

* **Čtení jednoho nebo více řádků**

  Přístup k datům tabulky `IQueryable<T> <paramName>`pomocí parametru metody . Ve skriptu `paramName` jazyka C# je `name` hodnota zadaná ve vlastnosti *function.json*. `T`musí být typ, `ITableEntity` který implementuje nebo je odvozen od `TableEntity`. Můžete použít `IQueryable` metody k provedení jakékoli filtrování požadované. V `partitionKey`tomto scénáři se nepoužívají vlastnosti , `take` a . `rowKey` `filter`  

  > [!NOTE]
  > `IQueryable`není podporována v [modulu runtime Funkce v2](functions-versions.md). Alternativou je [použití parametru metody CloudTable paramName](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) ke čtení tabulky pomocí sady Azure Storage SDK. Pokud se pokusíte `CloudTable` vytvořit vazbu a zobrazí se chybová zpráva, ujistěte se, že máte odkaz na [správnou verzi sady Storage SDK](#azure-storage-sdk-version-in-functions-1x).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Nastavte `filter` vlastnosti a. `take` `partitionKey` Nenastavuj `rowKey`nebo . Přístup k entitě vstupní tabulky `context.bindings.<BINDING_NAME>`(nebo entitami) pomocí . Deserializované objekty `RowKey` `PartitionKey` mají a vlastnosti.

# <a name="python"></a>[Python](#tab/python)

Data tabulky jsou předána funkci jako řetězec JSON. De-serialize zprávy voláním, `json.loads` jak je znázorněno ve vstupním [příkladu](#input).

# <a name="java"></a>[Java](#tab/java)

Atribut [TableInput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.tableinput) umožňuje přístup k řádku tabulky, který spustil funkci.

---

## <a name="output"></a>Výstup

K zápisu entit do tabulky v účtu Azure Storage použijte výstupní vazbu úložiště Tabulky Azure.

> [!NOTE]
> Tato výstupní vazba nepodporuje aktualizaci existujících entit. Pomocí `TableOperation.Replace` operace [ze sady Azure Storage SDK](../cosmos-db/tutorial-develop-table-dotnet.md#delete-an-entity) aktualizujte existující entitu.

# <a name="c"></a>[C#](#tab/csharp)

Následující příklad ukazuje [funkci Jazyka C#,](functions-dotnet-class-library.md) která používá aktivační událost HTTP k zápisu jednoho řádku tabulky. 

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableOutput")]
    [return: Table("MyTable")]
    public static MyPoco TableOutput([HttpTrigger] dynamic input, ILogger log)
    {
        log.LogInformation($"C# http trigger function processed: {input.Text}");
        return new MyPoco { PartitionKey = "Http", RowKey = Guid.NewGuid().ToString(), Text = input.Text };
    }
}
```

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Následující příklad ukazuje výstupní vazbu tabulky v souboru *function.json* a kódu [skriptu Jazyka C#,](functions-reference-csharp.md) který vazbu používá. Funkce zapisuje více entit tabulky.

Zde je *soubor function.json:*

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

[Konfigurační](#output---configuration) část vysvětluje tyto vlastnosti.

Zde je kód skriptu C#:

```csharp
public static void Run(string input, ICollector<Person> tableBinding, ILogger log)
{
    for (int i = 1; i < 10; i++)
        {
            log.LogInformation($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Následující příklad ukazuje výstupní vazbu tabulky v souboru *function.json* a [funkci JavaScriptu,](functions-reference-node.md) která vazbu používá. Funkce zapisuje více entit tabulky.

Zde je *soubor function.json:*

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

[Konfigurační](#output---configuration) část vysvětluje tyto vlastnosti.

Zde je kód JavaScript:

```javascript
module.exports = function (context) {

    context.bindings.tableBinding = [];

    for (var i = 1; i < 10; i++) {
        context.bindings.tableBinding.push({
            PartitionKey: "Test",
            RowKey: i.toString(),
            Name: "Name " + i
        });
    }
    
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Následující příklad ukazuje, jak používat table storage výstupní vazbu. `table` Vazba je konfigurována v *souboru function.json* přiřazením hodnot , `name` `tableName`, `partitionKey`a `connection`:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "message",
      "type": "table",
      "tableName": "messages",
      "partitionKey": "message",
      "connection": "AzureWebJobsStorage",
      "direction": "out"
    },
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

Následující funkce generuje jedinečné uui `rowKey` pro hodnotu a zachová zprávu do úložiště tabulky.

```python
import logging
import uuid
import json

import azure.functions as func

def main(req: func.HttpRequest, message: func.Out[str]) -> func.HttpResponse:

    rowKey = str(uuid.uuid4())

    data = {
        "Name": "Output binding message",
        "PartitionKey": "message",
        "RowKey": rowKey
    }

    message.set(json.dumps(data))

    return func.HttpResponse(f"Message created with the rowKey: {rowKey}")
```

# <a name="java"></a>[Java](#tab/java)

Následující příklad ukazuje funkci Java, která používá aktivační událost HTTP k zápisu jednoho řádku tabulky.

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() {return this.PartitionKey;}
    public void setPartitionKey(String key) {this.PartitionKey = key; }
    public String getRowKey() {return this.RowKey;}
    public void setRowKey(String key) {this.RowKey = key; }
    public String getName() {return this.Name;}
    public void setName(String name) {this.Name = name; }
}

public class AddPerson {

    @FunctionName("addPerson")
    public HttpResponseMessage get(
            @HttpTrigger(name = "postPerson", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION, route="persons/{partitionKey}/{rowKey}") HttpRequestMessage<Optional<Person>> request,
            @BindingName("partitionKey") String partitionKey,
            @BindingName("rowKey") String rowKey,
            @TableOutput(name="person", partitionKey="{partitionKey}", rowKey = "{rowKey}", tableName="%MyTableName%", connection="MyConnectionString") OutputBinding<Person> person,
            final ExecutionContext context) {

        Person outPerson = new Person();
        outPerson.setPartitionKey(partitionKey);
        outPerson.setRowKey(rowKey);
        outPerson.setName(request.getBody().get().getName());

        person.setValue(outPerson);

        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(outPerson)
                        .build();
    }
}
```

Následující příklad ukazuje funkci Java, která používá aktivační událost HTTP k zápisu více řádků tabulky.

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() {return this.PartitionKey;}
    public void setPartitionKey(String key) {this.PartitionKey = key; }
    public String getRowKey() {return this.RowKey;}
    public void setRowKey(String key) {this.RowKey = key; }
    public String getName() {return this.Name;}
    public void setName(String name) {this.Name = name; }
}

public class AddPersons {

    @FunctionName("addPersons")
    public HttpResponseMessage get(
            @HttpTrigger(name = "postPersons", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION, route="persons/") HttpRequestMessage<Optional<Person[]>> request,
            @TableOutput(name="person", tableName="%MyTableName%", connection="MyConnectionString") OutputBinding<Person[]> persons,
            final ExecutionContext context) {

        persons.setValue(request.getBody().get());

        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(request.getBody().get())
                        .build();
    }
}
```

---

## <a name="output---attributes-and-annotations"></a>Výstup - atributy a poznámky

# <a name="c"></a>[C#](#tab/csharp)

V [knihovnách tříd jazyka C#](functions-dotnet-class-library.md)použijte [atribut TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs).

Konstruktor atributu přebírá název tabulky. Atribut lze použít na `out` parametr nebo na vrácenou hodnotu funkce, jak je znázorněno v následujícím příkladu:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

Vlastnost můžete `Connection` nastavit tak, aby určila účet úložiště, který se má použít, jak je znázorněno v následujícím příkladu:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable", Connection = "StorageConnectionAppSetting")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

Úplný příklad naleznete v [tématu Output - C# example](#output).

`StorageAccount` Atribut můžete použít k určení účtu úložiště na úrovni třídy, metody nebo parametru. Další informace naleznete v tématu [Input - attributes](#input---attributes-and-annotations).

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Atributy nejsou podporovány skriptem jazyka C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atributy nejsou podporovány javascriptem.

# <a name="python"></a>[Python](#tab/python)

Atributy nejsou podporovány Pythonem.

# <a name="java"></a>[Java](#tab/java)

V [knihovně runtime funkcí jazyka Java](/java/api/overview/azure/functions/runtime)použijte poznámku [TableOutput](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/TableOutput.java/) o parametrech k zápisu hodnot do úložiště tabulek.

Viz [příklad pro více podrobností](#output).

---

## <a name="output---configuration"></a>Výstup - konfigurace

Následující tabulka vysvětluje vlastnosti konfigurace vazby, které jste nastavili `Table` v souboru *function.json* a atributu.

|vlastnost function.json | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**Typ** | neuvedeno | Musí být `table`nastavena na . Tato vlastnost se nastaví automaticky při vytváření vazby na webu Azure Portal.|
|**direction** | neuvedeno | Musí být `out`nastavena na . Tato vlastnost se nastaví automaticky při vytváření vazby na webu Azure Portal. |
|**Jméno** | neuvedeno | Název proměnné použitý v kódu funkce, který představuje tabulku nebo entitu. Nastavte `$return` tak, aby odkazovalna vrácenou hodnotu funkce.| 
|**Tablename** |**TableName** | Název tabulky.| 
|**partitionKey** |**PartitionKey** | Klíč oddílu entity tabulky pro zápis. Pokyny k použití této vlastnosti najdete v [části použití.](#output---usage)| 
|**řádekKlíč** |**RowKey** | Klíč řádku entity tabulky k zápisu. Pokyny k použití této vlastnosti najdete v [části použití.](#output---usage)| 
|**Připojení** |**Připojení** | Název nastavení aplikace, která obsahuje připojovací řetězec úložiště pro tuto vazbu. Pokud název nastavení aplikace začíná "AzureWebJobs", můžete zadat pouze zbytek názvu zde. Například pokud nastavíte `connection` na "MyStorage", funkce runtime hledá nastavení aplikace s názvem "MyStorage". Pokud necháte `connection` prázdné, spustí se s funkcí, která použije výchozí `AzureWebJobsStorage`připojovací řetězec úložiště v nastavení aplikace s názvem .|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Výstup - využití

# <a name="c"></a>[C#](#tab/csharp)

Přístup k entitě výstupní `ICollector<T> paramName` tabulky `IAsyncCollector<T> paramName` `T` pomocí `PartitionKey` parametru metody nebo kde obsahuje vlastnosti a. `RowKey` Tyto vlastnosti jsou často `ITableEntity` doprovázeny `TableEntity`implementací nebo děděním .

Případně můžete použít `CloudTable` parametr metody pro zápis do tabulky pomocí sady Azure Storage SDK. Pokud se pokusíte `CloudTable` vytvořit vazbu a zobrazí se chybová zpráva, ujistěte se, že máte odkaz na [správnou verzi sady Storage SDK](#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Přístup k entitě výstupní `ICollector<T> paramName` tabulky `IAsyncCollector<T> paramName` `T` pomocí `PartitionKey` parametru metody nebo kde obsahuje vlastnosti a. `RowKey` Tyto vlastnosti jsou často `ITableEntity` doprovázeny `TableEntity`implementací nebo děděním . Hodnota `paramName` je určena `name` ve vlastnosti *function.json*.

Případně můžete použít `CloudTable` parametr metody pro zápis do tabulky pomocí sady Azure Storage SDK. Pokud se pokusíte `CloudTable` vytvořit vazbu a zobrazí se chybová zpráva, ujistěte se, že máte odkaz na [správnou verzi sady Storage SDK](#azure-storage-sdk-version-in-functions-1x).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Přístup k výstupní `context.bindings.<name>` události `<name>` pomocí kde je `name` hodnota zadaná ve vlastnosti *function.json*.

# <a name="python"></a>[Python](#tab/python)

Existují dvě možnosti pro vynětí zprávy řádku úložiště tabulky z funkce:

- **Vrácená hodnota** `name` : Nastavte vlastnost *function.json* na `$return`. Při této konfiguraci je vrácená hodnota funkce zachována jako řádek úložiště tabulky.

- **Imperativní**: Předají hodnotu [metodě set](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) parametru deklarovaného [Out](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python) jako out typ. Hodnota předaná je `set` trvalé jako zpráva centra událostí.

# <a name="java"></a>[Java](#tab/java)

Existují dvě možnosti pro výstup řádku úložiště tabulky z funkce pomocí anotace [TableStorageOutput:](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.tableoutput?view=azure-java-stablet)

- **Vrácená hodnota**: Použitím poznámky na samotnou funkci je vrácená hodnota funkce zachována jako řádek úložiště tabulky.

- **Imperativní**: Chcete-li explicitně nastavit hodnotu zprávy, použijte poznámku na konkrétní parametr typu [`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding), kde `T` obsahuje vlastnosti `PartitionKey` a. `RowKey` Tyto vlastnosti jsou často `ITableEntity` doprovázeny `TableEntity`implementací nebo děděním .

---

## <a name="exceptions-and-return-codes"></a>Výjimky a návratové kódy

| Vazba | Odkaz |
|---|---|
| Table | [Kódy chyb tabulky](https://docs.microsoft.com/rest/api/storageservices/fileservices/table-service-error-codes) |
| Blob, Tabulka, Fronta | [Chybové kódy úložiště](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, Tabulka, Fronta | [Řešení potíží](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o aktivačních událostech a vazbách funkcí Azure](functions-triggers-bindings.md)
