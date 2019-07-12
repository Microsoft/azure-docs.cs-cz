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
ms.date: 07/08/2019
ms.author: azfuncdf
ms.openlocfilehash: 7aef7eb2e3d88bef7d2700d9945b9ff343c17536
ms.sourcegitcommit: af31deded9b5836057e29b688b994b6c2890aa79
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67812817"
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

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/HttpStart/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (funguje pouze 2.x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

Tyto funkce příklad vytvoří následující data JSON odpovědi. Datový typ všech polí je `string`.

| Pole                   |Popis                           |
|-----------------------------|--------------------------------------|
| **`id`**                    |ID instance Orchestrace. |
| **`statusQueryGetUri`**     |Adresa URL stavu instance Orchestrace. |
| **`sendEventPostUri`**      |"Vyvolat událost" adresa URL instance Orchestrace. |
| **`terminatePostUri`**      |"Ukončit" adresa URL instance Orchestrace. |
| **`purgeHistoryDeleteUri`** |"Vyprázdnit historii" adresa URL instance Orchestrace. |
| **`rewindPostUri`**         |(preview) "Zpět" adresa URL instance Orchestrace. |

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
> Formát adresy URL webhooku se může lišit v závislosti na tom, které verze hostitelů Azure Functions, kterou používáte. Výše uvedeném příkladu se pro hostitele 2.x Azure Functions.

## <a name="async-operation-tracking"></a>Asynchronní operace sledování

Odpověď HTTP již bylo zmíněno dříve slouží k implementaci asynchronní HTTP dlouho běžící rozhraní API pomocí Durable Functions. To se někdy označuje jako *dotazování vzoru příjemce*. Klient/server tok funguje takto:

1. Klient odešle požadavek HTTP spusťte dlouhotrvající proces, jako je například funkce orchestrátoru.
2. HTTP cílový trigger vrátí odpověď HTTP 202 s `Location` záhlaví s `statusQueryGetUri` hodnotu.
3. Klient dotazuje na adresu URL v `Location` záhlaví. Pokračuje v odpovědi HTTP 202 s najdete v článku `Location` záhlaví.
4. Když instance dokončí (nebo selže), koncový bod `Location` vrátí hlavičky HTTP 200.

Tento protokol umožňuje koordinace dlouho běžící procesy s externími klienty nebo služby, které podporují dotazování koncový bod protokolu HTTP a postupovat `Location` záhlaví. Základní částí jsou již součástí trvalý rozhraní HTTP API funkce.

> [!NOTE]
> Ve výchozím nastavení, všechny akce založené na protokolu HTTP poskytuje [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) podporovat model standardní asynchronní operace. Tato funkce umožňuje vložit funkci trvalý dlouho běžící jako součást pracovního postupu Logic Apps. Další informace o Logic Apps podporu pro asynchronní schémata HTTP najdete v [dokumentaci akcí a triggerů pracovního postupu Azure Logic Apps](../../logic-apps/logic-apps-workflow-actions-triggers.md#asynchronous-patterns).

## <a name="http-api-reference"></a>Reference k rozhraní API protokolu HTTP

Všechna rozhraní API HTTP implementováno rozšíření zkuste následující parametry. Datový typ všech parametrů je `string`.

| Parametr        | Typ parametru  | Popis |
|------------------|-----------------|-------------|
| **`taskHub`**    | Řetězec dotazu    | Název [centra úloh](durable-functions-task-hubs.md). Pokud není zadán, předpokládá se název centra úloh aktuální aplikaci funkcí. |
| **`connection`** | Řetězec dotazu    | **Název** připojovacího řetězce pro účet úložiště. Pokud není zadán, předpokládá se výchozí připojovací řetězec pro danou aplikaci funkcí. |
| **`systemKey`**  | Řetězec dotazu    | Autorizační klíč požadované k vyvolání rozhraní API. |

`systemKey` automaticky generované tímto hostitelem Azure Functions je autorizačního klíče. Konkrétně uděluje přístup k rozšíření trvalý úlohy rozhraní API a je možné spravovat stejným způsobem jako [jiných autorizace klíčů](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API). Nejjednodušší způsob, jak zjistit `systemKey` hodnotu s použitím `CreateCheckStatusResponse` API již bylo zmíněno dříve.

Několik částí zahrnují konkrétní rozhraní API HTTP nepodporuje rozšíření a poskytnout příklady, jak můžete použít.

### <a name="get-instance-status"></a>Získat stav instance

Získá stav instance zadaného Orchestrace.

#### <a name="request"></a>Žádost

Pro verzi 1.x modul runtime služby Functions, požadavek formátována následujícím způsobem (více řádků jsou platné pro přehlednost):

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

Ve verzi 2.x modul runtime služby Functions, formát adresy URL má stejné parametry, ale s mírně odlišné předpony:

```http
GET /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

Požádat o výchozí sadu a také následující unikátní parametry již bylo zmíněno dříve zahrnují parametry pro toto rozhraní API:

| Pole                   | Typ parametru  | Popis |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL             | ID instance Orchestrace. |
| **`showInput`**         | Řetězec dotazu    | Volitelný parametr. Pokud hodnotu `false`, vstup funkce nebudou zahrnuty do datové části odpovědi.|
| **`showHistory`**       | Řetězec dotazu    | Volitelný parametr. Pokud hodnotu `true`, historie spouštění Orchestrace budou zahrnuty do datové části odpovědi.|
| **`showHistoryOutput`** | Řetězec dotazu    | Volitelný parametr. Pokud hodnotu `true`, funkce výstupy, budou zahrnuty do historie spouštění Orchestrace.|
| **`createdTimeFrom`**   | Řetězec dotazu    | Volitelný parametr. -Li zadána, vyfiltruje seznam vráceného instancí, které byly vytvořeny na nebo za dané časové razítko ISO8601.|
| **`createdTimeTo`**     | Řetězec dotazu    | Volitelný parametr. -Li zadána, vyfiltruje seznam vráceného instancí, které byly vytvořeny pozici nebo před daným časovým razítkem ISO8601.|
| **`runtimeStatus`**     | Řetězec dotazu    | Volitelný parametr. -Li zadána, filtry seznamu vrácených instancí na základě jejich stav modulu runtime. Pokud chcete zobrazit seznam hodnot stavu modulu runtime je to možné, naleznete v tématu [dotazování instance](durable-functions-instance-management.md) tématu. |

#### <a name="response"></a>Odpověď

Několik hodnot kód stavu je to možné, může být vrácen.

* **HTTP 200 (OK)** : Zadaná instance je ve stavu dokončení.
* **HTTP 202 (přijato)** : Probíhá určené instance.
* **HTTP 400 (Chybný požadavek)** : Zadaná instance se nezdařilo nebo bylo ukončeno.
* **HTTP 404 (Nenalezeno)** : Zadaná instance neexistuje nebo nebyl spuštěn.
* **HTTP 500 (vnitřní chyba serveru)** : Zadaná instance selhala s neošetřenou výjimkou.

Datová část odpovědi **HTTP 200** a **HTTP 202** případech je objekt JSON s následující pole:

| Pole                 | Datový typ | Popis |
|-----------------------|-----------|-------------|
| **`runtimeStatus`**   | řetězec    | Stav běhu instance. Mezi hodnoty patří *systémem*, *čekající*, *neúspěšné*, *zrušeno*, *ukončeno*, *Dokončit*. |
| **`input`**           | JSON      | Data JSON, která používají k inicializaci instance. Toto pole je `null` Pokud `showInput` parametru řetězce dotazu je nastavena `false`.|
| **`customStatus`**    | JSON      | Data JSON pro stav vlastní Orchestrace. Toto pole je `null` Pokud není nastavena. |
| **`output`**          | JSON      | Výstup JSON instance. Toto pole je `null` Pokud instance není v dokončeném stavu. |
| **`createdTime`**     | řetězec    | Čas, kdy byla vytvořena instance. Využívá rozšířené notace formátu ISO 8601. |
| **`lastUpdatedTime`** | řetězec    | Čas, ve kterém instance poslední trvale uložena. Využívá rozšířené notace formátu ISO 8601. |
| **`historyEvents`**   | JSON      | Pole JSON obsahující historii spouštění Orchestrace. Toto pole je `null` není-li `showHistory` parametru řetězce dotazu je nastavena `true`. |

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

Můžete také zjistit stav všech instancí tak, že odeberete `instanceId` z požadavku "Získat stav instance". Základní parametry v tomto případě jsou stejné jako stav instance Get. Parametry řetězce dotazu pro filtrování jsou také podporovány.

Je jedna věc, mějte na paměti, že `connection` a `code` jsou volitelné. Pokud máte anonymní ověřování na funkci kódu není povinné.
Pokud nechcete použít jiné než definované v nastavení aplikace, které AzureWebJobsStorage jiného úložiště připojovací řetězec, můžete bezpečně ignorovat připojení parametru řetězce dotazu.

#### <a name="request"></a>Žádost

Pro verzi 1.x modul runtime služby Functions, požadavek formátována následujícím způsobem (více řádků jsou platné pro přehlednost):

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

Ve verzi 2.x modul runtime služby Functions, formát adresy URL má stejné parametry, ale s mírně odlišné předpony:

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

Požádat o výchozí sadu a také následující unikátní parametry již bylo zmíněno dříve zahrnují parametry pro toto rozhraní API:

| Pole                   | Typ parametru  | Popis |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL             | ID instance Orchestrace. |
| **`showInput`**         | Řetězec dotazu    | Volitelný parametr. Pokud hodnotu `false`, vstup funkce nebudou zahrnuty do datové části odpovědi.|
| **`showHistory`**       | Řetězec dotazu    | Volitelný parametr. Pokud hodnotu `true`, historie spouštění Orchestrace budou zahrnuty do datové části odpovědi.|
| **`showHistoryOutput`** | Řetězec dotazu    | Volitelný parametr. Pokud hodnotu `true`, funkce výstupy, budou zahrnuty do historie spouštění Orchestrace.|
| **`createdTimeFrom`**   | Řetězec dotazu    | Volitelný parametr. -Li zadána, vyfiltruje seznam vráceného instancí, které byly vytvořeny na nebo za dané časové razítko ISO8601.|
| **`createdTimeTo`**     | Řetězec dotazu    | Volitelný parametr. -Li zadána, vyfiltruje seznam vráceného instancí, které byly vytvořeny pozici nebo před daným časovým razítkem ISO8601.|
| **`runtimeStatus`**     | Řetězec dotazu    | Volitelný parametr. -Li zadána, filtry seznamu vrácených instancí na základě jejich stav modulu runtime. Pokud chcete zobrazit seznam hodnot stavu modulu runtime je to možné, naleznete v tématu [dotazování instance](durable-functions-instance-management.md) tématu. |
| **`top`**               | Řetězec dotazu    | Volitelný parametr. -Li zadána, omezení počtu instancí vrácených dotazem. |

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
> Tato operace může být velmi náročné z hlediska vstupně-výstupních operací Azure Storage, pokud obsahuje mnoho řádků v tabulce instancí. Další podrobnosti o instanci tabulky najdete v [výkon a škálování v Durable Functions (Azure Functions)](durable-functions-perf-and-scale.md#instances-table) dokumentaci.
>

Pokud existují další výsledky, vrátí se token pro pokračování v hlavičce odpovědi.  Název hlavičky je `x-ms-continuation-token`.

Pokud nastavíte hodnotu token pokračování v dalším záhlaví požadavku, můžete získat další stránky výsledků. Tento název hlavičky žádosti je také `x-ms-continuation-token`.

### <a name="purge-single-instance-history"></a>Vyprázdnit historii jedna instance

Vymaže historii a související artefakty pro instanci zadaného Orchestrace.

#### <a name="request"></a>Žádost

Pro verzi 1.x modul runtime služby Functions, požadavek formátována následujícím způsobem (více řádků jsou platné pro přehlednost):

```http
DELETE /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

Ve verzi 2.x modul runtime služby Functions, formát adresy URL má stejné parametry, ale s mírně odlišné předpony:

```http
DELETE /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

Požádat o výchozí sadu a také následující unikátní parametry již bylo zmíněno dříve zahrnují parametry pro toto rozhraní API:

| Pole             | Typ parametru  | Popis |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | ID instance Orchestrace. |

#### <a name="response"></a>Odpověď

Následující hodnoty kód stavu HTTP může být vrácen.

* **HTTP 200 (OK)** : Historie instance byla úspěšně odstraněna.
* **HTTP 404 (Nenalezeno)** : Zadaná instance neexistuje.

Datová část odpovědi **HTTP 200** případem je objekt JSON s následující pole:

| Pole                  | Datový typ | Popis |
|------------------------|-----------|-------------|
| **`instancesDeleted`** | integer   | Počet instancí odstraněn. Pro případ, jedna instance, by měl vždy být tato hodnota `1`. |

Tady je datovou část odpovědi příklad (ve formátu pro lepší čitelnost):

```json
{
    "instancesDeleted": 1
}
```

### <a name="purge-multiple-instance-history"></a>Vyprázdnit historii více instancí

Můžete také odstranit historii a související artefakty pro víc instancí v rámci centra úloh tak, že odeberete `{instanceId}` z požadavku "Vyprázdnit historii jediné instance". Selektivní vymazání historie instance, použijte stejné filtry, které je popsáno v žádosti "Získat všechny stav instance".

#### <a name="request"></a>Žádost

Pro verzi 1.x modul runtime služby Functions, požadavek formátována následujícím způsobem (více řádků jsou platné pro přehlednost):

```http
DELETE /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

Ve verzi 2.x modul runtime služby Functions, formát adresy URL má stejné parametry, ale s mírně odlišné předpony:

```http
DELETE /runtime/webhooks/durabletask/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

Požádat o výchozí sadu a také následující unikátní parametry již bylo zmíněno dříve zahrnují parametry pro toto rozhraní API:

| Pole                 | Typ parametru  | Popis |
|-----------------------|-----------------|-------------|
| **`createdTimeFrom`** | Řetězec dotazu    | Vyfiltruje seznam instancí vymazány, které byly vytvořeny na nebo za dané časové razítko ISO8601.|
| **`createdTimeTo`**   | Řetězec dotazu    | Volitelný parametr. -Li zadána, vyfiltruje seznam instancí vymazány, které byly vytvořeny pozici nebo před daným časovým razítkem ISO8601.|
| **`runtimeStatus`**   | Řetězec dotazu    | Volitelný parametr. -Li zadána, filtry seznam vymazány instancí na základě jejich stav modulu runtime. Pokud chcete zobrazit seznam hodnot stavu modulu runtime je to možné, naleznete v tématu [dotazování instance](durable-functions-instance-management.md) tématu. |

> [!NOTE]
> Tato operace může být velmi náročné z hlediska vstupně-výstupních operací Azure Storage, pokud existuje hodně řádků v instancí a/nebo historie tabulky. Další informace o těchto tabulkách najdete v [výkon a škálování v Durable Functions (Azure Functions)](durable-functions-perf-and-scale.md#instances-table) dokumentaci.

#### <a name="response"></a>Odpověď

Následující hodnoty kód stavu HTTP může být vrácen.

* **HTTP 200 (OK)** : Historie instance byla úspěšně odstraněna.
* **HTTP 404 (Nenalezeno)** : Nebyly nalezeny žádné instance, která odpovídají výraz filtru.

Datová část odpovědi **HTTP 200** případem je objekt JSON s následující pole:

| Pole                   | Datový typ | Popis |
|-------------------------|-----------|-------------|
| **`instancesDeleted`**  | integer   | Počet instancí odstraněn. |

Tady je datovou část odpovědi příklad (ve formátu pro lepší čitelnost):

```json
{
    "instancesDeleted": 250
}
```

### <a name="raise-event"></a>Vyvolání události

Odešle zprávu oznámení události ke spuštěné instanci Orchestrace.

#### <a name="request"></a>Žádost

Pro verzi 1.x modul runtime služby Functions, požadavek formátována následujícím způsobem (více řádků jsou platné pro přehlednost):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

Ve verzi 2.x modul runtime služby Functions, formát adresy URL má stejné parametry, ale s mírně odlišné předpony:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

Požádat o výchozí sadu a také následující unikátní parametry již bylo zmíněno dříve zahrnují parametry pro toto rozhraní API:

| Pole             | Typ parametru  | Popis |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | ID instance Orchestrace. |
| **`eventName`**   | URL             | Název události, která cílová instance Orchestrace čeká na. |
| **`{content}`**   | Požadavek na obsah | Datová část události ve formátu JSON. |

#### <a name="response"></a>Odpověď

Několik hodnot kód stavu je to možné, může být vrácen.

* **HTTP 202 (přijato)** : Vyvolána událost byla přijata ke zpracování.
* **HTTP 400 (Chybný požadavek)** : Obsah požadavku nebyl typu `application/json` nebo nebyl platný kód JSON.
* **HTTP 404 (Nenalezeno)** : Zadané instance nebyla nalezena.
* **HTTP 410 (pryč)** : Zadaná instance dokončí nebo se nezdařilo a nemůže zpracovat žádné vyvolanou události.

Tady je příklad žádosti, která odešle řetězec JSON `"incr"` k čekání na událost s názvem instance **operace**:

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code=XXX
Content-Type: application/json
Content-Length: 6

"incr"
```

Odpovědi pro toto rozhraní API neobsahuje žádný obsah.

### <a name="terminate-instance"></a>Ukončení instance

Ukončí běžící instanci Orchestrace.

#### <a name="request"></a>Žádost

Pro verzi 1.x modul runtime služby Functions, požadavek formátována následujícím způsobem (více řádků jsou platné pro přehlednost):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Ve verzi 2.x modul runtime služby Functions, formát adresy URL má stejné parametry, ale s mírně odlišné předpony:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Požádat o parametry pro toto rozhraní API obsahovat výchozí sadu a také následující jedinečný parametr již bylo zmíněno dříve.

| Pole             | Typ parametru  | Popis |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | ID instance Orchestrace. |
| **`reason`**      | Řetězec dotazu    | Volitelné. Důvod ukončení instance Orchestrace. |

#### <a name="response"></a>Odpověď

Několik hodnot kód stavu je to možné, může být vrácen.

* **HTTP 202 (přijato)** : Ukončení požadavku byla přijata ke zpracování.
* **HTTP 404 (Nenalezeno)** : Zadané instance nebyla nalezena.
* **HTTP 410 (pryč)** : Zadaná instance dokončí nebo se nezdařilo.

Tady je příklad žádosti, která ukončí spuštěné instance a určí důvod z **buggy**:

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Odpovědi pro toto rozhraní API neobsahuje žádný obsah.

### <a name="rewind-instance-preview"></a>REWIND instance (preview)

Obnoví instanci neúspěšné Orchestrace do spuštěného stavu přehráním nejnovější neúspěšné operace.

#### <a name="request"></a>Žádost

Pro verzi 1.x modul runtime služby Functions, požadavek formátována následujícím způsobem (více řádků jsou platné pro přehlednost):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Ve verzi 2.x modul runtime služby Functions, formát adresy URL má stejné parametry, ale s mírně odlišné předpony:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Požádat o parametry pro toto rozhraní API obsahovat výchozí sadu a také následující jedinečný parametr již bylo zmíněno dříve.

| Pole             | Typ parametru  | Popis |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | ID instance Orchestrace. |
| **`reason`**      | Řetězec dotazu    | Volitelné. Důvod převíjení Orchestrace instance. |

#### <a name="response"></a>Odpověď

Několik hodnot kód stavu je to možné, může být vrácen.

* **HTTP 202 (přijato)** : Rewind žádost byla přijata ke zpracování.
* **HTTP 404 (Nenalezeno)** : Zadané instance nebyla nalezena.
* **HTTP 410 (pryč)** : Zadaná instance byla dokončena nebo byl ukončen.

Tady je příklad žádosti, která přetočí nezdařených instancí a určí důvod z **oprava**:

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/rewind?reason=fixed&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Odpovědi pro toto rozhraní API neobsahuje žádný obsah.

### <a name="signal-entity-preview"></a>Signál entity (preview)

Odešle zprávu Jednosměrná operace [trvalý Entity](durable-functions-types-features-overview.md#entity-functions). Pokud entita neexistuje, vytvoří se automaticky.

#### <a name="request"></a>Žádost

Požadavek HTTP je ve formátu následujícím způsobem (více řádků jsou platné pro přehlednost):

```http
POST /runtime/webhooks/durabletask/entities/{entityType}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &op={operationName}
```

Požádat o výchozí sadu a také následující unikátní parametry již bylo zmíněno dříve zahrnují parametry pro toto rozhraní API:

| Pole             | Typ parametru  | Popis |
|-------------------|-----------------|-------------|
| **`entityType`**  | URL             | Typ entity. |
| **`entityKey`**   | URL             | Jedinečný název entity. |
| **`op`**          | Řetězec dotazu    | Volitelné. Název definovaný uživatelem k vyvolání operace. |
| **`{content}`**   | Požadavek na obsah | Datová část události ve formátu JSON. |

Tady je příklad žádosti, která odesílá zprávu "Add" uživatelem definované pro `Counter` entitu s názvem `steps`. Obsah zprávy je hodnota `5`. Pokud entita ještě neexistuje, vytvoří se v této žádosti:

```http
POST /runtime/webhooks/durabletask/entities/Counter/steps?op=Add
Content-Type: application/json

5
```

#### <a name="response"></a>Odpověď

Tato operace má několik možných odpovědi:

* **HTTP 202 (přijato)** : Pro asynchronní zpracování byl přijat signál operace.
* **HTTP 400 (Chybný požadavek)** : Obsah požadavku nebyl typu `application/json`, nebyl platný kód JSON, nebo měl neplatný `entityKey` hodnotu.
* **HTTP 404 (Nenalezeno)** : Zadaný `entityType` nebyl nalezen.

Úspěšného požadavku HTTP neobsahuje žádný obsah v odpovědi. Neúspěšné žádosti HTTP může obsahovat informace o chybě ve formátu JSON v obsahu odpovědi.

### <a name="query-entity-preview"></a>Entita dotazu (preview)

Získá stav zadané entity.

#### <a name="request"></a>Žádost

Požadavek HTTP je ve formátu následujícím způsobem (více řádků jsou platné pro přehlednost):

```http
GET /runtime/webhooks/durabletask/entities/{entityType}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

#### <a name="response"></a>Odpověď

Tato operace má dva možné odpovědi:

* **HTTP 200 (OK)** : Zadaná entita existuje.
* **HTTP 404 (Nenalezeno)** : Zadaná entita se nenašla.

Úspěšná odpověď obsahuje stav serializací JSON entity jako jeho obsah.

#### <a name="example"></a>Příklad
Následuje příklad požadavku protokolu HTTP, která získá stav existující `Counter` entitu s názvem `steps`:

```http
GET /runtime/webhooks/durabletask/entities/Counter/steps
```

Pokud `Counter` entity jednoduše obsahovala řadu kroků, které jsou uložené v `currentValue` pole, obsah odpovědi může vypadat třeba takto (ve formátu pro lepší čitelnost):

```json
{
    "currentValue": 5
}
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Zjistěte, jak zpracovávat chyby](durable-functions-error-handling.md)
