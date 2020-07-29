---
title: Azure Functions vlastní obslužné rutiny (Preview)
description: Naučte se používat Azure Functions s libovolným jazykem nebo verzí modulu runtime.
author: craigshoemaker
ms.author: cshoe
ms.date: 3/18/2020
ms.topic: article
ms.openlocfilehash: cdbb5bbde1e5efef9bef992a62a54f1525a16df7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85052571"
---
# <a name="azure-functions-custom-handlers-preview"></a>Azure Functions vlastní obslužné rutiny (Preview)

Každá aplikace Functions se spustí obslužnou rutinou specifickou pro konkrétní jazyk. I když Azure Functions podporuje mnoho [obslužných rutin jazyka](./supported-languages.md) ve výchozím nastavení, existují případy, kdy budete chtít mít větší kontrolu nad prostředím pro provádění aplikace. Vlastní obslužné rutiny poskytují tento další ovládací prvek.

Vlastní obslužné rutiny jsou jednoduché webové servery, které přijímají události z hostitele Functions. Libovolný jazyk, který podporuje primitivní prvky HTTP, může implementovat vlastní obslužnou rutinu.

Vlastní obslužné rutiny se nejlépe hodí pro situace, kdy chcete:

- Implementujte aplikaci Function App v jazyce, který není oficiálně podporován.
- Implementujte aplikaci Function App v jazykové verzi nebo modul runtime, který není ve výchozím nastavení podporován.
- Poskytněte přesnější kontrolu nad spouštěcím prostředím aplikace Function App.

S vlastními obslužnými rutinami jsou všechny [triggery a vstupní a výstupní vazby](./functions-triggers-bindings.md) podporovány prostřednictvím [sad rozšíření](./functions-bindings-register.md).

## <a name="overview"></a>Přehled

Následující diagram znázorňuje vztah mezi hostitelem funkcí a webovým serverem implementovaným jako vlastní obslužná rutina.

![Přehled Azure Functions vlastní obslužné rutiny](./media/functions-custom-handlers/azure-functions-custom-handlers-overview.png)

- Události aktivují požadavek odeslaný na hostitele Functions. Událost uchovává buď nezpracovaná datová část HTTP (pro funkce aktivované protokolem HTTP bez vazeb), nebo datovou část, která obsahuje data vstupní vazby pro funkci.
- Hostitel funkce potom proxy požadavek vystaví na webový server vyvoláním [datové části požadavku](#request-payload).
- Webový server spustí jednotlivé funkce a vrátí [datovou část odpovědi](#response-payload) na hostitele Functions.
- Služby Functions hostují odpověď jako výstupní datovou vazbu k cíli.

Azure Functions aplikace implementovaná jako vlastní obslužná rutina musí nakonfigurovat *host.jsna* souborech a *function.jsna* souborech podle několika konvencí.

## <a name="application-structure"></a>Struktura aplikace

K implementaci vlastní obslužné rutiny potřebujete následující aspekty aplikace:

- *host.jsv* souboru v kořenovém adresáři vaší aplikace
- *function.js* souboru pro jednotlivé funkce (uvnitř složky, která odpovídá názvu funkce)
- Příkaz, skript nebo spustitelný soubor, který spouští webový server

Následující diagram ukazuje, jak tyto soubory vypadají v systému souborů pro funkci s názvem "Order".

```bash
| /order
|   function.json
|
| host.json
```

### <a name="configuration"></a>Konfigurace

Aplikace je nakonfigurována prostřednictvím *host.jsv* souboru. Tento soubor oznamuje hostiteli Functions, kam odesílají požadavky, ukázáním na webový server schopný zpracovávat události HTTP.

Vlastní obslužná rutina je definována konfigurací *host.jsv* souboru s podrobnostmi o tom, jak spustit webový server prostřednictvím `httpWorker` části.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "server.exe"
        }
    }
}
```

`httpWorker`Oddíl odkazuje na cíl definovaný v `defaultExecutablePath` . Cíl spuštění může být buď příkaz, spustitelný soubor nebo soubor, kde je webový server implementován.

Pro skriptované aplikace `defaultExecutablePath` odkazují na modul runtime skriptovacího jazyka a `defaultWorkerPath` odkazuje na umístění souboru skriptu. Následující příklad ukazuje, jak je aplikace JavaScriptu v Node.js nakonfigurovaná jako vlastní obslužná rutina.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js"
        }
    }
}
```

