---
title: Vlastní obslužné rutiny Azure Functions (preview)
description: Naučte se používat Funkce Azure s libovolným jazykem nebo runtime verzí.
author: craigshoemaker
ms.author: cshoe
ms.date: 3/18/2020
ms.topic: article
ms.openlocfilehash: 5abc216e182d7becd9d6f42e0f566ee96d09c2a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479251"
---
# <a name="azure-functions-custom-handlers-preview"></a>Vlastní obslužné rutiny Azure Functions (preview)

Každá aplikace Funkce je spuštěna obslužnou rutinou specifickou pro jazyk. Zatímco Azure Functions ve výchozím nastavení podporuje mnoho [jazykových obslužných rutin,](./supported-languages.md) existují případy, kdy můžete chtít další kontrolu nad prostředím spuštění aplikace. Vlastní obslužné rutiny poskytují tento další ovládací prvek.

Vlastní obslužné rutiny jsou zjednodušené webové servery, které přijímají události z hostitele Functions. Libovolný jazyk, který podporuje základní prvky HTTP můžete implementovat vlastní obslužnou rutinu.

Vlastní obslužné rutiny jsou nejvhodnější pro situace, kdy chcete:

- Implementace aplikace Functions v jazyce mimo oficiálně podporované jazyky
- Implementace aplikace Functions v jazykové verzi nebo modulu runtime, který není ve výchozím nastavení podporován
- Mít podrobnou kontrolu nad prostředím spouštění aplikací

S vlastní obslužné rutiny, všechny [aktivační události a vstupní a výstupní vazby](./functions-triggers-bindings.md) jsou podporovány prostřednictvím rozšíření [svazky](./functions-bindings-register.md).

## <a name="overview"></a>Přehled

Následující diagram znázorňuje vztah mezi hostitelem Functions a webovým serverem implementovaným jako vlastní obslužná rutina.

![Přehled vlastní obslužné rutiny Azure Functions](./media/functions-custom-handlers/azure-functions-custom-handlers-overview.png)

