---
title: Azure Functions vlastní obslužné rutiny
description: Naučte se používat Azure Functions s libovolným jazykem nebo verzí modulu runtime.
author: anthonychu
ms.author: antchu
ms.date: 12/1/2020
ms.topic: article
ms.openlocfilehash: 099f90ba8c5d9dabb6c4c505e50d8c077e3eaf0f
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2020
ms.locfileid: "96746025"
---
# <a name="azure-functions-custom-handlers"></a>Azure Functions vlastní obslužné rutiny

Každá aplikace Functions se spustí obslužnou rutinou specifickou pro konkrétní jazyk. I když Azure Functions podporuje mnoho [obslužných rutin jazyka](./supported-languages.md) ve výchozím nastavení, existují případy, kdy je vhodné použít jiné jazyky nebo moduly runtime.

Vlastní obslužné rutiny jsou jednoduché webové servery, které přijímají události z hostitele Functions. Libovolný jazyk, který podporuje primitivní prvky HTTP, může implementovat vlastní obslužnou rutinu.

Vlastní obslužné rutiny se nejlépe hodí pro situace, kdy chcete:

- Implementujte aplikaci Function App v jazyce, který není v současné době podporován, například jít nebo Rust.
- Implementujte aplikaci Function App v modulu runtime, který není aktuálně podporován, například deno.

S vlastními obslužnými rutinami můžete použít [triggery a vstupní a výstupní vazby](./functions-triggers-bindings.md) prostřednictvím [sad rozšíření](./functions-bindings-register.md).

Začínáme s Azure Functions vlastní obslužné rutiny s [rychlým startem v cestách a Rust](create-first-function-vs-code-other.md).

## <a name="overview"></a>Přehled

Následující diagram znázorňuje vztah mezi hostitelem funkcí a webovým serverem implementovaným jako vlastní obslužná rutina.

![Přehled Azure Functions vlastní obslužné rutiny](./media/functions-custom-handlers/azure-functions-custom-handlers-overview.png)

1. Každá událost aktivuje požadavek odeslaný na hostitele Functions. Událost je jakákoli aktivační událost, která je podporována Azure Functions.
1. Hostitel funkcí potom vydá [datovou část požadavku](#request-payload) webovému serveru. Datová část obsahuje aktivační událost a vstupní vazby dat a další metadata pro funkci.
1. Webový server spustí jednotlivé funkce a vrátí [datovou část odpovědi](#response-payload) na hostitele Functions.
1. Hostitel Functions předá data z odpovědi na výstupní vazby funkce pro zpracování.

Azure Functions aplikace implementovaná jako vlastní obslužná rutina musí nakonfigurovat *host.jszapnutá*, *local.settings.jszapnutá* a *function.js* soubory podle několika konvencí.

## <a name="application-structure"></a>Struktura aplikace

K implementaci vlastní obslužné rutiny potřebujete následující aspekty aplikace:

- *host.jsv* souboru v kořenovém adresáři vaší aplikace
- *local.settings.jsv* souboru v kořenovém adresáři vaší aplikace
- *function.js* souboru pro jednotlivé funkce (uvnitř složky, která odpovídá názvu funkce)
- Příkaz, skript nebo spustitelný soubor, který spouští webový server

Následující diagram ukazuje, jak tyto soubory vypadají v systému souborů pro funkci s názvem "MyQueueFunction" a vlastní spustitelný soubor obslužné rutiny s názvem *handler.exe*.

```bash
| /MyQueueFunction
|   function.json
|
| host.json
| local.settings.json
| handler.exe
```

### <a name="configuration"></a>Konfigurace

Aplikace se konfiguruje prostřednictvím *host.jsv* a *local.settings.jsna* souborech.

#### <a name="hostjson"></a>host.jsna

*host.jsv systému* oznamuje hostiteli Functions, kam odesílají požadavky tak, že odkazují na webový server schopný zpracovávat události http.

Vlastní obslužná rutina je definována konfigurací *host.jsv* souboru s podrobnostmi o tom, jak spustit webový server prostřednictvím `customHandler` části.

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    }
  }
}
```

`customHandler`Oddíl odkazuje na cíl definovaný v `defaultExecutablePath` . Cíl spuštění může být buď příkaz, spustitelný soubor nebo soubor, kde je webový server implementován.

Použijte `arguments` pole k předání všech argumentů spustitelnému souboru. Argumenty podporují rozšíření proměnných prostředí (nastavení aplikace) pomocí `%%` notace Notation.

Pracovní adresář používaný spustitelným souborem lze také změnit pomocí `workingDirectory` .

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "app/handler.exe",
      "arguments": [
        "--database-connection-string",
        "%DATABASE_CONNECTION_STRING%"
      ],
      "workingDirectory": "app"
    }
  }
}
```