Argumenty můžete předat také pomocí `arguments` pole:

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js",
            "arguments": [ "--argument1", "--argument2" ]
        }
    }
}
```

Argumenty jsou nezbytné pro mnoho nastavení ladění. Další podrobnosti najdete v části [ladění](#debugging) .

> [!NOTE]
> *host.jsv* souboru musí být ve struktuře adresáře na stejné úrovni jako běžící webový server. Některé jazyky a sady nástrojů nemusí ve výchozím nastavení umístit tento soubor do kořenového adresáře aplikace.

#### <a name="bindings-support"></a>Podpora vazeb

Standardní triggery spolu se vstupními a výstupními vazbami jsou k dispozici odkazem na [sady rozšíření](./functions-bindings-register.md) ve vašem *host.jsv* souboru.

### <a name="function-metadata"></a>Metadata funkce

Při použití s vlastní obslužnou rutinou se *function.js* obsahu neliší od toho, jak byste definovali funkci v jakémkoli jiném kontextu. Jediným požadavkem je, že *function.js* souborů musí být ve složce s názvem, aby odpovídala názvu funkce.

### <a name="request-payload"></a>Datová část požadavku

Datová část požadavku pro funkce čistého protokolu HTTP je nezpracované datové části požadavku HTTP. Funkce čistého protokolu HTTP jsou definovány jako funkce bez vstupních nebo výstupních vazeb, které vracejí odpověď HTTP.

Všechny ostatní typy funkcí, které zahrnují vstupní, výstupní vazby nebo aktivované prostřednictvím jiného zdroje událostí než HTTP, mají vlastní datovou část požadavku.

Následující kód představuje ukázkovou datovou část požadavku. Datová část obsahuje strukturu JSON se dvěma členy: `Data` a `Metadata` .

`Data`Člen obsahuje klíče, které odpovídají vstupům a názvům triggerů, jak jsou definovány v poli vazeb v *function.jsv* souboru.

`Metadata`Člen zahrnuje [metadata generovaná ze zdroje události](./functions-bindings-expressions-patterns.md#trigger-metadata).

S ohledem na vazby definované v následujících *function.js* souboru:

```json
{
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages-incoming",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "type": "queue",
      "direction": "out",
      "queueName": "messages-outgoing",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

Vrátí se datová část požadavku podobná tomuto příkladu:

```json
{
    "Data": {
        "myQueueItem": "{ message: \"Message sent\" }"
    },
    "Metadata": {
        "DequeueCount": 1,
        "ExpirationTime": "2019-10-16T17:58:31+00:00",
        "Id": "800ae4b3-bdd2-4c08-badd-f08e5a34b865",
        "InsertionTime": "2019-10-09T17:58:31+00:00",
        "NextVisibleTime": "2019-10-09T18:08:32+00:00",
        "PopReceipt": "AgAAAAMAAAAAAAAAAgtnj8x+1QE=",
        "sys": {
            "MethodName": "QueueTrigger",
            "UtcNow": "2019-10-09T17:58:32.2205399Z",
            "RandGuid": "24ad4c06-24ad-4e5b-8294-3da9714877e9"
        }
    }
}
```

### <a name="response-payload"></a>Datová část odpovědi

Podle konvencí jsou odpovědi na funkce naformátované jako páry klíč/hodnota. Mezi podporované klíče patří:

| <nobr>Klíč datové části</nobr>   | Datový typ | Poznámky                                                      |
| ------------- | --------- | ------------------------------------------------------------ |
| `Outputs`     | JSON      | Uchovává hodnoty odpovědí definované `bindings` polem, které *function.jsv* souboru.<br /><br />Pokud je například funkce nakonfigurovaná s výstupní vazbou úložiště objektů BLOB s názvem "blob", pak `Outputs` obsahuje klíč s názvem `blob` , který je nastavený na hodnotu objektu BLOB. |
| `Logs`        | pole     | Zprávy se zobrazí v protokolech vyvolání funkcí.<br /><br />Při spuštění v Azure se zprávy zobrazí v Application Insights. |
| `ReturnValue` | řetězec    | Slouží k poskytnutí odpovědi, pokud je výstup nakonfigurován jako `$return` v *function.js* v souboru. |

Podívejte se na [Příklad pro vzorovou datovou část](#bindings-implementation).

## <a name="examples"></a>Příklady

Vlastní obslužné rutiny se dají implementovat v jakémkoli jazyce, který podporuje události HTTP. I když Azure Functions [plně podporuje JavaScript a Node.js](./functions-reference-node.md), následující příklady ukazují, jak implementovat vlastní obslužnou rutinu pomocí javascriptu v Node.js pro účely instrukcí.

> [!TIP]
> I když se naučíte, jak implementovat vlastní obslužnou rutinu v jiných jazycích, uvedené příklady založené na Node.js můžou být užitečné také v případě, že jste chtěli spustit aplikaci Functions v nepodporované verzi Node.js.

## <a name="http-only-function"></a>Funkce pouze HTTP

Následující příklad ukazuje, jak nakonfigurovat funkci aktivované protokolem HTTP bez dalších vazeb nebo výstupů. Scénář implementovaný v tomto příkladu obsahuje funkci s názvem `http` , která přijímá `GET` nebo `POST` .

Následující fragment kódu představuje způsob, jakým se skládá požadavek na funkci.

```http
POST http://127.0.0.1:7071/api/hello HTTP/1.1
content-type: application/json

{
  "message": "Hello World!"
}
```

<a id="hello-implementation" name="hello-implementation"></a>

### <a name="implementation"></a>Implementace

Ve složce s názvem *http*nakonfiguruje *function.jsv* souboru funkce aktivované protokolem HTTP.

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": ["get", "post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ]
}
```

Funkce je nakonfigurována tak, aby přijímala `GET` požadavky i i `POST` Výsledná hodnota je poskytnuta prostřednictvím argumentu s názvem `res` .

V kořenovém adresáři aplikace je *host.js* pro soubor nakonfigurovaný tak, aby běžel Node.js a odkazoval na `server.js` soubor.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js"
        }
    }
}
```

