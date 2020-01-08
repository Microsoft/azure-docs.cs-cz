---
title: Vazby úložiště tabulek Azure pro Azure Functions
description: Naučte se používat vazby Azure Table Storage v Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.openlocfilehash: bca3c246db25e86772a7009bf85e7d302b4b17b2
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/31/2019
ms.locfileid: "75562064"
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Vazby úložiště tabulek Azure pro Azure Functions

Tento článek vysvětluje, jak pracovat s vazbami služby Azure Table Storage v Azure Functions. Azure Functions podporuje vstupní a výstupní vazby pro Azure Table Storage.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Balíčky – funkce 1.x

Vazby tabulkového úložiště jsou k dispozici v balíčku NuGet [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) , verze 2. x. Zdrojový kód pro tento balíček je v [sadu sdk azure webjobs](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Table) úložiště GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x-and-higher"></a>Balíčky – funkce 2. x a vyšší

Vazby tabulkového úložiště jsou k dispozici v balíčku NuGet [Microsoft. Azure. WebJobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) , verze 3. x. Zdrojový kód pro tento balíček je v [sadu sdk azure webjobs](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables) úložiště GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="input"></a>Vstup

Pomocí vstupní vazby Azure Table Storage si můžete přečíst tabulku v účtu Azure Storage.

## <a name="input---example"></a>Vstupní příklad

Podívejte se na příklad specifické pro jazyk:

* [C#číst jednu entitu](#input---c-example---one-entity)
* [C#vazba na IQueryable](#input---c-example---iqueryable)
* [C#vazba na cloudovou databázi](#input---c-example---cloudtable)
* [C#načtení skriptu pro jednu entitu](#input---c-script-example---one-entity)
* [C#vazba skriptu na IQueryable](#input---c-script-example---iqueryable)
* [C#vazba skriptu na cloudovou databázi](#input---c-script-example---cloudtable)
* [F#](#input---f-example)
* [JavaScript](#input---javascript-example)
* [Java](#input---java-example)

### <a name="input---c-example---one-entity"></a>Vstupní- C# příklad-jedna entita

Následující příklad ukazuje [ C# funkci](functions-dotnet-class-library.md) , která čte jeden řádek tabulky. 

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

### <a name="input---c-example---iqueryable"></a>Vstupní C# příklad – IQueryable

Následující příklad ukazuje [ C# funkci](functions-dotnet-class-library.md) , která čte více řádků tabulky. Všimněte si, že třída `MyPoco` je odvozena z `TableEntity`.

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

### <a name="input---c-example---cloudtable"></a>Vstupní C# příklad – cloudová

`IQueryable` není v [modulu runtime Functions v2](functions-versions.md)podporován. Alternativou je použití parametru `CloudTable` metody ke čtení tabulky pomocí sady Azure Storage SDK. Tady je příklad funkce, která se dotazuje Azure Functions tabulce protokolu:

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

Další informace o tom, jak používat cloudovou tabulku, najdete v tématu Začínáme [s Azure Table Storage](../cosmos-db/table-storage-how-to-use-dotnet.md).

Pokud se pokusíte vytvořit navázání na `CloudTable` a zobrazí se chybová zpráva, ujistěte se, že máte odkaz na [správnou verzi sady SDK služby úložiště](#azure-storage-sdk-version-in-functions-1x).

### <a name="input---c-script-example---one-entity"></a>Ukázka C# skriptu Input-Script – jedna entita

Následující příklad ukazuje vstupní vazbu tabulky v souboru *Function. JSON* a [ C# kódu skriptu](functions-reference-csharp.md) , který používá vazbu. Funkce používá Trigger fronty ke čtení jednoho řádku tabulky. 

Soubor *Function. JSON* určuje `partitionKey` a `rowKey`. Hodnota `rowKey` {queueTrigger} označuje, že klíč řádku pochází z řetězce zprávy fronty.

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

[Konfigurace](#input---configuration) bodu vysvětluje tyto vlastnosti.

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

### <a name="input---c-script-example---iqueryable"></a>Ukázka C# skriptu Input-Script – IQueryable

Následující příklad ukazuje vstupní vazbu tabulky v souboru *Function. JSON* a [ C# kódu skriptu](functions-reference-csharp.md) , který používá vazbu. Funkce přečte entity pro klíč oddílu, který je určen ve zprávě fronty.

Tady je *function.json* souboru:

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

[Konfigurace](#input---configuration) bodu vysvětluje tyto vlastnosti.

Kód C# skriptu přidá odkaz na sadu Azure Storage SDK, aby typ entity mohl odvozovat z `TableEntity`:

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

### <a name="input---c-script-example---cloudtable"></a>Příklad C# skriptu Input-Cloud

`IQueryable` není podporován v modulu runtime Functions pro [verze 2. x a vyšší)](functions-versions.md). Alternativou je použití parametru `CloudTable` metody ke čtení tabulky pomocí sady Azure Storage SDK. Tady je příklad funkce, která se dotazuje Azure Functions tabulce protokolu:

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

Další informace o tom, jak používat cloudovou tabulku, najdete v tématu Začínáme [s Azure Table Storage](../cosmos-db/table-storage-how-to-use-dotnet.md).

Pokud se pokusíte vytvořit navázání na `CloudTable` a zobrazí se chybová zpráva, ujistěte se, že máte odkaz na [správnou verzi sady SDK služby úložiště](#azure-storage-sdk-version-in-functions-1x).

### <a name="input---f-example"></a>Vstupní F# příklad

Následující příklad ukazuje vstupní vazbu tabulky v souboru *Function. JSON* a [ F# kódu skriptu](functions-reference-fsharp.md) , který používá vazbu. Funkce používá Trigger fronty ke čtení jednoho řádku tabulky. 

Soubor *Function. JSON* určuje `partitionKey` a `rowKey`. Hodnota `rowKey` {queueTrigger} označuje, že klíč řádku pochází z řetězce zprávy fronty.

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

[Konfigurace](#input---configuration) bodu vysvětluje tyto vlastnosti.

Tady je F# kódu:

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(myQueueItem: string, personEntity: Person) =
    log.LogInformation(sprintf "F# Queue trigger function processed: %s" myQueueItem)
    log.LogInformation(sprintf "Name in Person entity: %s" personEntity.Name)
```

### <a name="input---javascript-example"></a>Input – příklad JavaScriptu

Následující příklad ukazuje vstupní vazbu tabulky v souboru *Function. JSON* a [kódu jazyka JavaScript](functions-reference-node.md) , který používá vazbu. Funkce používá Trigger fronty ke čtení jednoho řádku tabulky. 

Soubor *Function. JSON* určuje `partitionKey` a `rowKey`. Hodnota `rowKey` {queueTrigger} označuje, že klíč řádku pochází z řetězce zprávy fronty.

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

[Konfigurace](#input---configuration) bodu vysvětluje tyto vlastnosti.

Tady je kód jazyka JavaScript:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

### <a name="input---java-example"></a>Příklad Input-Java

Následující příklad ukazuje funkci aktivovanou protokolem HTTP, která vrací celkový počet položek v zadaném oddílu v úložišti tabulek.

```java
@FunctionName("getallcount")
public int run(
   @HttpTrigger(name = "req",
                 methods = {HttpMethod.GET},
                 authLevel = AuthorizationLevel.ANONYMOUS) Object dummyShouldNotBeUsed,
   @TableInput(name = "items",
                tableName = "mytablename",  partitionKey = "myparkey",
                connection = "myconnvarname") MyItem[] items
) {
    return items.length;
}
```


## <a name="input---attributes"></a>(Vstup) – atributy
 
V [ C# knihovnách tříd](functions-dotnet-class-library.md)použijte následující atributy ke konfiguraci vstupní vazby tabulky:

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)

  Konstruktor atributu vezme název tabulky, klíč oddílu a klíč řádku. Dá se použít pro výstupní parametr nebo pro návratovou hodnotu funkce, jak je znázorněno v následujícím příkladu:

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

  Vlastnost `Connection` můžete nastavit tak, aby určovala účet úložiště, který se má použít, jak je znázorněno v následujícím příkladu:

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

  Úplný příklad naleznete v tématu Input- C# example.

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Poskytuje jiný způsob určení účtu úložiště, který se má použít. Konstruktor převezme název nastavení aplikace, které obsahuje připojovací řetězec úložiště. Atribut je použít na parametr, metody nebo třídy úroveň. Následující příklad ukazuje úrovni třídy a metody:

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

* `Table` Atributu `Connection` vlastnost.
* `StorageAccount` Použije pro stejný parametr, jako `Table` atribut.
* `StorageAccount` Použije pro funkci.
* `StorageAccount` Atribut aplikován třídu.
* Výchozí účet úložiště pro aplikaci Function App (nastavení aplikace "AzureWebJobsStorage").

## <a name="input---java-annotations"></a>Zadávání poznámek Java

V [knihovně modulu runtime Functions jazyka Java](/java/api/overview/azure/functions/runtime)použijte `@TableInput` anotaci u parametrů, jejichž hodnota by pocházela z tabulkového úložiště.  Tuto poznámku lze použít s nativními typy s možnou hodnotou null, Pojo nebo Nullable pomocí volitelných >\<T. 

## <a name="input---configuration"></a>Vstup - konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `Table` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** | – | Musí být nastaveno na `table`. Tato vlastnost je nastavena automaticky při vytváření vazby v Azure Portal.|
|**direction** | – | Musí být nastaveno na `in`. Tato vlastnost je nastavena automaticky při vytváření vazby v Azure Portal. |
|**name** | – | Název proměnné, která představuje tabulku nebo entitu v kódu funkce. | 
|**tableName** | **TableName** | Název tabulky.| 
|**partitionKey** | **partitionKey** |Nepovinný parametr. Klíč oddílu entity tabulky, která se má přečíst Návod, jak tuto vlastnost používat, najdete v části věnované [používání](#input---usage) .| 
|**rowKey** |**RowKey** | Nepovinný parametr. Klíč řádku entity tabulky, která se má přečíst Návod, jak tuto vlastnost používat, najdete v části věnované [používání](#input---usage) .| 
|**take** |**Nezbytná** | Nepovinný parametr. Maximální počet entit, které mají být načteny v jazyce JavaScript. Návod, jak tuto vlastnost používat, najdete v části věnované [používání](#input---usage) .| 
|**filter (filtr)** |**Filtr** | Nepovinný parametr. Výraz filtru OData pro vstup tabulky v JavaScriptu Návod, jak tuto vlastnost používat, najdete v části věnované [používání](#input---usage) .| 
|**připojení** |**připojení** | Název nastavení aplikace, které obsahuje připojovací řetězec úložiště, který se má použít pro tuto vazbu. Pokud název nastavení aplikace začíná řetězcem "AzureWebJobs", můžete zde zadat pouze zbytek názvu. Například pokud nastavíte `connection` na "MyStorage", modul runtime Functions vyhledá nastavení aplikace s názvem "AzureWebJobsMyStorage". Pokud necháte `connection` prázdné, modul runtime Functions použije výchozí připojovací řetězec úložiště v nastavení aplikace s názvem `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>(Vstup) – využití

Vstupní vazba tabulkového úložiště podporuje následující scénáře:

* **Čtení jednoho řádku C# nebo C# skriptu**

  Nastavte `partitionKey` a `rowKey`. Přístup k datům tabulky pomocí parametru metody `T <paramName>`. Ve C# skriptu `paramName` je hodnota zadaná ve vlastnosti `name` *Function. JSON*. `T` je obvykle typ, který implementuje `ITableEntity` nebo je odvozen z `TableEntity`. Vlastnosti `filter` a `take` se v tomto scénáři nepoužívají. 

* **Čtení jednoho nebo více řádků v C# nebo C# skriptu**

  Přístup k datům tabulky pomocí parametru metody `IQueryable<T> <paramName>`. Ve C# skriptu `paramName` je hodnota zadaná ve vlastnosti `name` *Function. JSON*. `T` musí být typ, který implementuje `ITableEntity` nebo je odvozen z `TableEntity`. Pomocí metod `IQueryable` můžete provést jakékoli filtrování. V tomto scénáři nejsou použity vlastnosti `partitionKey`, `rowKey`, `filter`a `take`.  

  > [!NOTE]
  > `IQueryable` není v [modulu runtime Functions v2](functions-versions.md)podporován. Alternativou je [použití parametru paramName metody cloudu](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) pro čtení tabulky pomocí sady SDK Azure Storage. Pokud se pokusíte vytvořit navázání na `CloudTable` a zobrazí se chybová zpráva, ujistěte se, že máte odkaz na [správnou verzi sady SDK služby úložiště](#azure-storage-sdk-version-in-functions-1x).

* **Čtení jednoho nebo více řádků v JavaScriptu**

  Nastavte vlastnosti `filter` a `take`. Nenastavte `partitionKey` ani `rowKey`. Přístup k entitě vstupní tabulky (nebo entit) pomocí `context.bindings.<BINDING_NAME>`. Deserializované objekty mají vlastnosti `RowKey` a `PartitionKey`.

## <a name="output"></a>Výstup

Použijte výstupní vazbu služby Azure Table Storage k zápisu entit do tabulky v Azure Storagem účtu.

> [!NOTE]
> Tato výstupní vazba nepodporuje aktualizaci existujících entit. Pomocí příslušné [`TableOperation`](/dotnet/api/microsoft.azure.cosmos.table.tableoperation?view=azure-dotnet) ze [sady Azure Storage SDK](/azure/cosmos-db/tutorial-develop-table-dotnet#insert-or-merge-an-entity) aktualizujte existující entitu podle potřeby.   

## <a name="output---example"></a>Výstup – příklad

Podívejte se na příklad specifické pro jazyk:

* [C#](#output---c-example)
* [C# skript (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Výstup – příklad v jazyce C#

Následující příklad ukazuje [ C# funkci](functions-dotnet-class-library.md) , která pomocí triggeru http zapisuje jeden řádek tabulky. 

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

### <a name="output---c-script-example"></a>Výstup – příklad skriptu jazyka C#

Následující příklad ukazuje výstupní vazbu tabulky v souboru *Function. JSON* a [ C# kódu skriptu](functions-reference-csharp.md) , který používá vazbu. Funkce zapisuje více entit tabulky.

Tady je *function.json* souboru:

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

[Konfigurace](#output---configuration) bodu vysvětluje tyto vlastnosti.

Tady je kód skriptu jazyka C#:

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

### <a name="output---f-example"></a>Výstup – F# příklad

Následující příklad ukazuje výstupní vazbu tabulky v souboru *Function. JSON* a [ F# kódu skriptu](functions-reference-fsharp.md) , který používá vazbu. Funkce zapisuje více entit tabulky.

Tady je *function.json* souboru:

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

[Konfigurace](#output---configuration) bodu vysvětluje tyto vlastnosti.

Tady je F# kódu:

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(input: string, tableBinding: ICollector<Person>, log: ILogger) =
    for i = 1 to 10 do
        log.LogInformation(sprintf "Adding Person entity %d" i)
        tableBinding.Add(
            { PartitionKey = "Test"
              RowKey = i.ToString()
              Name = "Name" + i.ToString() })
```

### <a name="output---javascript-example"></a>Výstup – příklad v jazyce JavaScript

Následující příklad ukazuje výstupní vazbu tabulky v souboru *Function. JSON* a [funkci JavaScriptu](functions-reference-node.md) , která používá vazbu. Funkce zapisuje více entit tabulky.

Tady je *function.json* souboru:

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

[Konfigurace](#output---configuration) bodu vysvětluje tyto vlastnosti.

Tady je kód jazyka JavaScript:

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

## <a name="output---attributes"></a>Výstup – atributy

V [ C# knihovně tříd](functions-dotnet-class-library.md)použijte [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs).

Konstruktor atributu vezme název tabulky. Dá se použít na parametr `out` nebo na návratovou hodnotu funkce, jak je znázorněno v následujícím příkladu:

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

Vlastnost `Connection` můžete nastavit tak, aby určovala účet úložiště, který se má použít, jak je znázorněno v následujícím příkladu:

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

Kompletní příklad naleznete v tématu [výstup – příklad v jazyce C#](#output---c-example).

Atribut `StorageAccount` lze použít k určení účtu úložiště na úrovni třídy, metody nebo parametru. Další informace naleznete v tématu [input-Attributes](#input---attributes).

## <a name="output---configuration"></a>Výstup – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `Table` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** | – | Musí být nastaveno na `table`. Tato vlastnost je nastavena automaticky při vytváření vazby v Azure Portal.|
|**direction** | – | Musí být nastaveno na `out`. Tato vlastnost je nastavena automaticky při vytváření vazby v Azure Portal. |
|**name** | – | Název proměnné použitý v kódu funkce, který představuje tabulku nebo entitu. Nastavte na `$return` pro odkaz na návratovou hodnotu funkce.| 
|**tableName** |**TableName** | Název tabulky.| 
|**partitionKey** |**partitionKey** | Klíč oddílu entity tabulky, která se má zapsat Návod, jak tuto vlastnost používat, najdete v [části věnované používání](#output---usage) .| 
|**rowKey** |**RowKey** | Klíč řádku entity tabulky, která se má zapsat Návod, jak tuto vlastnost používat, najdete v [části věnované používání](#output---usage) .| 
|**připojení** |**připojení** | Název nastavení aplikace, které obsahuje připojovací řetězec úložiště, který se má použít pro tuto vazbu. Pokud název nastavení aplikace začíná řetězcem "AzureWebJobs", můžete zde zadat pouze zbytek názvu. Například pokud nastavíte `connection` na "MyStorage", modul runtime Functions vyhledá nastavení aplikace s názvem "AzureWebJobsMyStorage". Pokud necháte `connection` prázdné, modul runtime Functions použije výchozí připojovací řetězec úložiště v nastavení aplikace s názvem `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Výstup – využití

Vazba na výstup tabulkového úložiště podporuje následující scénáře:

* **Zápis jednoho řádku v libovolném jazyce**

  Ve C# skriptu C# a přejděte k entitě výstupní tabulky pomocí parametru metody, jako je například `out T paramName` nebo návratová hodnota funkce. Ve C# skriptu `paramName` je hodnota zadaná ve vlastnosti `name` *Function. JSON*. Pokud klíč oddílu a klíč řádku poskytuje soubor *Function. JSON* nebo atribut `Table`, `T` může být jakýkoli serializovatelný typ. Jinak `T` musí být typ, který zahrnuje vlastnosti `PartitionKey` a `RowKey`. V tomto scénáři `T` obvykle implementuje `ITableEntity` nebo je odvozena z `TableEntity`, ale není nutné.

* **Zápis jednoho nebo více řádků do C# nebo C# skriptu**

  Ve C# skriptu C# a přejděte k entitě výstupní tabulky pomocí parametru metody `ICollector<T> paramName` nebo `IAsyncCollector<T> paramName`. Ve C# skriptu `paramName` je hodnota zadaná ve vlastnosti `name` *Function. JSON*. `T` Určuje schéma entit, které chcete přidat. Obvykle `T` je odvozen z `TableEntity` nebo implementuje `ITableEntity`, ale není nutné. Hodnoty klíče oddílu a klíče řádku v *Function. JSON* nebo konstruktoru atributu `Table` se v tomto scénáři nepoužívají.

  Alternativou je použití parametru `CloudTable` metody k zápisu do tabulky pomocí sady Azure Storage SDK. Pokud se pokusíte vytvořit navázání na `CloudTable` a zobrazí se chybová zpráva, ujistěte se, že máte odkaz na [správnou verzi sady SDK služby úložiště](#azure-storage-sdk-version-in-functions-1x). Příklad kódu, který se váže k `CloudTable`, naleznete v části Příklady vstupních vazeb pro [C#](#input---c-example---cloudtable) nebo [ C# skriptu](#input---c-script-example---cloudtable) dříve v tomto článku.

* **Zápis jednoho nebo více řádků v JavaScriptu**

  Ve funkcích JavaScriptu získáte přístup k výstupu tabulky pomocí `context.bindings.<BINDING_NAME>`.

## <a name="exceptions-and-return-codes"></a>Výjimky a návratové kódy

| Vazba | Referenční informace |
|---|---|
| Table | [Kódy chyb tabulky](https://docs.microsoft.com/rest/api/storageservices/fileservices/table-service-error-codes) |
| Objekt blob, tabulka, fronta | [Kódy chyb úložiště](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Objekt blob, tabulka, fronta | [Řešení potíží](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o aktivačních událostech Azure functions a vazby](functions-triggers-bindings.md)