##### <a name="bindings-support"></a>Podpora vazeb

Standardní triggery spolu se vstupními a výstupními vazbami jsou k dispozici odkazem na [sady rozšíření](./functions-bindings-register.md) ve vašem *host.jsv* souboru.

#### <a name="localsettingsjson"></a>local.settings.json

*local.settings.js* definuje nastavení aplikace používané při místním spuštění aplikace Function App. Vzhledem k tomu, že mohou obsahovat tajné kódy, *local.settings.js* by měl být vyloučen ze správy zdrojového kódu. V Azure použijte místo toho nastavení aplikace.

Pro vlastní obslužné rutiny nastavte `FUNCTIONS_WORKER_RUNTIME` na `Custom` in *local.settings.json*.

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "Custom"
  }
}
```

> [!NOTE]
> `Custom` nemusí být rozpoznán jako platný modul runtime v plánech pro Linux Premium nebo App Service. Pokud je to váš cíl nasazení, nastavte `FUNCTIONS_WORKER_RUNTIME` na prázdný řetězec.

### <a name="function-metadata"></a>Metadata funkce

Při použití s vlastní obslužnou rutinou se *function.js* obsahu neliší od toho, jak byste definovali funkci v jakémkoli jiném kontextu. Jediným požadavkem je, že *function.js* souborů musí být ve složce s názvem, aby odpovídala názvu funkce.

Následující *function.jsv* konfiguraci funkce, která má aktivační událost fronty a výstupní vazbu fronty. Protože je ve složce s názvem *MyQueueFunction*, definuje funkci s názvem *MyQueueFunction*.

**MyQueueFunction/function.jsna**

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

### <a name="request-payload"></a>Datová část požadavku

Když je přijata zpráva fronty, hostitel Functions odešle požadavek HTTP POST do vlastní obslužné rutiny s datovou částí v těle.

Následující kód představuje ukázkovou datovou část požadavku. Datová část obsahuje strukturu JSON se dvěma členy: `Data` a `Metadata` .

`Data`Člen obsahuje klíče, které odpovídají vstupům a názvům triggerů, jak jsou definovány v poli vazeb v *function.jsv* souboru.

`Metadata`Člen zahrnuje [metadata generovaná ze zdroje události](./functions-bindings-expressions-patterns.md#trigger-metadata).

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
| `Outputs`     | object    | Uchovává hodnoty odpovědí definované `bindings` polem v *function.js*.<br /><br />Pokud je například funkce nakonfigurovaná s výstupní vazbou fronty s názvem "myQueueOutput", pak `Outputs` obsahuje klíč s názvem `myQueueOutput` , který je nastavením vlastní obslužné rutiny na zprávy odesílané do fronty. |
| `Logs`        | array     | Zprávy se zobrazí v protokolech vyvolání funkcí.<br /><br />Při spuštění v Azure se zprávy zobrazí v Application Insights. |
| `ReturnValue` | řetězec    | Slouží k poskytnutí odpovědi, pokud je výstup nakonfigurován jako `$return` v *function.js* v souboru. |

Toto je příklad datové části odpovědi.

```json
{
  "Outputs": {
    "res": {
      "body": "Message enqueued"
    },
    "myQueueOutput": [
      "queue message 1",
      "queue message 2"
    ]
  },
  "Logs": [
    "Log message 1",
    "Log message 2"
  ],
  "ReturnValue": "{\"hello\":\"world\"}"
}
```

## <a name="examples"></a>Příklady

Vlastní obslužné rutiny se dají implementovat v jakémkoli jazyce, který podporuje přijímání událostí HTTP. Následující příklady ukazují, jak implementovat vlastní obslužnou rutinu pomocí programovacího jazyka na cestách.

### <a name="function-with-bindings"></a>Funkce s vazbami

Scénář implementovaný v tomto příkladu obsahuje funkci s názvem `order` , která přijímá `POST` datovou část reprezentující produktovou objednávku. Při odeslání objednávky do funkce se vytvoří Queue Storage zpráva a vrátí se odpověď HTTP.

<a id="bindings-implementation" name="bindings-implementation"></a>

#### <a name="implementation"></a>Implementace

Ve složce s názvem *order* function.jsse *v* souboru NAkonfiguruje funkce aktivované protokolem HTTP.

**pořadí/function.jsna**

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
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

V kořenovém adresáři aplikace je *host.jsv* souboru nakonfigurovaná tak, aby spouštěla spustitelný soubor s názvem `handler.exe` ( `handler` v systému Linux nebo MacOS).

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    }
  },
  "extensionBundle": {
    "id": "Microsoft.Azure.Functions.ExtensionBundle",
    "version": "[1.*, 2.0.0)"
  }
}
```