Soubor *server.js* souboru implementuje webový server a funkci http.

```javascript
const express = require("express");
const app = express();

app.use(express.json());

const PORT = process.env.FUNCTIONS_HTTPWORKER_PORT;

const server = app.listen(PORT, "localhost", () => {
  console.log(`Your port is ${PORT}`);
  const { address: host, port } = server.address();
  console.log(`Example app listening at http://${host}:${port}`);
});

app.get("/hello", (req, res) => {
  res.json("Hello World!");
});

app.post("/hello", (req, res) => {
  res.json({ value: req.body });
});
```

V tomto příkladu se Express používá k vytvoření webového serveru pro zpracování událostí HTTP a je nastaven na naslouchání požadavkům přes `FUNCTIONS_HTTPWORKER_PORT` .

Funkce je definována v cestě k `/hello` . `GET`požadavky jsou zpracovávány vrácením jednoduchého objektu JSON a `POST` požadavky mají přístup k textu žádosti prostřednictvím `req.body` .

Trasa pro funkci Order tady je `/hello` a není, `/api/hello` protože hostitel Functions hostuje požadavek na vlastní obslužnou rutinu.

>[!NOTE]
>Nejedná se `FUNCTIONS_HTTPWORKER_PORT` o veřejný port, který se používá k volání funkce. Tento port je používán hostitelem Functions pro volání vlastní obslužné rutiny.

## <a name="function-with-bindings"></a>Funkce s vazbami

Scénář implementovaný v tomto příkladu obsahuje funkci s názvem `order` , která přijímá `POST` datovou část reprezentující produktovou objednávku. Při odeslání objednávky do funkce se vytvoří Queue Storage zpráva a vrátí se odpověď HTTP.

```http
POST http://127.0.0.1:7071/api/order HTTP/1.1
content-type: application/json

{
  "id": 1005,
  "quantity": 2,
  "color": "black"
}
```

<a id="bindings-implementation" name="bindings-implementation"></a>

### <a name="implementation"></a>Implementace

Ve složce s názvem *order*function.jsse *v* souboru NAkonfiguruje funkce aktivované protokolem HTTP.

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "authLevel": "function",
      "direction": "in",
      "name": "req",
      "methods": ["post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "queue",
      "name": "message",
      "direction": "out",
      "queueName": "orders",
      "connection": "AzureWebJobsStorage"
    }
  ]
}

```

Tato funkce je definovaná jako [funkce aktivovaná protokolem HTTP](./functions-bindings-http-webhook-trigger.md) , která vrátí [odpověď HTTP](./functions-bindings-http-webhook-output.md) a vytvoří výstup zprávy [úložiště fronty](./functions-bindings-storage-queue-output.md) .

