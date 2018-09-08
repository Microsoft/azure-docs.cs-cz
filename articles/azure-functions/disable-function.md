---
title: Zakázání funkcí ve službě Azure Functions
description: Zjistěte, jak zakázat a povolit funkce v Azure Functions 1.x a 2.x.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 07/24/2018
ms.author: glenga
ms.openlocfilehash: ab9cf429a0af69db116fe910ab90b83d404afbb7
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44093630"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Zakázání funkcí ve službě Azure Functions

Tento článek vysvětluje, jak zakázat funkce ve službě Azure Functions. K *zakázat* funkce znamená, že aby modul runtime ignorovat Automatická aktivační událost, která je definována pro funkci. Způsob, můžete to udělat závisí na verzi modulu runtime a programovací jazyk:

* Functions 1.x
  * Skriptovací jazyky
  * Knihovny tříd jazyka C#
* Functions 2.x
  * Jedním ze způsobů pro všechny jazyky
  * Volitelným způsobem pro knihovny tříd jazyka C#

## <a name="functions-1x---scripting-languages"></a>Funkce 1.x – skriptovacích jazyků

Pro skriptovací jazyky, jako je skript jazyka C# a JavaScript, je použít `disabled` vlastnost *function.json* souboru říct modul runtime není pro aktivaci funkce. Tuto vlastnost lze nastavit `true` nebo na název nastavení aplikace:

```json
{
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ],
    "disabled": true
}
```
nebo 

```json
    "bindings": [
        ...
    ],
    "disabled": "IS_DISABLED"
```

V druhém příkladu je zakázána funkce po nastavení aplikace, který má název IS_DISABLED a je nastavená na `true` nebo 1.

Můžete upravit soubor v portálu Azure portal nebo použitím **stav funkce** zapnout funkce **spravovat** kartu. Na portálu přepínač funguje tak, že změníte *function.json* souboru.

![Stav přepínače – funkce](media/disable-function/function-state-switch.png)

## <a name="functions-1x---c-class-libraries"></a>Funkce 1.x – knihoven tříd C#

V knihovně tříd 1.x funkce, můžete použít `Disable` atribut zabránit aktivaci funkce. Můžete použít atribut bez konstruktoru parametru, jak je znázorněno v následujícím příkladu:

```csharp
public static class QueueFunctions
{
    [Disable]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

Atribut bez parametru konstruktor vyžaduje, aby znovu zkompilovat a znovu nasaďte projekt tak, aby změna této funkce zakázaná. Pružnější způsob, jak použít atribut má zahrnout parametr konstruktoru, který odkazuje na nastavení logické aplikaci, jak je znázorněno v následujícím příkladu:

```csharp
public static class QueueFunctions
{
    [Disable("MY_TIMER_DISABLED")]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

Tato metoda umožňuje povolit nebo zakázat funkci změnou nastavení aplikace bez opětovné kompilace nebo opětovného nasazení. Změna nastavení aplikace způsobí, že aplikace function app k restartování, takže okamžitě rozpozná změnu stavu zakázáno.

> [!IMPORTANT]
> `Disabled` Atribut je jediný způsob, jak zakázat funkce knihovny tříd. Vygenerovaný *function.json* soubor pro funkce knihovny třídy neměl být upravován přímo. Při úpravě tohoto souboru, cokoli, co můžete udělat, abyste `disabled` vlastnost nebude mít žádný efekt.
>
> Totéž platí pro **funkce stavu** zapnout **spravovat** kartu, protože funguje tak, že změníte *function.json* souboru.
>
> Všimněte si také, že na portálu může znamenat, že funkce je zakázáno, pokud tomu tak není.



## <a name="functions-2x---all-languages"></a>Funkce 2.x – všechny jazyky

Ve funkcích 2.x je funkce vypnout pomocí nastavení aplikace. Například zakázat funkci s názvem `QueueTrigger`, můžete vytvořit nastavení aplikace s názvem `AzureWebJobs.QueueTrigger.Disabled`a nastavte ho na `true`. Pokud chcete povolit funkci, nastavte nastavení aplikace, které `false`. Můžete také použít **stav funkce** zapnout funkce **spravovat** kartu. Přepínač funguje tak, že vytváření a odstraňování `AzureWebJobs.<functionname>.Disabled` nastavení aplikace.

![Stav přepínače – funkce](media/disable-function/function-state-switch.png)

## <a name="functions-2x---c-class-libraries"></a>Funkce 2.x - knihoven tříd C#

V knihovně tříd 2.x funkcí doporučujeme používat metodu, která funguje pro všechny jazyky. Pokud dáváte přednost, můžete, ale [použít atribut zakázat funkce v 1.x](#functions-1x---c-class-libraries).

## <a name="next-steps"></a>Další postup

Tento článek je o vypnutí automatických aktivačních procedur. Další informace o aktivačních událostech najdete v tématu [aktivačními událostmi a vazbami](functions-triggers-bindings.md).
