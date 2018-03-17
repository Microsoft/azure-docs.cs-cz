---
title: "Rozhraní API HTTP trvanlivý funkcí – Azure"
description: "Zjistěte, jak implementovat rozhraní API HTTP v rozšíření trvanlivý funkce pro Azure Functions."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 5fa5d9e66912bdeffdf553ddc0cb7d3feb0a5b77
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="http-apis-in-durable-functions-azure-functions"></a>Rozhraní API HTTP trvanlivý funkcí (Azure Functions)

Trvanlivý úloh rozšíření zveřejňuje sadu rozhraní API HTTP, který slouží k provádění následujících úloh:

* Načtěte stav instance orchestration.
* Odeslání události do instance orchestration čekání.
* Ukončete spuštěné instance orchestration.


Každá z těchto rozhraní API HTTP je webhooku operaci, kterou provádí přímo rozšířením trvanlivý úloh. Nejsou specifické pro libovolnou funkci v aplikaci funkce.

> [!NOTE]
> Tyto operace může vyvolat také přímo pomocí rozhraní API pro správu instance na [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) třídy. Další informace najdete v tématu [Správa instancí](durable-functions-instance-management.md).

## <a name="http-api-url-discovery"></a>Adresa URL rozhraní API HTTP zjišťování

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) třídy zpřístupňuje [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_) rozhraní API, které lze použít ke generování datovou část odpovědi HTTP obsahující odkazy na všechny podporované operace. Tady je příklad funkce triggeru protokolu HTTP, které ukazuje, jak používat toto rozhraní API:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/HttpStart/run.csx)]

Tento příklad funkce vyvolá data odpovědi na následující JSON. Datový typ všech polí je `string`.

| Pole             |Popis                           |
|-------------------|--------------------------------------|
| id                |ID orchestration instance. |
| statusQueryGetUri |Adresa URL stav instance orchestration. |
| sendEventPostUri  |"Vyvolat událost" adresa URL orchestration instance. |
| terminatePostUri  |"Ukončit" adresa URL instance orchestration. |

Tady je příklad odpověď:

```http
HTTP/1.1 202 Accepted
Content-Length: 923
Content-Type: application/json; charset=utf-8
Location: https://{host}/webhookextensions/handler/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX

{
    "id":"34ce9a28a6834d8492ce6a295f1a80e2",
    "statusQueryGetUri":"https://{host}/webhookextensions/handler/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "sendEventPostUri":"https://{host}/webhookextensions/handler/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "terminatePostUri":"https://{host}/webhookextensions/handler/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code=XXX"
}
```
> [!NOTE]
> Formát adresy URL webhooku se můžou lišit v závislosti na spuštěné verze hostitele Azure Functions. Výše uvedeném příkladu je pro hostitele Azure funkce 2.0.

## <a name="async-operation-tracking"></a>Asynchronní operace sledování

Odpověď HTTP, která již bylo zmíněno dříve usnadňuje implementaci asynchronní HTTP dlouho běžící rozhraní API s trvanlivý funkce. To se někdy označuje jako *dotazování vzoru příjemce*. Tok klienta nebo serveru funguje takto:

1. Klient odešle požadavek HTTP spusťte dlouhotrvající proces, jako je například funkce produktu orchestrator.
2. Aktivační událost cíl HTTP vrátí odpověď HTTP 202 s `Location` hlavička s `statusQueryGetUri` hodnotu.
3. Klient odesílá dotaz na adresu URL v `Location` záhlaví. Pokračuje v tématu odpovědi HTTP 202 s `Location` záhlaví.
4. Pokud instance dokončení (nebo selže), koncového bodu v `Location` vrátí hlavičky HTTP 200.

Tento protokol umožňuje spolupráci dlouho běžící procesy s externími klienty nebo služby, které podporují dotazování koncový bod HTTP a následující `Location` záhlaví. Základní součásti jsou již součástí trvanlivý rozhraní API funkce protokolu HTTP.