V kořenovém adresáři aplikace je *host.js* pro soubor nakonfigurovaný tak, aby běžel Node.js a odkazoval na `server.js` soubor.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js"
        }
    }
}
```

Soubor *server.js* souboru implementuje webový server a funkci http.

```javascript
const express = require("express");
const app = express();

app.use(express.json());

const PORT = process.env.FUNCTIONS_HTTPWORKER_PORT;

const server = app.listen(PORT, "localhost", () => {
  console.log(`Your port is ${PORT}`);
  const { address: host, port } = server.address();
  console.log(`Example app listening at http://${host}:${port}`);
});

app.post("/order", (req, res) => {
  const message = req.body.Data.req.Body;
  const response = {
    Outputs: {
      message: message,
      res: {
        statusCode: 200,
        body: "Order complete"
      }
    },
    Logs: ["order processed"]
  };
  res.json(response);
});
```

V tomto příkladu se Express používá k vytvoření webového serveru pro zpracování událostí HTTP a je nastaven na naslouchání požadavkům přes `FUNCTIONS_HTTPWORKER_PORT` .

Funkce je definována v cestě k `/order` .  Trasa pro funkci Order tady je `/order` a není, `/api/order` protože hostitel Functions hostuje požadavek na vlastní obslužnou rutinu.

Po `POST` odeslání požadavků do této funkce jsou data zveřejněna prostřednictvím několika bodů:

- Text žádosti je k dispozici prostřednictvím`req.body`
- Data odeslaná do funkce jsou k dispozici prostřednictvím`req.body.Data.req.Body`

Odpověď funkce je formátována na dvojici klíč/hodnota, kde `Outputs` člen obsahuje hodnotu JSON, kde klíče odpovídají výstupům, jak jsou definovány v *function.jsv* souboru.

Když je nastavení `message` rovno zprávě, která byla součástí z požadavku, a `res` k očekávané odpovědi HTTP, tato funkce vypíše zprávu do Queue Storage a vrátí odpověď HTTP.

## <a name="debugging"></a>Ladění

Chcete-li ladit vlastní aplikaci obslužné rutiny Functions, je nutné přidat argumenty, které jsou vhodné pro jazyk a modul runtime pro povolení ladění.

Například pro ladění aplikace Node.js `--inspect` je příznak předán jako argument v *host.js* v souboru.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js",
            "arguments": [ "--inspect" ]
        }
    }
}
```

> [!NOTE]
> Konfigurace ladění je součástí *host.jsv* souboru. to znamená, že před nasazením do produkčního prostředí možná budete muset některé argumenty odebrat.

Pomocí této konfigurace můžete spustit hostitelský proces funkce pomocí následujícího příkazu:

```bash
func host start
```

Po spuštění procesu můžete připojit ladicí program a zarážky volání.

### <a name="visual-studio-code"></a>Visual Studio Code

Následující příklad je Ukázková konfigurace, která předvádí, jak můžete nastavit *launch.js* pro připojení aplikace k ladicímu programu Visual Studio Code.

Tento příklad je určen pro Node.js, takže možná budete muset změnit tento příklad pro jiné jazyky nebo moduly runtime.

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Attach to Node Functions",
      "type": "node",
      "request": "attach",
      "port": 9229,
      "preLaunchTask": "func: host start"
    }
  ]
}
```

## <a name="deploying"></a>Nasazení

Vlastní obslužnou rutinu lze nasadit téměř každou možnost hostování Azure Functions (viz [omezení](#restrictions)). Pokud vaše obslužná rutina vyžaduje vlastní závislosti (například modul runtime jazyka), může být nutné použít [vlastní kontejner](./functions-create-function-linux-custom-image.md).

K nasazení vlastní aplikace obslužných rutin pomocí Azure Functions Core Tools spusťte následující příkaz.

```bash
func azure functionapp publish $functionAppName --no-build --force
```

## <a name="restrictions"></a>Omezení

- Webový server musí být spuštěn do 60 sekund.

## <a name="samples"></a>ukázky

Příklady implementace funkcí v různých jazycích najdete v části [vlastní obslužné rutiny ukázky v úložišti GitHub](https://github.com/Azure-Samples/functions-custom-handlers) .
