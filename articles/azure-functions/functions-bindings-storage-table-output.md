---
title: Výstupní vazby služby Azure Table Storage pro Azure Functions
description: Naučte se používat výstupní vazby Azure Table Storage v Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 825a336b5ad07f50d96b19036d70c759b907bd07
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102209675"
---
# <a name="azure-table-storage-output-bindings-for-azure-functions"></a>Výstupní vazby služby Azure Table Storage pro Azure Functions

Použijte výstupní vazbu služby Azure Table Storage k zápisu entit do tabulky v Azure Storagem účtu.

> [!NOTE]
> Tato výstupní vazba nepodporuje aktualizaci existujících entit. `TableOperation.Replace`K aktualizaci existující entity použijte operaci [ze sady Azure Storage SDK](../cosmos-db/tutorial-develop-table-dotnet.md#delete-an-entity) .

## <a name="example"></a>Příklad

# <a name="c"></a>[C#](#tab/csharp)

Následující příklad ukazuje [funkci jazyka C#](functions-dotnet-class-library.md) , která pomocí TRIGGERu http zapisuje jeden řádek tabulky.

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

Následující příklad ukazuje výstupní vazbu tabulky v *function.js* souboru a kódu [skriptu jazyka C#](functions-reference-csharp.md) , který používá vazbu. Funkce zapisuje více entit tabulky.

Tady je *function.js* souboru:

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

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#configuration) .

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

# <a name="java"></a>[Java](#tab/java)

Následující příklad ukazuje funkci jazyka Java, která pomocí triggeru HTTP zapisuje jeden řádek tabulky.

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

Následující příklad ukazuje funkci jazyka Java, která pomocí triggeru HTTP zapisuje více řádků tabulky.

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Následující příklad ukazuje výstupní vazbu tabulky v *function.js* souboru a [funkci JavaScriptu](functions-reference-node.md) , která používá vazbu. Funkce zapisuje více entit tabulky.

Tady je *function.js* souboru:

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

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#configuration) .

Tady je kód JavaScriptu:

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

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Následující příklad ukazuje, jak zapsat více entit do tabulky z funkce.

Konfigurace vazby v _function.jsna_:

```json
{
  "bindings": [
    {
      "name": "InputData",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "TableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Kód PowerShellu v _run.ps1_:

```powershell
param($InputData, $TriggerMetadata)
  
foreach ($i in 1..10) {
    Push-OutputBinding -Name TableBinding -Value @{
        PartitionKey = 'Test'
        RowKey = "$i"
        Name = "Name $i"
    }
}
```

# <a name="python"></a>[Python](#tab/python)

Následující příklad ukazuje, jak použít výstupní vazbu tabulkového úložiště. `table`Vazba je konfigurována v *function.js* , když přiřazujete hodnoty k `name` , `tableName` , `partitionKey` a `connection` :

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

Následující funkce vygeneruje jedinečnou UUI pro hodnotu a zachová `rowKey` tuto zprávu do tabulkového úložiště.

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

---

## <a name="attributes-and-annotations"></a>Atributy a poznámky

# <a name="c"></a>[C#](#tab/csharp)

V [knihovnách tříd jazyka C#](functions-dotnet-class-library.md)použijte rozhraní [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs).

Konstruktor atributu vezme název tabulky. Atribut lze použít pro `out` parametr nebo návratovou hodnotu funkce, jak je znázorněno v následujícím příkladu:

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

Vlastnost můžete nastavit `Connection` tak, aby určovala účet úložiště, který se má použít, jak je znázorněno v následujícím příkladu:

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

Úplný příklad naleznete v [příkladu jazyka C#](#example).

Atribut můžete použít `StorageAccount` k určení účtu úložiště na úrovni třídy, metody nebo parametru. Další informace naleznete v tématu [input-Attributes](./functions-bindings-storage-table-input.md#attributes-and-annotations).

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Skripty jazyka C# nepodporují atributy.

# <a name="java"></a>[Java](#tab/java)

V [běhové knihovně funkcí jazyka Java](/java/api/overview/azure/functions/runtime)použijte pro zápis hodnot do tabulkového úložiště [TableOutput](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/TableOutput.java/) anotaci parametrů.

[Další podrobnosti](#example)najdete v příkladu.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atributy nejsou podporovány jazykem JavaScript.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell nepodporuje atributy.

# <a name="python"></a>[Python](#tab/python)

Python nepodporuje atributy.

---

## <a name="configuration"></a>Konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.jspro* soubor a `Table` atribut.

|function.jsvlastnost | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**textový** | Není k dispozici | Musí být nastaven na hodnotu `table` . Tato vlastnost je nastavena automaticky při vytváření vazby v Azure Portal.|
|**směr** | Není k dispozici | Musí být nastaven na hodnotu `out` . Tato vlastnost je nastavena automaticky při vytváření vazby v Azure Portal. |
|**Jméno** | Není k dispozici | Název proměnné použitý v kódu funkce, který představuje tabulku nebo entitu. Nastavte na `$return` odkaz na návratovou hodnotu funkce.| 
|**Tabulky** |**TableName** | Název tabulky| 
|**partitionKey** |**PartitionKey** | Klíč oddílu entity tabulky, která se má zapsat Návod, jak tuto vlastnost používat, najdete v [části věnované používání](#usage) .| 
|**rowKey** |**RowKey** | Klíč řádku entity tabulky, která se má zapsat Návod, jak tuto vlastnost používat, najdete v [části věnované používání](#usage) .| 
|**vázán** |**Připojení** | Název nastavení aplikace, které obsahuje připojovací řetězec úložiště, který se má použít pro tuto vazbu. Pokud název nastavení aplikace začíná řetězcem "AzureWebJobs", můžete zde zadat pouze zbytek názvu. Například pokud nastavíte `connection` na "MyStorage", modul runtime Functions vyhledá nastavení aplikace s názvem "MyStorage". Pokud necháte `connection` prázdné, modul runtime Functions použije výchozí připojovací řetězec úložiště v nastavení aplikace s názvem `AzureWebJobsStorage` .|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Využití

# <a name="c"></a>[C#](#tab/csharp)

Přístup k entitě výstupní tabulky pomocí parametru metody `ICollector<T> paramName` nebo `IAsyncCollector<T> paramName` kde `T` zahrnuje `PartitionKey` `RowKey` vlastnosti a. Tyto vlastnosti jsou často doprovázeny implementací `ITableEntity` nebo děděním `TableEntity` .

Alternativně můžete použít `CloudTable` parametr metody k zápisu do tabulky pomocí sady Azure Storage SDK. Pokud se pokusíte vytvořit navázání `CloudTable` a získat chybovou zprávu, ujistěte se, že máte odkaz na [správnou verzi sady SDK úložiště](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Přístup k entitě výstupní tabulky pomocí parametru metody `ICollector<T> paramName` nebo `IAsyncCollector<T> paramName` kde `T` zahrnuje `PartitionKey` `RowKey` vlastnosti a. Tyto vlastnosti jsou často doprovázeny implementací `ITableEntity` nebo děděním `TableEntity` . `paramName`Hodnota je určena ve `name` vlastnosti *function.jsv*.

Alternativně můžete použít `CloudTable` parametr metody k zápisu do tabulky pomocí sady Azure Storage SDK. Pokud se pokusíte vytvořit navázání `CloudTable` a získat chybovou zprávu, ujistěte se, že máte odkaz na [správnou verzi sady SDK úložiště](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x).

# <a name="java"></a>[Java](#tab/java)

K dispozici jsou dvě možnosti pro výstup řádku úložiště tabulky z funkce pomocí anotace [TableStorageOutput](/java/api/com.microsoft.azure.functions.annotation.tableoutput) :

- **Návratová hodnota**: použitím poznámky k samotné funkci je návratová hodnota funkce trvalá jako řádek úložiště tabulky.

- **Imperativní**: Chcete-li explicitně nastavit hodnotu zprávy, použijte poznámku na konkrétní parametr typu [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) , kde `T` zahrnuje `PartitionKey` `RowKey` vlastnosti a. Tyto vlastnosti jsou často doprovázeny implementací `ITableEntity` nebo děděním `TableEntity` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Přístup k události výstupu pomocí `context.bindings.<name>` Where `<name>` je hodnota zadaná ve `name` vlastnosti *function.json*.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Chcete-li zapisovat do tabulkových dat, použijte `Push-OutputBinding` rutinu, nastavte `-Name TableBinding` parametr a `-Value` parametr rovnou na data řádku. Další podrobnosti najdete v [příkladu PowerShellu](#example) .

# <a name="python"></a>[Python](#tab/python)

K dispozici jsou dvě možnosti pro výstup zprávy řádku úložiště tabulky z funkce:

- **Návratová hodnota**: nastavte `name` vlastnost v *function.jsna* `$return` . V této konfiguraci je návratová hodnota funkce trvalá jako řádek tabulkového úložiště.

- **Imperativní**: předejte hodnotu metodě [set](/python/api/azure-functions/azure.functions.out?view=azure-python&preserve-view=true#set-val--t-----none) parametru deklarovaného jako typ [out](/python/api/azure-functions/azure.functions.out?view=azure-python&preserve-view=true) . Předaná hodnota `set` je trvalá jako zpráva centra událostí.

---

## <a name="exceptions-and-return-codes"></a>Výjimky a návratové kódy

| Vazba | Reference |
|---|---|
| Tabulka | [Kódy chyb tabulky](/rest/api/storageservices/fileservices/table-service-error-codes) |
| Objekt blob, tabulka, fronta | [Kódy chyb úložiště](/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Objekt blob, tabulka, fronta | [Řešení potíží](/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o aktivačních událostech a vazbách Azure Functions](functions-triggers-bindings.md)