> [!NOTE]
> Ve výchozím nastavení, všechny akce založené na protokolu HTTP poskytuje [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) podporují vzor standardní asynchronní operaci. Tato funkce umožňuje vložit funkci trvanlivý dlouho běžící v rámci pracovního postupu Logic Apps. Další informace o Logic Apps podpora asynchronními vzory HTTP lze nalézt v [Azure Logic Apps pracovního postupu akce a aktivační události dokumentaci](../logic-apps/logic-apps-workflow-actions-triggers.md#asynchronous-patterns).

## <a name="http-api-reference"></a>Referenční dokumentace rozhraní API HTTP

Všechna rozhraní API HTTP implementované rozšíření proveďte následující parametry. Datový typ všech parametrů je `string`.

| Parametr  | Typ parametru  | Popis |
|------------|-----------------|-------------|
| identifikátor instanceId | zprostředkovatele identity             | ID orchestration instance. |
| taskHub    | Řetězec dotazu    | Název [úloh centra](durable-functions-task-hubs.md). Pokud není zadáno, je předpokládá, že název centra úloh aktuální aplikaci funkce. |
| připojení | Řetězec dotazu    | **Název** připojovacího řetězce pro účet úložiště. Pokud není zadaný, se předpokládá výchozí připojovací řetězec pro funkce aplikace. |
| systemKey  | Řetězec dotazu    | Klíč autorizace vyžadovaná k vyvolání rozhraní API. |
| showHistory| Řetězec dotazu    | Volitelný parametr. Pokud nastavena na `true`, historie provádění orchestration budou zahrnuty do datové části odpovědi.| 
| showHistoryOutput| Řetězec dotazu    | Volitelný parametr. Pokud nastavena na `true`, že výstupem aktivity budou zahrnuty do historie provádění orchestration.| 

`systemKey` automaticky generovaný hostitelem Azure Functions je autorizační klíč. Konkrétně uděluje přístup k rozšíření trvanlivý úloh rozhraní API a je možné spravovat stejným způsobem jako [jiných autorizace klíčů](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API). Nejjednodušší způsob, jak zjistit, `systemKey` hodnota je pomocí `CreateCheckStatusResponse` rozhraní API již bylo zmíněno dříve.

V dalších oddílech několik obsahuje konkrétní rozhraní API HTTP podporuje rozšíření a příklady o tom, jak můžete používat.

### <a name="get-instance-status"></a>Získat stav instance

Získá stav instance zadaný orchestration.

#### <a name="request"></a>Žádost

Pro funkce 1.0 formát požadavku je následující:

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}?taskHub={taskHub}&connection={connection}&code={systemKey}
```

Formát 2.0 funkce nemá stejné parametry, ale má mírně odlišné předponu adresy URL:

```http
GET /webhookextensions/handler/DurableTaskExtension/instances/{instanceId}?taskHub={taskHub}&connection={connection}&code={systemKey}&showHistory={showHistory}&showHistoryOutput={showHistoryOutput}
```

#### <a name="response"></a>Odpověď

Mohou být vráceny několik možných hodnot.

* **HTTP 200 (OK)**: Zadaná instance je ve stavu dokončení.
* **HTTP 202 (platných)**: Zadaná instance právě probíhá.
* **HTTP 400 (Chybný požadavek)**: Zadaná instance se nezdařilo nebo bylo ukončeno.
* **HTTP 404 (Nenalezeno)**: Zadaná instance neexistuje nebo nebyl spuštění.

Pro datové části odpovědi **HTTP 200** a **HTTP 202** případech je objekt JSON s následující pole:

| Pole           | Typ dat | Popis |
|-----------------|-----------|-------------|
| runtimeStatus   | řetězec    | Stav běhu instance. Hodnoty zahrnují *systémem*, *čekající*, *se nezdařilo*, *zrušeno*, *ukončeno*, *Dokončit*. |
| Vstup           | JSON      | Data JSON, která používá k inicializaci instance. |
| output          | JSON      | Výstup JSON instance. Toto pole je `null` Pokud instance není ve stavu dokončení. |
| createdTime     | řetězec    | Čas vytvoření instance. Využívá rozšířené zápis ISO 8601. |
| lastUpdatedTime | řetězec    | Čas, kdy se k instanci poslední trvalé. Využívá rozšířené zápis ISO 8601. |
| historyEvents   | JSON      | Pole JSON obsahující historie provádění orchestration. Toto pole je `null` Pokud `showHistory` parametr řetězce dotazu je nastavena na `true`.  | 

Tady je datovou část odpovědi příklad včetně orchestration provádění historie a aktivity výstupy (ve formátu čitelnější):

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
  "lastUpdatedTime": "2018-02-28T05:18:54Z",
  "output": [
      "Hello Tokyo!",
      "Hello Seattle!",
      "Hello London!"
  ],
  "runtimeStatus": "Completed"
}
```

