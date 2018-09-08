---
title: Rozhraní API protokolu HTTP v Durable Functions – Azure
description: Zjistěte, jak implementovat rozhraní API HTTP v rozšíření Durable Functions pro službu Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 3fa4f230f5e2d15e815c47792c3955aa93d29fc4
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44094735"
---
# <a name="http-apis-in-durable-functions-azure-functions"></a>Rozhraní API protokolu HTTP v Durable Functions (Azure Functions)

Rozšíření trvalý úloha zveřejňuje sadu rozhraní API HTTP, který slouží k provádění následujících úloh:

* Načte stav instance Orchestrace.
* Odeslání události do instance Orchestrace čekání.
* Ukončí běžící instanci Orchestrace.


Každá z těchto rozhraní API protokolu HTTP se operace webhooku, která, který přímo zpracovává rozšíření trvalý úlohy. Nejsou specifické pro všechny funkce v aplikaci function app.

> [!NOTE]
> Tyto operace může být vyvoláno také přímo pomocí rozhraní API pro správu instancí na [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) třídy. Další informace najdete v tématu [Správa instancí](durable-functions-instance-management.md).

## <a name="http-api-url-discovery"></a>Zjišťování adresy URL rozhraní API protokolu HTTP

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) třídy zpřístupňuje [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_) API, které můžete použít ke generování datovou část odpovědi protokolu HTTP obsahující odkazy na všechny podporované operace. Tady je příklad funkce triggeru HTTP, který ukazuje, jak pomocí tohoto rozhraní API:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/HttpStart/run.csx)]

Tato funkce příklad vytváří následující data JSON odpovědi. Datový typ všech polí je `string`.

| Pole             |Popis                           |
|-------------------|--------------------------------------|
| id                |ID instance Orchestrace. |
| statusQueryGetUri |Adresa URL stavu instance Orchestrace. |
| sendEventPostUri  |"Vyvolat událost" adresa URL instance Orchestrace. |
| terminatePostUri  |"Ukončit" adresa URL instance Orchestrace. |

Tady je příklad odpovědi:

```http
HTTP/1.1 202 Accepted
Content-Length: 923
Content-Type: application/json; charset=utf-8
Location: https://{host}/runtime/webhooks/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX

{
    "id":"34ce9a28a6834d8492ce6a295f1a80e2",
    "statusQueryGetUri":"https://{host}/runtime/webhooks/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "sendEventPostUri":"https://{host}/runtime/webhooks/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "terminatePostUri":"https://{host}/runtime/webhooks/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code=XXX"
}
```
> [!NOTE]
> Formát adresy URL webhooku se může lišit v závislosti na tom, které verze hostitelů Azure Functions, kterou používáte. Výše uvedeném příkladu je pro Azure Functions 2.0 hostitele.

## <a name="async-operation-tracking"></a>Asynchronní operace sledování

Odpověď HTTP již bylo zmíněno dříve slouží k implementaci asynchronní HTTP dlouho běžící rozhraní API pomocí Durable Functions. To se někdy označuje jako *dotazování vzoru příjemce*. Klient/server tok funguje takto:

1. Klient odešle požadavek HTTP spusťte dlouhotrvající proces, jako je například funkce orchestrátoru.
2. HTTP cílový trigger vrátí odpověď HTTP 202 s `Location` záhlaví s `statusQueryGetUri` hodnotu.
3. Klient dotazuje na adresu URL v `Location` záhlaví. Pokračuje v odpovědi HTTP 202 s najdete v článku `Location` záhlaví.
4. Když instance dokončí (nebo selže), koncový bod `Location` vrátí hlavičky HTTP 200.

Tento protokol umožňuje koordinace dlouho běžící procesy s externími klienty nebo služby, které podporují dotazování koncový bod protokolu HTTP a postupovat `Location` záhlaví. Základní částí jsou již součástí trvalý rozhraní HTTP API funkce.

