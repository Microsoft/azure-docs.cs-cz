---
title: Aktivační událost zahřívání Azure Functions
description: Vysvětlení použití triggeru zahřívání v Azure Functions.
documentationcenter: na
author: alexkarcher-msft
manager: gwallace
keywords: funkce Azure Functions, Functions, zpracování událostí, zahřívání, studená Start, Premium, dynamické výpočty, architektura bez serveru
ms.service: azure-functions
ms.topic: reference
ms.date: 11/08/2019
ms.author: alkarche
ms.openlocfilehash: 108294e3f125da9fb009eb0a85585dab026c8d01
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2020
ms.locfileid: "75933318"
---
# <a name="azure-functions-warm-up-trigger"></a>Aktivační událost Azure Functions zahřívání

Tento článek vysvětluje, jak pracovat s triggerem zahřívání v Azure Functions. Aktivační událost zahřívání je podporovaná jenom pro aplikace Function App běžící v [plánu Premium](functions-premium-plan.md). Trigger zahřívání je vyvolán, když je přidána instance pro škálování běžící aplikace Function App. Pomocí triggeru zahřívání můžete předem načíst vlastní závislosti během [procesu před zahříváním](./functions-premium-plan.md#pre-warmed-instances) , aby byly vaše funkce připravené na okamžité zpracování požadavků. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x-and-higher"></a>Balíčky – funkce 2. x a vyšší

Vyžaduje se balíček NuGet [Microsoft. Azure. WebJobs. Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) , verze **3.0.5 nebo vyšší** . Zdrojový kód balíčku je v úložišti GitHub [Azure-WebJobs-SDK-Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/) . 

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>Trigger

Aktivační událost zahřívání umožňuje definovat funkci, která se spustí na nové instanci při přidání do spuštěné aplikace. Funkci zahřívání můžete použít k otevření připojení, načtení závislostí nebo spuštění libovolné jiné vlastní logiky ještě předtím, než aplikace začne přijímat přenosy. 

Aktivační událost zahřívání slouží k vytváření sdílených závislostí, které budou používány jinými funkcemi ve vaší aplikaci. [Tady najdete příklady sdílených závislostí](./manage-connections.md#client-code-examples).

Všimněte si, že Trigger zahřívání je volán pouze během operací škálování na více instancí, nikoli během restartování nebo jiných neškálovatelných spuštění. Je nutné zajistit, aby vaše logika mohla načíst všechny nezbytné závislosti bez použití triggeru zahřívání. K tomu je dobrým vzorem opožděné načítání.

## <a name="trigger---example"></a>Aktivační události – příklad

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Následující příklad ukazuje [ C# funkci](functions-dotnet-class-library.md) , která se spustí na každé nové instanci při přidání do vaší aplikace. Atribut návratové hodnoty není povinný.


* Vaše funkce musí mít název ```warmup``` (nerozlišuje velká a malá písmena) a v každé aplikaci může být jenom jedna zahřívání funkce.
* Pokud chcete používat zahřívání jako funkci knihovny tříd .NET, ujistěte se prosím, že máte odkaz na balíček **Microsoft. Azure. WebJobs. extensions > = 3.0.5**
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
# <a name="c-scripttabcsharp-script"></a>[C#Pravidel](#tab/csharp-script)


Následující příklad ukazuje Trigger zahřívání v souboru *Function. JSON* a [ C# funkci skriptu](functions-reference-csharp.md) , která se spustí na každé nové instanci při přidání do aplikace.

Vaše funkce musí mít název ```warmup``` (nerozlišuje velká a malá písmena) a pro každou aplikaci může existovat jenom jedna zahřívání funkce.

Tady je *function.json* souboru:

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

[Konfigurace](#trigger---configuration) bodu vysvětluje tyto vlastnosti.

Zde je C# kód skriptu, který se váže k `HttpRequest`:

```cs
public static void Run(ILogger log)
{
    log.LogInformation("Function App instance is warm 🌞🌞🌞");  
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Následující příklad ukazuje Trigger zahřívání v souboru *Function. JSON* a [funkci JavaScriptu](functions-reference-node.md) , která se spustí na každé nové instanci při přidání do aplikace.

Vaše funkce musí mít název ```warmup``` (nerozlišuje velká a malá písmena) a v každé aplikaci může být jenom jedna zahřívání funkce.

Tady je *function.json* souboru:

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

[Konfigurace](#trigger---configuration) bodu vysvětluje tyto vlastnosti.

Tady je kód jazyka JavaScript:

```javascript
module.exports = async function (context, warmupContext) {
    context.log('Function App instance is warm 🌞🌞🌞');
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Následující příklad ukazuje Trigger zahřívání v souboru *Function. JSON* a [funkci Pythonu](functions-reference-python.md) , která se spustí na každé nové instanci při přidání do vaší aplikace.

Vaše funkce musí mít název ```warmup``` (nerozlišuje velká a malá písmena) a v každé aplikaci může být jenom jedna zahřívání funkce.

Tady je *function.json* souboru:

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

[Konfigurace](#trigger---configuration) bodu vysvětluje tyto vlastnosti.

Tady je kód Pythonu:

```python
import logging
import azure.functions as func


def main(warmupContext: func.Context) -> None:
    logging.info('Function App instance is warm 🌞🌞🌞')
```

# <a name="javatabjava"></a>[Java](#tab/java)

Následující příklad ukazuje Trigger zahřívání v souboru *Function. JSON* a [funkce jazyka Java](functions-reference-java.md) , které se spustí na každé nové instanci při přidání do vaší aplikace.

Vaše funkce musí mít název ```warmup``` (nerozlišuje velká a malá písmena) a v každé aplikaci může být jenom jedna zahřívání funkce.

Tady je *function.json* souboru:

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

Tady je kód Java:

```java
@FunctionName("Warmup")
public void run( ExecutionContext context) {
       context.getLogger().info("Function App instance is warm 🌞🌞🌞");
}
```

---

## <a name="trigger---attributes"></a>Aktivační události – atributy

V [ C# knihovnách tříd](functions-dotnet-class-library.md)je k dispozici atribut `WarmupTrigger` pro konfiguraci funkce.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Tento příklad ukazuje, jak použít atribut [zahřívání](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions/Extensions/Warmup/Trigger/WarmupTriggerAttribute.cs) .

Všimněte si, že funkce musí být volána ```Warmup``` a může být pouze jedna funkce zahřívání na aplikaci.

```csharp
 [FunctionName("Warmup")]
        public static void Run(
            [WarmupTrigger()] WarmupContext context, ILogger log)
        {
            ...
        }
```

Úplný příklad najdete v [příkladu triggeru](#trigger---example).

# <a name="c-scripttabcsharp-script"></a>[C#Pravidel](#tab/csharp-script)

C# Skript nepodporuje atributy.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Atributy nejsou podporovány jazykem JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Python nepodporuje atributy.

# <a name="javatabjava"></a>[Java](#tab/java)

Aktivační událost zahřívání není v jazyce Java podporována jako atribut.

---

## <a name="trigger---configuration"></a>Aktivační události – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `WarmupTrigger` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
| **type** | –| Požadováno – musí být nastavené na `warmupTrigger`. |
| **direction** | –| Požadováno – musí být nastavené na `in`. |
| **name** | –| Požadováno – název proměnné použitý v kódu funkce.|

## <a name="trigger---usage"></a>Aktivační události – využití

Při vyvolání funkce aktivované zahřívání nejsou k dispozici žádné další informace.

## <a name="trigger---limits"></a>Aktivační události – omezení

* Aktivační událost zahřívání je dostupná jenom pro aplikace, které běží na [plánu Premium](./functions-premium-plan.md).
* Aktivační událost zahřívání se volá jenom během operací s horizontálním škálováním, ne během restartování nebo jiných neškálovatelných spuštění. Je nutné zajistit, aby vaše logika mohla načíst všechny nezbytné závislosti bez použití triggeru zahřívání. K tomu je dobrým vzorem opožděné načítání.
* Aktivační událost zahřívání nejde vyvolat, když už je instance spuštěná.
* Na aplikaci Function App může být jenom jedna funkce triggeru zahřívání.

## <a name="next-steps"></a>Další kroky

[Další informace o aktivačních událostech Azure functions a vazby](functions-triggers-bindings.md)
