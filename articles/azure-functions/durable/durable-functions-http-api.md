---
title: HTTP API v trvalých funkcích – funkce Azure
description: Zjistěte, jak implementovat http api v rozšíření trvalé funkce pro funkce Azure.
author: cgillum
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: 4e4081ecca4714c713d105d363a83a4f96a0d3fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278165"
---
# <a name="http-api-reference"></a>Odkaz na rozhraní API HTTP

Rozšíření Trvalé funkce zpřístupňuje sadu předdefinovaných http api, které lze použít k provádění úloh správy na [orchestrations](durable-functions-types-features-overview.md#orchestrator-functions), [entity](durable-functions-types-features-overview.md#entity-functions)a [centra úloh](durable-functions-task-hubs.md). Tato http api jsou rozšiřitelnost webhooky, které jsou autorizovány hostitelem Azure Functions, ale zpracovány přímo rozšíření durable functions.

Všechna rozhraní HTTP API implementovaná rozšířením vyžadují následující parametry. Datový typ všech parametrů `string`je .

| Parametr        | Typ parametru  | Popis |
|------------------|-----------------|-------------|
| **`taskHub`**    | Řetězec dotazu    | Název centra [úloh](durable-functions-task-hubs.md). Pokud není zadán, předpokládá se název centra úloh aktuální aplikace funkce. |
| **`connection`** | Řetězec dotazu    | **Název** připojovacího řetězce pro účet úložiště. Pokud není zadán, předpokládá se výchozí připojovací řetězec pro aplikaci funkce. |
| **`systemKey`**  | Řetězec dotazu    | Autorizační klíč potřebný k vyvolání rozhraní API. |

`systemKey`je autorizační klíč automaticky vygenerovaný hostitelem Funkce Azure. Konkrétně uděluje přístup k rozšíření trvalé úlohy API a lze spravovat stejným způsobem jako [ostatní autorizační klíče](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API). Můžete generovat adresy URL, které `taskHub` `connection`obsahují `systemKey` správné hodnoty řetězce , a hodnoty řetězce `CreateCheckStatusResponse` dotazu pomocí rozhraní API `createCheckStatusResponse` [vazby klienta orchestrace,](durable-functions-bindings.md#orchestration-client) jako jsou rozhraní API a rozhraní `CreateHttpManagementPayload` API v rozhraní .NET nebo rozhraní API a `createHttpManagementPayload` v jazyce JavaScript.

V několika následujících částech se týkají konkrétních http api podporovaných rozšíření a poskytují příklady, jak je lze použít.

## <a name="start-orchestration"></a>Spustit orchestraci

Spustí novou instanci zadané funkce orchestrator.

### <a name="request"></a>Žádost

Pro verzi 1.x functions runtime je požadavek formátován následujícím způsobem (pro přehlednost je zobrazeno více řádků):

```http
POST /admin/extensions/DurableTaskExtension/orchestrators/{functionName}/{instanceId?}
     ?taskHub={taskHub}
     &connection={connectionName}
     &code={systemKey}
```

Ve verzi 2.x funkce runtime, formát URL má všechny stejné parametry, ale s mírně odlišnou předponou:

```http
POST /runtime/webhooks/durabletask/orchestrators/{functionName}/{instanceId?}
     ?taskHub={taskHub}
     &connection={connectionName}
     &code={systemKey}
```

Parametry požadavku pro toto rozhraní API zahrnují výchozí sadu uvedenou dříve, stejně jako následující jedinečné parametry:

| Pole              | Typ parametru  | Popis |
|--------------------|-----------------|-------------|
| **`functionName`** | zprostředkovatele identity             | Název funkce orchestrator spustit. |
| **`instanceId`**   | zprostředkovatele identity             | Volitelný parametr. ID instance orchestrace. Pokud není zadán, funkce orchestrator začne s ID náhodné instance. |
| **`{content}`**    | Žádost o obsah | Nepovinný parametr. Vstup funkce orchestrator ve formátu JSON. |

### <a name="response"></a>Odpověď

Lze vrátit několik možných hodnot stavového kódu.

* **HTTP 202 (Přijato)**: Zadaná funkce orchestratoru byla naplánována spuštění. Hlavička `Location` odpovědi obsahuje adresu URL pro dotazování stavu orchestrace.
* **HTTP 400 (Chybný požadavek):** Zadaná funkce orchestrator neexistuje, zadané ID instance nebylo platné nebo obsah požadavku nebyl platný JSON.

Následuje příklad požadavku, který `RestartVMs` spustí funkci orchestrator a obsahuje datovou část objektu JSON:

```http
POST /runtime/webhooks/durabletask/orchestrators/RestartVMs?code=XXX
Content-Type: application/json
Content-Length: 83

{
    "resourceGroup": "myRG",
    "subscriptionId": "111deb5d-09df-4604-992e-a968345530a9"
}
```

Datová část odpovědi pro případy **HTTP 202** je objekt JSON s následujícími poli:

| Pole                       | Popis                          |
|-----------------------------|--------------------------------------|
| **`id`**                    |ID instance orchestrace. |
| **`statusQueryGetUri`**     |Adresa URL stavu instance orchestrace. |
| **`sendEventPostUri`**      |Adresa URL "raise event" instance orchestrace. |
| **`terminatePostUri`**      |"Ukončit" URL instance orchestrace. |
| **`purgeHistoryDeleteUri`** |Adresa URL "historie vymazání" instance orchestrace. |
| **`rewindPostUri`**         |(náhled) Adresa URL "převinout" instance orchestrace. |

Datový typ všech polí `string`je .

Zde je příklad datové části odpovědi pro `abc123` instanci orchestrace s jako jeho ID (formátován pro čitelnost):

```http
{
    "id": "abc123",
    "purgeHistoryDeleteUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/raiseEvent/{eventName}?code=XXX",
    "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/terminate?reason={text}&code=XXX"
}
```

Odpověď HTTP je určena jako kompatibilní se *vzorem dotazování spotřebitelů*. Obsahuje také následující hlavičky významné odpovědi:

* **Umístění**: Adresa URL koncového bodu stavu. Tato adresa URL obsahuje `statusQueryGetUri` stejnou hodnotu jako pole.
* **Opakování po:** Počet sekund čekání mezi operacemi dotazování. Výchozí hodnota je `10`.

Další informace o asynchronní http dotazování vzor, naleznete v [dokumentaci sledování asynchronní operace HTTP.](durable-functions-http-features.md#async-operation-tracking)

## <a name="get-instance-status"></a>Získat stav instance

Získá stav zadané instance orchestrace.

### <a name="request"></a>Žádost

Pro verzi 1.x functions runtime je požadavek formátován následujícím způsobem (pro přehlednost je zobrazeno více řádků):

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

Ve verzi 2.x funkce runtime, formát URL má všechny stejné parametry, ale s mírně odlišnou předponou:

```http
GET /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

Parametry požadavku pro toto rozhraní API zahrnují výchozí sadu uvedenou dříve, stejně jako následující jedinečné parametry:

| Pole                   | Typ parametru  | Popis |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | zprostředkovatele identity             | ID instance orchestrace. |
| **`showInput`**         | Řetězec dotazu    | Volitelný parametr. Pokud je `false`nastavena na , vstup funkce nebude zahrnut a datová část odpovědi.|
| **`showHistory`**       | Řetězec dotazu    | Volitelný parametr. Pokud je `true`nastavena na , historie spuštění orchestrace budou zahrnuty do datové části odpovědi.|
| **`showHistoryOutput`** | Řetězec dotazu    | Volitelný parametr. Pokud je `true`nastavena na , výstupy funkce budou zahrnuty do historie provádění orchestrace.|
| **`createdTimeFrom`**   | Řetězec dotazu    | Volitelný parametr. Pokud je zadán, filtruje seznam vrácených instancí, které byly vytvořeny na nebo po dané časové razítko ISO8601.|
| **`createdTimeTo`**     | Řetězec dotazu    | Volitelný parametr. Pokud je zadán, filtruje seznam vrácených instancí, které byly vytvořeny na nebo před dané časové razítko ISO8601.|
| **`runtimeStatus`**     | Řetězec dotazu    | Volitelný parametr. Pokud je zadán, filtruje seznam vrácených instancí na základě jejich stavu za běhu. Seznam možných hodnot stavu za běhu naleznete v článku [Dotazování instancí.](durable-functions-instance-management.md) |

### <a name="response"></a>Odpověď

Lze vrátit několik možných hodnot stavového kódu.

* **HTTP 200 (OK)**: Zadaná instance je v dokončeném stavu.
* **HTTP 202 (Přijato)**: Probíhá zadaná instance.
* **HTTP 400 (Chybný požadavek):** Zadaná instance se nezdařila nebo byla ukončena.
* **HTTP 404 (Nebyl nalezen)**: Zadaná instance neexistuje nebo nebyla spuštěna.
* **HTTP 500 (Vnitřní chyba serveru):** Zadaná instance se nezdařila s neošetřenou výjimkou.

Datová část odpovědi pro případy **HTTP 200** a **HTTP 202** je objekt JSON s následujícími poli:

| Pole                 | Datový typ | Popis |
|-----------------------|-----------|-------------|
| **`runtimeStatus`**   | řetězec    | Stav runtime instance. Mezi hodnoty patří *Spuštěno*, *Čeká na vyřízení*, *Nepodařilo se,* *Zrušilo,* *Ukončeno*, *Dokončeno*. |
| **`input`**           | JSON      | Data JSON použitá k inicializaci instance. Toto `null` pole `showInput` je, pokud je `false`parametr řetězce dotazu nastaven na hodnotu .|
| **`customStatus`**    | JSON      | Data JSON použitá pro vlastní stav orchestrace. Toto `null` pole je nastaveno, pokud není nastaveno. |
| **`output`**          | JSON      | Výstup JSON instance. Toto `null` pole je, pokud instance není v dokončeném stavu. |
| **`createdTime`**     | řetězec    | Čas, kdy byla instance vytvořena. Používá rozšířený zápis ISO 8601. |
| **`lastUpdatedTime`** | řetězec    | Čas, kdy instance naposledy trvala. Používá rozšířený zápis ISO 8601. |
| **`historyEvents`**   | JSON      | Pole JSON obsahující historii spuštění orchestrace. Toto `null` pole `showHistory` je, pokud není `true`parametr řetězce dotazu nastaven na . |

Zde je příklad datové části odpovědi včetně historie provádění orchestrace a výstupů aktivity (formátované pro čitelnost):

```json
{
  "createdTime": "2018-02-28T05:18:49Z",
  "historyEvents": [
      {
          "EventType": "ExecutionStarted",
          "FunctionName": "E1_HelloSequence",
          "Timestamp": "2018-02-28T05:18:49.3452372Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Tokyo!",
          "ScheduledTime": "2018-02-28T05:18:51.3939873Z",
          "Timestamp": "2018-02-28T05:18:52.2895622Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Seattle!",
          "ScheduledTime": "2018-02-28T05:18:52.8755705Z",
          "Timestamp": "2018-02-28T05:18:53.1765771Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello London!",
          "ScheduledTime": "2018-02-28T05:18:53.5170791Z",
          "Timestamp": "2018-02-28T05:18:53.891081Z"
      },
      {
          "EventType": "ExecutionCompleted",
          "OrchestrationStatus": "Completed",
          "Result": [
              "Hello Tokyo!",
              "Hello Seattle!",
              "Hello London!"
          ],
          "Timestamp": "2018-02-28T05:18:54.3660895Z"
      }
  ],
  "input": null,
  "customStatus": { "nextActions": ["A", "B", "C"], "foo": 2 },
  "lastUpdatedTime": "2018-02-28T05:18:54Z",
  "output": [
      "Hello Tokyo!",
      "Hello Seattle!",
      "Hello London!"
  ],
  "runtimeStatus": "Completed"
}
```

Odpověď **HTTP 202** také obsahuje hlavičku odpovědi **umístění,** `statusQueryGetUri` která odkazuje na stejnou adresu URL jako výše uvedené pole.

## <a name="get-all-instances-status"></a>Získat stav všech instancí

Můžete také dotaz na stav všech instancí odebráním `instanceId` z požadavku "Získat stav instance". V tomto případě jsou základní parametry stejné jako stav get instance. Parametry řetězce dotazu pro filtrování jsou také podporovány.

Jedna věc k `connection` zapamatování je, že a `code` jsou volitelné. Pokud máte anonymní auth na `code` funkci, pak není nutné.
Pokud nechcete používat jiný řetězec připojení úložiště, než je definováno v nastavení aplikace AzureWebJobsStorage, můžete bezpečně ignorovat parametr řetězce dotazu připojení.

### <a name="request"></a>Žádost

Pro verzi 1.x functions runtime je požadavek formátován následujícím způsobem (pro přehlednost je zobrazeno více řádků):

```http
GET /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
    &showInput=[true|false]
    &top={integer}
```

Ve verzi 2.x funkce runtime, formát URL má všechny stejné parametry, ale s mírně odlišnou předponou:

```http
GET /runtime/webhooks/durableTask/instances?
    taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
    &showInput=[true|false]
    &top={integer}
```

Parametry požadavku pro toto rozhraní API zahrnují výchozí sadu uvedenou dříve, stejně jako následující jedinečné parametry:

| Pole                   | Typ parametru  | Popis |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | zprostředkovatele identity             | ID instance orchestrace. |
| **`showInput`**         | Řetězec dotazu    | Volitelný parametr. Pokud je `false`nastavena na , vstup funkce nebude zahrnut a datová část odpovědi.|
| **`showHistory`**       | Řetězec dotazu    | Volitelný parametr. Pokud je `true`nastavena na , historie spuštění orchestrace budou zahrnuty do datové části odpovědi.|
| **`showHistoryOutput`** | Řetězec dotazu    | Volitelný parametr. Pokud je `true`nastavena na , výstupy funkce budou zahrnuty do historie provádění orchestrace.|
| **`createdTimeFrom`**   | Řetězec dotazu    | Volitelný parametr. Pokud je zadán, filtruje seznam vrácených instancí, které byly vytvořeny na nebo po dané časové razítko ISO8601.|
| **`createdTimeTo`**     | Řetězec dotazu    | Volitelný parametr. Pokud je zadán, filtruje seznam vrácených instancí, které byly vytvořeny na nebo před dané časové razítko ISO8601.|
| **`runtimeStatus`**     | Řetězec dotazu    | Volitelný parametr. Pokud je zadán, filtruje seznam vrácených instancí na základě jejich stavu za běhu. Seznam možných hodnot stavu za běhu naleznete v článku [Dotazování instancí.](durable-functions-instance-management.md) |
| **`top`**               | Řetězec dotazu    | Volitelný parametr. Pokud je zadán, omezuje počet instancí vrácených dotazem. |

### <a name="response"></a>Odpověď

Zde je příklad datové části odpovědí včetně stavu orchestrace (formátován pro čitelnost):

```json
[
    {
        "instanceId": "7af46ff000564c65aafbfe99d07c32a5",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-06-04T10:46:39Z",
        "lastUpdatedTime": "2018-06-04T10:46:47Z"
    },
    {
        "instanceId": "80eb7dd5c22f4eeba9f42b062794321e",
        "runtimeStatus": "Running",
        "input": null,
        "customStatus": null,
        "output": null,
        "createdTime": "2018-06-04T15:18:28Z",
        "lastUpdatedTime": "2018-06-04T15:18:38Z"
    },
    {
        "instanceId": "9124518926db408ab8dfe84822aba2b1",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-06-04T10:46:54Z",
        "lastUpdatedTime": "2018-06-04T10:47:03Z"
    },
    {
        "instanceId": "d100b90b903c4009ba1a90868331b11b",
        "runtimeStatus": "Pending",
        "input": null,
        "customStatus": null,
        "output": null,
        "createdTime": "2018-06-04T15:18:39Z",
        "lastUpdatedTime": "2018-06-04T15:18:39Z"
    }
]
```

> [!NOTE]
> Tato operace může být velmi nákladné z hlediska Azure Storage V/V, pokud existuje velké množství řádků v tabulce instance. Další podrobnosti o tabulce instance najdete v [dokumentaci výkon a škálování v trvalé funkce (Azure Functions).](durable-functions-perf-and-scale.md#instances-table)
>

Pokud existují další výsledky, je v hlavičce odpovědi vrácen token pokračování.  Název záhlaví je `x-ms-continuation-token`.

Pokud nastavíte hodnotu tokenu pokračování v další hlavičce požadavku, můžete získat další stránku výsledků. Tento název hlavičky požadavku `x-ms-continuation-token`je také .

## <a name="purge-single-instance-history"></a>Vyčistit historii jedné instance

Odstraní historii a související artefakty pro zadanou instanci orchestrace.

### <a name="request"></a>Žádost

Pro verzi 1.x functions runtime je požadavek formátován následujícím způsobem (pro přehlednost je zobrazeno více řádků):

```http
DELETE /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

Ve verzi 2.x funkce runtime, formát URL má všechny stejné parametry, ale s mírně odlišnou předponou:

```http
DELETE /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

Parametry požadavku pro toto rozhraní API zahrnují výchozí sadu uvedenou dříve, stejně jako následující jedinečné parametry:

| Pole             | Typ parametru  | Popis |
|-------------------|-----------------|-------------|
| **`instanceId`**  | zprostředkovatele identity             | ID instance orchestrace. |

### <a name="response"></a>Odpověď

Lze vrátit následující hodnoty stavového kódu PROTOKOLU HTTP.

* **HTTP 200 (OK)**: Historie instancí byla úspěšně vymazána.
* **HTTP 404 (Nebyl nalezen)**: Zadaná instance neexistuje.

Datová část odpovědi pro případ **HTTP 200** je objekt JSON s následujícím polem:

| Pole                  | Datový typ | Popis |
|------------------------|-----------|-------------|
| **`instancesDeleted`** | celé číslo   | Počet odstraněných instancí. Pro případ jedné instance by tato `1`hodnota měla být vždy . |

Zde je příklad užitečné části odpovědí (formátován pro čitelnost):

```json
{
    "instancesDeleted": 1
}
```

## <a name="purge-multiple-instance-histories"></a>Vymazání více historie instancí

Můžete také odstranit historii a související artefakty pro více instancí v rámci centra úloh odebráním `{instanceId}` požadavku "Vymazat historii jedné instance". Chcete-li selektivně vymazat historii instancí, použijte stejné filtry popsané v požadavku "Získat všechny instance".

### <a name="request"></a>Žádost

Pro verzi 1.x functions runtime je požadavek formátován následujícím způsobem (pro přehlednost je zobrazeno více řádků):

```http
DELETE /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

Ve verzi 2.x funkce runtime, formát URL má všechny stejné parametry, ale s mírně odlišnou předponou:

```http
DELETE /runtime/webhooks/durabletask/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

Parametry požadavku pro toto rozhraní API zahrnují výchozí sadu uvedenou dříve, stejně jako následující jedinečné parametry:

| Pole                 | Typ parametru  | Popis |
|-----------------------|-----------------|-------------|
| **`createdTimeFrom`** | Řetězec dotazu    | Filtruje seznam vyčištěných instancí, které byly vytvořeny na daném časovém razítku ISO8601 nebo po něm.|
| **`createdTimeTo`**   | Řetězec dotazu    | Volitelný parametr. Pokud je zadán, filtruje seznam vyčištěných instancí, které byly vytvořeny na nebo před daným časovým razítkem ISO8601.|
| **`runtimeStatus`**   | Řetězec dotazu    | Volitelný parametr. Pokud je zadán, filtruje seznam vyčištěných instancí na základě jejich stavu za běhu. Seznam možných hodnot stavu za běhu naleznete v článku [Dotazování instancí.](durable-functions-instance-management.md) |

> [!NOTE]
> Tato operace může být velmi nákladné z hlediska Azure Storage V/V, pokud existuje velké množství řádků v instancích nebo historie tabulek. Další podrobnosti o těchto tabulkách najdete v dokumentaci [k výkonu a škálování v dokumentaci durable functions (Azure Functions).](durable-functions-perf-and-scale.md#instances-table)

### <a name="response"></a>Odpověď

Lze vrátit následující hodnoty stavového kódu PROTOKOLU HTTP.

* **HTTP 200 (OK)**: Historie instancí byla úspěšně vymazána.
* **HTTP 404 (Nebyl nalezen)**: Nebyly nalezeny žádné instance, které by odpovídaly výrazu filtru.

Datová část odpovědi pro případ **HTTP 200** je objekt JSON s následujícím polem:

| Pole                   | Datový typ | Popis |
|-------------------------|-----------|-------------|
| **`instancesDeleted`**  | celé číslo   | Počet odstraněných instancí. |

Zde je příklad užitečné části odpovědí (formátován pro čitelnost):

```json
{
    "instancesDeleted": 250
}
```

## <a name="raise-event"></a>Vyvolat událost

Odešle zprávu s oznámením události spuštěné instanci orchestrace.

### <a name="request"></a>Žádost

Pro verzi 1.x functions runtime je požadavek formátován následujícím způsobem (pro přehlednost je zobrazeno více řádků):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

Ve verzi 2.x funkce runtime, formát URL má všechny stejné parametry, ale s mírně odlišnou předponou:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

Parametry požadavku pro toto rozhraní API zahrnují výchozí sadu uvedenou dříve, stejně jako následující jedinečné parametry:

| Pole             | Typ parametru  | Popis |
|-------------------|-----------------|-------------|
| **`instanceId`**  | zprostředkovatele identity             | ID instance orchestrace. |
| **`eventName`**   | zprostředkovatele identity             | Název události, na kterou čeká instance orchestrace cíle. |
| **`{content}`**   | Žádost o obsah | Datová část události ve formátu JSON. |

### <a name="response"></a>Odpověď

Lze vrátit několik možných hodnot stavového kódu.

* **HTTP 202 (Přijato)**: Vyzdvaná událost byla přijata ke zpracování.
* **HTTP 400 (Chybný požadavek)**: `application/json` Obsah požadavku nebyl typu nebo nebyl platný JSON.
* **HTTP 404 (Nebyl nalezen)**: Zadaná instance nebyla nalezena.
* **HTTP 410 (Gone):** Zadaná instance byla dokončena nebo se nezdařila a nemůže zpracovat žádné vyvolané události.

Zde je příklad požadavku, který odešle řetězec `"incr"` JSON instanci čekající na operaci s názvem **:**

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code=XXX
Content-Type: application/json
Content-Length: 6

"incr"
```

Odpovědi pro toto rozhraní API neobsahují žádný obsah.

## <a name="terminate-instance"></a>Ukončit instanci

Ukončí spuštěnou instanci orchestrace.

### <a name="request"></a>Žádost

Pro verzi 1.x functions runtime je požadavek formátován následujícím způsobem (pro přehlednost je zobrazeno více řádků):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Ve verzi 2.x funkce runtime, formát URL má všechny stejné parametry, ale s mírně odlišnou předponou:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Parametry požadavku pro toto rozhraní API zahrnují výchozí sadu uvedenou dříve, stejně jako následující jedinečný parametr.

| Pole             | Typ parametru  | Popis |
|-------------------|-----------------|-------------|
| **`instanceId`**  | zprostředkovatele identity             | ID instance orchestrace. |
| **`reason`**      | Řetězec dotazu    | Nepovinný parametr. Důvod pro ukončení instance orchestrace. |

### <a name="response"></a>Odpověď

Lze vrátit několik možných hodnot stavového kódu.

* **HTTP 202 (Přijato)**: Požadavek na ukončení byl přijat ke zpracování.
* **HTTP 404 (Nebyl nalezen)**: Zadaná instance nebyla nalezena.
* **HTTP 410 (Gone):** Zadaná instance byla dokončena nebo se nezdařila.

Zde je příklad požadavku, který ukončí spuštěnou instanci a určuje důvod **buggy**:

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Odpovědi pro toto rozhraní API neobsahují žádný obsah.

## <a name="rewind-instance-preview"></a>Instance převinout zpět (náhled)

Obnoví instanci orchestrace se selháním do spuštěného stavu přehráním posledních neúspěšných operací.

### <a name="request"></a>Žádost

Pro verzi 1.x functions runtime je požadavek formátován následujícím způsobem (pro přehlednost je zobrazeno více řádků):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Ve verzi 2.x funkce runtime, formát URL má všechny stejné parametry, ale s mírně odlišnou předponou:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Parametry požadavku pro toto rozhraní API zahrnují výchozí sadu uvedenou dříve, stejně jako následující jedinečný parametr.

| Pole             | Typ parametru  | Popis |
|-------------------|-----------------|-------------|
| **`instanceId`**  | zprostředkovatele identity             | ID instance orchestrace. |
| **`reason`**      | Řetězec dotazu    | Nepovinný parametr. Důvod pro převíjení instance orchestrace. |

### <a name="response"></a>Odpověď

Lze vrátit několik možných hodnot stavového kódu.

* **HTTP 202 (Přijato)**: Požadavek na převíjení zpět byl přijat ke zpracování.
* **HTTP 404 (Nebyl nalezen)**: Zadaná instance nebyla nalezena.
* **HTTP 410 (Gone)**: Zadaná instance byla dokončena nebo byla ukončena.

Zde je příklad požadavku, který převine instanci se selháním a určuje důvod **pevné**:

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/rewind?reason=fixed&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Odpovědi pro toto rozhraní API neobsahují žádný obsah.

## <a name="signal-entity"></a>Entita signálu

Odešle zprávu o jednosměrné operaci [trvalé entitě](durable-functions-types-features-overview.md#entity-functions). Pokud entita neexistuje, bude vytvořena automaticky.

> [!NOTE]
> Trvalé entity jsou k dispozici počínaje trvalé funkce 2.0.

### <a name="request"></a>Žádost

Požadavek HTTP je formátován následujícím způsobem (pro přehlednost je zobrazeno více řádků):

```http
POST /runtime/webhooks/durabletask/entities/{entityName}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &op={operationName}
```

Parametry požadavku pro toto rozhraní API zahrnují výchozí sadu uvedenou dříve, stejně jako následující jedinečné parametry:

| Pole             | Typ parametru  | Popis |
|-------------------|-----------------|-------------|
| **`entityName`**  | zprostředkovatele identity             | Název (typ) entity. |
| **`entityKey`**   | zprostředkovatele identity             | Klíč (jedinečné ID) entity. |
| **`op`**          | Řetězec dotazu    | Nepovinný parametr. Název uživatelem definované operace vyvolat. |
| **`{content}`**   | Žádost o obsah | Datová část události ve formátu JSON. |

Zde je příklad požadavku, který odešle uživatelem `Counter` definovanou `steps`zprávu "Přidat" entitě s názvem . Obsah zprávy je hodnota `5`. Pokud entita ještě neexistuje, bude vytvořena tímto požadavkem:

```http
POST /runtime/webhooks/durabletask/entities/Counter/steps?op=Add
Content-Type: application/json

5
```

> [!NOTE]
> Ve výchozím nastavení s [entitami založenými na třídách v rozhraní .NET](durable-functions-dotnet-entities.md#defining-entity-classes)bude zadání `op` hodnoty `delete` odstraněn stav entity. Pokud entita definuje `delete`operaci s názvem , bude však tato uživatelem definovaná operace vyvolána.

### <a name="response"></a>Odpověď

Tato operace má několik možných odpovědí:

* **HTTP 202 (Přijato)**: Provoz signálu byl přijat pro asynchronní zpracování.
* **HTTP 400 (Chybný požadavek)**: `application/json`Obsah požadavku nebyl typu , nebyl `entityKey` platný JSON nebo měl neplatnou hodnotu.
* **HTTP 404 (Nebyl nalezen)**: Zadaný `entityName` nebyl nalezen.

Úspěšný požadavek HTTP neobsahuje žádný obsah v odpovědi. Neúspěšný požadavek HTTP může obsahovat informace o chybě ve formátu JSON v obsahu odpovědi.

## <a name="get-entity"></a>Získat entitu

Získá stav zadané entity.

### <a name="request"></a>Žádost

Požadavek HTTP je formátován následujícím způsobem (pro přehlednost je zobrazeno více řádků):

```http
GET /runtime/webhooks/durabletask/entities/{entityName}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

### <a name="response"></a>Odpověď

Tato operace má dvě možné odpovědi:

* **HTTP 200 (OK)**: Zadaná entita existuje.
* **HTTP 404 (Nebyl nalezen)**: Zadaná entita nebyla nalezena.

Úspěšná odpověď obsahuje json serializovaný stav entity jako její obsah.

### <a name="example"></a>Příklad
Následující příklad požadavku HTTP získá stav `Counter` existující `steps`entity s názvem :

```http
GET /runtime/webhooks/durabletask/entities/Counter/steps
```

Pokud `Counter` entita jednoduše obsahovala několik `currentValue` kroků uložených v poli, může obsah odpovědi vypadat takto (formátován pro čitelnost):

```json
{
    "currentValue": 5
}
```

## <a name="list-entities"></a>Entity seznamu

Můžete dotazovat na více entit podle názvu entity nebo podle data poslední operace.

### <a name="request"></a>Žádost

Požadavek HTTP je formátován následujícím způsobem (pro přehlednost je zobrazeno více řádků):

```http
GET /runtime/webhooks/durabletask/entities/{entityName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &lastOperationTimeFrom={timestamp}
    &lastOperationTimeTo={timestamp}
    &fetchState=[true|false]
    &top={integer}
```

Parametry požadavku pro toto rozhraní API zahrnují výchozí sadu uvedenou dříve, stejně jako následující jedinečné parametry:

| Pole                       | Typ parametru  | Popis |
|-----------------------------|-----------------|-------------|
| **`entityName`**            | zprostředkovatele identity             | Nepovinný parametr. Pokud je zadán, filtruje seznam vrácených entit podle jejich názvu entity (malá a velká písmena). |
| **`fetchState`**            | Řetězec dotazu    | Volitelný parametr. Pokud je `true`nastavena na , stav entity bude zahrnut do datové části odpovědi. |
| **`lastOperationTimeFrom`** | Řetězec dotazu    | Volitelný parametr. Pokud je zadán, filtruje seznam vrácených entit, které zpracovávaly operace po daném časovém razítku ISO8601. |
| **`lastOperationTimeTo`**   | Řetězec dotazu    | Volitelný parametr. Pokud je zadán, filtruje seznam vrácených entit, které zpracovávaly operace před daným časovým razítkem ISO8601. |
| **`top`**                   | Řetězec dotazu    | Volitelný parametr. Pokud je zadán, omezuje počet entit vrácených dotazem. |


### <a name="response"></a>Odpověď

Úspěšná odpověď HTTP 200 obsahuje serializované pole entit JSON a volitelně stav každé entity.

Ve výchozím nastavení vrátí operace prvních 100 entit, které odpovídají kritériím dotazu. Volající může zadat hodnotu parametru řetězce dotazu pro `top` vrácení jiného maximálního počtu výsledků. Pokud existují další výsledky nad rámec co je vrácena, token pokračování je také vrácena v záhlaví odpovědi. Název záhlaví je `x-ms-continuation-token`.

Pokud nastavíte hodnotu tokenu pokračování v další hlavičce požadavku, můžete získat další stránku výsledků. Tento název hlavičky požadavku `x-ms-continuation-token`je také .

### <a name="example---list-all-entities"></a>Příklad - seznam všech entit

V následujícím příkladu požadavku HTTP jsou uvedeny všechny entity v centru úloh:

```http
GET /runtime/webhooks/durabletask/entities
```

Odpověď JSON může vypadat takto (formátován pro čitelnost):

```json
[
    {
        "entityId": { "key": "cats", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:45:44.6326361Z",
    },
    {
        "entityId": { "key": "dogs", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:46:01.9477382Z"
    },
    {
        "entityId": { "key": "mice", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:46:15.4626159Z"
    },
    {
        "entityId": { "key": "radio", "name": "device" },
        "lastOperationTime": "2019-12-18T21:46:18.2616154Z"
    },
]
```

### <a name="example---filtering-the-list-of-entities"></a>Příklad - filtrování seznamu entit

Následující příklad požadavku HTTP uvádí pouze první `counter` dvě entity typu a také načte jejich stav:

```http
GET /runtime/webhooks/durabletask/entities/counter?top=2&fetchState=true
```

Odpověď JSON může vypadat takto (formátován pro čitelnost):

```json
[
    {
        "entityId": { "key": "cats", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:45:44.6326361Z",
        "state": { "value": 9 }
    },
    {
        "entityId": { "key": "dogs", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:46:01.9477382Z",
        "state": { "value": 10 }
    }
]
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Naučte se používat Application Insights ke sledování odolných funkcí](durable-functions-diagnostics.md)