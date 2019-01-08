---
title: Vazby služby Azure storage tabulky pro službu Azure Functions
description: Vysvětlení použití vazby služby Azure Table storage ve službě Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: Azure functions, funkce, zpracování událostí, dynamické výpočty, architektura bez serveru
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.openlocfilehash: bd85214efc3c8f67d41563e3ca46a1e2278c4868
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2019
ms.locfileid: "54062669"
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Vazby služby Azure storage tabulky pro službu Azure Functions

Tento článek vysvětluje, jak pracovat s Azure Table storage vazby ve službě Azure Functions. Azure Functions podporuje vstupní a výstupní vazby pro Azure Table storage.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Balíčky – funkce 1.x

Vazby tabulek úložiště jsou součástí [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) balíčku NuGet, verze 2.x. Zdrojový kód pro tento balíček je v [sadu sdk azure webjobs](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Table) úložiště GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x"></a>Balíčky – funkce 2.x

Vazby tabulek úložiště jsou součástí [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) balíčku NuGet, verze 3.x. Zdrojový kód pro tento balíček je v [sadu sdk azure webjobs](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables) úložiště GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="input"></a>Vstup

Čtení tabulky v účtu služby Azure Storage pomocí vstupní vazby Azure Table storage.

## <a name="input---example"></a>Input – příklad

Podívejte se na příklad specifické pro jazyk:

