---
title: Azure Table úložiště vazby pro Azure Functions
description: Pochopit, jak používat Azure Table storage vazby v Azure Functions.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: Funkce Azure, funkce zpracování událostí, dynamické výpočetní architektura bez serveru
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/08/2017
ms.author: tdykstra
ms.openlocfilehash: cdfde0d888c8434443dcd05109f646eca8c0df19
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Azure Table úložiště vazby pro Azure Functions

Tento článek vysvětluje, jak pracovat s Azure Table storage vazeb v Azure Functions. Azure Functions podporuje vstup a výstup vazby pro Azure Table storage.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages"></a>Balíčky

Vazby tabulky úložiště jsou součástí [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) balíček NuGet. Zdrojový kód pro balíček je v [azure webjobs sdk](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/) úložiště GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="input"></a>Vstup

Čtení tabulky v účtu Azure Storage pomocí Vstupní vazba úložiště Azure Table.

## <a name="input---example"></a>Vstup – příklad

Podívejte se na konkrétní jazyk příklad:

* [Načíst jednu entitu C#](#input---c-example-1)
* [Přečtěte si více entit C#](#input---c-example-2)
* [C# skript - načíst jednu entitu](#input---c-script-example-1)
* [C# skript – přečtěte si více entit](#input---c-script-example-2)
* [F#](#input---f-example-2)
* [JavaScript](#input---javascript-example)

### <a name="input---c-example-1"></a>Vstup - C# příklad 1

Následující příklad ukazuje [C# funkce](functions-dotnet-class-library.md) jedné tabulky řádek, který čte. 

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
        TraceWriter log)
    {
        log.Info($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
    }
}
```

### <a name="input---c-example-2"></a>Vstup - C# příklad 2

Následující příklad ukazuje [C# funkce](functions-dotnet-class-library.md) více řádků tabulky, který čte. Všimněte si, že `MyPoco` třída odvozená z `TableEntity`.

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
        TraceWriter log)
    {
        foreach (MyPoco poco in pocos)
        {
            log.Info($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
        }
    }
}
```

### <a name="input---c-script-example-1"></a>(Vstup) – příklad skriptu jazyka C# 1

Následující příklad ukazuje vazbu vstupní tabulka ve *function.json* souboru a [C# skript](functions-reference-csharp.md) kód, který používá vazby. Funkce používá aktivační procedury fronty načíst řádek jednu tabulku. 

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

[Konfigurace](#input---configuration) část vysvětluje tyto vlastnosti.

Tady je kód skriptu jazyka C#:

```csharp
public static void Run(string myQueueItem, Person personEntity, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    log.Info($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

### <a name="input---c-script-example-2"></a>(Vstup) – příklad skriptu jazyka C# 2

Následující příklad ukazuje vazbu vstupní tabulka ve *function.json* souboru a [C# skript](functions-reference-csharp.md) kód, který používá vazby. Funkce přečte entity pro klíč oddílu, který je uveden v zprávu fronty.

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

[Konfigurace](#input---configuration) část vysvětluje tyto vlastnosti.

Kód skriptu jazyka C# přidá odkaz na sady SDK úložiště Azure tak, aby typ entity můžete odvozena od `TableEntity`:

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.Info($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
```

### <a name="input---f-example"></a>(Vstup) – příklad F #

Následující příklad ukazuje vazbu vstupní tabulka ve *function.json* souboru a [F # skript](functions-reference-fsharp.md) kód, který používá vazby. Funkce používá aktivační procedury fronty načíst řádek jednu tabulku. 

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

[Konfigurace](#input---configuration) část vysvětluje tyto vlastnosti.

Tady je kód F #:

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(myQueueItem: string, personEntity: Person) =
    log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
    log.Info(sprintf "Name in Person entity: %s" personEntity.Name)
```

### <a name="input---javascript-example"></a>(Vstup) – příklad v jazyce JavaScript

Následující příklad ukazuje vazbu vstupní tabulka ve *function.json* soubor a [kód JavaScript] (funkce node.md odkaz), který používá vazby. Funkce používá aktivační procedury fronty načíst řádek jednu tabulku. 

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

[Konfigurace](#input---configuration) část vysvětluje tyto vlastnosti.

Tady je kód jazyka JavaScript:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

## <a name="input---attributes"></a>(Vstup) – atributy
 
V [knihovny tříd jazyka C#](functions-dotnet-class-library.md), použijte následující atributy ke konfiguraci vazbu vstupní tabulky:

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs)

  Konstruktoru atributu přebírá název tabulky, klíč oddílu a klíč řádku. Může sloužit na parametr typu out nebo návratovou hodnotu funkce, jak je znázorněno v následujícím příkladu:

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco, 
      TraceWriter log)
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
      TraceWriter log)
  {
      ...
  }
  ```

  Úplný příklad najdete v tématu [(vstup) – příklad jazyka C#](#input---c-example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Poskytuje další možnost zadejte účet úložiště, který chcete použít. Konstruktor přebírá název nastavení aplikace, který obsahuje připojovací řetězec úložiště. Atribut lze použít na parametr, metoda nebo na úrovni třídy. Následující příklad ukazuje úroveň třídy a metody:

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

Účet úložiště, který chcete použít, je určen v následujícím pořadí:

* `Table` Atributu `Connection` vlastnost.
* `StorageAccount` Atribut použitý pro parametr stejné jako `Table` atribut.
* `StorageAccount` Atribut použitý k funkci.
* `StorageAccount` Atribut použitý k třídě.
* Výchozí účet úložiště pro funkce aplikace (nastavení aplikace "AzureWebJobsStorage").

## <a name="input---configuration"></a>Vstup - konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `Table` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** | neuvedeno | musí být nastavena na `table`. Tato vlastnost nastavena automaticky při vytvoření vazby na portálu Azure.|
|**direction** | neuvedeno | musí být nastavena na `in`. Tato vlastnost nastavena automaticky při vytvoření vazby na portálu Azure. |
|**Jméno** | neuvedeno | Název proměnné, která představuje tabulky nebo entity v kód funkce. | 
|**tableName** | **TableName** | Název tabulky.| 
|**Klíč oddílu** | **Klíč oddílu** |Volitelné. Klíč oddílu tabulka entity ke čtení. Najdete v článku [využití](#input---usage) části Pokyny o tom, jak tuto vlastnost použít.| 
|**RowKey** |**RowKey** | Volitelné. Klíč řádku entity tabulky ke čtení. Najdete v článku [využití](#input---usage) části Pokyny o tom, jak tuto vlastnost použít.| 
|**proveďte** |**proveďte** | Volitelné. Maximální počet entit ke čtení v jazyce JavaScript. Najdete v článku [využití](#input---usage) části Pokyny o tom, jak tuto vlastnost použít.| 
|**Filtr** |**Filtr** | Volitelné. Výraz filtru OData pro tabulku, vstupní v jazyce JavaScript. Najdete v článku [využití](#input---usage) části Pokyny o tom, jak tuto vlastnost použít.| 
|**Připojení** |**Připojení** | Název nastavení aplikace, který obsahuje připojovací řetězec úložiště k použití pro tuto vazbu. Název nastavení aplikace začíná "AzureWebJobs", můžete zadat pouze zbytku názvu sem. Například pokud nastavíte `connection` na "MyStorage" Functions runtime vypadá pro aplikaci nastavení, která je s názvem "AzureWebJobsMyStorage." Pokud necháte `connection` prázdný, funkce používá modul runtime výchozí úložiště připojovací řetězec v nastavení aplikace, který je pojmenován `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>(Vstup) – použití

Vstupní vazbu tabulky úložiště podporuje následující scénáře:

* **Přečtěte si jeden řádek v C# nebo skriptu jazyka C#**

  Nastavit `partitionKey` a `rowKey`. Přístup k datům tabulky pomocí parametru metody `T <paramName>`. V jazyce C# skript `paramName` je hodnota zadaná v `name` vlastnost *function.json*. `T` Obvykle se jedná o typ, který implementuje `ITableEntity` nebo odvozuje od `TableEntity`. `filter` a `take` vlastnosti nejsou v tomto scénáři používají. 

* **Přečtěte si jeden nebo více řádků v C# nebo skriptu jazyka C#**

  Přístup k datům tabulky pomocí parametru metody `IQueryable<T> <paramName>`. V jazyce C# skript `paramName` je hodnota zadaná v `name` vlastnost *function.json*. `T` musí být typ, který implementuje `ITableEntity` nebo odvozuje od `TableEntity`. Můžete použít `IQueryable` metod jakéhokoli filtrování vyžaduje. `partitionKey`, `rowKey`, `filter`, A `take` vlastnosti nejsou v tomto scénáři používají.  

> [!NOTE]
> `IQueryable` není podporována v [Functions v2 runtime](functions-versions.md). Alternativou je [pomocí parametru metody paramName CloudTable](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) ke čtení tabulky pomocí sady SDK úložiště Azure.

* **Přečtěte si jeden nebo více řádků v jazyce JavaScript**

  Nastavte `filter` a `take` vlastnosti. Není nastavený `partitionKey` nebo `rowKey`. Přístup k vstupní tabulka entity (nebo entity) pomocí `context.bindings.<name>`. Deserializovaná objekty mají `RowKey` a `PartitionKey` vlastnosti.

## <a name="output"></a>Výstup

Pomocí Azure Table storage výstup vazby k zápisu entity do tabulky v účtu Azure Storage.

> [!NOTE]
> Tato vazba výstup nepodporuje aktualizaci existující entity. Použití `TableOperation.Replace` operace [ze sady SDK úložiště Azure](https://docs.microsoft.com/azure/cosmos-db/table-storage-how-to-use-dotnet#replace-an-entity) aktualizace stávající entity.   

## <a name="output---example"></a>Výstup – příklad

Podívejte se na konkrétní jazyk příklad:

* [C#](#output---c-example)
* [C# skript (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Výstup – příklad jazyka C#

Následující příklad ukazuje [C# funkce](functions-dotnet-class-library.md) používající aktivační procedury HTTP zapsat řádek jednu tabulku. 

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
    public static MyPoco TableOutput([HttpTrigger] dynamic input, TraceWriter log)
    {
        log.Info($"C# http trigger function processed: {input.Text}");
        return new MyPoco { PartitionKey = "Http", RowKey = Guid.NewGuid().ToString(), Text = input.Text };
    }
}
```

### <a name="output---c-script-example"></a>Výstup – příklad skriptu jazyka C#

Následující příklad ukazuje tabulkový výstup vazby ve *function.json* souboru a [C# skript](functions-reference-csharp.md) kód, který používá vazby. Funkce zapíše více entity tabulky.

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

[Konfigurace](#output---configuration) část vysvětluje tyto vlastnosti.

Tady je kód skriptu jazyka C#:

```csharp
public static void Run(string input, ICollector<Person> tableBinding, TraceWriter log)
{
    for (int i = 1; i < 10; i++)
        {
            log.Info($"Adding Person entity {i}");
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

### <a name="output---f-example"></a>Výstup – příklad F #

Následující příklad ukazuje tabulkový výstup vazby ve *function.json* souboru a [F # skript](functions-reference-fsharp.md) kód, který používá vazby. Funkce zapíše více entity tabulky.

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

[Konfigurace](#output---configuration) část vysvětluje tyto vlastnosti.

Tady je kód F #:

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(input: string, tableBinding: ICollector<Person>, log: TraceWriter) =
    for i = 1 to 10 do
        log.Info(sprintf "Adding Person entity %d" i)
        tableBinding.Add(
            { PartitionKey = "Test"
              RowKey = i.ToString()
              Name = "Name" + i.ToString() })
```

### <a name="output---javascript-example"></a>Výstup – příklad v jazyce JavaScript

Následující příklad ukazuje tabulkový výstup vazby ve *function.json* souboru a [funkce JavaScript, která](functions-reference-node.md) používající vazby. Funkce zapíše více entity tabulky.

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

[Konfigurace](#output---configuration) část vysvětluje tyto vlastnosti.

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

V [knihovny tříd jazyka C#](functions-dotnet-class-library.md), použijte [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs).

Konstruktoru atributu přebírá název tabulky. Je možné použít s `out` parametr nebo na základě návratové hodnoty funkce, jak je znázorněno v následujícím příkladu:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    TraceWriter log)
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
    TraceWriter log)
{
    ...
}
```

Úplný příklad najdete v tématu [výstup - C# příklad](#output---c-example).

Můžete použít `StorageAccount` atribut určete účet úložiště na úrovni třídy, metoda nebo parametr. Další informace najdete v tématu [(vstup) – atributy](#input---attributes).

## <a name="output---configuration"></a>Výstup – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `Table` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** | neuvedeno | musí být nastavena na `table`. Tato vlastnost nastavena automaticky při vytvoření vazby na portálu Azure.|
|**direction** | neuvedeno | musí být nastavena na `out`. Tato vlastnost nastavena automaticky při vytvoření vazby na portálu Azure. |
|**Jméno** | neuvedeno | Název proměnné používá v kódu funkce, který představuje tabulky nebo entity. Nastavte na `$return` Chcete-li funkce návratovou hodnotu.| 
|**tableName** |**TableName** | Název tabulky.| 
|**Klíč oddílu** |**Klíč oddílu** | Klíč oddílu tabulka entity k zápisu. Najdete v článku [oddíl využití](#output---usage) pokyny o tom, jak tuto vlastnost použít.| 
|**RowKey** |**RowKey** | Klíč řádku entity tabulky pro zápis. Najdete v článku [oddíl využití](#output---usage) pokyny o tom, jak tuto vlastnost použít.| 
|**Připojení** |**Připojení** | Název nastavení aplikace, který obsahuje připojovací řetězec úložiště k použití pro tuto vazbu. Název nastavení aplikace začíná "AzureWebJobs", můžete zadat pouze zbytku názvu sem. Například pokud nastavíte `connection` na "MyStorage" Functions runtime vypadá pro aplikaci nastavení, která je s názvem "AzureWebJobsMyStorage." Pokud necháte `connection` prázdný, funkce používá modul runtime výchozí úložiště připojovací řetězec v nastavení aplikace, který je pojmenován `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Výstup – použití

Úložiště Table výstup vazba podporuje následující scénáře:

* **Zápis jeden řádek v libovolném jazyce**

  V jazyce C# a C# skript, přístup k entitu výstupní tabulky pomocí parametru metody `out T paramName` nebo funkce vrátí hodnotu. V jazyce C# skript `paramName` je hodnota zadaná v `name` vlastnost *function.json*. `T` mohou být jakéhokoli typu serializable, pokud klíč oddílu a klíč řádku jsou poskytovány *function.json* souboru nebo `Table` atribut. V opačném `T` musí být typu, který zahrnuje `PartitionKey` a `RowKey` vlastnosti. V tomto scénáři `T` obvykle implementuje `ITableEntity` nebo odvozuje od `TableEntity`, ale nemusí to.

* **Vytvořit jeden nebo více řádků v C# nebo C#**

  V jazyce C# a C# skript, přístup k entitu výstupní tabulky pomocí parametru metody `ICollector<T> paramName` nebo `IAsyncCollector<T> paramName`. V jazyce C# skript `paramName` je hodnota zadaná v `name` vlastnost *function.json*. `T` Určuje schéma entity, které chcete přidat. Obvykle `T` je odvozena z `TableEntity` nebo implementuje `ITableEntity`, ale nemusí to. Klíč oddílu a řádku klíče hodnoty v *function.json* nebo `Table` nejsou v tomto scénáři používají konstruktoru atributu.

  Další možností je použít `CloudTable paramName` parametru metody k zápisu do tabulky pomocí sady SDK úložiště Azure.

* **Zapisovat jeden nebo více řádků v jazyce JavaScript**

  V funkce jazyka JavaScript, přístup k tabulce výstup pomocí `context.bindings.<name>`.

## <a name="exceptions-and-return-codes"></a>Výjimky a návratové kódy

| Vazba | Referenční informace |
|---|---|
| Table | [Kódy chyb tabulky](https://docs.microsoft.com/rest/api/storageservices/fileservices/table-service-error-codes) |
| Objekt BLOB, tabulky, fronty | [Kódy chyb úložiště](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Objekt BLOB, tabulky, fronty | [Řešení potíží](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o Azure functions triggerů a vazeb](functions-triggers-bindings.md)
