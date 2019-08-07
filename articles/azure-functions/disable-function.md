---
title: Postup zakázání funkcí v Azure Functions
description: Přečtěte si, jak zakázat a povolit funkce v Azure Functions 1. x a 2. x.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: glenga
ms.openlocfilehash: 183056d01146194b2854a70df790802e1a0bb839
ms.sourcegitcommit: f7998db5e6ba35cbf2a133174027dc8ccf8ce957
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/05/2019
ms.locfileid: "68782240"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Postup zakázání funkcí v Azure Functions

Tento článek vysvětluje, jak zakázat funkci v Azure Functions. Pro *vypnutí* funkce znamená, že modul runtime bude ignorovat automatickou aktivační událost, která je definována pro funkci. Způsob, jakým to provedete, závisí na verzi modulu runtime a programovacím jazyce:

* Funkce 2. x:
  * Jeden způsob pro všechny jazyky
  * Volitelný způsob pro C# knihovny tříd
* Funkce 1. x:
  * Skriptovací jazyky
  * C#knihovny tříd

## <a name="functions-2x---all-languages"></a>Functions 2. x – všechny jazyky

Ve funkcích 2. x zakážete funkci pomocí nastavení aplikace ve formátu `AzureWebJobs.<FUNCTION_NAME>.Disabled`. Toto nastavení můžete vytvořit a upravit programově pomocí rozhraní příkazového řádku Azure CLI. To můžete provést také na kartě **Správa** vaší funkce v [Azure Portal](https://portal.azure.com). 

### <a name="azure-cli"></a>Azure CLI

V Azure CLI pomocí [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) příkazu vytvořte a upravte nastavení aplikace. Následující příkaz zakáže funkci s názvem `QueueTrigger` vytvořením nastavení aplikace s názvem `AzureWebJobs.QueueTrigger.Disabled` nastaveným na `true`. 

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

Pokud chcete funkci znovu povolit, spusťte znovu stejný příkaz s hodnotou `false`.

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

### <a name="portal"></a>Portál

Na kartě **Spravovat** funkce můžete také použít přepínač **stavu funkce** . Přepínač funguje tak, že se `AzureWebJobs.<FUNCTION_NAME>.Disabled` vytvoří a odstraní nastavení aplikace.

![Přepínač stavu funkce](media/disable-function/function-state-switch.png)

## <a name="functions-2x---c-class-libraries"></a>Knihovny Functions 2. C# x-Class

V knihovně tříd Functions 2. x doporučujeme použít metodu, která funguje pro všechny jazyky. Pokud ale dáváte přednost, můžete [použít atribut Disable jako ve funkci 1. x](#functions-1x---c-class-libraries).

## <a name="functions-1x---scripting-languages"></a>Funkce 1. x – skriptovací jazyky

Pro skriptovací jazyky, jako C# jsou skripty a JavaScript, můžete použít `disabled` vlastnost souboru *Function. JSON* k oznámení, že modul runtime neaktivuje funkci. Tato vlastnost může být nastavena na `true` nebo na název nastavení aplikace:

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
or 

```json
    "bindings": [
        ...
    ],
    "disabled": "IS_DISABLED"
```

V druhém příkladu je funkce neaktivní, pokud existuje nastavení aplikace s názvem IS_DISABLED a je nastavené na `true` nebo 1.

Soubor můžete upravit ve Azure Portal nebo použít přepínač **stavu funkce** na kartě **Spravovat** funkce. Přepínač portálu funguje změnou souboru *Function. JSON* .

![Přepínač stavu funkce](media/disable-function/function-state-switch.png)

## <a name="functions-1x---c-class-libraries"></a>Funkce 1. x – C# knihovny tříd

V knihovně tříd Functions 1. x použijete `Disable` atribut, který zabrání spuštění funkce. Můžete použít atribut bez parametru konstruktoru, jak je znázorněno v následujícím příkladu:

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

Atribut bez parametru konstruktoru vyžaduje, abyste znovu zkompilujete a znovu nasadili projekt a změnili jste zakázaný stav funkce. Pružnější způsob použití atributu je zahrnutí parametru konstruktoru, který odkazuje na nastavení logické aplikace, jak je znázorněno v následujícím příkladu:

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

Tato metoda umožňuje povolit a zakázat funkci změnou nastavení aplikace, aniž by bylo nutné znovu kompilovat nebo znovu nasazovat. Změna nastavení aplikace způsobí, že se aplikace Function App restartuje, takže se změna zakázaného stavu rozpozná okamžitě.

> [!IMPORTANT]
> `Disabled` Atribut je jediným způsobem, jak zakázat funkci knihovny tříd. Vygenerovaný soubor *Function. JSON* pro funkci knihovny tříd není určen k úpravám přímo. Pokud tento soubor upravíte, nebude mít cokoli vliv na `disabled` vlastnost.
>
> Totéž platí pro přepínač **stavu funkce** na kartě **Spravovat** , protože funguje změnou souboru *Function. JSON* .
>
> Všimněte si také, že portál může indikovat, že funkce je zakázaná, když ne.

## <a name="next-steps"></a>Další postup

Tento článek se týká zakázání automatických triggerů. Další informace o aktivačních událostech najdete v tématu [triggery a vazby](functions-triggers-bindings.md).