* [C# načíst jednu entitu](#input---c-example---one-entity)
* [C# vazby položku IQueryable](#input---c-example---iqueryable)
* [C# svázat CloudTable](#input---c-example---cloudtable)
* [Skript jazyka C# načíst jednu entitu](#input---c-script-example---one-entity)
* [C# skript vazby položku IQueryable](#input---c-script-example---iqueryable)
* [C# skript vazba na CloudTable](#input---c-script-example---cloudtable)
* [F#](#input---f-example)
* [JavaScript](#input---javascript-example)
* [Java](#input---java-example)

### <a name="input---c-example---one-entity"></a>Zadejte jednu entitu – příklad jazyka C# –

Následující příklad ukazuje [funkce jazyka C#](functions-dotnet-class-library.md) , který přečte řádek jedné tabulky. 

Hodnota klíče na řádek "{queueTrigger}" označuje, že klíč řádku pochází z řetězec zprávy fronty.

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

### <a name="input---c-example---iqueryable"></a>Položka IQueryable vstup - příklad jazyka C# –

Následující příklad ukazuje [funkce jazyka C#](functions-dotnet-class-library.md) , který čte více řádků tabulky. Všimněte si, že `MyPoco` třída odvozena z `TableEntity`.

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

### <a name="input---c-example---cloudtable"></a>CloudTable vstup - příklad jazyka C# –

`IQueryable` nepodporuje [modul runtime služby Functions v2](functions-versions.md). Další možností je použít `CloudTable` parametr metody číst z tabulky pomocí sady SDK služby Azure Storage. Tady je příklad funkce 2.x, který se dotazuje tabulku protokolu Azure Functions:

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

Další informace o tom, jak používat CloudTable najdete v tématu [Začínáme s Azure Table storage](../cosmos-db/table-storage-how-to-use-dotnet.md).

Pokud se pokusíte vytvořit vazbu na `CloudTable` a zobrazí se chybová zpráva, ujistěte se, že máte odkaz na [správnou verzi sady SDK služby Storage](#azure-storage-sdk-version-in-functions-1x).

### <a name="input---c-script-example---one-entity"></a>Zadejte jednu entitu – příklad jazyka C# skript-

Následující příklad ukazuje vstupní Vazba tabulky v *function.json* souboru a [skript jazyka C#](functions-reference-csharp.md) kód, který používá vazba. Funkce používá aktivační událost fronty přečíst řádek jedné tabulky. 

*Function.json* Určuje soubor `partitionKey` a `rowKey`. `rowKey` Hodnota "{queueTrigger}" znamená, že klíč řádku pochází z řetězec zprávy fronty.

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

### <a name="input---c-script-example---iqueryable"></a>IQueryable – příklad jazyka C# skript – vstup

Následující příklad ukazuje vstupní Vazba tabulky v *function.json* souboru a [skript jazyka C#](functions-reference-csharp.md) kód, který používá vazba. Funkce přečte entity pro klíč oddílu, který je zadán ve zprávě fronty.

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

Kód skriptu jazyka C# přidá odkaz na sadu SDK služby Azure Storage tak, aby typ entity lze odvodit z `TableEntity`:

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

### <a name="input---c-script-example---cloudtable"></a>Vstup - příklad jazyka C# skript - CloudTable

`IQueryable` nepodporuje [modul runtime služby Functions v2](functions-versions.md). Další možností je použít `CloudTable` parametr metody číst z tabulky pomocí sady SDK služby Azure Storage. Tady je příklad funkce 2.x, který se dotazuje tabulku protokolu Azure Functions:

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

Další informace o tom, jak používat CloudTable najdete v tématu [Začínáme s Azure Table storage](../cosmos-db/table-storage-how-to-use-dotnet.md).

Pokud se pokusíte vytvořit vazbu na `CloudTable` a zobrazí se chybová zpráva, ujistěte se, že máte odkaz na [správnou verzi sady SDK služby Storage](#azure-storage-sdk-version-in-functions-1x).

### <a name="input---f-example"></a>Vstup - F# příklad

Následující příklad ukazuje vstupní Vazba tabulky v *function.json* souboru a [ F# skript](functions-reference-fsharp.md) kód, který používá vazba. Funkce používá aktivační událost fronty přečíst řádek jedné tabulky. 

*Function.json* Určuje soubor `partitionKey` a `rowKey`. `rowKey` Hodnota "{queueTrigger}" znamená, že klíč řádku pochází z řetězec zprávy fronty.

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

### <a name="input---javascript-example"></a>(Vstup) – příklad v jazyce JavaScript

Následující příklad ukazuje vstupní Vazba tabulky v *function.json* souboru a [kódu jazyka JavaScript](functions-reference-node.md) , který používá vazba. Funkce používá aktivační událost fronty přečíst řádek jedné tabulky. 

*Function.json* Určuje soubor `partitionKey` a `rowKey`. `rowKey` Hodnota "{queueTrigger}" znamená, že klíč řádku pochází z řetězec zprávy fronty.

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

### <a name="input---java-example"></a>(Vstup) – příklad v jazyce Java

Následující příklad ukazuje funkci aktivovanou protokolem HTTP, která vrátí celkový počet položek v zadaný oddíl ve službě Table storage.

```java
@FunctionName("getallcount")
public int run(
   @HttpTrigger(name = "req",
                 methods = {"get"},
                 authLevel = AuthorizationLevel.ANONYMOUS) Object dummyShouldNotBeUsed,
   @TableInput(name = "items",
                tableName = "mytablename",  partitionKey = "myparkey",
                connection = "myconnvarname") MyItem[] items
) {
    return items.length;
}
```


## <a name="input---attributes"></a>(Vstup) – atributy
 
V [knihoven tříd C#](functions-dotnet-class-library.md), použít ke konfiguraci vazby vstupní tabulky následující atributy:

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)

  Konstruktor atributu má název tabulky, klíč oddílu a klíče řádku. To je možné na výstupní parametr nebo návratovou hodnotu funkce, jak je znázorněno v následujícím příkladu:

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

  Můžete nastavit `Connection` vlastnosti a určit účet úložiště, který chcete použít, jak je znázorněno v následujícím příkladu:

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

  Kompletní příklad naleznete v tématu [(vstup) – příklad v jazyce C#](#input---c-example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Poskytuje další způsob, jak zadat účet úložiště. Konstruktor přijme název nastavení aplikace, které obsahuje připojovací řetězec úložiště. Atribut je použít na parametr, metody nebo třídy úroveň. Následující příklad ukazuje úrovni třídy a metody:

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

Účet úložiště se určuje v tomto pořadí:

* `Table` Atributu `Connection` vlastnost.
* `StorageAccount` Použije pro stejný parametr, jako `Table` atribut.
* `StorageAccount` Použije pro funkci.
* `StorageAccount` Atribut aplikován třídu.
* Výchozí účet úložiště pro aplikaci function app (nastavení aplikace "AzureWebJobsStorage").

## <a name="input---java-annotations"></a>(Vstup) – Java poznámky

V [Java funkce knihovny prostředí runtime](/java/api/overview/azure/functions/runtime), použijte `@TableInput` poznámku o parametrech, jehož hodnota bude pocházet z Table storage.  Tato poznámka je možné s nativní typy v jazyce Java, objektů Pojo nebo s povolenou hodnotou Null hodnoty pomocí nepovinné<T>. 

## <a name="input---configuration"></a>Vstup - konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `Table` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** | neuvedeno | Musí být nastaveno na `table`. Tato vlastnost je nastavena automaticky při vytváření vazby na webu Azure Portal.|
|**direction** | neuvedeno | Musí být nastaveno na `in`. Tato vlastnost je nastavena automaticky při vytváření vazby na webu Azure Portal. |
|**Jméno** | neuvedeno | Název proměnné, která představuje tabulky nebo entity v kódu funkce. | 
|**tableName** | **TableName** | Název tabulky.| 
|**partitionKey** | **partitionKey** |Volitelné. Klíč oddílu entitu tabulky ke čtení. Najdete v článku [využití](#input---usage) části Pokyny o tom, jak pomocí této vlastnosti.| 
|**RowKey** |**RowKey** | Volitelné. Klíč řádku entity tabulky pro čtení. Najdete v článku [využití](#input---usage) části Pokyny o tom, jak pomocí této vlastnosti.| 
|**Take** |**Take** | Volitelné. Maximální počet entit ke čtení v jazyce JavaScript. Najdete v článku [využití](#input---usage) části Pokyny o tom, jak pomocí této vlastnosti.| 
|**Filtr** |**Filtr** | Volitelné. Výraz filtru OData pro tabulku vstup v jazyce JavaScript. Najdete v článku [využití](#input---usage) části Pokyny o tom, jak pomocí této vlastnosti.| 
|**připojení** |**připojení** | Název nastavení aplikace, které obsahuje připojovací řetězec úložiště má použít pro tuto vazbu. Pokud název nastavení aplikace začíná řetězcem "AzureWebJobs", můžete zadat pouze zbytek název tady. Například pokud nastavíte `connection` na "MyStorage", modul runtime služby Functions vypadá pro aplikaci nastavení, která je s názvem "AzureWebJobsMyStorage." Pokud necháte `connection` prázdný, modul runtime služby Functions používá výchozí úložiště připojovací řetězec v nastavení aplikace, který je pojmenován `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>(Vstup) – využití

Vstupní vazby tabulky storage podporuje následující scénáře:

* **Přečtěte si jeden řádek v jazyce C# nebo skript jazyka C#**

  Nastavte `partitionKey` a `rowKey`. Přístup k datům tabulky s použitím parametru metody `T <paramName>`. Ve skriptu jazyka C# `paramName` je zadaná hodnota v `name` vlastnost *function.json*. `T` typ, který implementuje je obvykle `ITableEntity` nebo je z `TableEntity`. `filter` a `take` vlastnosti nejsou v tomto scénáři použít. 

* **Přečtěte si jeden nebo více řádků v jazyce C# nebo skript jazyka C#**

  Přístup k datům tabulky s použitím parametru metody `IQueryable<T> <paramName>`. Ve skriptu jazyka C# `paramName` je zadaná hodnota v `name` vlastnost *function.json*. `T` musí být typ, který implementuje `ITableEntity` nebo je z `TableEntity`. Můžete použít `IQueryable` provedete filtrování požadovaných metod. `partitionKey`, `rowKey`, `filter`, A `take` vlastnosti nejsou v tomto scénáři použít.  

  > [!NOTE]
  > `IQueryable` nepodporuje [modul runtime služby Functions v2](functions-versions.md). Alternativou je [použít parametr metody paramName CloudTable](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) číst z tabulky pomocí sady SDK služby Azure Storage. Pokud se pokusíte vytvořit vazbu na `CloudTable` a zobrazí se chybová zpráva, ujistěte se, že máte odkaz na [správnou verzi sady SDK služby Storage](#azure-storage-sdk-version-in-functions-1x).

* **Přečtěte si jeden nebo více řádků v jazyce JavaScript**

  Nastavte `filter` a `take` vlastnosti. Nenastavujte `partitionKey` nebo `rowKey`. Přístup k vstupní tabulka entity (nebo entitách) pomocí `context.bindings.<name>`. Deserializovaná objekty mají `RowKey` a `PartitionKey` vlastnosti.

## <a name="output"></a>Výstup

Pomocí Azure Table výstupní vazby úložiště pro zápis entity do tabulky v účtu služby Azure Storage.

> [!NOTE]
> Tento výstupní vazbu nepodporuje aktualizaci existující entity. Použití `TableOperation.Replace` operace [z Azure Storage SDK](https://docs.microsoft.com/azure/cosmos-db/table-storage-how-to-use-dotnet#replace-an-entity) aktualizovat existující entity.   

## <a name="output---example"></a>Výstup – příklad

Podívejte se na příklad specifické pro jazyk:

* [C#](#output---c-example)
* [C# skript (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Výstup – příklad v jazyce C#

Následující příklad ukazuje [funkce jazyka C#](functions-dotnet-class-library.md) triggeru HTTP, který používá k zápisu řádku jedné tabulky. 

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

Následující příklad ukazuje tabulkového výstupu vazby ve *function.json* souboru a [skript jazyka C#](functions-reference-csharp.md) kód, který používá vazba. Tato funkce zapíše více tabulkové entity.

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

Následující příklad ukazuje tabulkového výstupu vazby ve *function.json* souboru a [ F# skript](functions-reference-fsharp.md) kód, který používá vazba. Tato funkce zapíše více tabulkové entity.

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

Následující příklad ukazuje tabulkového výstupu vazby ve *function.json* souboru a [funkce jazyka JavaScript](functions-reference-node.md) , který používá vazba. Tato funkce zapíše více tabulkové entity.

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

V [knihoven tříd C#](functions-dotnet-class-library.md), použijte [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs).

Konstruktor atributu má název tabulky. Je možné použít na `out` parametr nebo návratovou hodnotu funkce, jak je znázorněno v následujícím příkladu:

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

Můžete nastavit `Connection` vlastnosti a určit účet úložiště, který chcete použít, jak je znázorněno v následujícím příkladu:

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

Můžete použít `StorageAccount` atribut k určení účtu úložiště na úrovni třídy, metody nebo parametr. Další informace najdete v tématu [(vstup) – atributy](#input---attributes).

## <a name="output---configuration"></a>Výstup – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `Table` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** | neuvedeno | Musí být nastaveno na `table`. Tato vlastnost je nastavena automaticky při vytváření vazby na webu Azure Portal.|
|**direction** | neuvedeno | Musí být nastaveno na `out`. Tato vlastnost je nastavena automaticky při vytváření vazby na webu Azure Portal. |
|**Jméno** | neuvedeno | Název této proměnné v kódu funkce, která představuje tabulky nebo entity. Nastavte na `$return` tak, aby odkazovaly návratovou hodnotu funkce.| 
|**tableName** |**TableName** | Název tabulky.| 
|**partitionKey** |**partitionKey** | Klíč oddílu entitu tabulky pro zápis. Najdete v článku [části využívání](#output---usage) pokyny o tom, jak pomocí této vlastnosti.| 
|**RowKey** |**RowKey** | Klíč řádku entity tabulky pro zápis. Najdete v článku [části využívání](#output---usage) pokyny o tom, jak pomocí této vlastnosti.| 
|**připojení** |**připojení** | Název nastavení aplikace, které obsahuje připojovací řetězec úložiště má použít pro tuto vazbu. Pokud název nastavení aplikace začíná řetězcem "AzureWebJobs", můžete zadat pouze zbytek název tady. Například pokud nastavíte `connection` na "MyStorage", modul runtime služby Functions vypadá pro aplikaci nastavení, která je s názvem "AzureWebJobsMyStorage." Pokud necháte `connection` prázdný, modul runtime služby Functions používá výchozí úložiště připojovací řetězec v nastavení aplikace, který je pojmenován `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Výstup – využití

Table storage Výstupní vazba podporuje následující scénáře:

* **Zápis jeden řádek v jakémkoli jazyce**

  V jazyce C# a skript jazyka C#, přístup k entitu výstupní tabulky pomocí parametru metody `out T paramName` nebo návratovou hodnotu funkce. Ve skriptu jazyka C# `paramName` je zadaná hodnota v `name` vlastnost *function.json*. `T` může být jakékoli serializovatelný typ., pokud jsou poskytovány klíče oddílu a klíč řádku *function.json* souboru nebo `Table` atribut. V opačném případě `T` musí být typ, který zahrnuje `PartitionKey` a `RowKey` vlastnosti. V tomto scénáři `T` obvykle implementují `ITableEntity` nebo je z `TableEntity`, ale nemusí to.

* **Napište jednu nebo více řádků v C# nebo C# skriptu**

  V C# a skript jazyka C#, přístup k entitu výstupní tabulky pomocí parametru metody `ICollector<T> paramName` nebo `IAsyncCollector<T> paramName`. Ve skriptu jazyka C# `paramName` je zadaná hodnota v `name` vlastnost *function.json*. `T` Určuje schématu entity, které chcete přidat. Obvykle `T` je odvozena z `TableEntity` nebo implementuje `ITableEntity`, ale nemusí to. Klíč oddílu a řádku hodnoty v klíče *function.json* nebo `Table` konstruktor atributu nejsou použity v tomto scénáři.

  Další možností je použít `CloudTable` parametr metody zapsat do tabulky s použitím sady SDK služby Azure Storage. Pokud se pokusíte vytvořit vazbu na `CloudTable` a zobrazí se chybová zpráva, ujistěte se, že máte odkaz na [správnou verzi sady SDK služby Storage](#azure-storage-sdk-version-in-functions-1x). Příklad kódu, který se váže k `CloudTable`, podívejte se na příklady vstupní vazby pro [jazyka C#](#input---c-example---cloudtable) nebo [skript jazyka C#](#input---c-script-example---cloudtable) dříve v tomto článku.

* **Zapisovat jeden nebo více řádků v jazyce JavaScript**

  Do funkce jazyka JavaScript, přístup k výstupu pomocí tabulky `context.bindings.<name>`.

## <a name="exceptions-and-return-codes"></a>Výjimky a návratové kódy

| Vazba | Referenční informace |
|---|---|
| Table | [Kódy chyb tabulky](https://docs.microsoft.com/rest/api/storageservices/fileservices/table-service-error-codes) |
| Objekt BLOB, tabulky, fronty | [Kódy chyb úložiště](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Objekt BLOB, tabulky, fronty | [Řešení potíží](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o aktivačních událostech Azure functions a vazby](functions-triggers-bindings.md)
