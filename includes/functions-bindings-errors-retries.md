---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/01/2020
ms.author: glenga
ms.openlocfilehash: 2ccff72be66a88b9bf0a5e9eb9c29ade8397804b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96356189"
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
- [Implementujte zásady opakování](#retry-policies-preview) (tam, kde je to vhodné).

### <a name="use-structured-error-handling"></a>Použití strukturovaného zpracování chyb

Zaznamenávání a protokolování chyb je klíčové pro monitorování stavu vaší aplikace. Nejvyšší úroveň každého kódu funkce by měla obsahovat blok try/catch. V bloku catch můžete zaznamenávat a protokolovat chyby.

## <a name="retry-policies-preview"></a>Zásady opakování (Preview)

Zásady opakování se dají definovat u libovolné funkce pro libovolný typ triggeru ve vaší aplikaci Function App.  Zásada opakování znovu spustí funkci až do úspěšného provedení nebo do doby, než dojde k maximálnímu počtu opakovaných pokusů.  Zásady opakování lze definovat pro všechny funkce aplikace nebo pro jednotlivé funkce.  Ve výchozím nastavení aplikace Function App neopakuje zprávy (kromě [konkrétních triggerů, které mají ve zdroji triggeru zásady opakování](#using-retry-support-on-top-of-trigger-resilience)).  Zásada opakování je vyhodnocena vždy, když je výsledkem spuštění nezachycená výjimka.  V rámci osvědčeného postupu byste měli zachytit všechny výjimky v kódu a znovu vyvolat všechny chyby, které by měly mít za následek opakování.  Kontrolní body Event Hubs a Azure Cosmos DB nebudou zapsány, dokud zásady opakování pro spuštění nebudou dokončeny, což znamená, že průběh tohoto oddílu bude pozastaven až do dokončení aktuální dávky.

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

Opakování vyžadují balíček NuGet [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) >= 3.0.23

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

# <a name="powershell"></a>[PowerShell](#tab/powershell)

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

Opakování vyžadují balíček NuGet [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) >= 3.0.23

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

# <a name="powershell"></a>[PowerShell](#tab/powershell)

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

|function.jsvlastnost  |Vlastnost atributu | Description |
|---------|---------|---------| 
|strategie|Není k dispozici|Povinná hodnota. Používaná strategie opakování. Platné hodnoty jsou `fixedDelay` nebo `exponentialBackoff` .|
|maxRetryCount|Není k dispozici|Povinná hodnota. Maximální počet opakovaných pokusů povolených pro spuštění funkce. `-1` způsob, jak to provést po neomezenou dobu.|
|delayInterval|Není k dispozici|Prodleva, která bude použita mezi opakovanými pokusy při použití `fixedDelay` strategie.|
|minimumInterval|Není k dispozici|Minimální prodleva při opakovaném pokusu při použití `exponentialBackoff` strategie.|
|maximumInterval|Není k dispozici|Maximální prodleva při opakovaném pokusu při použití `exponentialBackoff` strategie.| 

### <a name="retry-limitations-during-preview"></a>Omezení počtu opakování během období Preview

- Pro projekty .NET může být nutné ručně načíst ve verzi [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) >= 3.0.23.
- V plánu spotřeby může být aplikace při opakovaném pokusu o ukončení poslední zprávy ve frontě škálovaná na nulu.
- V plánu spotřeby může být aplikace při provádění opakování škálovaná.  Pro dosažení nejlepších výsledků vyberte interval opakování <= 00:01:00 a <= 5 opakování.

## <a name="using-retry-support-on-top-of-trigger-resilience"></a>Použití podpory opakování na odolnost triggeru

Zásada opakování aplikace Function App je nezávislá na jakýchkoli opakovaných pokusech nebo odolnosti, které aktivační událost poskytuje.  Zásady opakování funkce budou převrstvit na horním panelu opakovaného pokusu o spuštění.  Pokud například používáte Azure Service Bus, mají ve výchozím nastavení fronty počet doručování zpráv 10.  Výchozí počet doručení znamená po 10 pokusech o doručení zprávy fronty Service Bus nedoručené zprávě.  Můžete definovat zásady opakování pro funkci, která má aktivační událost Service Bus, ale opakované pokusy budou překryty na Service Bus pokusů o doručení.  

Například pokud jste použili výchozí počet doručování Service Bus 10 a definovali jste zásady opakování funkce 5.  Zpráva by nejdřív vyřadí z fronty a zvyšuje účet pro doručení Service Bus na 1.  Pokud při každém spuštění nedošlo k chybě, po pěti pokusech o aktivaci stejné zprávy bude tato zpráva označena jako opuštěno.  Service Bus by zprávu hned znovu zařadila do fronty, aktivovala se funkce a zvýší se počet doručení na 2.  Nakonec, až 50 případné pokusy (10 doručení služby Service Bus * pět opakování funkce za doručení), zpráva bude opuštěna a spustí nedoručené oznámení na službě Service Bus.

> [!WARNING]
> U triggeru, jako je Service Bus front na 1, se nedoporučuje nastavovat počet doručení, což znamená, že se zpráva bude nedoručená ihned po opakovaném cyklu jedné funkce.  Důvodem je to, že triggery zajišťují odolnost proti opakovaným pokusům, zatímco zásady opakování funkce jsou nejlepší a můžou být menší, než je požadovaný celkový počet opakovaných pokusů.

### <a name="triggers-with-additional-resiliency-or-retries"></a>Aktivační události s dodatečnou odolností nebo opakovanými pokusy

Následující aktivační události podporují opakování ve zdroji triggeru:

* [Azure Blob Storage](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Úložiště front Azure](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Azure Service Bus (fronta/téma)](../articles/azure-functions/functions-bindings-service-bus.md)

Ve výchozím nastavení většina aktivačních událostí opakuje žádost až pětkrát. Po pátém opakování bude úložiště front Azure zapisovat zprávu do [fronty nezpracovatelných](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#poison-messages)zpráv.  Výchozí Service Bus fronty a zásady pro témata zapisují zprávu do [fronty nedoručených](../articles/service-bus-messaging/service-bus-dead-letter-queues.md) zpráv po 10 pokusech.
