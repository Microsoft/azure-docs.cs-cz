---
title: Aktivační událost zahřívání Azure Functions
description: Vysvětlení použití triggeru zahřívání v Azure Functions.
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: funkce Azure Functions, Functions, zpracování událostí, zahřívání, studená Start, Premium, dynamické výpočty, architektura bez serveru
ms.service: azure-functions
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 11/08/2019
ms.author: cshoe
ms.openlocfilehash: ea418576ab8fe06964a61e48f16393e1a0566ce8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182243"
---
# <a name="azure-functions-warm-up-trigger"></a>Aktivační událost Azure Functions zahřívání

Tento článek vysvětluje, jak pracovat s triggerem zahřívání v Azure Functions. Trigger zahřívání je vyvolán, když je přidána instance pro škálování běžící aplikace Function App. Pomocí triggeru zahřívání můžete předem načíst vlastní závislosti během [procesu před zahříváním](./functions-premium-plan.md#pre-warmed-instances) , aby byly vaše funkce připravené na okamžité zpracování požadavků. 

> [!NOTE]
> Aktivační událost zahřívání se nepodporuje pro aplikace Function App spuštěné v plánu spotřeby.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x-and-higher"></a>Balíčky – funkce 2. x a vyšší

Vyžaduje se balíček NuGet [Microsoft. Azure. WebJobs. Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) , verze **3.0.5 nebo vyšší** . Zdrojový kód balíčku je v úložišti GitHub [Azure-WebJobs-SDK-Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/main/src/WebJobs.Extensions/Extensions/Warmup) . 

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>Trigger

Aktivační událost zahřívání umožňuje definovat funkci, která se spustí na nové instanci při přidání do spuštěné aplikace. Funkci zahřívání můžete použít k otevření připojení, načtení závislostí nebo spuštění libovolné jiné vlastní logiky ještě předtím, než aplikace začne přijímat přenosy. 

Aktivační událost zahřívání slouží k vytváření sdílených závislostí, které budou používány jinými funkcemi ve vaší aplikaci. [Tady najdete příklady sdílených závislostí](./manage-connections.md#client-code-examples).

Všimněte si, že Trigger zahřívání je volán pouze během operací škálování na více instancí, nikoli během restartování nebo jiných neškálovatelných spuštění. Je nutné zajistit, aby vaše logika mohla načíst všechny nezbytné závislosti bez použití triggeru zahřívání. K tomu je dobrým vzorem opožděné načítání.

## <a name="trigger---example"></a>Aktivační procedura – příklad

# <a name="c"></a>[C#](#tab/csharp)

Následující příklad ukazuje [funkci jazyka C#](functions-dotnet-class-library.md) , která se spustí na každé nové instanci při přidání do aplikace. Atribut návratové hodnoty není povinný.


* Vaše funkce musí být pojmenovaná ```warmup``` (nerozlišuje velká a malá písmena) a pro každou aplikaci může existovat jenom jedna zahřívání funkce.
* Pokud chcete používat zahřívání jako funkci knihovny tříd .NET, ujistěte se prosím, že máte odkaz na balíček **Microsoft. Azure. WebJobs. extensions >= 3.0.5**
    * ```<PackageReference Include="Microsoft.Azure.WebJobs.Extensions" Version="3.0.5" />```


Komentáře k zástupným symbolům ukazují, kam v aplikaci deklarovat a inicializovat sdílené závislosti. 
[Další informace o sdílených závislostech najdete tady](./manage-connections.md#client-code-examples).

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


Následující příklad ukazuje Trigger zahřívání v souboru *vfunction.js* a [funkce skriptu jazyka C#](functions-reference-csharp.md) , která se spustí na každé nové instanci při přidání do vaší aplikace.

Vaše funkce musí být pojmenována ```warmup``` (nerozlišuje velká a malá písmena) a pro každou aplikaci může existovat pouze jedna zahřívání funkce.

Tady je *function.js* souboru:

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

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#trigger---configuration) .

```cs
public static void Run(WarmupContext warmupContext, ILogger log)
{
    log.LogInformation("Function App instance is warm 🌞🌞🌞");  
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Následující příklad ukazuje Trigger zahřívání v souboru *function.js* a [funkce JavaScriptu](functions-reference-node.md)  , která se spustí na každé nové instanci při přidání do vaší aplikace.

Vaše funkce musí být pojmenovaná ```warmup``` (nerozlišuje velká a malá písmena) a pro každou aplikaci může existovat jenom jedna zahřívání funkce.

Tady je *function.js* souboru:

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

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#trigger---configuration) .

Tady je kód JavaScriptu:

```javascript
module.exports = async function (context, warmupContext) {
    context.log('Function App instance is warm 🌞🌞🌞');
};
```

# <a name="python"></a>[Python](#tab/python)

Následující příklad ukazuje Trigger zahřívání v souboru *function.js* a [funkce jazyka Python](functions-reference-python.md) , která se spustí na každé nové instanci při přidání do vaší aplikace.

Vaše funkce musí být pojmenovaná ```warmup``` (nerozlišuje velká a malá písmena) a pro každou aplikaci může existovat jenom jedna zahřívání funkce.

Tady je *function.js* souboru:

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

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#trigger---configuration) .

Tady je kód Pythonu:

```python
import logging
import azure.functions as func


def main(warmupContext: func.Context) -> None:
    logging.info('Function App instance is warm 🌞🌞🌞')
```

# <a name="java"></a>[Java](#tab/java)

Následující příklad ukazuje Trigger zahřívání, který se spustí při přidání každé nové instance do aplikace.

Vaše funkce musí být pojmenovaná `warmup` (nerozlišuje velká a malá písmena) a pro každou aplikaci může existovat jenom jedna zahřívání funkce.

```java
@FunctionName("Warmup")
public void run( ExecutionContext context) {
       context.getLogger().info("Function App instance is warm 🌞🌞🌞");
}
```

---

## <a name="trigger---attributes"></a>Aktivační atributy

V [knihovnách tříd jazyka C#](functions-dotnet-class-library.md) `WarmupTrigger` je atribut k dispozici pro konfiguraci funkce.

# <a name="c"></a>[C#](#tab/csharp)

Tento příklad ukazuje, jak použít atribut [zahřívání](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions/Extensions/Warmup/Trigger/WarmupTriggerAttribute.cs) .

Všimněte si, že funkce musí být volána ```Warmup``` a může být pouze jedna zahřívání funkce na aplikaci.

```csharp
 [FunctionName("Warmup")]
        public static void Run(
            [WarmupTrigger()] WarmupContext context, ILogger log)
        {
            ...
        }
```

Úplný příklad najdete v [příkladu triggeru](#trigger---example).

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Skripty jazyka C# nepodporují atributy.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atributy nejsou podporovány jazykem JavaScript.

# <a name="python"></a>[Python](#tab/python)

Python nepodporuje atributy.

# <a name="java"></a>[Java](#tab/java)

Aktivační událost zahřívání není v jazyce Java podporována jako atribut.

---

## <a name="trigger---configuration"></a>Aktivační událost – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.jspro* soubor a `WarmupTrigger` atribut.

|function.jsvlastnost | Vlastnost atributu |Description|
|---------|---------|----------------------|
| **textový** | Není k dispozici| Požadováno – musí být nastavené na `warmupTrigger` . |
| **směr** | Není k dispozici| Požadováno – musí být nastavené na `in` . |
| **Jméno** | Není k dispozici| Požadováno – název proměnné použitý v kódu funkce.|

## <a name="trigger---usage"></a>Aktivační událost – využití

Při vyvolání funkce aktivované zahřívání nejsou k dispozici žádné další informace.

## <a name="trigger---limits"></a>Aktivační události – omezení

* Aktivační událost zahřívání je dostupná jenom pro aplikace, které běží na [plánu Premium](./functions-premium-plan.md).
* Aktivační událost zahřívání je volána pouze během operací škálování na více instancí, nikoli během restartování nebo jiných neškálovatelných spuštění. Je nutné zajistit, aby vaše logika mohla načíst všechny nezbytné závislosti bez použití triggeru zahřívání. K tomu je dobrým vzorem opožděné načítání.
* Aktivační událost zahřívání nejde vyvolat, když už je instance spuštěná.
* Na aplikaci Function App může být jenom jedna funkce triggeru zahřívání.

## <a name="next-steps"></a>Další kroky

[Další informace o aktivačních událostech a vazbách Azure Functions](functions-triggers-bindings.md)