Toto je požadavek HTTP odeslaný do modulu runtime Functions.

```http
POST http://127.0.0.1:7071/api/order HTTP/1.1
Content-Type: application/json

{
  "id": 1005,
  "quantity": 2,
  "color": "black"
}
```

Modul runtime Functions pak pošle následující požadavek HTTP vlastní obslužné rutině:

```http
POST http://127.0.0.1:<FUNCTIONS_CUSTOMHANDLER_PORT>/order HTTP/1.1
Content-Type: application/json

{
  "Data": {
    "req": {
      "Url": "http://localhost:7071/api/order",
      "Method": "POST",
      "Query": "{}",
      "Headers": {
        "Content-Type": [
          "application/json"
        ]
      },
      "Params": {},
      "Body": "{\"id\":1005,\"quantity\":2,\"color\":\"black\"}"
    }
  },
  "Metadata": {
  }
}
```

> [!NOTE]
> Některé části datové části se odebraly pro zkrácení.

*handler.exe* je vlastní obslužný program obslužné rutiny, který spouští webový server a reaguje na žádosti o vyvolání funkcí od hostitele Functions.

```go
package main

import (
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "os"
)

type InvokeRequest struct {
    Data     map[string]json.RawMessage
    Metadata map[string]interface{}
}

type InvokeResponse struct {
    Outputs     map[string]interface{}
    Logs        []string
    ReturnValue interface{}
}

func orderHandler(w http.ResponseWriter, r *http.Request) {
    var invokeRequest InvokeRequest

    d := json.NewDecoder(r.Body)
    d.Decode(&invokeRequest)

    var reqData map[string]interface{}
    json.Unmarshal(invokeRequest.Data["req"], &reqData)

    outputs := make(map[string]interface{})
    outputs["message"] = reqData["Body"]

    resData := make(map[string]interface{})
    resData["body"] = "Order enqueued"
    outputs["res"] = resData
    invokeResponse := InvokeResponse{outputs, nil, nil}

    responseJson, _ := json.Marshal(invokeResponse)

    w.Header().Set("Content-Type", "application/json")
    w.Write(responseJson)
}

func main() {
    customHandlerPort, exists := os.LookupEnv("FUNCTIONS_CUSTOMHANDLER_PORT")
    if !exists {
        customHandlerPort = "8080"
    }
    mux := http.NewServeMux()
    mux.HandleFunc("/order", orderHandler)
    fmt.Println("Go server Listening on: ", customHandlerPort)
    log.Fatal(http.ListenAndServe(":"+customHandlerPort, mux))
}
```

V tomto příkladu vlastní obslužná rutina spustí webový server pro zpracování událostí HTTP a je nastavená na naslouchání požadavkům přes `FUNCTIONS_CUSTOMHANDLER_PORT` .

I když hostitel funkce přijal původní požadavek HTTP na `/api/order` , vyvolá vlastní obslužnou rutinu pomocí názvu funkce (název složky). V tomto příkladu je funkce definována v cestě k `/order` . Hostitel odešle vlastní obslužnou rutinu požadavku HTTP na cestě `/order` .

`POST`Po odeslání požadavků do této funkce jsou data triggeru a metadata funkce k dispozici prostřednictvím textu požadavku HTTP. Původní text požadavku HTTP se dá použít v datové části `Data.req.Body` .

Odpověď funkce je naformátována na páry klíč/hodnota, kde `Outputs` člen obsahuje hodnotu JSON, kde klíče odpovídají výstupům definovaným v *function.jsv* souboru.

Toto je ukázková datová část, kterou tato obslužná rutina vrátí do hostitele Functions.

```json
{
  "Outputs": {
    "message": "{\"id\":1005,\"quantity\":2,\"color\":\"black\"}",
    "res": {
      "body": "Order enqueued"
    }
  },
  "Logs": null,
  "ReturnValue": null
}
```

Nastavením výstupu, který `message` se rovná datům objednávky, které byly získány z požadavku, výstupy funkce, které přiřadí data do nakonfigurované fronty. Hostitel funkcí také vrátí odpověď HTTP nakonfigurovanou `res` pro volajícího.

### <a name="http-only-function"></a>Funkce pouze HTTP

