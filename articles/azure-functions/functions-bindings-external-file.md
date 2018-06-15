---
title: Externí soubor vazby pro Azure Functions (experimentální)
description: Používání vazeb externí soubor v Azure Functions
services: functions
documentationcenter: ''
author: alexkarcher-msft
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/27/2017
ms.author: alkarche
ms.openlocfilehash: 4e9c2c336df465d7488de84bd2a02cc5d9e42f30
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2018
ms.locfileid: "27607917"
---
# <a name="azure-functions-external-file-bindings-experimental"></a>Azure vazby funkcí externí soubor (experimentální)
Tento článek ukazuje, jak pracovat se soubory od různých poskytovatelů SaaS (například Google Drive nebo Dropbox) v Azure Functions. Azure Functions podporuje aktivaci, vstup a výstup vazby pro externí soubory. Tyto vazby vytvořit rozhraní API připojení k poskytovatelů SaaS, nebo použít existující API připojení ze skupiny prostředků aplikaci funkce.

> [!IMPORTANT]
> Externí soubor vazby jsou experimentální a může být nikdy dosáhne stavu obecně k dispozici (GA). Jsou zahrnuty pouze v Azure funguje 1.x, a neexistují žádné plány, které chcete přidat do Azure Functions 2.x. Pro scénáře, které vyžadují přístup k datům ve zprostředkovatelích SaaS, zvažte použití [logiku aplikace, které volají do funkce](functions-twitter-email.md). Najdete v článku [systém souborů aplikace logiky konektor](../logic-apps/logic-apps-using-file-connector.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="available-file-connections"></a>Připojení k dispozici soubor

|Konektor|Trigger|Vstup|Výstup|
|:-----|:---:|:---:|:---:|
|[Pole](https://www.box.com)|x|x|x
|[Dropbox](https://www.dropbox.com)|x|x|x
|[FTP](https://docs.microsoft.com/azure/app-service/app-service-deploy-ftp)|x|x|x
|[OneDrive](https://onedrive.live.com)|x|x|x
|[OneDrive pro firmy](https://onedrive.live.com/about/business/)|x|x|x
|[SFTP](https://docs.microsoft.com/azure/connectors/connectors-create-api-sftp)|x|x|x
|[Google Drive](https://www.google.com/drive/)||x|x|

> [!NOTE]
> Externí připojení souboru lze použít také v [Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list).

## <a name="trigger"></a>Trigger

Externí soubor aktivační událost umožňuje monitorovat to vzdálená složka a spustíte kód funkce při zjištění změny.

## <a name="trigger---example"></a>Aktivační událost – příklad

Podívejte se na konkrétní jazyk příklad:

* [Skript jazyka C#](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-script-example"></a>Aktivační událost – příklad skriptu jazyka C#

Následující příklad ukazuje aktivační procedury externí soubor vazby ve *function.json* souboru a [funkce skriptu jazyka C#](functions-reference-csharp.md) používající vazby. Funkce zaznamená obsah každý soubor, který je přidán do sledované složky.

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

### <a name="trigger---javascript-example"></a>Aktivační událost – příklad v jazyce JavaScript

Následující příklad ukazuje aktivační procedury externí soubor vazby ve *function.json* souboru a [funkce JavaScript, která](functions-reference-node.md) používající vazby. Funkce zaznamená obsah každý soubor, který je přidán do sledované složky.

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

## <a name="trigger---configuration"></a>Aktivační událost - konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru.

|Vlastnost Function.JSON | Popis|
|---------|---------|----------------------|
|**Typ** | musí být nastavena na `apiHubFileTrigger`. Tato vlastnost nastavena automaticky při vytváření aktivační události na portálu Azure.|
|**směr** | musí být nastavena na `in`. Tato vlastnost nastavena automaticky při vytváření aktivační události na portálu Azure. |
|**Jméno** | Název proměnné, která představuje položku událostí v kódu funkce. | 
|**připojení**| Určuje nastavení aplikace, která ukládá připojovací řetězec. Nastavení aplikace je vytvořena automaticky při přidání připojení v integrací uživatelského rozhraní na portálu Azure.|
|**Cesta** | Složka monitorování a volitelně vzor názvu.|

### <a name="name-patterns"></a>Vzory názvů

Můžete zadat vzor názvů souborů v `path` vlastnost. Složka odkazuje musí existovat ve zprostředkovateli SaaS.

Příklady:

```json
"path": "input/original-{name}",
```

Tato cesta by vyhledat soubor s názvem *původní File1.txt* v *vstupní* složku a hodnota `name` proměnné v kódu funkce by `File1.txt`.

Další příklad:

```json
"path": "input/{filename}.{fileextension}",
```

Tato cesta by také vyhledat soubor s názvem *původní File1.txt*a hodnota `filename` a `fileextension` proměnných v kódu funkce by *původní File1* a *txt* .

Typ souboru souborů můžete omezit pomocí příkazu pevnou hodnotu pro tuto příponu. Příklad:

```json
"path": "samples/{name}.png",
```

V tomto případě pouze *.png* soubory *ukázky* složky aktivovat funkci.

Složené závorky jsou speciální znaky v vzory názvů. Pokud chcete zadat názvy souborů, které mají v názvu složené závorky, dvakrát složené závorky.
Příklad:

```json
"path": "images/{{20140101}}-{name}",
```

Tato cesta by vyhledat soubor s názvem *{20140101}-soundfile.mp3* v *bitové kopie* složku a `name` hodnotu proměnné v kód funkce by *soundfile.mp3*.

## <a name="trigger---usage"></a>Aktivační událost - využití

V jazyce C# funkce, vytvoření vazby na data vstupní soubor pomocí parametr s názvem v podpisu funkce, jako je třeba `<T> <name>`.
Kde `T` je datový typ chcete deserializaci dat do, a `paramName` je název, který jste zadali v [aktivovat JSON](#trigger). V Node.js funkce získáte přístup, data vstupní soubor pomocí `context.bindings.<name>`.

Soubor lze deserializovat do jakéhokoli z těchto typů:

* Všechny [objekt](https://msdn.microsoft.com/library/system.object.aspx) – vhodné pro data souboru serializací JSON.
  Pokud je deklarovat vlastní vstupní typ (například `FooType`), Azure Functions se pokusí rekonstruovat JSON data do zadaného typu.
* String – vhodné pro data textového souboru.

V jazyce C# funkce můžete také vytvořit vazbu na některý z následujících typů a Functions runtime pokusí k deserializaci dat souboru pomocí typu:

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

## <a name="trigger---poison-files"></a>Aktivační událost - poškozených souborů

Pokud se aktivace funkce externí soubor nezdaří, Azure Functions opakuje této funkce až 5 výskyty ve výchozím nastavení (včetně prvního pokusu) pro daný soubor.
Pokud selžou všechny 5 pokusů, funkce přidá zprávu do fronty úložiště s názvem *webjobs. apihubtrigger poison*. Zprávy ve frontě poškozených souborů je objekt JSON, který obsahuje následující vlastnosti:

* FunctionId (ve formátu  *&lt;funkce název aplikace >*. Funkce.  *&lt;název funkce >*)
* Typ souboru
* Název složky
* Název souboru
* Značka ETag (identifikátor verze souboru, například: "0x8D1DC6E70A277EF")

## <a name="input"></a>Vstup

Vstupní vazba Azure externí soubor můžete použít soubor z externí složky ve vaší funkci.

## <a name="input---example"></a>Vstup – příklad

Podívejte se na konkrétní jazyk příklad:

* [Skript jazyka C#](#input---c-script-example)
* [JavaScript](#input---javascript-example)

### <a name="input---c-script-example"></a>(Vstup) – příklad skriptu jazyka C#

Následující příklad ukazuje externí soubor vstupní a výstupní vazeb v *function.json* souboru a [funkce skriptu jazyka C#](functions-reference-csharp.md) používající vazby. Funkce zkopíruje do výstupního souboru vstupního souboru.

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

Následující příklad ukazuje externí soubor vstupní a výstupní vazeb v *function.json* souboru a [funkce JavaScript, která](functions-reference-node.md) používající vazby. Funkce zkopíruje do výstupního souboru vstupního souboru.

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
|**Typ** | musí být nastavena na `apiHubFile`. Tato vlastnost nastavena automaticky při vytváření aktivační události na portálu Azure.|
|**směr** | musí být nastavena na `in`. Tato vlastnost nastavena automaticky při vytváření aktivační události na portálu Azure. |
|**Jméno** | Název proměnné, která představuje položku událostí v kódu funkce. | 
|**připojení**| Určuje nastavení aplikace, která ukládá připojovací řetězec. Nastavení aplikace je vytvořena automaticky při přidání připojení v integrací uživatelského rozhraní na portálu Azure.|
|**Cesta** | Musí obsahovat název složky a název souboru. Pokud máte například [aktivační událost fronty](functions-bindings-storage-queue.md) ve funkci, můžete použít `"path": "samples-workitems/{queueTrigger}"` tak, aby odkazoval na soubor v `samples-workitems` složku s názvem, který odpovídá názvu souboru, který je uvedený v aktivační události zprávě.   

## <a name="input---usage"></a>(Vstup) – použití

V jazyce C# funkce, vytvoření vazby na data vstupní soubor pomocí parametr s názvem v podpisu funkce, jako je třeba `<T> <name>`. `T`je datový typ chcete deserializaci dat do, a `name` je název, který jste zadali v vstupní vazby. V Node.js funkce získáte přístup, data vstupní soubor pomocí `context.bindings.<name>`.

Soubor lze deserializovat do jakéhokoli z těchto typů:

* Všechny [objekt](https://msdn.microsoft.com/library/system.object.aspx) – vhodné pro data souboru serializací JSON.
  Pokud je deklarovat vlastní vstupní typ (například `InputType`), Azure Functions se pokusí rekonstruovat JSON data do zadaného typu.
* String – vhodné pro data textového souboru.

V jazyce C# funkce můžete také vytvořit vazbu na některý z následujících typů a Functions runtime pokusí k deserializaci dat souboru pomocí typu:

* `string`
* `byte[]`
* `Stream`
* `StreamReader`
* `TextReader`

## <a name="output"></a>Výstup

Azure externí soubor výstup vazby umožňuje zapisovat soubory do složky externí ve vaší funkci.

## <a name="output---example"></a>Výstup – příklad

Najdete v článku [vstupní vazby příklad](#input---example).

## <a name="output---configuration"></a>Výstup – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru.

|Vlastnost Function.JSON | Popis|
|---------|---------|----------------------|
|**Typ** | musí být nastavena na `apiHubFile`. Tato vlastnost nastavena automaticky při vytváření aktivační události na portálu Azure.|
|**směr** | musí být nastavena na `out`. Tato vlastnost nastavena automaticky při vytváření aktivační události na portálu Azure. |
|**Jméno** | Název proměnné, která představuje položku událostí v kódu funkce. | 
|**připojení**| Určuje nastavení aplikace, která ukládá připojovací řetězec. Nastavení aplikace je vytvořena automaticky při přidání připojení v integrací uživatelského rozhraní na portálu Azure.|
|**Cesta** | Musí obsahovat název složky a název souboru. Pokud máte například [aktivační událost fronty](functions-bindings-storage-queue.md) ve funkci, můžete použít `"path": "samples-workitems/{queueTrigger}"` tak, aby odkazoval na soubor v `samples-workitems` složku s názvem, který odpovídá názvu souboru, který je uvedený v aktivační události zprávě.   

## <a name="output---usage"></a>Výstup – použití

V C# funkce, můžete vytvořit vazbu k výstupnímu souboru pomocí pojmenované `out` jako parametr ve vaší podpis funkce `out <T> <name>`, kde `T` je datový typ chcete serializovat data do, a `name` je název, který jste zadali v Vazba výstup. V Node.js funkce, přístup k výstupu pomocí souboru `context.bindings.<name>`.

Můžete napsat do výstupního souboru pomocí kteréhokoli z následujících typů:

* Všechny [objekt](https://msdn.microsoft.com/library/system.object.aspx) – užitečné pro serializaci JSON.
  Pokud je deklarovat vlastní výstupní typ (například `out OutputType paramName`), Azure Functions se pokusí o serializaci objektu do formátu JSON. Pokud výstupní parametr hodnotu null při ukončení funkce, funkce runtime vytvoří soubor jako objekt s hodnotou null.
* String – (`out string paramName`) vhodné pro data textového souboru. modul runtime funkce vytvoří soubor pouze v případě, že parametr řetězce má jinou hodnotu než null, při ukončení funkce.

V jazyce C# funkce také výstup můžete na některý z následujících typů:

* `TextWriter`
* `Stream`
* `CloudFileStream`
* `ICloudFile`
* `CloudBlockFile`
* `CloudPageFile`

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o Azure functions triggerů a vazeb](functions-triggers-bindings.md)
