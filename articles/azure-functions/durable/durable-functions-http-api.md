---
title: Rozhraní API HTTP v Durable Functions – Azure
description: Naučte se implementovat rozhraní API HTTP v rozšíření Durable Functions pro Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: azfuncdf
ms.openlocfilehash: 11ae418ddbe007c6fd5aa44ef22ed7fddec9c702
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087275"
---
# <a name="http-apis-in-durable-functions-azure-functions"></a>Rozhraní API HTTP v Durable Functions (Azure Functions)

Trvalé rozšíření úlohy zpřístupňuje sadu rozhraní API HTTP, která se dají použít k provádění následujících úloh:

* Načtěte stav instance Orchestration.
* Odešle událost do čekající instance orchestrace.
* Ukončí spuštěnou instanci orchestrace.

Každé z těchto rozhraní API HTTP je operace Webhooku, která je zpracována přímo rozšířením trvalé úlohy. Nejsou specifické pro žádnou funkci aplikace Function App.

> [!NOTE]
> Tyto operace lze také vyvolat přímo pomocí rozhraní API pro správu instancí třídy [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) . Další informace najdete v tématu [Správa instancí](durable-functions-instance-management.md).

## <a name="http-api-url-discovery"></a>Zjišťování adresy URL rozhraní API HTTP