Pro funkce aktivované protokolem HTTP bez dalších vazeb nebo výstupů můžete chtít, aby obslužná rutina pracovala přímo s požadavkem HTTP a odpovědí namísto vlastní [žádosti o](#request-payload) obslužnou rutinu a datové části [odpovědi](#response-payload) . Toto chování je možné nakonfigurovat v *host.js* pomocí `enableForwardingHttpRequest` nastavení.

> [!IMPORTANT]
> Hlavním účelem funkce vlastní obslužné rutiny je povolit jazyky a moduly runtime, které aktuálně nemají na Azure Functions podporu první třídy. I když může být možné spouštět webové aplikace pomocí vlastních obslužných rutin, Azure Functions není standardní reverzní proxy. Některé funkce, jako je třeba streamování odpovědí, HTTP/2 a WebSockets, nejsou k dispozici. Některé součásti požadavku HTTP, jako jsou například určité hlavičky a trasy, mohou být omezeny. Vaše aplikace může také docházet [k nadměrnému startu.](functions-scale.md#cold-start)
>
> Pokud chcete tyto okolnosti vyřešit, zvažte spuštění webových aplikací na [Azure App Service](../app-service/overview.md).

Následující příklad ukazuje, jak nakonfigurovat funkci aktivované protokolem HTTP bez dalších vazeb nebo výstupů. Scénář implementovaný v tomto příkladu obsahuje funkci s názvem `hello` , která přijímá `GET` nebo `POST` .

<a id="hello-implementation" name="hello-implementation"></a>

#### <a name="implementation"></a>Implementace

Ve složce s názvem *hello* *function.jsv* souboru nakonfiguruje funkci aktivovanou protokolem HTTP.

**Hello/function.jsna**

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "authLevel": "anonymous",
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

V kořenovém adresáři aplikace je *host.js* pro soubor nakonfigurovaný tak, aby běžel `handler.exe` a `enableForwardingHttpRequest` byl nastavený na `true` .

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    },
    "enableForwardingHttpRequest": true
  }
}
```

`enableForwardingHttpRequest` `true` V takovém případě se chování funkcí pouze http liší od výchozího chování vlastních obslužných rutin v těchto způsobech:

* Požadavek HTTP neobsahuje datovou část [žádosti o](#request-payload) vlastní obslužnou rutinu. Místo toho hostitel Functions vyvolá obslužnou rutinu s kopií původního požadavku HTTP.
* Hostitel Functions vyvolá obslužnou rutinu se stejnou cestou jako původní požadavek včetně parametrů řetězce dotazu.
* Hostitel Functions vrátí kopii odpovědi HTTP obslužné rutiny jako odpověď na původní požadavek.

Následuje požadavek POST na hostitele Functions. Hostitel funkcí pak pošle kopii žádosti do vlastní obslužné rutiny ve stejné cestě.

```http
POST http://127.0.0.1:7071/api/hello HTTP/1.1
Content-Type: application/json

{
  "message": "Hello World!"
}
```

Soubor *obslužných rutin souboru. přejít* implementuje webový server a funkci http.

```go
package main

import (
    "fmt"
    "io/ioutil"
    "log"
    "net/http"
    "os"
)

func helloHandler(w http.ResponseWriter, r *http.Request) {
    w.Header().Set("Content-Type", "application/json")
    if r.Method == "GET" {
        w.Write([]byte("hello world"))
    } else {
        body, _ := ioutil.ReadAll(r.Body)
        w.Write(body)
    }
}