- Události aktivují požadavek odeslaný hostiteli Functions. Událost nese buď nezpracovaná datová část PROTOKOLU HTTP (pro funkce spouštěné protokolem HTTP bez vazby), nebo datová část, která obsahuje vstupní data vazby pro funkci.
- Hostitel Functions pak přetáčí požadavek na webový server vydáním [datové části požadavku](#request-payload).
- Webový server spustí jednotlivé funkce a vrátí [datovou část odpovědi](#response-payload) na hostitele Functions.
- Funkce hostitele proxy odpověď jako datová část výstupní vazby na cíl.

Aplikace Azure Functions implementovaná jako vlastní obslužná rutina musí nakonfigurovat soubory *host.json* a *function.json* podle několika konvencí.

## <a name="application-structure"></a>Struktura aplikace

Chcete-li implementovat vlastní obslužnou rutinu, potřebujete následující aspekty aplikace:

- Soubor *host.json* v kořenovém adresáři aplikace
- Soubor *function.json* pro každou funkci (uvnitř složky, která odpovídá názvu funkce)
- Příkaz, skript nebo spustitelný soubor, který spouští webový server

Následující diagram znázorňuje, jak tyto soubory vypadají v systému souborů pro funkci s názvem "pořadí".

```bash
| /order
|   function.json
|
| host.json
```

### <a name="configuration"></a>Konfigurace

Aplikace je konfigurována prostřednictvím souboru *host.json.* Tento soubor informuje hostitele functions, kam má odesílat požadavky, a to tak, že odkazuje na webový server schopný zpracovávat události PROTOKOLU HTTP.

Vlastní obslužná rutina je definována konfigurací souboru *host.json* s podrobnostmi o tom, jak spustit webový server prostřednictvím oddílu. `httpWorker`

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

Oddíl `httpWorker` odkazuje na cíl definovaný `defaultExecutablePath`. Cílem spuštění může být příkaz, spustitelný soubor nebo soubor, kde je implementován webový server.

U skriptovaných `defaultExecutablePath` aplikací odkazuje na dobu běhu `defaultWorkerPath` jazyka skriptu a odkazuje na umístění souboru skriptu. Následující příklad ukazuje, jak je aplikace JavaScript v souboru Node.js konfigurována jako vlastní obslužná rutina.

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

Argumenty můžete také předat `arguments` pomocí pole:

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

Argumenty jsou nezbytné pro mnoho nastavení ladění. Další podrobnosti najdete v části [Ladění.](#debugging)

> [!NOTE]
> Soubor *host.json* musí být ve struktuře adresáře na stejné úrovni jako spuštěný webový server. Některé jazyky a řetězce nástrojů nemusí umístit tento soubor do kořenového adresáře aplikace ve výchozím nastavení.

#### <a name="bindings-support"></a>Podpora pro Bindings

Standardní aktivační události spolu se vstupními a výstupními vazbami jsou k dispozici odkazováním na [balíčky rozšíření](./functions-bindings-register.md) v souboru *host.json.*

### <a name="function-metadata"></a>Metadata funkce

Při použití s vlastní obslužnou rutinou se obsah *function.json* neliší od toho, jak byste definovali funkci v jiném kontextu. Jediným požadavkem je, že soubory *function.json* musí být ve složce s názvem tak, aby odpovídala názvu funkce.

### <a name="request-payload"></a>Požadavek na datová část

Datová část požadavku pro čisté funkce PROTOKOLU HTTP je nezpracovaná datová část požadavku HTTP. Funkce Pure HTTP jsou definovány jako funkce bez vstupních nebo výstupních vazeb, které vracejí odpověď HTTP.

Jakýkoli jiný typ funkce, která zahrnuje buď vstup, výstupní vazby nebo je spuštěna prostřednictvím zdroje událostí než HTTP mají vlastní datové části požadavku.

Následující kód představuje ukázkovou datovou část požadavku. Datová část zahrnuje strukturu JSON `Data` se `Metadata`dvěma členy: a .

Člen `Data` obsahuje klíče, které odpovídají vstupní a aktivační názvy, jak je definováno v matici vazby v souboru *function.json.*

Člen `Metadata` obsahuje [metadata vygenerovaná ze zdroje událostí](./functions-bindings-expressions-patterns.md#trigger-metadata).

Vzhledem k vazbám definovaným v následujícím souboru *function.json:*

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

Je vrácena datová část požadavku podobná tomuto příkladu:

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

Podle konvence jsou odpovědi na funkce formátovány jako dvojice klíč/hodnota. Mezi podporované klíče patří:

| <nobr>Klíč datové části</nobr>   | Datový typ | Poznámky                                                      |
| ------------- | --------- | ------------------------------------------------------------ |
| `Outputs`     | JSON      | Obsahuje hodnoty odpovědí definované `bindings` polem souboru *function.json.*<br /><br />Například pokud je funkce nakonfigurována s vazbou výstupu úložiště `Outputs` objektů blob `blob`s názvem "objekt blob", pak obsahuje klíč s názvem , který je nastaven na hodnotu objektu blob. |
| `Logs`        | pole     | Zprávy se zobrazí v protokolech vyvolání funkcí.<br /><br />Když běží v Azure, zprávy se zobrazí v Application Insights. |
| `ReturnValue` | řetězec    | Slouží k poskytnutí odpovědi při konfiguraci výstupu jako `$return` v souboru *function.json.* |

Viz [příklad ukázkové datové části](#bindings-implementation).

## <a name="examples"></a>Příklady

Vlastní obslužné rutiny lze implementovat v libovolném jazyce, který podporuje události PROTOKOLU HTTP. Zatímco Funkce Azure [plně podporuje JavaScript a Node.js](./functions-reference-node.md), následující příklady ukazují, jak implementovat vlastní obslužnou rutinu pomocí jazyka JavaScript v Node.js pro účely instrukce.

> [!TIP]
> Zatímco je průvodce pro učení, jak implementovat vlastní obslužnou rutinu v jiných jazycích, Node.js příklady uvedené zde může být také užitečné, pokud jste chtěli spustit aplikace Funkce v nepodporované verzi Node.js.

## <a name="http-only-function"></a>Pouze http

Následující příklad ukazuje, jak nakonfigurovat funkci spouštěnou protokolem HTTP bez dalších vazeb nebo výstupů. Scénář implementovaný v tomto příkladu obsahuje funkci s názvem, `http` která přijímá `GET` nebo `POST` .

Následující úryvek představuje, jak se skládá požadavek na funkci.

```http
POST http://127.0.0.1:7071/api/hello HTTP/1.1
content-type: application/json

{
  "message": "Hello World!"
}
```

<a id="hello-implementation" name="hello-implementation"></a>

### <a name="implementation"></a>Implementace

Ve složce s názvem *http*konfiguruje soubor *function.json* funkci spouštěnou protokolem HTTP.

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

Funkce je nakonfigurována `GET` `POST` tak, aby přijímala požadavky i `res`požadavky a výsledná hodnota je poskytována prostřednictvím argumentu s názvem .

V kořenovém adresáři aplikace je soubor *host.json* nakonfigurován tak, `server.js` aby spouštěl soubor Node.js a ukazoval soubor.

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

Soubor *souborový server.js* implementuje webový server a funkci HTTP.

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

V tomto příkladu express se používá k vytvoření webového serveru pro zpracování `FUNCTIONS_HTTPWORKER_PORT`událostí HTTP a je nastaven a naslouchá požadavkům prostřednictvím .

Funkce je definována na `/hello`cestě . `GET`požadavky jsou zpracovávány vrácením jednoduchého objektu `POST` JSON a požadavky `req.body`mají přístup k tělu požadavku prostřednictvím .

Trasa pro funkci objednávky `/hello` `/api/hello` je zde a ne proto, že hostitel Functions proxy požadavek na vlastní obslužnou rutinu.

>[!NOTE]
>Není `FUNCTIONS_HTTPWORKER_PORT` veřejný přístupový port používaný k volání funkce. Tento port používá hostitel Functions k volání vlastní obslužné rutiny.

## <a name="function-with-bindings"></a>Funkce s vazbami

Scénář implementovaný v tomto příkladu obsahuje funkci s názvem, `order` která přijímá `POST` datovou část představující objednávku produktu. Jako objednávka je zaúčtována do funkce, je vytvořena zpráva úložiště fronty a je vrácena odpověď HTTP.

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

Ve složce s názvem *Order*nakonfiguruje soubor *function.json* funkci spouštěnou protokolem HTTP.

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

Tato funkce je definována jako [funkce spouštěná protokolem HTTP,](./functions-bindings-http-webhook-trigger.md) která vrací [odpověď HTTP](./functions-bindings-http-webhook-output.md) a vypisuje zprávu [o úložišti fronty.](./functions-bindings-storage-queue-output.md)

V kořenovém adresáři aplikace je soubor *host.json* nakonfigurován tak, `server.js` aby spouštěl soubor Node.js a ukazoval soubor.

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

Soubor *souborový server.js* implementuje webový server a funkci HTTP.

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

V tomto příkladu express se používá k vytvoření webového serveru pro zpracování `FUNCTIONS_HTTPWORKER_PORT`událostí HTTP a je nastaven a naslouchá požadavkům prostřednictvím .

Funkce je definována na `/order` cestě .  Trasa pro funkci objednávky `/order` `/api/order` je zde a ne proto, že hostitel Functions proxy požadavek na vlastní obslužnou rutinu.

Jako `POST` požadavky jsou odesílány do této funkce, data jsou vystaveny prostřednictvím několika bodů:

- Tělo žádosti je k dispozici prostřednictvím`req.body`
- Údaje zaúčtované do funkce jsou k dispozici prostřednictvím`req.body.Data.req.Body`

Odpověď funkce je formátována do páru klíč/hodnota, `Outputs` kde člen obsahuje hodnotu JSON, kde klíče odpovídají výstupům definovaným v souboru *function.json.*

Nastavením `message` rovné zprávy, která přišla z `res` požadavku a očekávané odpovědi HTTP, tato funkce vyveze zprávu do úložiště fronty a vrátí odpověď HTTP.

## <a name="debugging"></a>Ladění

Chcete-li ladit vlastní obslužnou rutinu aplikace funkce, musíte přidat argumenty vhodné pro jazyk a runtime povolit ladění.

Například ladit aplikaci Node.js, `--inspect` příznak je předán jako argument v souboru *host.json.*

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
> Konfigurace ladění je součástí souboru *host.json,* což znamená, že budete muset odebrat některé argumenty před nasazením do produkčního prostředí.

Pomocí této konfigurace můžete spustit hostitelský proces funkce pomocí následujícího příkazu:

```bash
func host start
```

Po spuštění procesu můžete připojit ladicí program a zarážky přístupů.

### <a name="visual-studio-code"></a>Visual Studio Code

Následující příklad je ukázková konfigurace, která ukazuje, jak můžete nastavit soubor *launch.json* pro připojení aplikace k ladicímu programu kódu Visual Studia.

Tento příklad je pro Node.js, takže budete muset změnit tento příklad pro jiné jazyky nebo runtimes.

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

Vlastní obslužná rutina může být nasazena téměř na všechny možnosti hostování Azure Functions (viz [omezení).](#restrictions) Pokud vaše obslužná rutina vyžaduje vlastní závislosti (například za běhu jazyka), budete muset použít [vlastní kontejner](./functions-create-function-linux-custom-image.md).

## <a name="restrictions"></a>Omezení

- Vlastní obslužné rutiny nejsou podporovány v plánech spotřeby Linuxu.
- Webový server musí být zahájen do 60 sekund.

## <a name="samples"></a>ukázky

Naleznete vlastní [obslužné rutiny ukázky GitHub úložiště](https://github.com/Azure-Samples/functions-custom-handlers) příklady, jak implementovat funkce v různých jazycích.