Třída [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) zpřístupňuje rozhraní [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_) API, které lze použít ke generování datové části odpovědi HTTP obsahující odkazy na všechny podporované operace. Tady je příklad funkce triggeru HTTP, která ukazuje, jak používat toto rozhraní API:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/HttpStart/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (jenom funkce 2. x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

Tyto ukázkové funkce vyvolávají následující data odpovědi JSON. Datový typ všech polí je `string`.

| Pole                   |Popis                           |
|-----------------------------|--------------------------------------|
| **`id`**                    |ID instance orchestrace. |
| **`statusQueryGetUri`**     |Adresa URL stavu instance orchestrace. |
| **`sendEventPostUri`**      |Adresa URL události vyvolání instance orchestrace. |
| **`terminatePostUri`**      |Adresa URL pro ukončení instance orchestrace. |
| **`purgeHistoryDeleteUri`** |Adresa URL vyprázdnit historii instance Orchestration |
| **`rewindPostUri`**         |Tisk Adresa URL "Rewind" instance orchestrace. |

Tady je příklad odpovědi:

```http
HTTP/1.1 202 Accepted
Content-Length: 923
Content-Type: application/json; charset=utf-8
Location: https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX

{
    "id":"34ce9a28a6834d8492ce6a295f1a80e2",
    "statusQueryGetUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "sendEventPostUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "terminatePostUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "purgeHistoryDeleteUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX"
    "rewindPostUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2/rewind?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code=XXX"
}
```

> [!NOTE]
> Formát adres URL Webhooku se může lišit v závislosti na tom, kterou verzi Azure Functions hostitele používáte. Výše uvedený příklad je určen pro hostitele Azure Functions 2. x.

## <a name="async-operation-tracking"></a>Sledování asynchronních operací

Výše uvedená odpověď protokolu HTTP je navržena tak, aby pomáhala implementaci dlouhotrvajících asynchronních rozhraní API HTTP pomocí Durable Functions. Někdy se označuje jako *vzor příjemce cyklického dotazování*. Tok klienta/serveru funguje takto:

1. Klient vydá požadavek HTTP na spuštění dlouhotrvajícího procesu, jako je například funkce Orchestrator.
2. Cílový aktivační procedura http vrátí odpověď HTTP 202 s `Location` hlavičkou `statusQueryGetUri` s hodnotou.
3. Klient dotazuje adresu URL v `Location` hlavičce. Nadále zobrazuje odpovědi HTTP 202 s `Location` hlavičkou.
4. Když se instance dokončí (nebo se nezdařila), `Location` koncový bod v hlavičce vrátí HTTP 200.

Tento protokol umožňuje koordinovat dlouhotrvající procesy s externími klienty nebo službami, které podporují cyklické dotazování koncového bodu http a `Location` následují za hlavičkou. Základní součásti jsou již integrovány do Durable Functions rozhraní API protokolu HTTP.

> [!NOTE]
> Ve výchozím nastavení všechny akce založené na protokolu HTTP, které poskytuje [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) , podporují standardní vzorek asynchronní operace. Tato možnost umožňuje vložit dlouhodobě spuštěnou trvalou funkci jako součást pracovního postupu Logic Apps. Další podrobnosti o podpoře Logic Apps pro asynchronní vzory HTTP najdete v [dokumentaci k akcím pracovního postupu Azure Logic Apps a triggerům](../../logic-apps/logic-apps-workflow-actions-triggers.md#asynchronous-patterns).

## <a name="http-api-reference"></a>Reference k rozhraní HTTP API

Všechna rozhraní API HTTP implementovaná rozšířením přebírají následující parametry. Datový typ všech parametrů je `string`.

| Parametr        | Typ parametru  | Popis |
|------------------|-----------------|-------------|
| **`taskHub`**    | Řetězec dotazu    | Název [centra úloh](durable-functions-task-hubs.md). Pokud není zadaný, předpokládá se aktuální název centra úloh aplikace Function App. |
| **`connection`** | Řetězec dotazu    | **Název** připojovacího řetězce pro účet úložiště. Pokud není zadaný, předpokládá se výchozí připojovací řetězec pro aplikaci Function App. |
| **`systemKey`**  | Řetězec dotazu    | Autorizační klíč vyžadovaný k vyvolání rozhraní API. |

`systemKey`je autorizační klíč automaticky generovaný hostitelem Azure Functions. Konkrétně udělí přístup k rozhraním API rozšíření odolného úkolu a lze ho spravovat stejným způsobem jako [ostatní autorizační klíče](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API). Nejjednodušší způsob, jak zjistit hodnotu `systemKey` , je `CreateCheckStatusResponse` pomocí výše zmíněného rozhraní API.

Několik dalších oddílů pokrývá konkrétní rozhraní HTTP API podporovaná rozšířením a poskytuje příklady, jak je lze použít.

### <a name="get-instance-status"></a>Získat stav instance

Získá stav zadané instance orchestrace.

#### <a name="request"></a>Žádost

V případě verze 1. x modulu runtime Functions je požadavek formátován následujícím způsobem (pro přehlednost je zobrazeno více řádků):

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub
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
| **`showInput`**         | Řetězec dotazu    | Volitelný parametr. Pokud je nastavená na `false`, vstup funkce nebude zahrnutý v datové části odpovědi.|
| **`showHistory`**       | Řetězec dotazu    | Volitelný parametr. Pokud je nastaveno `true`na, bude historie spouštění orchestrace obsažena v datové části odpovědi.|
| **`showHistoryOutput`** | Řetězec dotazu    | Volitelný parametr. Pokud je nastaveno `true`na, budou výstupy funkce zahrnuty do historie spouštění orchestrace.|
| **`createdTimeFrom`**   | Řetězec dotazu    | Volitelný parametr. Když se tato klauzule zadá, vyfiltruje seznam vrácených instancí, které byly vytvořené na základě zadaného časového razítka ISO8601 nebo po ní.|
| **`createdTimeTo`**     | Řetězec dotazu    | Volitelný parametr. Při zadání se vyfiltruje seznam vrácených instancí, které byly vytvořeny před zadaným časovým razítkem ISO8601 nebo před ním.|
| **`runtimeStatus`**     | Řetězec dotazu    | Volitelný parametr. Když se tato parametr zadá, vyfiltruje seznam vrácených instancí na základě jejich běhového stavu. Seznam možných hodnot běhového stavu najdete v tématu [dotazování instancí](durable-functions-instance-management.md) . |

#### <a name="response"></a>Odpověď

Může být vráceno několik možných hodnot stavového kódu.

* **HTTP 200 (OK)** : Zadaná instance je v dokončeném stavu.
* **HTTP 202 (přijato)** : Zadaná instance probíhá.
* **HTTP 400 (chybný požadavek)** : Zadaná instance se nezdařila nebo byla ukončena.
* **HTTP 404 (Nenalezeno)** : Zadaná instance neexistuje nebo nebyla spuštěna.
* **HTTP 500 (interní chyba serveru)** : Zadaná instance se nezdařila, došlo k neošetřené výjimce.

Datová část odpovědi pro případy **http 200** a **HTTP 202** je objekt JSON s následujícími poli:

| Pole                 | Datový typ | Popis |
|-----------------------|-----------|-------------|
| **`runtimeStatus`**   | řetězec    | Běhový stav instance Mezi hodnoty patří *spuštění*, *čeká*, *Chyba*, *zrušeno*, *ukončeno*, *dokončeno*. |
| **`input`**           | JSON      | Data JSON používaná k inicializaci instance. Toto pole je `null` , `showInput` Pokud je parametr řetězce dotazu nastaven na `false`hodnotu.|
| **`customStatus`**    | JSON      | Data JSON používaná pro vlastní stav orchestrace. Toto pole je `null` v případě, že není nastaveno. |
| **`output`**          | JSON      | Výstup JSON instance Toto pole je `null` v případě, že instance není v dokončeném stavu. |
| **`createdTime`**     | řetězec    | Čas, kdy byla vytvořena instance. Používá rozšířený zápis ISO 8601. |
| **`lastUpdatedTime`** | řetězec    | Čas posledního trvalého uložení instance. Používá rozšířený zápis ISO 8601. |
| **`historyEvents`**   | JSON      | Pole JSON obsahující historii spuštění orchestrace. Toto pole je `null` v případě `showHistory` , že parametr řetězce dotazu není `true`nastaven na hodnotu. |

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

Odpověď **HTTP 202** obsahuje také hlavičku odpovědi **umístění** , která odkazuje na stejnou `statusQueryGetUri` adresu URL jako pole uvedené dříve.

### <a name="get-all-instances-status"></a>Získat stav všech instancí

Můžete také zadat dotaz na stav všech instancí odebráním `instanceId` z požadavku načíst stav instance. V tomto případě jsou základní parametry stejné jako ' načíst stav instance '. Podporují se taky parametry řetězce dotazu pro filtrování.

Jedna z možností, jak si `connection` pamatovat `code` , je to, že a jsou volitelné. Pokud máte funkci anonymního ověřování, pak kód není povinný.
Pokud nechcete použít jiný připojovací řetězec úložiště, který je jiný než definovaný v nastavení aplikace AzureWebJobsStorage, můžete parametr řetězce dotazu připojení bezpečně ignorovat.

#### <a name="request"></a>Žádost

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
| **`showInput`**         | Řetězec dotazu    | Volitelný parametr. Pokud je nastavená na `false`, vstup funkce nebude zahrnutý v datové části odpovědi.|
| **`showHistory`**       | Řetězec dotazu    | Volitelný parametr. Pokud je nastaveno `true`na, bude historie spouštění orchestrace obsažena v datové části odpovědi.|
| **`showHistoryOutput`** | Řetězec dotazu    | Volitelný parametr. Pokud je nastaveno `true`na, budou výstupy funkce zahrnuty do historie spouštění orchestrace.|
| **`createdTimeFrom`**   | Řetězec dotazu    | Volitelný parametr. Když se tato klauzule zadá, vyfiltruje seznam vrácených instancí, které byly vytvořené na základě zadaného časového razítka ISO8601 nebo po ní.|
| **`createdTimeTo`**     | Řetězec dotazu    | Volitelný parametr. Při zadání se vyfiltruje seznam vrácených instancí, které byly vytvořeny před zadaným časovým razítkem ISO8601 nebo před ním.|
| **`runtimeStatus`**     | Řetězec dotazu    | Volitelný parametr. Když se tato parametr zadá, vyfiltruje seznam vrácených instancí na základě jejich běhového stavu. Seznam možných hodnot běhového stavu najdete v tématu [dotazování instancí](durable-functions-instance-management.md) . |
| **`top`**               | Řetězec dotazu    | Volitelný parametr. Při zadání omezí počet instancí vrácených dotazem. |

#### <a name="response"></a>Odpověď

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

Pokud existuje více výsledků, vrátí se token pokračování v hlavičce odpovědi.  Název záhlaví je `x-ms-continuation-token`.

Pokud nastavíte hodnotu tokenu pokračování v další hlavičce žádosti, můžete získat další stránku s výsledky. Tento název je také `x-ms-continuation-token`v hlavičce požadavku.

### <a name="purge-single-instance-history"></a>Vymazat historii jedné instance

Odstraní historii a související artefakty pro zadanou instanci Orchestration.

#### <a name="request"></a>Žádost

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

#### <a name="response"></a>Odpověď

Je možné vrátit následující hodnoty stavového kódu HTTP.

* **HTTP 200 (OK)** : Historie instancí se úspěšně vymazala.
* **HTTP 404 (Nenalezeno)** : Zadaná instance neexistuje.

Datová část odpovědi pro případ **HTTP 200** je objekt JSON s následujícím polem:

| Pole                  | Datový typ | Popis |
|------------------------|-----------|-------------|
| **`instancesDeleted`** | integer   | Počet odstraněných instancí. Pro případ jedné instance by tato hodnota měla být `1`vždy. |

Tady je příklad datové části odezvy (naformátovaná pro čitelnost):

```json
{
    "instancesDeleted": 1
}
```

### <a name="purge-multiple-instance-history"></a>Vyprázdnit historii více instancí

Můžete také odstranit historii a související artefakty pro více instancí v rámci centra úloh odebráním `{instanceId}` z žádosti o vyprázdnění jedné instance z historie. Chcete-li selektivně vymazat historii instancí, použijte stejné filtry popsané v žádosti o získání všech instancí status.

#### <a name="request"></a>Žádost

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

| Pole                 | Typ parametru  | Popis |
|-----------------------|-----------------|-------------|
| **`createdTimeFrom`** | Řetězec dotazu    | Filtruje seznam vyčištěných instancí, které byly vytvořeny v nebo po daném časovém razítku ISO8601.|
| **`createdTimeTo`**   | Řetězec dotazu    | Volitelný parametr. Při zadání se vyfiltruje seznam vyčištěných instancí, které byly vytvořeny před zadaným časovým razítkem ISO8601 nebo před ním.|
| **`runtimeStatus`**   | Řetězec dotazu    | Volitelný parametr. Když se tato pole zadáte, vyfiltruje seznam vyčištěných instancí na základě jejich běhového stavu. Seznam možných hodnot běhového stavu najdete v tématu [dotazování instancí](durable-functions-instance-management.md) . |

> [!NOTE]
> Tato operace může být velmi náročná, pokud jde o Azure Storage I/O, pokud jsou v tabulkách instance a/nebo historie velké množství řádků. Další podrobnosti o těchto tabulkách najdete v dokumentaci [výkon a škálování v Durable Functions (Azure Functions)](durable-functions-perf-and-scale.md#instances-table) .

#### <a name="response"></a>Odpověď

Je možné vrátit následující hodnoty stavového kódu HTTP.

* **HTTP 200 (OK)** : Historie instancí se úspěšně vymazala.
* **HTTP 404 (Nenalezeno)** : Nenašly se žádné instance, které by odpovídaly výrazu filtru.

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

### <a name="raise-event"></a>Vyvolat událost

Odešle zprávu s oznámením o události do spuštěné instance orchestrace.

#### <a name="request"></a>Žádost

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

#### <a name="response"></a>Odpověď

Může být vráceno několik možných hodnot stavového kódu.

* **HTTP 202 (přijato)** : Událost vyvolání byla přijata ke zpracování.
* **HTTP 400 (chybný požadavek)** : Obsah požadavku nebyl typu nebo byl neplatný `application/json` formát JSON.
* **HTTP 404 (Nenalezeno)** : Zadaná instance nebyla nalezena.
* **HTTP 410 (pryč)** : Zadaná instance byla dokončena nebo se nezdařila a nemůže zpracovat žádné události, které byly vyvolány.

Tady je příklad požadavku, který odesílá řetězec `"incr"` JSON do instance, která čeká na **operaci**s názvem události:

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code=XXX
Content-Type: application/json
Content-Length: 6

"incr"
```

Odpovědi pro toto rozhraní API neobsahují žádný obsah.

### <a name="terminate-instance"></a>Ukončit instanci

Ukončí spuštěnou instanci orchestrace.

#### <a name="request"></a>Žádost

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
| **`reason`**      | Řetězec dotazu    | Volitelný parametr. Důvod ukončení instance Orchestration. |

#### <a name="response"></a>Odpověď

Může být vráceno několik možných hodnot stavového kódu.

* **HTTP 202 (přijato)** : Žádost o ukončení byla přijata ke zpracování.
* **HTTP 404 (Nenalezeno)** : Zadaná instance nebyla nalezena.
* **HTTP 410 (pryč)** : Zadaná instance se dokončila nebo se nezdařila.

Tady je příklad požadavku, který ukončí spuštěnou instanci a určuje důvod **ladění**:

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Odpovědi pro toto rozhraní API neobsahují žádný obsah.

### <a name="rewind-instance-preview"></a>Instance Rewind (Preview)

Obnoví instanci orchestrace, která selhala, do běžícího stavu přehráním posledních neúspěšných operací.

#### <a name="request"></a>Žádost

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
| **`reason`**      | Řetězec dotazu    | Volitelný parametr. Důvod pro převinutí instance Orchestration. |

#### <a name="response"></a>Odpověď

Může být vráceno několik možných hodnot stavového kódu.

* **HTTP 202 (přijato)** : Požadavek Rewind byl přijat ke zpracování.
* **HTTP 404 (Nenalezeno)** : Zadaná instance nebyla nalezena.
* **HTTP 410 (pryč)** : Zadaná instance se dokončila nebo se ukončila.

Tady je příklad požadavku, který převinutí neúspěšné instance a určuje důvod **pevné**:

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/rewind?reason=fixed&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Odpovědi pro toto rozhraní API neobsahují žádný obsah.

### <a name="signal-entity-preview"></a>Entita signálu (Preview)

Pošle jednosměrnou zprávu o operaci na [trvalou entitu](durable-functions-types-features-overview.md#entity-functions). Pokud entita neexistuje, vytvoří se automaticky.

#### <a name="request"></a>Žádost

Požadavek HTTP je naformátován takto (pro přehlednost se zobrazí více řádků):

```http
POST /runtime/webhooks/durabletask/entities/{entityType}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &op={operationName}
```

Parametry žádosti pro toto rozhraní API zahrnují výchozí sadu uvedenou výše a následující jedinečné parametry:

| Pole             | Typ parametru  | Popis |
|-------------------|-----------------|-------------|
| **`entityType`**  | URL             | Typ entity |
| **`entityKey`**   | URL             | Jedinečný název entity |
| **`op`**          | Řetězec dotazu    | Volitelný parametr. Název uživatelsky definované operace, která má být vyvolána. |
| **`{content}`**   | Obsah žádosti | Datová část události ve formátu JSON. |

Tady je příklad požadavku, který pošle uživatelem definovanou zprávu "Add" do `Counter` entity s názvem. `steps` Obsah zprávy je hodnota `5`. Pokud entita ještě neexistuje, vytvoří se tímto požadavkem:

```http
POST /runtime/webhooks/durabletask/entities/Counter/steps?op=Add
Content-Type: application/json

5
```

#### <a name="response"></a>Odpověď

Tato operace má několik možných odpovědí:

* **HTTP 202 (přijato)** : Operace signálu byla přijata pro asynchronní zpracování.
* **HTTP 400 (chybný požadavek)** : Obsah požadavku nebyl typu, neplatný formát `application/json`JSON nebo má neplatnou `entityKey` hodnotu.
* **HTTP 404 (Nenalezeno)** : Zadaný parametr `entityType` nebyl nalezen.

Úspěšný požadavek protokolu HTTP neobsahuje žádný obsah v odpovědi. Neúspěšný požadavek HTTP může v obsahu odpovědi obsahovat informace o chybě ve formátu JSON.

### <a name="query-entity-preview"></a>Entita dotazu (Preview)

Získá stav zadané entity.

#### <a name="request"></a>Žádost

Požadavek HTTP je naformátován takto (pro přehlednost se zobrazí více řádků):

```http
GET /runtime/webhooks/durabletask/entities/{entityType}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

#### <a name="response"></a>Odpověď

Tato operace má dvě možné odpovědi:

* **HTTP 200 (OK)** : Zadaná entita existuje.
* **HTTP 404 (Nenalezeno)** : Zadaná entita se nenašla.

Úspěšná odpověď obsahuje stav serializovaného prvku ve formátu JSON, který je jeho obsahem.

#### <a name="example"></a>Příklad
Následuje příklad požadavku HTTP, který získá stav existující `Counter` entity s názvem: `steps`

```http
GET /runtime/webhooks/durabletask/entities/Counter/steps
```

Pokud entita jednoduše obsahovala určitý počet kroků uložených `currentValue` v poli, může obsah odpovědi vypadat jako v následujícím formátu (formátovaný pro čtení): `Counter`

```json
{
    "currentValue": 5
}
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Informace o tom, jak zpracovávat chyby](durable-functions-error-handling.md)
