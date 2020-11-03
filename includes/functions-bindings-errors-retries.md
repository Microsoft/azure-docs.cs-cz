---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/01/2020
ms.author: glenga
ms.openlocfilehash: 285c3bf37e9d6de042cb028745fc8b094d34c3a1
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2020
ms.locfileid: "93284412"
---
Chyby vyvolané v Azure Functions můžou pocházet z některého z následujících zdrojů:

- Použití integrovaných [triggerů Azure functions a vazeb](..\articles\azure-functions\functions-triggers-bindings.md)
- Volání rozhraní API základních služeb Azure
- Volání koncových bodů REST
- Volání klientských knihoven, balíčků nebo rozhraní API třetích stran

Tyto osvědčené postupy při zpracování chyb jsou důležité, aby nedošlo ke ztrátě dat nebo nezmeškaných zpráv. Doporučené postupy pro zpracování chyb zahrnují následující akce:

- [Povolit Application Insights](../articles/azure-functions/functions-monitoring.md)
- [Použití strukturovaného zpracování chyb](#use-structured-error-handling)
- [Návrh pro idempotence](../articles/azure-functions/functions-idempotent.md)
- [Implementujte zásady opakování](#retry-policies) (tam, kde je to vhodné).

### <a name="use-structured-error-handling"></a>Použití strukturovaného zpracování chyb

Zaznamenávání a protokolování chyb je klíčové pro monitorování stavu vaší aplikace. Nejvyšší úroveň každého kódu funkce by měla obsahovat blok try/catch. V bloku catch můžete zaznamenávat a protokolovat chyby.

## <a name="retry-policies"></a>Zásady opakování

Zásady opakování se dají definovat u libovolné funkce pro libovolný typ triggeru ve vaší aplikaci Function App.  Zásada opakování znovu spustí funkci až do úspěšného provedení nebo do doby, než dojde k maximálnímu počtu opakovaných pokusů.  Zásady opakování lze definovat pro všechny funkce aplikace nebo pro jednotlivé funkce.  Ve výchozím nastavení aplikace Function App neopakuje zprávy (kromě [konkrétních triggerů, které mají ve zdroji triggeru zásady opakování](#trigger-specific-retry-support)).  Zásada opakování je vyhodnocena vždy, když je výsledkem spuštění nezachycená výjimka.  V rámci osvědčeného postupu byste měli zachytit všechny výjimky v kódu a znovu vyvolávat všechny chyby, které by měly mít za následek opakování.  Kontrolní body Event Hubs a Azure Cosmos DB nebudou zapsány, dokud zásady opakování pro spuštění nebudou dokončeny, což znamená, že průběh tohoto oddílu bude pozastaven až do dokončení aktuální dávky.

### <a name="retry-policy-options"></a>Možnosti zásad opakování

K dispozici jsou následující možnosti pro definování zásad opakování.

Maximální **počet opakování** je maximální počet opakovaných pokusů o spuštění před případným selháním. Hodnota `-1` znamená, že se má opakovat po neomezenou dobu. Aktuální počet opakování je uložený v paměti instance. Je možné, že došlo k selhání instance mezi opakovanými pokusy.  Pokud dojde k chybě během zásady opakování, bude počet opakování ztracen.  Pokud dojde k selhání instance, triggery, jako je Event Hubs, Azure Cosmos DB a Queue Storage, můžou pokračovat ve zpracování a opakovat dávku na nové instanci, přičemž počet opakování se resetuje na nula.  Další triggery, jako HTTP a časovač, se neobnoví na nové instanci.  To znamená, že maximální počet opakování je nejlepší úsilí a v některých výjimečných případech může být spuštění opakováno více, než je maximum, nebo u aktivačních událostí, jako je HTTP a časovač, bude opakováno méně, než je maximum.

**Strategie opakování** určuje, jak se budou pokusy chovat.  Níže jsou uvedené dvě podporované možnosti opakování:

| Možnost | Popis|
|---|---|
|**`fixedDelay`**| Doba mezi jednotlivými pokusy může uplynout od určitého času.|
| **`exponentialBackoff`**| První pokus čeká na minimální zpoždění. Při dalších opakovaných pokusech se čas přidá exponenciálně k počátečnímu trvání každého opakování, dokud nedosáhnete maximálního zpoždění.  Exponenciální Back-off přidává k prodlevám při opakovaných pokusech v případě vysoké propustnosti několik malých náhodných cyklů.|

#### <a name="app-level-configuration"></a>Konfigurace na úrovni aplikace

Zásady opakování lze definovat pro všechny funkce aplikace [pomocí `host.json` souboru](../articles/azure-functions/functions-host-json.md#retry). 

#### <a name="function-level-configuration"></a>Konfigurace na úrovni funkcí

Pro konkrétní funkci je možné definovat zásady opakování.  Konfigurace specifická pro funkci má přednost před konfigurací na úrovni aplikace.

#### <a name="fixed-delay-retry"></a>Pevné zpoždění opakování

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("EventHubTrigger")]
[FixedDelayRetry(5, "00:00:10")]
public static async Task Run([EventHubTrigger("myHub", Connection = "EventHubConnection")] EventData[] events, ILogger log)
{
// ...
}
```

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Tady jsou zásady opakování v *function.js* souboru:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```
# <a name="javascript"></a>[JavaScript](#tab/javascript)

Tady jsou zásady opakování v *function.js* souboru:


```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```

# <a name="python"></a>[Python](#tab/python)

Tady jsou zásady opakování v *function.js* souboru:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```

# <a name="java"></a>[Java](#tab/java)

Tady jsou zásady opakování v *function.js* souboru:


```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```
---

#### <a name="exponential-backoff-retry"></a>Exponenciální omezení rychlosti opakování

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("EventHubTrigger")]
[ExponentialBackoffRetry(5, "00:00:04", "00:15:00")]
public static async Task Run([EventHubTrigger("myHub", Connection = "EventHubConnection")] EventData[] events, ILogger log)
{
// ...
}
```

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Tady jsou zásady opakování v *function.js* souboru:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Tady jsou zásady opakování v *function.js* souboru:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="python"></a>[Python](#tab/python)

Tady jsou zásady opakování v *function.js* souboru:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="java"></a>[Java](#tab/java)

Tady jsou zásady opakování v *function.js* souboru:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```
---

|function.jsvlastnost  |Vlastnost atributu | Popis |
|---------|---------|---------| 
|strategie|neuvedeno|Povinná hodnota. Používaná strategie opakování. Platné hodnoty jsou `fixedDelay` nebo `exponentialBackoff` .|
|maxRetryCount|neuvedeno|Povinná hodnota. Maximální počet opakovaných pokusů povolených pro spuštění funkce. `-1` způsob, jak to provést po neomezenou dobu.|
|delayInterval|neuvedeno|Prodleva, která bude použita mezi opakovanými pokusy při použití `fixedDelay` strategie.|
|minimumInterval|neuvedeno|Minimální prodleva při opakovaném pokusu při použití `exponentialBackoff` strategie.|
|maximumInterval|neuvedeno|Maximální prodleva při opakovaném pokusu při použití `exponentialBackoff` strategie.| 

## <a name="trigger-specific-retry-support"></a>Podpora opakování specifická pro aktivační události

Některé triggery zajišťují opakované pokusy ve zdroji triggeru.  Tyto opakované pokusy lze použít společně s nebo jako náhrada pro zásady opakování hostitele aplikace Function App.  Pokud se požaduje pevný počet opakovaných pokusů, použijte zásady opakování specifické pro danou aktivační událost pomocí zásad obecného opakování hostitele.  Následující aktivační události podporují opakování ve zdroji triggeru:

* [Azure Blob Storage](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Azure Queue Storage](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Azure Service Bus (fronta/téma)](../articles/azure-functions/functions-bindings-service-bus.md)

Ve výchozím nastavení se tyto triggery spustí znovu a požádá se o pět časů. Po pátém opakování se aktivační událost Azure Queue Storage i Azure Service Bus zapisují do [nepoškozené fronty](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#poison-messages).
