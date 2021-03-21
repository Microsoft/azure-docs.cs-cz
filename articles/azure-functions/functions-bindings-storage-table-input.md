---
title: Vstupní vazby služby Azure Table Storage pro Azure Functions
description: Naučte se používat vstupní vazby Azure Table Storage v Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 4fc2426189384856d2d2e95887cdabd2f9e9ebea
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98033774"
---
# <a name="azure-table-storage-input-bindings-for-azure-functions"></a>Vstupní vazby služby Azure Table Storage pro Azure Functions

Pomocí vstupní vazby Azure Table Storage si můžete přečíst tabulku v účtu Azure Storage.

## <a name="example"></a>Příklad

# <a name="c"></a>[C#](#tab/csharp)

### <a name="one-entity"></a>Jedna entita

Následující příklad ukazuje [funkci jazyka C#](functions-dotnet-class-library.md) , která čte jeden řádek tabulky. U každé zprávy odeslané do fronty se funkce aktivuje.

Hodnota klíče řádku {queueTrigger} označuje, že klíč řádku pochází z řetězce zprávy fronty.

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

### <a name="cloudtable"></a>CloudTable

`CloudTable` je podporován pouze v [modulech runtime Functions v2 a vyšší](functions-versions.md).

`CloudTable`K načtení tabulky pomocí sady Azure Storage SDK použijte parametr metody. Tady je příklad funkce, která se dotazuje Azure Functions tabulce protokolu:

```csharp
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.Cosmos.Table;
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

Další informace o tom, jak používat cloudovou tabulku, najdete v tématu Začínáme [s Azure Table Storage](../cosmos-db/tutorial-develop-table-dotnet.md).

Pokud se pokusíte vytvořit navázání `CloudTable` a získat chybovou zprávu, ujistěte se, že máte odkaz na [správnou verzi sady SDK úložiště](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x).

### <a name="iqueryable"></a>IQueryable

`IQueryable` je podporován pouze v [modulu runtime Functions v1](functions-versions.md).

Následující příklad ukazuje [funkci jazyka C#](functions-dotnet-class-library.md) , která čte více řádků tabulky, ze kterých je `MyPoco` Třída odvozena `TableEntity` .

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

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

### <a name="one-entity"></a>Jedna entita

Následující příklad ukazuje vstupní vazbu tabulky v *function.js* souboru a kódu [skriptu jazyka C#](functions-reference-csharp.md) , který používá vazbu. Funkce používá Trigger fronty ke čtení jednoho řádku tabulky. 

*function.jsv* souboru Určuje `partitionKey` a `rowKey` . `rowKey`Hodnota {queueTrigger} označuje, že klíč řádku pochází z řetězce zprávy fronty.

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

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#configuration) .

Tady je kód skriptu jazyka C#:

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

### <a name="cloudtable"></a>CloudTable

`IQueryable` není podporováno v modulu runtime Functions pro [verze 2. x a vyšší)](functions-versions.md). Alternativou je použití `CloudTable` parametru metody pro čtení tabulky pomocí sady Azure Storage SDK. Tady je příklad funkce, která se dotazuje Azure Functions tabulce protokolu:

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

Další informace o tom, jak používat cloudovou tabulku, najdete v tématu Začínáme [s Azure Table Storage](../cosmos-db/tutorial-develop-table-dotnet.md).

Pokud se pokusíte vytvořit navázání `CloudTable` a získat chybovou zprávu, ujistěte se, že máte odkaz na [správnou verzi sady SDK úložiště](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x).

### <a name="iqueryable"></a>IQueryable

Následující příklad ukazuje vstupní vazbu tabulky v *function.js* souboru a kódu [skriptu jazyka C#](functions-reference-csharp.md) , který používá vazbu. Funkce přečte entity pro klíč oddílu, který je určen ve zprávě fronty.

Tady je *function.js* souboru:

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

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#configuration) .

Kód skriptu jazyka C# přidá odkaz na sadu Azure Storage SDK, takže typ entity může odvozovat z `TableEntity` :

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

# <a name="java"></a>[Java](#tab/java)

Následující příklad ukazuje funkci aktivovanou protokolem HTTP, která vrací seznam objektů Person, kteří jsou v zadaném oddílu v úložišti tabulek. V tomto příkladu se klíč oddílu extrahuje z trasy HTTP a tableName a připojení jsou z nastavení funkce. 

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

Anotace TableInput může také extrahovat vazby z těla JSON požadavku, podobně jako v následujícím příkladu.

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

Následující příklad používá filtr k dotazování na osoby s určitým názvem v tabulce Azure a omezuje počet možných shod na 10 výsledků.

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Následující příklad ukazuje vstupní vazbu tabulky v *function.js* souboru a [kódu JavaScriptu](functions-reference-node.md) , který používá vazbu. Funkce používá Trigger fronty ke čtení jednoho řádku tabulky. 

*function.jsv* souboru Určuje `partitionKey` a `rowKey` . `rowKey`Hodnota {queueTrigger} označuje, že klíč řádku pochází z řetězce zprávy fronty.

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

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#configuration) .

Tady je kód JavaScriptu:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Následující funkce používá Trigger fronty ke čtení jednoho řádku tabulky jako vstupu do funkce.

V tomto příkladu konfigurace vazby Určuje explicitní hodnotu pro tabulku `partitionKey` a používá výraz k předání do `rowKey` . `rowKey`Výraz označuje, `{queueTrigger}` že klíč řádku pochází z řetězce zprávy fronty.

Konfigurace vazby v _function.jsna_:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "MyQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "PersonEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Kód PowerShellu v _run.ps1_:

```powershell
param($MyQueueItem, $PersonEntity, $TriggerMetadata)
Write-Host "PowerShell queue trigger function processed work item: $MyQueueItem"
Write-Host "Person entity name: $($PersonEntity.Name)"
```

# <a name="python"></a>[Python](#tab/python)

Následující funkce používá Trigger fronty ke čtení jednoho řádku tabulky jako vstupu do funkce.

V tomto příkladu určuje konfigurace vazby explicitní hodnotu pro tabulku `partitionKey` a používá výraz, který se předává do `rowKey` . `rowKey`Výraz `{id}` označuje, že klíč řádku pochází z řetězce zprávy fronty.

Konfigurace vazby v _function.js_ souboru:

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

Kód Pythonu v souboru *\_ \_ init \_ \_ . py* :

```python
import json

import azure.functions as func

def main(req: func.HttpRequest, messageJSON) -> func.HttpResponse:

    message = json.loads(messageJSON)
    return func.HttpResponse(f"Table row: {messageJSON}")
```

---

## <a name="attributes-and-annotations"></a>Atributy a poznámky

# <a name="c"></a>[C#](#tab/csharp)

 V [knihovnách tříd jazyka C#](functions-dotnet-class-library.md)použijte následující atributy ke konfiguraci vstupní vazby tabulky:

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)

  Konstruktor atributu vezme název tabulky, klíč oddílu a klíč řádku. Atribut lze použít pro `out` parametr nebo návratovou hodnotu funkce, jak je znázorněno v následujícím příkladu:

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

  Vlastnost můžete nastavit `Connection` tak, aby určovala účet úložiště, který se má použít, jak je znázorněno v následujícím příkladu:

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

  Úplný příklad naleznete v příkladu jazyka C#.

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Poskytuje jiný způsob určení účtu úložiště, který se má použít. Konstruktor převezme název nastavení aplikace, které obsahuje připojovací řetězec úložiště. Atribut lze použít na úrovni parametru, metody nebo třídy. Následující příklad ukazuje úroveň třídy a úroveň metody:

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

Účet úložiště, který se má použít, se určuje v tomto pořadí:

* `Table` `Connection` Vlastnost atributu
* `StorageAccount`Atribut aplikovaný na stejný parametr jako `Table` atribut.
* `StorageAccount`Atribut aplikovaný na funkci.
* `StorageAccount`Atribut aplikovaný na třídu.
* Výchozí účet úložiště pro aplikaci Function App (nastavení aplikace "AzureWebJobsStorage").

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Skripty jazyka C# nepodporují atributy.

# <a name="java"></a>[Java](#tab/java)

V [knihovně modulu runtime Functions jazyka Java](/java/api/overview/azure/functions/runtime)použijte `@TableInput` anotaci pro parametry, jejichž hodnota by pocházela z úložiště tabulek.  Tato poznámka se dá použít s nativními typy s možnou hodnotou null, Pojo nebo Nullable pomocí `Optional<T>` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atributy nejsou podporovány jazykem JavaScript.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell nepodporuje atributy.

# <a name="python"></a>[Python](#tab/python)

Python nepodporuje atributy.

---

## <a name="configuration"></a>Konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.jspro* soubor a `Table` atribut.

|function.jsvlastnost | Vlastnost atributu |Description|
|---------|---------|----------------------|
|**textový** | Není k dispozici | Musí být nastaven na hodnotu `table` . Tato vlastnost je nastavena automaticky při vytváření vazby v Azure Portal.|
|**směr** | Není k dispozici | Musí být nastaven na hodnotu `in` . Tato vlastnost je nastavena automaticky při vytváření vazby v Azure Portal. |
|**Jméno** | Není k dispozici | Název proměnné, která představuje tabulku nebo entitu v kódu funkce. | 
|**Tabulky** | **TableName** | Název tabulky| 
|**partitionKey** | **PartitionKey** |Nepovinný parametr. Klíč oddílu entity tabulky, která se má přečíst Návod, jak tuto vlastnost používat, najdete v části věnované [používání](#usage) .| 
|**rowKey** |**RowKey** | Nepovinný parametr. Klíč řádku entity tabulky, která se má přečíst Návod, jak tuto vlastnost používat, najdete v části věnované [používání](#usage) .| 
|**nezbytná** |**Nezbytná** | Nepovinný parametr. Maximální počet entit, které mají být načteny v jazyce JavaScript. Návod, jak tuto vlastnost používat, najdete v části věnované [používání](#usage) .| 
|**filtrovací** |**Filtr** | Nepovinný parametr. Výraz filtru OData pro vstup tabulky v JavaScriptu Návod, jak tuto vlastnost používat, najdete v části věnované [používání](#usage) .| 
|**vázán** |**Připojení** | Název nastavení aplikace, které obsahuje připojovací řetězec úložiště, který se má použít pro tuto vazbu. Nastavení může být název nastavení předpevněné aplikace "AzureWebJobs" nebo název připojovacího řetězce. Pokud je název vašeho nastavení například "AzureWebJobsMyStorage", můžete zde zadat "MyStorage". Modul runtime Functions automaticky vyhledá nastavení aplikace, které má název "AzureWebJobsMyStorage". Pokud necháte `connection` prázdné, modul runtime Functions použije výchozí připojovací řetězec úložiště v nastavení aplikace s názvem `AzureWebJobsStorage` .|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Využití

# <a name="c"></a>[C#](#tab/csharp)

* **Číst jeden řádek v**

  Nastavte `partitionKey` a `rowKey` . Přístup k datům tabulky pomocí parametru metody `T <paramName>` . Ve skriptu jazyka C# `paramName` je hodnota zadaná ve `name` vlastnosti *function.jsv*. `T` je obvykle typ, který implementuje `ITableEntity` nebo je odvozen z `TableEntity` . `filter`Vlastnosti a `take` se v tomto scénáři nepoužívají.

* **Čtení jednoho nebo více řádků**

  Přístup k datům tabulky pomocí parametru metody `IQueryable<T> <paramName>` . Ve skriptu jazyka C# `paramName` je hodnota zadaná ve `name` vlastnosti *function.jsv*. `T` musí se jednat o typ, který implementuje `ITableEntity` nebo odvodí z `TableEntity` . Můžete použít `IQueryable` metody k provedení libovolného filtrování. `partitionKey`Vlastnosti, `rowKey` , `filter` a `take` nejsou v tomto scénáři použity.  

  > [!NOTE]
  > `IQueryable` není podporován v [modulu runtime Functions v2](functions-versions.md). Alternativou je [použití parametru paramName metody cloudu](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) pro čtení tabulky pomocí sady SDK Azure Storage. Pokud se pokusíte vytvořit navázání `CloudTable` a získat chybovou zprávu, ujistěte se, že máte odkaz na [správnou verzi sady SDK úložiště](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

* **Číst jeden řádek v**

  Nastavte `partitionKey` a `rowKey` . Přístup k datům tabulky pomocí parametru metody `T <paramName>` . Ve skriptu jazyka C# `paramName` je hodnota zadaná ve `name` vlastnosti *function.jsv*. `T` je obvykle typ, který implementuje `ITableEntity` nebo je odvozen z `TableEntity` . `filter`Vlastnosti a `take` se v tomto scénáři nepoužívají.

* **Čtení jednoho nebo více řádků**

  Přístup k datům tabulky pomocí parametru metody `IQueryable<T> <paramName>` . Ve skriptu jazyka C# `paramName` je hodnota zadaná ve `name` vlastnosti *function.jsv*. `T` musí se jednat o typ, který implementuje `ITableEntity` nebo odvodí z `TableEntity` . Můžete použít `IQueryable` metody k provedení libovolného filtrování. `partitionKey`Vlastnosti, `rowKey` , `filter` a `take` nejsou v tomto scénáři použity.  

  > [!NOTE]
  > `IQueryable` není podporován v [modulu runtime Functions v2](functions-versions.md). Alternativou je [použití parametru paramName metody cloudu](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) pro čtení tabulky pomocí sady SDK Azure Storage. Pokud se pokusíte vytvořit navázání `CloudTable` a získat chybovou zprávu, ujistěte se, že máte odkaz na [správnou verzi sady SDK úložiště](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x).

# <a name="java"></a>[Java](#tab/java)

Atribut [TableInput](/java/api/com.microsoft.azure.functions.annotation.tableinput) vám poskytne přístup k řádku tabulky, který funkci aktivoval.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Nastavte `filter` vlastnosti a `take` . Nenastavuje se `partitionKey` nebo `rowKey` . Přístup k entitě vstupní tabulky (entity) pomocí `context.bindings.<BINDING_NAME>` . Deserializované objekty mají `RowKey` vlastnosti a `PartitionKey` .

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Data jsou předána vstupnímu parametru, který je určen `name` klíčem v *function.jsv* souboru. Zadáním `partitionKey` a `rowKey` umožníte filtrovat konkrétní záznamy. Další podrobnosti najdete v [příkladu PowerShellu](#example) .

# <a name="python"></a>[Python](#tab/python)

Data tabulky se předávají funkci jako řetězec JSON. Zrušte serializaci zprávy voláním `json.loads` , jak je uvedeno v [příkladu](#example)vstupu.

---

## <a name="next-steps"></a>Další kroky

* [Zápis dat úložiště tabulek z funkce](./functions-bindings-storage-table-output.md)