**HTTP 202** odpověď obsahuje také **umístění** hlavičku odpovědi, která používá stejnou adresu URL jako `statusQueryGetUri` pole již bylo zmíněno dříve.

### <a name="raise-event"></a>Vyvolání události

Odešle zprávu oznámení o události spuštěné instance orchestration.

#### <a name="request"></a>Žádost

Pro funkce 1.0 formát požadavku je následující:

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection={connection}&code={systemKey}
```

Formát 2.0 funkce nemá stejné parametry, ale má mírně odlišné předponu adresy URL:

```http
POST /webhookextensions/handler/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection={connection}&code={systemKey}
```

Požadavku parametry pro toto rozhraní API patří výchozí nastavení, a také následující parametry jedinečný bylo zmíněno dříve:

| Pole       | Typ parametru  | TType dat | Popis |
|-------------|-----------------|-----------|-------------|
| eventName   | zprostředkovatele identity             | řetězec    | Název události, která je cílová instance orchestration čekání na. |
| {{obsah}   | Požadavek na obsah | JSON      | Datové části události formátu JSON. |

#### <a name="response"></a>Odpověď

Mohou být vráceny několik možných hodnot.

* **HTTP 202 (platných)**: byl přijat vyvolané události ke zpracování.
* **HTTP 400 (Chybný požadavek)**: obsah žádosti nebyla typu `application/json` nebo se nejedná o platný kód JSON.
* **HTTP 404 (Nenalezeno)**: Zadaná instance nebyla nalezena.
* **HTTP 410 (Gone)**: Zadaná instance má byla dokončena nebo se nezdařilo a nemůže zpracovat všechny události vyvolané.

Zde je požadavek příklad, který odesílá řetězce formátu JSON `"incr"` do instance čekání na událost s názvem **operace**:

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code=XXX
Content-Type: application/json
Content-Length: 6

"incr"
```

Odpovědi pro toto rozhraní API neobsahuje žádný obsah.

### <a name="terminate-instance"></a>Ukončení instance

Ukončí spuštěné instance orchestration.

#### <a name="request"></a>Žádost

Pro funkce 1.0 formát požadavku je následující:

```http
DELETE /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate?reason={reason}&taskHub={taskHub}&connection={connection}&code={systemKey}
```

Formát 2.0 funkce nemá stejné parametry, ale má mírně odlišné předponu adresy URL:

```http
DELETE /webhookextensions/handler/DurableTaskExtension/instances/{instanceId}/terminate?reason={reason}&taskHub={taskHub}&connection={connection}&code={systemKey}
```

Požadavku parametry pro toto rozhraní API patří výchozí nastavení, a také následující parametr jedinečný bylo zmíněno dříve.

| Pole       | Typ parametru  | Typ dat | Popis |
|-------------|-----------------|-----------|-------------|
| Důvod      | Řetězec dotazu    | řetězec    | Volitelné. Důvod pro ukončení orchestration instance. |

#### <a name="response"></a>Odpověď

Mohou být vráceny několik možných hodnot.

* **HTTP 202 (platných)**: byl přijat požadavek na ukončení ke zpracování.
* **HTTP 404 (Nenalezeno)**: Zadaná instance nebyla nalezena.
* **HTTP 410 (Gone)**: Zadaná instance má byla dokončena nebo se nezdařilo.

Zde je požadavek příklad, který ukončí spuštěné instance a Určuje důvod z **buggy**:

```
DELETE /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Odpovědi pro toto rozhraní API neobsahuje žádný obsah.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Zjistěte, jak se budou zpracovávat chyby](durable-functions-error-handling.md)