> [!NOTE]
> Ve výchozím nastavení, všechny akce založené na protokolu HTTP poskytuje [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) podporovat model standardní asynchronní operace. Tato funkce umožňuje vložit funkci trvalý dlouho běžící jako součást pracovního postupu Logic Apps. Další informace o Logic Apps podporu pro asynchronní schémata HTTP najdete v [dokumentaci akcí a triggerů pracovního postupu Azure Logic Apps](../logic-apps/logic-apps-workflow-actions-triggers.md#asynchronous-patterns).

## <a name="http-api-reference"></a>Reference k rozhraní API protokolu HTTP

Všechna rozhraní API HTTP implementováno rozšíření zkuste následující parametry. Datový typ všech parametrů je `string`.

| Parametr  | Typ parametru  | Popis |
|------------|-----------------|-------------|
| instanceId | zprostředkovatele identity             | ID instance Orchestrace. |
| taskHub    | Řetězec dotazu    | Název [centra úloh](durable-functions-task-hubs.md). Pokud není zadán, předpokládá se název centra úloh aktuální aplikaci funkcí. |
| připojení | Řetězec dotazu    | **Název** připojovacího řetězce pro účet úložiště. Pokud není zadán, předpokládá se výchozí připojovací řetězec pro danou aplikaci funkcí. |
| systemKey  | Řetězec dotazu    | Autorizační klíč požadované k vyvolání rozhraní API. |
| showHistory| Řetězec dotazu    | Volitelný parametr. Pokud hodnotu `true`, historie spouštění Orchestrace budou zahrnuty do datové části odpovědi.| 
| showHistoryOutput| Řetězec dotazu    | Volitelný parametr. Pokud hodnotu `true`, že výstupem aktivity budou zahrnuty do historie spouštění Orchestrace.| 

`systemKey` automaticky generované tímto hostitelem Azure Functions je autorizačního klíče. Konkrétně uděluje přístup k rozšíření trvalý úlohy rozhraní API a je možné spravovat stejným způsobem jako [jiných autorizace klíčů](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API). Nejjednodušší způsob, jak zjistit `systemKey` hodnotu s použitím `CreateCheckStatusResponse` API již bylo zmíněno dříve.

Několik částí zahrnují konkrétní rozhraní API HTTP nepodporuje rozšíření a poskytnout příklady, jak můžete použít.

### <a name="get-instance-status"></a>Získat stav instance

Získá stav instance zadaného Orchestrace.

#### <a name="request"></a>Žádost

Pro funkce 1.0 formát požadavku je následující:

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}?taskHub={taskHub}&connection={connection}&code={systemKey}
```

Formát Functions 2.0 má stejné parametry, ale má mírně odlišné předpony adresy URL:

```http
GET /runtime/webhooks/DurableTaskExtension/instances/{instanceId}?taskHub={taskHub}&connection={connection}&code={systemKey}&showHistory={showHistory}&showHistoryOutput={showHistoryOutput}
```

#### <a name="response"></a>Odpověď

Několik hodnot kód stavu je to možné, může být vrácen.

* **HTTP 200 (OK)**: Zadaná instance je ve stavu dokončení.
* **HTTP 202 (přijato)**: Zadaná instance právě probíhá.
* **HTTP 400 (Chybný požadavek)**: Zadaná instance se nezdařilo nebo bylo ukončeno.
* **HTTP 404 (Nenalezeno)**: Zadaná instance neexistuje nebo nebyl spuštěn.

Datová část odpovědi **HTTP 200** a **HTTP 202** případech je objekt JSON s následující pole:

| Pole           | Typ dat | Popis |
|-----------------|-----------|-------------|
| runtimeStatus   | řetězec    | Stav běhu instance. Mezi hodnoty patří *systémem*, *čekající*, *neúspěšné*, *zrušeno*, *ukončeno*, *Dokončit*. |
| vstup           | JSON      | Data JSON, která používají k inicializaci instance. |
| customStatus    | JSON      | Data JSON pro stav vlastní Orchestrace. Toto pole je `null` Pokud není nastavena. |
| output          | JSON      | Výstup JSON instance. Toto pole je `null` Pokud instance není v dokončeném stavu. |
| čas vytvoření     | řetězec    | Čas, kdy byla vytvořena instance. Využívá rozšířené notace formátu ISO 8601. |
| lastUpdatedTime | řetězec    | Čas, ve kterém instance poslední trvale uložena. Využívá rozšířené notace formátu ISO 8601. |
| historyEvents   | JSON      | Pole JSON obsahující historii spouštění Orchestrace. Toto pole je `null` není-li `showHistory` parametru řetězce dotazu je nastavena `true`.  | 

Tady je datovou část odpovědi příklad včetně Orchestrace provádění historie a aktivita výstupy (ve formátu pro lepší čitelnost):

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

**HTTP 202** odpovědi také zahrnují **umístění** hlavička odpovědi, který odkazuje na stejnou adresu URL jako `statusQueryGetUri` pole již bylo zmíněno dříve.

### <a name="get-all-instances-status"></a>Získat stav všech instancí

Dotazovat můžete také stav všech instancí. Odeberte `instanceId` z požadavku "Získat stav instance". Parametry jsou stejné jako "Get stav instance." 

#### <a name="request"></a>Žádost

Pro funkce 1.0 formát požadavku je následující:

```http
GET /admin/extensions/DurableTaskExtension/instances/?taskHub={taskHub}&connection={connection}&code={systemKey}
```

Formát Functions 2.0 má stejné parametry, ale mírně odlišné předpony adresy URL: 

```http
GET /runtime/webhooks/DurableTaskExtension/instances/?taskHub={taskHub}&connection={connection}&code={systemKey}
```

#### <a name="response"></a>Odpověď

Tady je příklad z datové části odpovědi včetně stavové Orchestrace (ve formátu pro lepší čitelnost):

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
> Tato operace může být velmi náročné z hlediska vstupně-výstupních operací Azure Storage, pokud obsahuje mnoho řádků v tabulce instancí. Další podrobnosti o instanci tabulky najdete v [výkon a škálování v Durable Functions (Azure Functions)](https://docs.microsoft.com/en-us/azure/azure-functions/durable-functions-perf-and-scale#instances-table) dokumentaci.
> 

### <a name="raise-event"></a>Vyvolání události

Odešle zprávu oznámení události ke spuštěné instanci Orchestrace.

#### <a name="request"></a>Žádost

Pro funkce 1.0 formát požadavku je následující:

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection={connection}&code={systemKey}
```

