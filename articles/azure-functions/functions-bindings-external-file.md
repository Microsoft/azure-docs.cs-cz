---
title: Externí soubor vazby pro službu Azure Functions (experimentální)
description: Pomocí vazby externích souborů ve službě Azure Functions
services: functions
author: craigshoemaker
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: cshoe
ms.openlocfilehash: 39036f17b8ceafebbe3660f2074e2a6c84c4df03
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50248689"
---
# <a name="azure-functions-external-file-bindings-experimental"></a>Azure Functions externího souboru vazby (experimentální)
Tento článek ukazuje, jak pracovat se soubory od různých poskytovatelů SaaS (například Dropbox nebo disk Google) ve službě Azure Functions. Azure Functions podporuje aktivaci, vstupní a výstupní vazby externích souborů. Tyto vazby vytvořte připojení rozhraní API pro poskytovatele SaaS, nebo použijte existující připojení rozhraní API ze skupiny prostředků vaší aplikace funkcí.

> [!IMPORTANT]
> Vazby externích souborů je experimentální a může být nikdy nedorazí stav všeobecně dostupná (GA). Jsou zahrnuty pouze v Azure Functions 1.x a nejsou žádné plány přidat do služby Azure Functions 2.x. Pro scénáře, které vyžadují přístup k datům v poskytovatelé SaaS, zvažte použití [aplikace logiky, které volají do funkce](functions-twitter-email.md). Zobrazit [konektoru systému souborů aplikace logiky](../logic-apps/logic-apps-using-file-connector.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="available-file-connections"></a>Připojení k dispozici soubor

|Spojovací čára|Trigger|Vstup|Výstup|
|:-----|:---:|:---:|:---:|
|[Pole](https://www.box.com)|x|x|x
|[Dropbox](https://www.dropbox.com)|x|x|x
|[FTP](https://docs.microsoft.com/azure/app-service/app-service-deploy-ftp)|x|x|x
|[OneDrive](https://onedrive.live.com)|x|x|x
|[OneDrive pro firmy](https://onedrive.live.com/about/business/)|x|x|x
|[SFTP](https://docs.microsoft.com/azure/connectors/connectors-create-api-sftp)|x|x|x
|[Disk Google](https://www.google.com/drive/)||x|x|

> [!NOTE]
> Externí připojení souboru lze použít také v [Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list).

## <a name="trigger"></a>Trigger

Aktivační událost externích souborů umožňuje monitorovat vzdálené složky a spustit kód vaší funkce, když se zjistí změny.

## <a name="trigger---example"></a>Aktivační události – příklad

Podívejte se na příklad specifické pro jazyk:

* [Skript jazyka C#](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-script-example"></a>Aktivační události – příklad skriptu jazyka C#

Následující příklad ukazuje vazby v aktivační událost externího souboru *function.json* souboru a [funkce skriptu jazyka C#](functions-reference-csharp.md) , který používá vazba. Funkce zaznamená obsah každého souboru, který se přidá do sledované složky.

Zde je vazba dat v *function.json* souboru:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myFile",
            "type": "apiHubFileTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection": "<name of external file connection>"
        }
    ]
}
```

Tady je kód skriptu jazyka C#:

```cs
public static void Run(string myFile, TraceWriter log)
{
    log.Info($"C# File trigger function processed: {myFile}");
}
```

### <a name="trigger---javascript-example"></a>Aktivační události – příklad v jazyce JavaScript

Následující příklad ukazuje vazby v aktivační událost externího souboru *function.json* souboru a [funkce jazyka JavaScript](functions-reference-node.md) , který používá vazba. Funkce zaznamená obsah každého souboru, který se přidá do sledované složky.

Zde je vazba dat v *function.json* souboru:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myFile",
            "type": "apiHubFileTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection": "<name of external file connection>"
        }
    ]
}
```

Tady je kód jazyka JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js File trigger function processed', context.bindings.myFile);
    context.done();
};
```

## <a name="trigger---configuration"></a>Aktivační události – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru.

|Vlastnost Function.JSON | Popis|
|---------|---------|----------------------|
|**type** | Musí být nastaveno na `apiHubFileTrigger`. Tato vlastnost je nastavena automaticky, když vytvoříte aktivační událost na webu Azure Portal.|
|**direction** | Musí být nastaveno na `in`. Tato vlastnost je nastavena automaticky, když vytvoříte aktivační událost na webu Azure Portal. |
|**Jméno** | Název proměnné, která představuje položku událost v kódu funkce. | 
|**připojení**| Určuje nastavení aplikace, který uchovává připojovací řetězec. Nastavení aplikace, které se vytvoří automaticky při přidání připojení v integrace uživatelského rozhraní na webu Azure Portal.|
|**Cesta** | Složku, kterou chcete monitorovat a volitelně vzor názvu.|

### <a name="name-patterns"></a>Vzory názvů

Můžete zadat vzor názvů souborů v `path` vlastnost. Složka odkazuje musí existovat v rámci zprostředkovatele SaaS.

Příklady:

```json
"path": "input/original-{name}",
```

Tato cesta by najít soubor s názvem *původní File1.txt* v *vstupní* složky a hodnota `name` proměnné v kódu funkce by `File1.txt`.

Další příklad:

```json
"path": "input/{filename}.{fileextension}",
```

Tato cesta by také vyhledat soubor s názvem *původní File1.txt*a hodnota `filename` a `fileextension` proměnné v kódu funkce budou *původní File1* a *txt* .

Typ souboru souborů můžete omezit s použitím pevné hodnoty pro příponu souboru. Příklad:

```json
"path": "samples/{name}.png",
```

V takovém případě pouze *.png* soubory *ukázky* složky aktivovat funkci.

Složené závorky jsou speciální znaky v názvu vzory. K určení názvů souborů, které mají v názvu složených závorek, dvakrát složených závorek.
Příklad:

```json
"path": "images/{{20140101}}-{name}",
```

Tato cesta by najít soubor s názvem  *{20140101}-soundfile.mp3* v *image* složky a `name` hodnotu proměnné v kódu funkce by *soundfile.mp3*.

## <a name="trigger---usage"></a>Aktivační události – využití

V funkcí jazyka C#, můžete vytvořit vazbu na vstupní soubor data s využitím pojmenovaným parametrem v podpisu funkce, jako je třeba `<T> <name>`.
Kde `T` je datový typ, že chcete zrušit serializaci dat, a `paramName` je název zadaný v [aktivovat JSON](#trigger). Ve funkcích Node.js přistupujete data vstupních souborů s využitím `context.bindings.<name>`.

Tento soubor lze deserializovat do některé z následujících typů:

* Žádné [objekt](https://msdn.microsoft.com/library/system.object.aspx) – vhodné pro data souboru serializací JSON.
  Pokud deklarujete vlastní vstupní typ (například `FooType`), Azure Functions, pokusí se deserializovat JSON data do zadaného typu.
* String – užitečná pro data z textových souborů.

Funkce jazyka C# můžete také navázat na některý z následujících typů a modul runtime služby Functions se pokusí rekonstruovat datový soubor pomocí tohoto typu:

* `string`
* `byte[]`
* `Stream`
* `StreamReader`
* `TextReader`

<!--- ## Trigger - file receipts
The Azure Functions runtime makes sure that no external file trigger function gets called more than once for the same new or updated file.
It does so by maintaining *file receipts* to determine if a given file version has been processed.

File receipts are stored in a folder named *azure-webjobs-hosts* in the Azure storage account for your function app
(specified by the `AzureWebJobsStorage` app setting). A file receipt has the following information:

* The triggered function ("*&lt;function app name>*.Functions.*&lt;function name>*", for example: "functionsf74b96f7.Functions.CopyFile")
* The folder name
* The file type ("BlockFile" or "PageFile")
* The file name
* The ETag (a file version identifier, for example: "0x8D1DC6E70A277EF")

To force reprocessing of a file, delete the file receipt for that file from the *azure-webjobs-hosts* folder manually.
--->

## <a name="trigger---poison-files"></a>Aktivační události – počet poškozených souborů

Když selže funkce pro aktivaci externí soubor, Azure Functions zopakuje pokus o tuto funkci až 5krát. ve výchozím nastavení (včetně prvního pokusu) pro daný soubor.
Pokud selžou i všechny 5 pokusech, funkce přidá zprávu do fronty úložiště s názvem *webjobs. apihubtrigger poison*. Fronty zpráv pro poškozené soubory je objekt JSON, který obsahuje následující vlastnosti:

* FunctionId (ve formátu  *&lt;názvem aplikace function app >*. Funkce.  *&lt;název funkce >*)
* Typ souboru
* Název složky
* Název souboru
* Značka ETag (identifikátor verze souboru, například: "0x8D1DC6E70A277EF")

## <a name="input"></a>Vstup

Vstupní vazby Azure externích souborů můžete použít soubor z externí složku ve své funkci.

## <a name="input---example"></a>Input – příklad

Podívejte se na příklad specifické pro jazyk:

* [Skript jazyka C#](#input---c-script-example)
* [JavaScript](#input---javascript-example)

### <a name="input---c-script-example"></a>(Vstup) – příklad skriptu jazyka C#

Následující příklad znázorňuje externí soubor vstupní a výstupní vazby v *function.json* souboru a [funkce skriptu jazyka C#](functions-reference-csharp.md) , který používá vazba. Funkce vstupní soubor zkopíruje do výstupního souboru.

Zde je vazba dat v *function.json* souboru:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "<name of external file connection>",
      "direction": "in"
    },
    {
      "name": "myOutputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "<name of external file connection>",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Tady je kód skriptu jazyka C#:

```cs
public static void Run(string myQueueItem, string myInputFile, out string myOutputFile, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputFile = myInputFile;
}
```

### <a name="input---javascript-example"></a>(Vstup) – příklad v jazyce JavaScript

Následující příklad znázorňuje externí soubor vstupní a výstupní vazby v *function.json* souboru a [funkce jazyka JavaScript](functions-reference-node.md) , který používá vazba. Funkce vstupní soubor zkopíruje do výstupního souboru.

Zde je vazba dat v *function.json* souboru:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "<name of external file connection>",
      "direction": "in"
    },
    {
      "name": "myOutputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "<name of external file connection>",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Tady je kód jazyka JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputFile = context.bindings.myInputFile;
    context.done();
};
```

## <a name="input---configuration"></a>Vstup - konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru.

|Vlastnost Function.JSON | Popis|
|---------|---------|----------------------|
|**type** | Musí být nastaveno na `apiHubFile`. Tato vlastnost je nastavena automaticky, když vytvoříte aktivační událost na webu Azure Portal.|
|**direction** | Musí být nastaveno na `in`. Tato vlastnost je nastavena automaticky, když vytvoříte aktivační událost na webu Azure Portal. |
|**Jméno** | Název proměnné, která představuje položku událost v kódu funkce. | 
|**připojení**| Určuje nastavení aplikace, který uchovává připojovací řetězec. Nastavení aplikace, které se vytvoří automaticky při přidání připojení v integrace uživatelského rozhraní na webu Azure Portal.|
|**Cesta** | Musí obsahovat název složky a název souboru. Pokud máte například [aktivační událost fronty](functions-bindings-storage-queue.md) ve své funkci, můžete použít `"path": "samples-workitems/{queueTrigger}"` tak, aby odkazoval na soubor v `samples-workitems` složku s názvem, který odpovídá názvu souboru, určená ve zprávě aktivační události.   

## <a name="input---usage"></a>(Vstup) – využití

V funkcí jazyka C#, můžete vytvořit vazbu na vstupní soubor data s využitím pojmenovaným parametrem v podpisu funkce, jako je třeba `<T> <name>`. `T` je datový typ, že chcete zrušit serializaci dat, a `name` je název zadaný v vstupní vazby. Ve funkcích Node.js přistupujete data vstupních souborů s využitím `context.bindings.<name>`.

Tento soubor lze deserializovat do některé z následujících typů:

* Žádné [objekt](https://msdn.microsoft.com/library/system.object.aspx) – vhodné pro data souboru serializací JSON.
  Pokud deklarujete vlastní vstupní typ (například `InputType`), Azure Functions, pokusí se deserializovat JSON data do zadaného typu.
* String – užitečná pro data z textových souborů.

Funkce jazyka C# můžete také navázat na některý z následujících typů a modul runtime služby Functions se pokusí rekonstruovat datový soubor pomocí tohoto typu:

* `string`
* `byte[]`
* `Stream`
* `StreamReader`
* `TextReader`

## <a name="output"></a>Výstup

Azure externí soubor výstupní vazbu umožňuje zapisovat soubory na externí složku ve své funkci.

## <a name="output---example"></a>Výstup – příklad

Zobrazit [vstupní vazby příklad](#input---example).

## <a name="output---configuration"></a>Výstup – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru.

|Vlastnost Function.JSON | Popis|
|---------|---------|----------------------|
|**type** | Musí být nastaveno na `apiHubFile`. Tato vlastnost je nastavena automaticky, když vytvoříte aktivační událost na webu Azure Portal.|
|**direction** | Musí být nastaveno na `out`. Tato vlastnost je nastavena automaticky, když vytvoříte aktivační událost na webu Azure Portal. |
|**Jméno** | Název proměnné, která představuje položku událost v kódu funkce. | 
|**připojení**| Určuje nastavení aplikace, který uchovává připojovací řetězec. Nastavení aplikace, které se vytvoří automaticky při přidání připojení v integrace uživatelského rozhraní na webu Azure Portal.|
|**Cesta** | Musí obsahovat název složky a název souboru. Pokud máte například [aktivační událost fronty](functions-bindings-storage-queue.md) ve své funkci, můžete použít `"path": "samples-workitems/{queueTrigger}"` tak, aby odkazoval na soubor v `samples-workitems` složku s názvem, který odpovídá názvu souboru, určená ve zprávě aktivační události.   

## <a name="output---usage"></a>Výstup – využití

V funkcí jazyka C#, můžete svázat do výstupního souboru s použitím pojmenované `out` parametrů v signatuře vaše funkce, jako jsou `out <T> <name>`, kde `T` je datový typ chcete serializovat data, a `name` je název zadaný v výstupní vazbu. Ve funkcích Node.js přistupujete k výstupní soubor pomocí `context.bindings.<name>`.

Může zapisovat do výstupního souboru pomocí kteréhokoli z následujících typů:

* Žádné [objekt](https://msdn.microsoft.com/library/system.object.aspx) – je to užitečné pro serializaci JSON.
  Pokud deklarujete vlastní výstupní typ (například `out OutputType paramName`), Azure Functions se pokusí serializovat objekt do formátu JSON. Pokud výstupní parametr má hodnotu null při ukončení funkce, modul runtime služby Functions vytvoří soubor jako objekt s hodnotou null.
* String – (`out string paramName`) užitečná pro data z textových souborů. modul runtime služby Functions vytvoří soubor pouze v případě, že parametr řetězce je jiná než null při ukončení funkce.

V funkcí jazyka C# můžete také výstup na některý z následujících typů:

* `TextWriter`
* `Stream`
* `CloudFileStream`
* `ICloudFile`
* `CloudBlockFile`
* `CloudPageFile`

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o aktivačních událostech Azure functions a vazby](functions-triggers-bindings.md)
