---
title: Jak zakázat funkce ve funkcích Azure
description: Zjistěte, jak zakázat a povolit funkce v Azure Functions.
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: fb8edf635856078655b8640ba0e1723fdd5e8a5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77116145"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Jak zakázat funkce ve funkcích Azure

Tento článek vysvětluje, jak zakázat funkci v Azure Functions. *Zakázání* funkce znamená, že doba běhu ignoruje automatickou aktivační událost, která je definována pro tuto funkci. To umožňuje zabránit spuštění určité funkce bez zastavení celé aplikace funkce.

Doporučený způsob, jak zakázat funkci, je použít `AzureWebJobs.<FUNCTION_NAME>.Disabled`nastavení aplikace ve formátu . Toto nastavení aplikace můžete vytvořit a upravit několika způsoby, například pomocí [příkazového příkazového příkazu Azure](/cli/azure/) a na kartě **Správa** vaší funkce na [webu Azure Portal](https://portal.azure.com). 

> [!NOTE]  
> Pokud zakážete funkci spuštěnou protokolem HTTP pomocí metod popsaných v tomto článku, koncový bod může být stále přístupný při spuštění v místním počítači.  

## <a name="use-the-azure-cli"></a>Použití Azure CLI

V nastavení příkazového příkazu [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) Azure pomocí příkazu můžete vytvořit a upravit nastavení aplikace. Následující příkaz zakáže `QueueTrigger` funkci s názvem `AzureWebJobs.QueueTrigger.Disabled` vytvořením `true`nastavení aplikace s názvem nastavit na . 

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

Chcete-li funkci znovu povolit, spusťte `false`znovu stejný příkaz s hodnotou .

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

## <a name="use-the-portal"></a>Použití portálu

Můžete také použít přepínač **Stav funkce** na kartě **Správa** funkce. Přepínač funguje tak, že vytvoří `AzureWebJobs.<FUNCTION_NAME>.Disabled` a smaže nastavení aplikace.

![Přepínač stavu funkce](media/disable-function/function-state-switch.png)

## <a name="other-methods"></a>Jiné metody

Zatímco metoda nastavení aplikace je doporučena pro všechny jazyky a všechny verze runtime, existuje několik dalších způsobů, jak zakázat funkce. Tyto metody, které se liší podle jazyka a runtime verze, jsou udržovány pro zpětnou kompatibilitu. 

### <a name="c-class-libraries"></a>Knihovny tříd C#

Ve funkci knihovny tříd můžete `Disable` také použít atribut, abyste zabránili aktivaci funkce. Atribut můžete použít bez parametru konstruktoru, jak je znázorněno v následujícím příkladu:

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

Atribut bez parametru konstruktoru vyžaduje, abyste znovu zkompilovali a znovu nasadili projekt, abyste změnili zakázaný stav funkce. Flexibilnější způsob použití atributu je zahrnout parametr konstruktoru, který odkazuje na nastavení logické aplikace, jak je znázorněno v následujícím příkladu:

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

Tato metoda umožňuje povolit a zakázat funkci změnou nastavení aplikace, bez opětovné kompilace nebo opětovnénasazení. Změna nastavení aplikace způsobí restartování aplikace funkce, takže se okamžitě rozpozná změna zakázaného stavu.

> [!IMPORTANT]
> Atribut `Disabled` je jediný způsob, jak zakázat funkci knihovny tříd. Generovaný soubor *function.json* pro funkci knihovny tříd není určen k přímé úpravě. Pokud tento soubor upravíte, `disabled` nebude mít nic společného s vlastností.
>
> Totéž platí pro přepínač **stavu funkce** na kartě **Správa,** protože funguje změnou souboru *function.json.*
>
> Všimněte si také, že portál může znamenat, že funkce je zakázána, pokud není.

### <a name="functions-1x---scripting-languages"></a>Funkce 1.x - skriptovací jazyky

Ve verzi 1.x můžete také `disabled` použít vlastnost souboru *function.json* a sdělit běhu, aby neaktivoval funkci. Tato metoda funguje pouze pro skriptovací jazyky, jako je například skript Jazyka C# a JavaScript. Vlastnost `disabled` lze nastavit `true` na nebo na název aplikace nastavení:

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
– nebo – 

```json
    "bindings": [
        ...
    ],
    "disabled": "IS_DISABLED"
```

V druhém příkladu je funkce zakázána, pokud je nastavení aplikace `true` s názvem IS_DISABLED a je nastavena na nebo 1.

Soubor můžete upravit na webu Azure Portal nebo použít přepínač **Stav funkce** na kartě **Správa** funkce. Přepínač portálu funguje změnou souboru *function.json.*

![Přepínač stavu funkce](media/disable-function/function-state-switch.png)

## <a name="next-steps"></a>Další kroky

Tento článek se zabývá zakázáním automatických aktivačních událostí. Další informace o aktivačních událostech naleznete v [tématu Aktivační události a vazby](functions-triggers-bindings.md).
