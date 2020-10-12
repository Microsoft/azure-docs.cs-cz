---
title: Rozhraní API HTTP v Durable Functions-Azure Functions
description: Naučte se implementovat rozhraní API HTTP v rozšíření Durable Functions pro Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: 4e4081ecca4714c713d105d363a83a4f96a0d3fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84697839"
---
# <a name="http-api-reference"></a>Reference k rozhraní HTTP API

Rozšíření Durable Functions zpřístupňuje sadu integrovaných rozhraní API HTTP, která se dají použít k provádění úloh správy [orchestrací](durable-functions-types-features-overview.md#orchestrator-functions), [entit](durable-functions-types-features-overview.md#entity-functions)a [Center úloh](durable-functions-task-hubs.md). Tato rozhraní API HTTP jsou rozšiřitelná Webhooky, které jsou autorizované hostitelem Azure Functions, ale zpracovávají se přímo rozšířením Durable Functions.

Všechna rozhraní API HTTP implementovaná rozšířením vyžadují následující parametry. Datový typ všech parametrů je `string` .

| Parametr        | Typ parametru  | Description |
|------------------|-----------------|-------------|
| **`taskHub`**    | Řetězec dotazu    | Název [centra úloh](durable-functions-task-hubs.md). Pokud není zadaný, předpokládá se aktuální název centra úloh aplikace Function App. |
| **`connection`** | Řetězec dotazu    | **Název** připojovacího řetězce pro účet úložiště. Pokud není zadaný, předpokládá se výchozí připojovací řetězec pro aplikaci Function App. |
| **`systemKey`**  | Řetězec dotazu    | Autorizační klíč vyžadovaný k vyvolání rozhraní API. |

`systemKey` je autorizační klíč automaticky generovaný hostitelem Azure Functions. Konkrétně udělí přístup k rozhraním API rozšíření odolného úkolu a lze ho spravovat stejným způsobem jako [ostatní autorizační klíče](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API). Můžete generovat adresy URL, které obsahují správné `taskHub` `connection` hodnoty, a `systemKey` řetězce dotazu pomocí rozhraní API pro [vázání klienta Orchestration](durable-functions-bindings.md#orchestration-client) , jako jsou `CreateCheckStatusResponse` `CreateHttpManagementPayload` rozhraní API a rozhraní .NET, nebo `createCheckStatusResponse` `createHttpManagementPayload` rozhraní API v JavaScriptu.

Několik dalších oddílů pokrývá konkrétní rozhraní HTTP API podporovaná rozšířením a poskytuje příklady, jak je lze použít.

## <a name="start-orchestration"></a>Spustit orchestraci

Spustí provádění nové instance zadané funkce Orchestrator.

### <a name="request"></a>Žádost

V případě verze 1. x modulu runtime Functions je požadavek formátován následujícím způsobem (pro přehlednost je zobrazeno více řádků):

```http
POST /admin/extensions/DurableTaskExtension/orchestrators/{functionName}/{instanceId?}
     ?taskHub={taskHub}
     &connection={connectionName}
     &code={systemKey}
```

Ve verzi 2. x modulu runtime Functions má formát adresy URL stejné parametry, ale mírně odlišnou předponou:

```http
POST /runtime/webhooks/durabletask/orchestrators/{functionName}/{instanceId?}
     ?taskHub={taskHub}
     &connection={connectionName}
     &code={systemKey}
```

Parametry žádosti pro toto rozhraní API zahrnují výchozí sadu uvedenou výše a následující jedinečné parametry:

| Pole              | Typ parametru  | Popis |
|--------------------|-----------------|-------------|
| **`functionName`** | URL             | Název funkce Orchestrator, která se má spustit. |
| **`instanceId`**   | URL             | Volitelný parametr. ID instance orchestrace. Pokud není zadán, funkce Orchestrator spustí s náhodným ID instance. |
| **`{content}`**    | Obsah žádosti | Nepovinný parametr. Vstup funkce Orchestrator ve formátu JSON. |

### <a name="response"></a>Odpověď

Může být vráceno několik možných hodnot stavového kódu.

* **HTTP 202 (přijato)**: zadaná funkce Orchestrator byla naplánována na spuštění. `Location`Hlavička odpovědi obsahuje adresu URL pro cyklické dotazování na stav orchestrace.
* **HTTP 400 (chybný požadavek)**: zadaná funkce nástroje Orchestrator neexistuje, zadané ID instance není platné nebo není obsah požadavku platný pro JSON.

Následuje příklad požadavku, který spouští `RestartVMs` funkci Orchestrator a obsahuje datovou část objektu JSON:

```http
POST /runtime/webhooks/durabletask/orchestrators/RestartVMs?code=XXX
Content-Type: application/json
Content-Length: 83

{
    "resourceGroup": "myRG",
    "subscriptionId": "111deb5d-09df-4604-992e-a968345530a9"
}
```

Datová část odpovědi pro případy **protokolu HTTP 202** je objekt JSON s následujícími poli:

| Pole                       | Description                          |
|-----------------------------|--------------------------------------|
| **`id`**                    |ID instance orchestrace. |
| **`statusQueryGetUri`**     |Adresa URL stavu instance orchestrace. |
| **`sendEventPostUri`**      |Adresa URL události vyvolání instance orchestrace. |
| **`terminatePostUri`**      |Adresa URL pro ukončení instance orchestrace. |
| **`purgeHistoryDeleteUri`** |Adresa URL vyprázdnit historii instance Orchestration |
| **`rewindPostUri`**         |Tisk Adresa URL "Rewind" instance orchestrace. |

Datový typ všech polí je `string` .

Tady je příklad datové části odezvy pro instanci orchestrace s `abc123` jako její ID (naformátovaná pro čitelnost):

```http
{
    "id": "abc123",
    "purgeHistoryDeleteUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/raiseEvent/{eventName}?code=XXX",
    "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/terminate?reason={text}&code=XXX"
}
```

Odpověď HTTP má být kompatibilní se *vzorem příjemce cyklického dotazování*. Obsahuje také následující významné hlavičky odpovědí:

* **Umístění**: adresa URL koncového bodu stavu. Tato adresa URL obsahuje stejnou hodnotu jako `statusQueryGetUri` pole.
* **Opakovat – za**: počet sekund, po který se má čekat mezi operacemi cyklického dotazování. Výchozí hodnota je `10`.

Další informace o vzoru asynchronního cyklického dotazování HTTP najdete v dokumentaci pro [sledování asynchronních operací HTTP](durable-functions-http-features.md#async-operation-tracking) .

## <a name="get-instance-status"></a>Získat stav instance

Získá stav zadané instance orchestrace.

### <a name="request"></a>Žádost

V případě verze 1. x modulu runtime Functions je požadavek formátován následujícím způsobem (pro přehlednost je zobrazeno více řádků):

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

Ve verzi 2. x modulu runtime Functions má formát adresy URL stejné parametry, ale mírně odlišnou předponou:

```http
GET /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

Parametry žádosti pro toto rozhraní API zahrnují výchozí sadu uvedenou výše a následující jedinečné parametry:

| Pole                   | Typ parametru  | Popis |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL             | ID instance orchestrace. |
| **`showInput`**         | Řetězec dotazu    | Volitelný parametr. Pokud je nastavená na `false` , vstup funkce nebude zahrnutý v datové části odpovědi.|
| **`showHistory`**       | Řetězec dotazu    | Volitelný parametr. Pokud je nastaveno na `true` , bude historie spouštění orchestrace obsažena v datové části odpovědi.|
| **`showHistoryOutput`** | Řetězec dotazu    | Volitelný parametr. Pokud je nastaveno na `true` , budou výstupy funkce zahrnuty do historie spouštění orchestrace.|
| **`createdTimeFrom`**   | Řetězec dotazu    | Volitelný parametr. Když se tato klauzule zadá, vyfiltruje seznam vrácených instancí, které byly vytvořené na základě zadaného časového razítka ISO8601 nebo po ní.|
| **`createdTimeTo`**     | Řetězec dotazu    | Volitelný parametr. Při zadání se vyfiltruje seznam vrácených instancí, které byly vytvořeny před zadaným časovým razítkem ISO8601 nebo před ním.|
| **`runtimeStatus`**     | Řetězec dotazu    | Volitelný parametr. Když se tato parametr zadá, vyfiltruje seznam vrácených instancí na základě jejich běhového stavu. Seznam možných hodnot běhového stavu najdete v článku [dotazování instancí](durable-functions-instance-management.md) . |

### <a name="response"></a>Odpověď

Může být vráceno několik možných hodnot stavového kódu.

* **HTTP 200 (ok)**: Zadaná instance je v dokončeném stavu.
* **HTTP 202 (přijato)**: Zadaná instance probíhá.
* **HTTP 400 (chybný požadavek)**: Zadaná instance se nezdařila nebo byla ukončena.
* **HTTP 404 (Nenalezeno)**: Zadaná instance neexistuje nebo nebyla spuštěna.
* **HTTP 500 (interní chyba serveru)**: Zadaná instance selhala s neošetřenou výjimkou.

Datová část odpovědi pro případy **http 200** a **HTTP 202** je objekt JSON s následujícími poli:

| Pole                 | Datový typ | Description |
|-----------------------|-----------|-------------|
| **`runtimeStatus`**   | řetězec    | Běhový stav instance Mezi hodnoty patří *spuštění*, *čeká*, *Chyba*, *zrušeno*, *ukončeno*, *dokončeno*. |
| **`input`**           | JSON      | Data JSON používaná k inicializaci instance. Toto pole je `null` , pokud je `showInput` parametr řetězce dotazu nastaven na hodnotu `false` .|
| **`customStatus`**    | JSON      | Data JSON používaná pro vlastní stav orchestrace. Toto pole je v `null` případě, že není nastaveno. |
| **`output`**          | JSON      | Výstup JSON instance Toto pole je `null` v případě, že instance není v dokončeném stavu. |
| **`createdTime`**     | řetězec    | Čas, kdy byla vytvořena instance. Používá rozšířený zápis ISO 8601. |
| **`lastUpdatedTime`** | řetězec    | Čas posledního trvalého uložení instance. Používá rozšířený zápis ISO 8601. |
| **`historyEvents`**   | JSON      | Pole JSON obsahující historii spuštění orchestrace. Toto pole je `null` v případě, že `showHistory` parametr řetězce dotazu není nastaven na hodnotu `true` . |

Tady je příklad datové části odpovědi, včetně historie spouštění Orchestrace a výstupů aktivit (s formátováním pro čitelnost):

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

Odpověď **HTTP 202** obsahuje také hlavičku odpovědi **umístění** , která odkazuje na stejnou adresu URL jako `statusQueryGetUri` pole uvedené dříve.

## <a name="get-all-instances-status"></a>Získat stav všech instancí

Můžete také zadat dotaz na stav všech instancí odebráním `instanceId` z požadavku načíst stav instance. V tomto případě jsou základní parametry stejné jako ' načíst stav instance '. Podporují se taky parametry řetězce dotazu pro filtrování.

Jedna z možností, jak si pamatovat, je to, že `connection` a `code` jsou volitelné. Pokud máte funkci anonymního ověřování, pak se `code` nevyžaduje.
Pokud nechcete použít jiný připojovací řetězec úložiště, který je jiný než definovaný v nastavení aplikace AzureWebJobsStorage, můžete parametr řetězce dotazu připojení bezpečně ignorovat.

### <a name="request"></a>Žádost

V případě verze 1. x modulu runtime Functions je požadavek formátován následujícím způsobem (pro přehlednost je zobrazeno více řádků):

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

Ve verzi 2. x modulu runtime Functions má formát adresy URL stejné parametry, ale mírně odlišnou předponou:

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

Parametry žádosti pro toto rozhraní API zahrnují výchozí sadu uvedenou výše a následující jedinečné parametry:

| Pole                   | Typ parametru  | Popis |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL             | ID instance orchestrace. |
| **`showInput`**         | Řetězec dotazu    | Volitelný parametr. Pokud je nastavená na `false` , vstup funkce nebude zahrnutý v datové části odpovědi.|
| **`showHistory`**       | Řetězec dotazu    | Volitelný parametr. Pokud je nastaveno na `true` , bude historie spouštění orchestrace obsažena v datové části odpovědi.|
| **`showHistoryOutput`** | Řetězec dotazu    | Volitelný parametr. Pokud je nastaveno na `true` , budou výstupy funkce zahrnuty do historie spouštění orchestrace.|
| **`createdTimeFrom`**   | Řetězec dotazu    | Volitelný parametr. Když se tato klauzule zadá, vyfiltruje seznam vrácených instancí, které byly vytvořené na základě zadaného časového razítka ISO8601 nebo po ní.|
| **`createdTimeTo`**     | Řetězec dotazu    | Volitelný parametr. Při zadání se vyfiltruje seznam vrácených instancí, které byly vytvořeny před zadaným časovým razítkem ISO8601 nebo před ním.|
| **`runtimeStatus`**     | Řetězec dotazu    | Volitelný parametr. Když se tato parametr zadá, vyfiltruje seznam vrácených instancí na základě jejich běhového stavu. Seznam možných hodnot běhového stavu najdete v článku [dotazování instancí](durable-functions-instance-management.md) . |
| **`top`**               | Řetězec dotazu    | Volitelný parametr. Při zadání omezí počet instancí vrácených dotazem. |

### <a name="response"></a>Odpověď

Tady je příklad datových částí odezvy, včetně stavu orchestrace (naformátovaná pro čitelnost):

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
> Tato operace může být velmi náročná, pokud jde o Azure Storage I/O, pokud je v tabulce instance mnoho řádků. Další podrobnosti o tabulce instance najdete v dokumentaci [výkon a škálování v Durable Functions (Azure Functions)](durable-functions-perf-and-scale.md#instances-table) .
>

Pokud existuje více výsledků, vrátí se token pokračování v hlavičce odpovědi.  Název záhlaví je `x-ms-continuation-token` .

Pokud nastavíte hodnotu tokenu pokračování v další hlavičce žádosti, můžete získat další stránku s výsledky. Tento název je také v hlavičce požadavku `x-ms-continuation-token` .

## <a name="purge-single-instance-history"></a>Vymazat historii jedné instance

Odstraní historii a související artefakty pro zadanou instanci Orchestration.

### <a name="request"></a>Žádost

V případě verze 1. x modulu runtime Functions je požadavek formátován následujícím způsobem (pro přehlednost je zobrazeno více řádků):

```http
DELETE /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

Ve verzi 2. x modulu runtime Functions má formát adresy URL stejné parametry, ale mírně odlišnou předponou:

```http
DELETE /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

Parametry žádosti pro toto rozhraní API zahrnují výchozí sadu uvedenou výše a následující jedinečné parametry:

| Pole             | Typ parametru  | Popis |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | ID instance orchestrace. |

### <a name="response"></a>Odpověď

Je možné vrátit následující hodnoty stavového kódu HTTP.

* **HTTP 200 (ok)**: Historie instancí byla úspěšně vyprázdněna.
* **HTTP 404 (Nenalezeno)**: Zadaná instance neexistuje.

Datová část odpovědi pro případ **HTTP 200** je objekt JSON s následujícím polem:

| Pole                  | Datový typ | Popis |
|------------------------|-----------|-------------|
| **`instancesDeleted`** | integer   | Počet odstraněných instancí. Pro případ jedné instance by tato hodnota měla být vždy `1` . |

Tady je příklad datové části odezvy (naformátovaná pro čitelnost):

```json
{
    "instancesDeleted": 1
}
```

## <a name="purge-multiple-instance-histories"></a>Vyprázdnit několik historií instancí

Můžete také odstranit historii a související artefakty pro více instancí v rámci centra úloh odebráním `{instanceId}` z žádosti o vyprázdnění jedné instance z historie. Chcete-li selektivně vymazat historii instancí, použijte stejné filtry popsané v žádosti o získání všech instancí status.

### <a name="request"></a>Žádost

V případě verze 1. x modulu runtime Functions je požadavek formátován následujícím způsobem (pro přehlednost je zobrazeno více řádků):

```http
DELETE /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

Ve verzi 2. x modulu runtime Functions má formát adresy URL stejné parametry, ale mírně odlišnou předponou:

```http
DELETE /runtime/webhooks/durabletask/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

Parametry žádosti pro toto rozhraní API zahrnují výchozí sadu uvedenou výše a následující jedinečné parametry:

| Pole                 | Typ parametru  | Description |
|-----------------------|-----------------|-------------|
| **`createdTimeFrom`** | Řetězec dotazu    | Filtruje seznam vyčištěných instancí, které byly vytvořeny v nebo po daném časovém razítku ISO8601.|
| **`createdTimeTo`**   | Řetězec dotazu    | Volitelný parametr. Při zadání se vyfiltruje seznam vyčištěných instancí, které byly vytvořeny před zadaným časovým razítkem ISO8601 nebo před ním.|
| **`runtimeStatus`**   | Řetězec dotazu    | Volitelný parametr. Když se tato pole zadáte, vyfiltruje seznam vyčištěných instancí na základě jejich běhového stavu. Seznam možných hodnot běhového stavu najdete v článku [dotazování instancí](durable-functions-instance-management.md) . |

> [!NOTE]
> Tato operace může být velmi náročná, pokud jde o Azure Storage I/O, pokud jsou v tabulkách instance a/nebo historie velké množství řádků. Další podrobnosti o těchto tabulkách najdete v dokumentaci [výkon a škálování v Durable Functions (Azure Functions)](durable-functions-perf-and-scale.md#instances-table) .

### <a name="response"></a>Odpověď

Je možné vrátit následující hodnoty stavového kódu HTTP.

* **HTTP 200 (ok)**: Historie instancí byla úspěšně vyprázdněna.
* **HTTP 404 (Nenalezeno)**: nebyly nalezeny žádné instance odpovídající výrazu filtru.

Datová část odpovědi pro případ **HTTP 200** je objekt JSON s následujícím polem:

| Pole                   | Datový typ | Popis |
|-------------------------|-----------|-------------|
| **`instancesDeleted`**  | integer   | Počet odstraněných instancí. |

Tady je příklad datové části odezvy (naformátovaná pro čitelnost):

```json
{
    "instancesDeleted": 250
}
```

## <a name="raise-event"></a>Vyvolat událost

Odešle zprávu s oznámením o události do spuštěné instance orchestrace.

### <a name="request"></a>Žádost

V případě verze 1. x modulu runtime Functions je požadavek formátován následujícím způsobem (pro přehlednost je zobrazeno více řádků):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

Ve verzi 2. x modulu runtime Functions má formát adresy URL stejné parametry, ale mírně odlišnou předponou:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

Parametry žádosti pro toto rozhraní API zahrnují výchozí sadu uvedenou výše a následující jedinečné parametry:

| Pole             | Typ parametru  | Popis |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | ID instance orchestrace. |
| **`eventName`**   | URL             | Název události, na které instance cílové orchestrace čeká. |
| **`{content}`**   | Obsah žádosti | Datová část události ve formátu JSON. |

### <a name="response"></a>Odpověď

Může být vráceno několik možných hodnot stavového kódu.

* **HTTP 202 (přijato)**: vyvolaná událost byla přijata ke zpracování.
* **HTTP 400 (chybný požadavek)**: obsah požadavku nebyl typu `application/json` nebo byl neplatný formát JSON.
* **HTTP 404 (Nenalezeno)**: Zadaná instance nebyla nalezena.
* **HTTP 410 (pryč)**: Zadaná instance byla dokončena nebo se nezdařila a nemůže zpracovat žádné události, které byly vyvolány.

Tady je příklad požadavku, který odesílá řetězec JSON `"incr"` do instance, která čeká na **operaci**s názvem události:

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

V případě verze 1. x modulu runtime Functions je požadavek formátován následujícím způsobem (pro přehlednost je zobrazeno více řádků):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Ve verzi 2. x modulu runtime Functions má formát adresy URL stejné parametry, ale mírně odlišnou předponou:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Parametry žádosti pro toto rozhraní API obsahují výchozí sadu uvedenou dříve a také následující jedinečný parametr.

| Pole             | Typ parametru  | Popis |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | ID instance orchestrace. |
| **`reason`**      | Řetězec dotazu    | Nepovinný parametr. Důvod ukončení instance Orchestration. |

### <a name="response"></a>Odpověď

Může být vráceno několik možných hodnot stavového kódu.

* **HTTP 202 (přijato)**: žádost o ukončení byla přijata ke zpracování.
* **HTTP 404 (Nenalezeno)**: Zadaná instance nebyla nalezena.
* **HTTP 410 (pryč)**: Zadaná instance je dokončená nebo se nezdařila.

Tady je příklad požadavku, který ukončí spuštěnou instanci a určuje důvod **ladění**:

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Odpovědi pro toto rozhraní API neobsahují žádný obsah.

## <a name="rewind-instance-preview"></a>Instance Rewind (Preview)

Obnoví instanci orchestrace, která selhala, do běžícího stavu přehráním posledních neúspěšných operací.

### <a name="request"></a>Žádost

V případě verze 1. x modulu runtime Functions je požadavek formátován následujícím způsobem (pro přehlednost je zobrazeno více řádků):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Ve verzi 2. x modulu runtime Functions má formát adresy URL stejné parametry, ale mírně odlišnou předponou:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Parametry žádosti pro toto rozhraní API obsahují výchozí sadu uvedenou dříve a také následující jedinečný parametr.

| Pole             | Typ parametru  | Popis |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | ID instance orchestrace. |
| **`reason`**      | Řetězec dotazu    | Nepovinný parametr. Důvod pro převinutí instance Orchestration. |

### <a name="response"></a>Odpověď

Může být vráceno několik možných hodnot stavového kódu.

* **HTTP 202 (přijato)**: žádost o převinutí byla přijata ke zpracování.
* **HTTP 404 (Nenalezeno)**: Zadaná instance nebyla nalezena.
* **HTTP 410 (pryč)**: Zadaná instance je dokončená nebo se ukončila.

Tady je příklad požadavku, který převinutí neúspěšné instance a určuje důvod **pevné**:

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/rewind?reason=fixed&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Odpovědi pro toto rozhraní API neobsahují žádný obsah.

## <a name="signal-entity"></a>Entita signálu

Pošle jednosměrnou zprávu o operaci na [trvalou entitu](durable-functions-types-features-overview.md#entity-functions). Pokud entita neexistuje, vytvoří se automaticky.

> [!NOTE]
> Trvalé entity jsou k dispozici od Durable Functions 2,0.

### <a name="request"></a>Žádost

Požadavek HTTP je naformátován takto (pro přehlednost se zobrazí více řádků):

```http
POST /runtime/webhooks/durabletask/entities/{entityName}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &op={operationName}
```

Parametry žádosti pro toto rozhraní API zahrnují výchozí sadu uvedenou výše a následující jedinečné parametry:

| Pole             | Typ parametru  | Popis |
|-------------------|-----------------|-------------|
| **`entityName`**  | URL             | Název (typ) entity. |
| **`entityKey`**   | URL             | Klíč (jedinečné ID) entity |
| **`op`**          | Řetězec dotazu    | Nepovinný parametr. Název uživatelsky definované operace, která má být vyvolána. |
| **`{content}`**   | Obsah žádosti | Datová část události ve formátu JSON. |

Tady je příklad požadavku, který pošle uživatelem definovanou zprávu "Add" do `Counter` entity s názvem `steps` . Obsah zprávy je hodnota `5` . Pokud entita ještě neexistuje, vytvoří se tímto požadavkem:

```http
POST /runtime/webhooks/durabletask/entities/Counter/steps?op=Add
Content-Type: application/json

5
```

> [!NOTE]
> Ve výchozím nastavení s [entitami založenými na třídě v .NET](durable-functions-dotnet-entities.md#defining-entity-classes), zadáním `op` hodnoty `delete` se odstraní stav entity. Pokud entita definuje operaci s názvem `delete` , bude namísto toho vyvolána operace definovaná uživatelem.

### <a name="response"></a>Odpověď

Tato operace má několik možných odpovědí:

* **HTTP 202 (přijato)**: operace signalizace byla přijata pro asynchronní zpracování.
* **HTTP 400 (chybný požadavek)**: obsah požadavku nebyl typu `application/json` , neplatný formát JSON nebo má neplatnou `entityKey` hodnotu.
* **HTTP 404 (Nenalezeno)**: zadaný parametr `entityName` nebyl nalezen.

Úspěšný požadavek protokolu HTTP neobsahuje žádný obsah v odpovědi. Neúspěšný požadavek HTTP může v obsahu odpovědi obsahovat informace o chybě ve formátu JSON.

## <a name="get-entity"></a>Získat entitu

Získá stav zadané entity.

### <a name="request"></a>Žádost

Požadavek HTTP je naformátován takto (pro přehlednost se zobrazí více řádků):

```http
GET /runtime/webhooks/durabletask/entities/{entityName}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

### <a name="response"></a>Odpověď

Tato operace má dvě možné odpovědi:

* **HTTP 200 (ok)**: zadaná entita existuje.
* **HTTP 404 (Nenalezeno)**: zadaná entita nebyla nalezena.

Úspěšná odpověď obsahuje stav serializovaného prvku ve formátu JSON, který je jeho obsahem.

### <a name="example"></a>Příklad
Následující příklad požadavku HTTP Získá stav existující `Counter` entity s názvem `steps` :

```http
GET /runtime/webhooks/durabletask/entities/Counter/steps
```

Pokud `Counter` entita jednoduše obsahovala určitý počet kroků uložených v `currentValue` poli, může obsah odpovědi vypadat jako v následujícím formátu (formátovaný pro čtení):

```json
{
    "currentValue": 5
}
```

## <a name="list-entities"></a>Vytvoření seznamu entit

Pomocí názvu entity nebo poslední datum operace můžete zadat dotaz na více entit.

### <a name="request"></a>Žádost

Požadavek HTTP je naformátován takto (pro přehlednost se zobrazí více řádků):

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

Parametry žádosti pro toto rozhraní API zahrnují výchozí sadu uvedenou výše a následující jedinečné parametry:

| Pole                       | Typ parametru  | Popis |
|-----------------------------|-----------------|-------------|
| **`entityName`**            | URL             | Nepovinný parametr. Když se tato pole zadáte, vyfiltruje seznam vrácených entit podle jejich názvu (bez rozlišení velkých a malých písmen). |
| **`fetchState`**            | Řetězec dotazu    | Volitelný parametr. Pokud je nastaveno na `true` , bude stav entity obsažen v datové části odpovědi. |
| **`lastOperationTimeFrom`** | Řetězec dotazu    | Volitelný parametr. Když se tato operace zadá, vyfiltruje seznam vrácených entit, které zpracovaly operace po zadaném časovém razítku ISO8601. |
| **`lastOperationTimeTo`**   | Řetězec dotazu    | Volitelný parametr. Když se tato operace zadá, vyfiltruje seznam vrácených entit, které zpracovaly operace před zadaným časovým razítkem ISO8601. |
| **`top`**                   | Řetězec dotazu    | Volitelný parametr. Při zadání omezí počet entit vrácených dotazem. |


### <a name="response"></a>Odpověď

Úspěšná odpověď HTTP 200 obsahuje pole entity Serializované ve formátu JSON a volitelně stav každé entity.

Ve výchozím nastavení tato operace vrátí prvních 100 entit, které odpovídají kritériím dotazu. Volající může zadat hodnotu parametru řetězce dotazu `top` , pro který vrátí jiný maximální počet výsledků. Pokud existuje více výsledků nad rámec toho, co je vráceno, v hlavičce odpovědi je také vrácen token pokračování. Název záhlaví je `x-ms-continuation-token` .

Pokud nastavíte hodnotu tokenu pokračování v další hlavičce žádosti, můžete získat další stránku s výsledky. Tento název je také v hlavičce požadavku `x-ms-continuation-token` .

### <a name="example---list-all-entities"></a>Příklad – seznam všech entit

Následující příklad požadavku HTTP obsahuje seznam všech entit v centru úloh:

```http
GET /runtime/webhooks/durabletask/entities
```

JSON odpovědi může vypadat podobně jako v následujícím textu (formátovaný pro čtení):

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

### <a name="example---filtering-the-list-of-entities"></a>Příklad: filtrování seznamu entit

Následující příklad požadavku HTTP uvádí pouze první dvě entity typu `counter` a načítá jejich stav:

```http
GET /runtime/webhooks/durabletask/entities/counter?top=2&fetchState=true
```

JSON odpovědi může vypadat podobně jako v následujícím textu (formátovaný pro čtení):

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
> [Naučte se používat Application Insights k monitorování trvalých funkcí](durable-functions-diagnostics.md)