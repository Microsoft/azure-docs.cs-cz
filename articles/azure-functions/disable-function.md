---
title: Postup zakázání funkcí v Azure Functions
description: Naučte se, jak zakázat a povolit funkce v Azure Functions.
ms.topic: conceptual
ms.date: 02/03/2021
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: cbb84308507ea15f1c44c00122a9a59472f12a88
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2021
ms.locfileid: "99551039"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Postup zakázání funkcí v Azure Functions

Tento článek vysvětluje, jak zakázat funkci v Azure Functions. Pro *vypnutí* funkce znamená, že modul runtime bude ignorovat automatickou aktivační událost, která je pro funkci definována. To umožňuje zabránit spuštění konkrétní funkce bez zastavení celé aplikace Function App.

Doporučený způsob, jak funkci zakázat, je nastavení aplikace ve formátu `AzureWebJobs.<FUNCTION_NAME>.Disabled` nastaveném na `true` . Toto nastavení aplikace můžete vytvořit a upravit mnoha různými způsoby, včetně použití [Azure CLI](/cli/azure/) a karty **přehledu** vaší funkce v [Azure Portal](https://portal.azure.com). 

> [!NOTE]  
> Když zakážete funkci aktivovanou protokolem HTTP pomocí metod popsaných v tomto článku, koncový bod může být dostupný i při spuštění na místním počítači.  

## <a name="use-the-azure-cli"></a>Použití Azure CLI

V Azure CLI pomocí [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) příkazu vytvořte a upravte nastavení aplikace. Následující příkaz zakáže funkci s názvem `QueueTrigger` vytvořením nastavení aplikace s názvem `AzureWebJobs.QueueTrigger.Disabled` nastaveným na `true` . 

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

Pokud chcete funkci znovu povolit, spusťte znovu stejný příkaz s hodnotou `false` .

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

## <a name="use-the-portal"></a>Použití portálu

Můžete také použít tlačítka **Povolit** a **Zakázat** na stránce **Přehled** funkce. Tato tlačítka fungují změnou hodnoty `AzureWebJobs.<FUNCTION_NAME>.Disabled` nastavení aplikace. Toto nastavení specifické pro tuto funkci je vytvořeno při prvním zakázání. 

![Přepínač stavu funkce](media/disable-function/function-state-switch.png)

I když publikujete do aplikace Function App z místního projektu, můžete i nadále používat portál k zakázání funkcí v aplikaci Function App. 

> [!NOTE]  
> Funkce testování integrované na portálu ignoruje `Disabled` nastavení. To znamená, že zakázaná funkce se pořád spustí při spuštění z okna **test** na portálu. 

## <a name="localsettingsjson"></a>local.settings.json

Funkce lze zakázat stejným způsobem při místním spuštění. Chcete-li zakázat funkci s názvem `HttpExample` , přidejte položku do kolekce Values v local.settings.jssouboru, a to následujícím způsobem:

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "UseDevelopmentStorage=true", 
    "AzureWebJobs.HttpExample.Disabled": "true"
  }
}
``` 

## <a name="other-methods"></a>Jiné metody

I když je metoda nastavení aplikace doporučována pro všechny jazyky a všechny verze modulu runtime, existuje několik dalších způsobů, jak zakázat funkce. Tyto metody, které se liší podle jazyka a verze modulu runtime, jsou zachovány kvůli zpětné kompatibilitě. 

### <a name="c-class-libraries"></a>Knihovny tříd jazyka C#

V knihovně tříd můžete také použít `Disable` atribut k zabránění aktivaci funkce. Tento atribut umožňuje přizpůsobit název nastavení používaného k zakázání funkce. Použijte verzi atributu, která vám umožní definovat parametr konstruktoru, který odkazuje na nastavení logické aplikace, jak je znázorněno v následujícím příkladu:

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

K dispozici je také konstruktor pro parametr, který nepřijímá řetězec pro název nastavení. Tato verze atributu se nedoporučuje. Použijete-li tuto verzi, je nutné znovu zkompilovat a znovu nasadit projekt, aby bylo možné změnit zakázaný stav funkce.

### <a name="functions-1x---scripting-languages"></a>Funkce 1. x – skriptovací jazyky

Ve verzi 1. x můžete také použít `disabled` vlastnost *function.jsv* souboru k oznámení, že modul runtime neaktivuje funkci. Tato metoda funguje pouze pro skriptovací jazyky, jako je skript jazyka C# a JavaScript. `disabled`Vlastnost může být nastavena na `true` nebo na název nastavení aplikace:

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

V druhém příkladu je funkce zakázána, pokud existuje nastavení aplikace s názvem IS_DISABLED a je nastaveno na `true` nebo 1.

>[!IMPORTANT]  
>Portál používá nastavení aplikace k zakázání funkcí v1. x. Když je nastavení aplikace v konfliktu s function.jsv souboru, může dojít k chybě. Vlastnost byste měli odebrat `disabled` z function.jssouboru, aby nedocházelo k chybám. 


## <a name="next-steps"></a>Další kroky

Tento článek se týká zakázání automatických triggerů. Další informace o aktivačních událostech najdete v tématu [triggery a vazby](functions-triggers-bindings.md).
