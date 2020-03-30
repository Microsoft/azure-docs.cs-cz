---
title: Aktivační událost zahřívání funkcí Azure
description: Zjistěte, jak používat aktivační událost zahřívání v Azure Functions.
documentationcenter: na
author: alexkarcher-msft
manager: gwallace
keywords: funkce azure, funkce, zpracování událostí, zahřívání, studený start, prémiová, dynamická výpočetní architektura, architektura bez serveru
ms.service: azure-functions
ms.topic: reference
ms.date: 11/08/2019
ms.author: alkarche
ms.openlocfilehash: c3ed780bc50b690b2f5c3285024695ec6426b9b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77167312"
---
# <a name="azure-functions-warm-up-trigger"></a>Aktivační událost zahřívání funkcí Azure

Tento článek vysvětluje, jak pracovat s aktivační událost zahřívání v Azure Functions. Aktivační událost zahřívání je podporována pouze pro funkční aplikace spuštěné v [plánu Premium](functions-premium-plan.md). Aktivační událost zahřívání je vyvolána, když je instance přidána do škálování spuštěné aplikace funkce. Aktivační událost zahřívání můžete použít k předběžnému načtení vlastních závislostí během [procesu předběžného zahřívání,](./functions-premium-plan.md#pre-warmed-instances) aby vaše funkce byly připraveny k okamžitému spuštění zpracování požadavků. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x-and-higher"></a>Balíčky - Funkce 2.x a vyšší

Balíček [Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet verze **3.0.5 nebo vyšší** je vyžadován. Zdrojový kód pro balíček je v úložišti [GitHub azure-webjobs-sdk-extensions.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/) 

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>Trigger

Aktivační událost zahřívání umožňuje definovat funkci, která bude spuštěna na nové instanci, když je přidána do spuštěné aplikace. Funkci zahřívání můžete použít k otevření připojení, načtení závislostí nebo spuštění jakékoli jiné vlastní logiky, než vaše aplikace začne přijímat provoz. 

Aktivační událost zahřívání je určena k vytvoření sdílených závislostí, které budou používat ostatní funkce ve vaší aplikaci. [Příklady sdílených závislostí naleznete zde](./manage-connections.md#client-code-examples).

Všimněte si, že aktivační událost zahřívání je volána pouze během operací škálování, nikoli během restartování nebo jiných spuštění bez škálování. Je nutné zajistit, aby vaše logika může načíst všechny potřebné závislosti bez použití aktivační události zahřívání. Opožděné načítání je dobrý vzor k dosažení tohoto cíle.

## <a name="trigger---example"></a>Aktivační událost – příklad

# <a name="c"></a>[C #](#tab/csharp)

Následující příklad ukazuje [funkci C#,](functions-dotnet-class-library.md) která se spustí na každé nové instanci při přidání do vaší aplikace. Atribut vrácené hodnoty není vyžadován.


* Vaše funkce musí ```warmup``` být pojmenována (malá a velká písmena) a může existovat pouze jedna funkce zahřívání v aplikaci.
* Chcete-li použít zahřívání jako funkci knihovny tříd .NET, ujistěte se, že máte odkaz na balíček **Microsoft.Azure.WebJobs.Extensions >= 3.0.5**
    * ```<PackageReference Include="Microsoft.Azure.WebJobs.Extensions" Version="3.0.5" />```


Zástupné komentáře ukazují, kde v aplikaci deklarovat a inicializovat sdílené závislosti. 
[Další informace o sdílených závislostech naleznete zde](./manage-connections.md#client-code-examples).

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
 
namespace WarmupSample
{

    //Declare shared dependencies here

    public static class Warmup
    {
        [FunctionName("Warmup")]
        public static void Run([WarmupTrigger()] WarmupContext context,
            ILogger log)
        {
            //Initialize shared dependencies here
            
            log.LogInformation("Function App instance is warm 🌞🌞🌞");
        }
    }
}
```
# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)


Následující příklad ukazuje aktivační událost zahřívání v souboru *function.json* a [funkci skriptu Jazyka C#,](functions-reference-csharp.md) která se spustí v každé nové instanci při přidání do aplikace.

Vaše funkce musí ```warmup``` být pojmenována (malá a velká písmena) a může existovat pouze jedna funkce zahřívání v aplikaci.

Zde je *soubor function.json:*

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

[Konfigurační](#trigger---configuration) část vysvětluje tyto vlastnosti.

Zde je C# skript kód, `HttpRequest`který váže na :

```cs
public static void Run(ILogger log)
{
    log.LogInformation("Function App instance is warm 🌞🌞🌞");  
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

Následující příklad ukazuje aktivační událost zahřívání v souboru *function.json* a [funkci JavaScriptu,](functions-reference-node.md) která se spustí v každé nové instanci při přidání do vaší aplikace.

Vaše funkce musí ```warmup``` být pojmenována (malá a velká písmena) a může existovat pouze jedna funkce zahřívání v aplikaci.

Zde je *soubor function.json:*

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

[Konfigurační](#trigger---configuration) část vysvětluje tyto vlastnosti.

Zde je kód JavaScript:

```javascript
module.exports = async function (context, warmupContext) {
    context.log('Function App instance is warm 🌞🌞🌞');
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Následující příklad ukazuje aktivační událost zahřívání v souboru *function.json* a [funkci Pythonu,](functions-reference-python.md) která se spustí v každé nové instanci při přidání do vaší aplikace.

Vaše funkce musí ```warmup``` být pojmenována (malá a velká písmena) a může existovat pouze jedna funkce zahřívání v aplikaci.

Zde je *soubor function.json:*

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

[Konfigurační](#trigger---configuration) část vysvětluje tyto vlastnosti.

Zde je kód Pythonu:

```python
import logging
import azure.functions as func


def main(warmupContext: func.Context) -> None:
    logging.info('Function App instance is warm 🌞🌞🌞')
```

# <a name="java"></a>[Java](#tab/java)

Následující příklad ukazuje aktivační událost zahřívání, která se spustí, když se do vaší aplikace přidá každá nová instance.

Vaše funkce musí `warmup` být pojmenována (malá a velká písmena) a může existovat pouze jedna funkce zahřívání v aplikaci.

```java
@FunctionName("Warmup")
public void run( ExecutionContext context) {
       context.getLogger().info("Function App instance is warm 🌞🌞🌞");
}
```

---

## <a name="trigger---attributes"></a>Aktivační událost - atributy

V [knihovnách tříd jazyka C#](functions-dotnet-class-library.md)je `WarmupTrigger` atribut k dispozici pro konfiguraci funkce.

# <a name="c"></a>[C #](#tab/csharp)

Tento příklad ukazuje, jak používat [warmup](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions/Extensions/Warmup/Trigger/WarmupTriggerAttribute.cs) atribut.

Všimněte si, že ```Warmup``` vaše funkce musí být volána a může existovat pouze jedna funkce zahřívání na aplikaci.

```csharp
 [FunctionName("Warmup")]
        public static void Run(
            [WarmupTrigger()] WarmupContext context, ILogger log)
        {
            ...
        }
```

Úplný příklad naleznete v [příkladu aktivační události](#trigger---example).

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Atributy nejsou podporovány skriptem jazyka C#.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Atributy nejsou podporovány javascriptem.

# <a name="python"></a>[Python](#tab/python)

Atributy nejsou podporovány Pythonem.

# <a name="java"></a>[Java](#tab/java)

Aktivační událost zahřívání není v jazyce Java jako atribut podporována.

---

## <a name="trigger---configuration"></a>Trigger - konfigurace

Následující tabulka vysvětluje vlastnosti konfigurace vazby, které jste nastavili `WarmupTrigger` v souboru *function.json* a atributu.

|vlastnost function.json | Vlastnost atributu |Popis|
|---------|---------|----------------------|
| **Typ** | neuvedeno| Povinné - musí `warmupTrigger`být nastavena na . |
| **direction** | neuvedeno| Povinné - musí `in`být nastavena na . |
| **Jméno** | neuvedeno| Povinné - název proměnné použitý v kódu funkce.|

## <a name="trigger---usage"></a>Aktivační událost - využití

Žádné další informace jsou k dispozici zahřívací aktivovanou funkci při vyvolání.

## <a name="trigger---limits"></a>Aktivační událost - limity

* Aktivační událost zahřívání je k dispozici pouze pro aplikace spuštěné v [plánu Premium](./functions-premium-plan.md).
* Aktivační událost zahřívání je volána pouze během operací škálování, nikoli během restartování nebo jiných neškálovacích spuštění. Je nutné zajistit, aby vaše logika může načíst všechny potřebné závislosti bez použití aktivační události zahřívání. Opožděné načítání je dobrý vzor k dosažení tohoto cíle.
* Aktivační událost zahřívání nelze vyvolat, jakmile je instance již spuštěna.
* Na aplikaci funkce může být pouze jedna funkce zahřívacích aktivačních událostí.

## <a name="next-steps"></a>Další kroky

[Další informace o aktivačních událostech a vazbách funkcí Azure](functions-triggers-bindings.md)