func main() {
    customHandlerPort, exists := os.LookupEnv("FUNCTIONS_CUSTOMHANDLER_PORT")
    if !exists {
        customHandlerPort = "8080"
    }
    mux := http.NewServeMux()
    mux.HandleFunc("/api/hello", helloHandler)
    fmt.Println("Go server Listening on: ", customHandlerPort)
    log.Fatal(http.ListenAndServe(":"+customHandlerPort, mux))
}
```

V tomto příkladu vlastní obslužná rutina vytvoří webový server pro zpracování událostí HTTP a je nastaven na naslouchání požadavkům prostřednictvím `FUNCTIONS_CUSTOMHANDLER_PORT` .

`GET` žádosti jsou zpracovávány vrácením řetězce a `POST` požadavky mají přístup k textu žádosti.

Trasa pro funkci Order je tady shodná s `/api/hello` původní žádostí.

>[!NOTE]
>Nejedná se `FUNCTIONS_CUSTOMHANDLER_PORT` o veřejný port, který se používá k volání funkce. Tento port je používán hostitelem Functions pro volání vlastní obslužné rutiny.

## <a name="deploying"></a>Nasazení

Vlastní obslužnou rutinu lze nasadit do každé Azure Functions možnosti hostování. Pokud vaše obslužná rutina vyžaduje operační systém nebo závislosti platforem (například modul runtime), může být nutné použít [vlastní kontejner](./functions-create-function-linux-custom-image.md).

Při vytváření aplikace Function App v Azure pro vlastní obslužné rutiny doporučujeme jako zásobník vybrat .NET Core. V budoucnu bude přidán vlastní zásobník pro vlastní obslužné rutiny.

K nasazení vlastní aplikace obslužných rutin pomocí Azure Functions Core Tools spusťte následující příkaz.

```bash
func azure functionapp publish $functionAppName
```

> [!NOTE]
> Ujistěte se, že všechny soubory potřebné ke spuštění vlastní obslužné rutiny jsou ve složce a jsou součástí nasazení. Pokud je vlastní obslužná rutina binární spustitelný soubor nebo má závislé závislosti na platformě, zajistěte, aby tyto soubory odpovídaly cílové platformě nasazení.

## <a name="restrictions"></a>Omezení

- Webový server vlastní obslužné rutiny musí být spuštěn do 60 sekund.

## <a name="samples"></a>ukázky

Příklady implementace funkcí v různých jazycích najdete v části [vlastní obslužné rutiny ukázky v úložišti GitHub](https://github.com/Azure-Samples/functions-custom-handlers) .

## <a name="troubleshooting-and-support"></a>Řešení potíží a podpora

### <a name="trace-logging"></a>Protokolování trasování

Pokud se vlastní proces obslužné rutiny nepodaří spustit nebo pokud má problémy s komunikací s hostitelem Functions, můžete zvýšit úroveň protokolu aplikace Function App na, `Trace` aby se zobrazily další diagnostické zprávy od hostitele.

Pokud chcete změnit výchozí úroveň protokolu aplikace Function App, nakonfigurujte `logLevel` nastavení v `logging` části *host.jsna*.

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    }
  },
  "logging": {
    "logLevel": {
      "default": "Trace"
    }
  }
}
```

Hostitel Functions výstupuje další zprávy protokolu, včetně informací souvisejících s vlastním procesem obslužné rutiny. Použijte protokoly k prozkoumání problémů s spuštěním vlastního procesu obslužné rutiny nebo volání funkcí ve vlastní obslužné rutině.

Místně se protokoly tisknou do konzoly.

V Azure se [dotazuje Application Insights trasování](analyze-telemetry-data.md#query-telemetry-data) , aby se zobrazily zprávy protokolu. Pokud vaše aplikace vytváří velký objem protokolů, pošle se Application Insights jenom podmnožina zpráv protokolu. [Zakáže vzorkování](configure-monitoring.md#configure-sampling) , aby bylo zajištěno, že budou protokolovány všechny zprávy.

### <a name="test-custom-handler-in-isolation"></a>Test vlastní obslužné rutiny v izolaci

Vlastní obslužné rutiny jsou procesy webového serveru, takže je možné ji spustit na svých vlastních a testovacích funkcích odesláním [nezpracovaných požadavků HTTP](#request-payload) pomocí nástroje, jako je například [kudrlinkou](https://curl.haxx.se/) nebo [post](https://www.postman.com/).

Tuto strategii můžete použít také v kanálech CI/CD a spouštět automatizované testy vlastní obslužné rutiny.

### <a name="execution-environment"></a>Spouštěcí prostředí

Vlastní obslužné rutiny běží ve stejném prostředí jako typická aplikace Azure Functions. Otestujte obslužnou rutinu, abyste zajistili, že prostředí obsahuje všechny závislosti, které musí spustit. U aplikací, které vyžadují další závislosti, je možná budete muset spustit pomocí [vlastní image kontejneru](functions-create-function-linux-custom-image.md) hostované v plánu Azure Functions [Premium](functions-premium-plan.md).

### <a name="get-support"></a>Získání podpory

Pokud potřebujete pomoc s vlastními obslužnými rutinami aplikace Function App, můžete odeslat žádost prostřednictvím běžných kanálů podpory. Vzhledem k nejrůznějším jazykům, které se používají k vytváření vlastních obslužných rutin, ale podpora není neomezené.

Podpora je k dispozici v případě, že hostitel Functions má potíže se spouštěním nebo komunikací s vlastním procesem obslužné rutiny. Pro problémy, které jsou specifické pro vnitřní pracovní procesy vlastní obslužné rutiny, jako jsou například problémy se zvoleným jazykem nebo architekturou, náš tým podpory nemůže v tomto kontextu poskytnout pomoc.

## <a name="next-steps"></a>Další kroky

Začněte sestavovat Azure Functions aplikaci v cestách nebo Rust pomocí [rychlého startu vlastních obslužných rutin](create-first-function-vs-code-other.md).