Formát Functions 2.0 má stejné parametry, ale má mírně odlišné předpony adresy URL:

```http
POST /runtime/webhooks/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection={connection}&code={systemKey}
```

Požádat o výchozí sadu a také následující unikátní parametry již bylo zmíněno dříve zahrnují parametry pro toto rozhraní API:

| Pole       | Typ parametru  | TType dat | Popis |
|-------------|-----------------|-----------|-------------|
| eventName   | zprostředkovatele identity             | řetězec    | Název události, která cílová instance Orchestrace čeká na. |
| {obsah}   | Požadavek na obsah | JSON      | Datová část události ve formátu JSON. |

#### <a name="response"></a>Odpověď

Několik hodnot kód stavu je to možné, může být vrácen.

* **HTTP 202 (přijato)**: vyvolanou událost byla přijata ke zpracování.
* **HTTP 400 (Chybný požadavek)**: obsah požadavku nebyl typu `application/json` nebo nebyl platný kód JSON.
* **HTTP 404 (Nenalezeno)**: zadané instance nebyla nalezena.
* **HTTP 410 (Gone)**: Zadaná instance dokončí nebo se nezdařilo a nemůže zpracovat žádné vyvolanou události.

Tady je příklad žádosti, která odešle řetězec JSON `"incr"` k čekání na událost s názvem instance **operace**:

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code=XXX
Content-Type: application/json
Content-Length: 6

"incr"
```

Odpovědi pro toto rozhraní API neobsahuje žádný obsah.

### <a name="terminate-instance"></a>Ukončení instance

Ukončí běžící instanci Orchestrace.

#### <a name="request"></a>Žádost

Pro funkce 1.0 formát požadavku je následující:

```http
DELETE /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate?reason={reason}&taskHub={taskHub}&connection={connection}&code={systemKey}
```

Formát Functions 2.0 má stejné parametry, ale má mírně odlišné předpony adresy URL:

```http
DELETE /runtime/webhooks/DurableTaskExtension/instances/{instanceId}/terminate?reason={reason}&taskHub={taskHub}&connection={connection}&code={systemKey}
```

Požádat o parametry pro toto rozhraní API obsahovat výchozí sadu a také následující jedinečný parametr již bylo zmíněno dříve.

| Pole       | Typ parametru  | Typ dat | Popis |
|-------------|-----------------|-----------|-------------|
| reason      | Řetězec dotazu    | řetězec    | Volitelné. Důvod ukončení instance Orchestrace. |

#### <a name="response"></a>Odpověď

Několik hodnot kód stavu je to možné, může být vrácen.

* **HTTP 202 (přijato)**: ukončení požadavku byla přijata ke zpracování.
* **HTTP 404 (Nenalezeno)**: zadané instance nebyla nalezena.
* **HTTP 410 (Gone)**: Zadaná instance dokončí nebo se nezdařilo.

Tady je příklad žádosti, která ukončí spuštěné instance a určí důvod z **buggy**:

```
DELETE /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Odpovědi pro toto rozhraní API neobsahuje žádný obsah.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Zjistěte, jak zpracovávat chyby](durable-functions-error-handling.md)
