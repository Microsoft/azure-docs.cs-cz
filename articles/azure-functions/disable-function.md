---
title: Postup zakázání funkcí v Azure Functions
description: Naučte se, jak zakázat a povolit funkce v Azure Functions.
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 1ad484804f66a2e2d4d0f1da4a37cf0d6c485f38
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104584733"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Postup zakázání funkcí v Azure Functions

Tento článek vysvětluje, jak zakázat funkci v Azure Functions. Pro *vypnutí* funkce znamená, že modul runtime bude ignorovat automatickou aktivační událost, která je pro funkci definována. To umožňuje zabránit spuštění konkrétní funkce bez zastavení celé aplikace Function App.

Doporučený způsob, jak funkci zakázat, je nastavení aplikace ve formátu `AzureWebJobs.<FUNCTION_NAME>.Disabled` nastaveném na `true` . Toto nastavení aplikace můžete vytvořit a upravit mnoha různými způsoby, včetně použití [Azure CLI](/cli/azure/) a karty **přehledu** vaší funkce v [Azure Portal](https://portal.azure.com). 

> [!NOTE]  
> Když zakážete funkci aktivovanou protokolem HTTP pomocí metod popsaných v tomto článku, koncový bod může být dostupný i při spuštění na místním počítači.  

## <a name="disable-a-function"></a>Vypnutí funkce

# <a name="portal"></a>[Azure Portal](#tab/portal)

Použijte tlačítka **Povolit** a **Zakázat** na stránce **Přehled** funkce. Tato tlačítka fungují změnou hodnoty `AzureWebJobs.<FUNCTION_NAME>.Disabled` nastavení aplikace. Toto nastavení specifické pro tuto funkci je vytvořeno při prvním zakázání. 

![Přepínač stavu funkce](media/disable-function/function-state-switch.png)

I když publikujete do aplikace Function App z místního projektu, můžete i nadále používat portál k zakázání funkcí v aplikaci Function App. 

> [!NOTE]  
> Funkce testování integrované na portálu ignoruje `Disabled` nastavení. To znamená, že zakázaná funkce se pořád spustí při spuštění z okna **test** na portálu. 

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

V Azure CLI pomocí [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) příkazu vytvořte a upravte nastavení aplikace. Následující příkaz zakáže funkci pojmenovanou `QueueTrigger` vytvořením nastavení aplikace s názvem `AzureWebJobs.QueueTrigger.Disabled` a nastavením na `true` . 

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

Pokud chcete funkci znovu povolit, spusťte znovu stejný příkaz s hodnotou `false` .

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

[`Update-AzFunctionAppSetting`](/powershell/module/az.functions/update-azfunctionappsetting)Příkaz přidá nebo aktualizuje nastavení aplikace. Následující příkaz zakáže funkci pojmenovanou `QueueTrigger` vytvořením nastavení aplikace s názvem `AzureWebJobs.QueueTrigger.Disabled` a nastavením na `true` . 

```azurepowershell-interactive
Update-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME> -AppSetting @{"AzureWebJobs.QueueTrigger.Disabled" = "true"}
```

Pokud chcete funkci znovu povolit, spusťte znovu stejný příkaz s hodnotou `false` .

```azurepowershell-interactive
Update-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME> -AppSetting @{"AzureWebJobs.QueueTrigger.Disabled" = "false"}
```
---

## <a name="functions-in-a-slot"></a>Funkce ve slotu

Ve výchozím nastavení se nastavení aplikace vztahují také na aplikace spuštěné v slotech nasazení. Nastavení aplikace, které používá slot, ale můžete přepsat nastavením aplikace na konkrétní pozici. Například můžete chtít, aby funkce byla aktivní v produkčním prostředí, ale ne během testování nasazení, například funkce aktivované časovačem. 

Zakázání funkce pouze v přípravném slotu:

# <a name="portal"></a>[Azure Portal](#tab/portal)

Přejděte do instance slotu aplikace Functions tak, že vyberete možnosti **sloty nasazení** v části **nasazení**, vyberete svou patici a vyberete **funkce** v instanci slot.  Zvolte funkci a pak použijte tlačítka **Povolit** a **Zakázat** na stránce **přehledu** této funkce. Tato tlačítka fungují změnou hodnoty `AzureWebJobs.<FUNCTION_NAME>.Disabled` nastavení aplikace. Toto nastavení specifické pro tuto funkci je vytvořeno při prvním zakázání. 

Můžete také přímo přidat nastavení aplikace s názvem `AzureWebJobs.<FUNCTION_NAME>.Disabled` s hodnotou `true` v **konfiguraci** pro instanci slot. Když přidáte nastavení aplikace specifické pro slot, nezapomeňte zaškrtnout políčko **nastavení slotu nasazení** . Tím se v průběhu zahození zachová hodnota nastavení pomocí slotu.

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> --slot <SLOT_NAME> \
--slot-settings AzureWebJobs.QueueTrigger.Disabled=true
```
Pokud chcete funkci znovu povolit, spusťte znovu stejný příkaz s hodnotou `false` .

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> --slot <SLOT_NAME> \
--slot-settings AzureWebJobs.QueueTrigger.Disabled=false
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Azure PowerShell aktuálně tuto funkci nepodporuje.

---

Další informace najdete v tématu [Azure Functions sloty nasazení](functions-deployment-slots.md).

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
