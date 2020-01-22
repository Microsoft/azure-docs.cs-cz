---
title: Jak cílit na verze modulu runtime Azure Functions
description: Azure Functions podporuje více verzí modulu runtime. Naučte se, jak určit běhovou verzi aplikace Function App hostované v Azure.
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: 60953d10aeb92083b0aad3432921c7c55bc77e39
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294163"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Jak cílit na verze modulu runtime Azure Functions

Aplikace Function App běží na konkrétní verzi Azure Functions runtime. Existují tři hlavní verze: [1. x, 2. x a 3. x](functions-versions.md). Ve výchozím nastavení se aplikace Function App vytvoří ve verzi 2. x modulu runtime. Tento článek vysvětluje, jak nakonfigurovat aplikaci Function App v Azure tak, aby běžela na zvolené verzi. Informace o tom, jak nakonfigurovat místní vývojové prostředí pro konkrétní verzi, naleznete v tématu [Code and test Azure Functions lokálně](functions-run-local.md).

## <a name="automatic-and-manual-version-updates"></a>Automatické a ruční aktualizace verze

Azure Functions umožňuje cílit na konkrétní verzi modulu runtime pomocí nastavení aplikace `FUNCTIONS_EXTENSION_VERSION` ve Function App. Aplikace Function App je udržována v zadané hlavní verzi, dokud nebudete explicitně chtít přejít na novou verzi.

Pokud zadáte pouze hlavní verzi, aplikace Function App se automaticky aktualizuje na nové podverze modulu runtime, jakmile budou k dispozici. Nové podverze nevedou k nedůležitým změnám. Pokud zadáte vedlejší verzi (například "2.0.12345"), aplikace Function App se přiřadí k dané konkrétní verzi, dokud ji explicitně nezměníte.

> [!NOTE]
> Pokud připnete ke konkrétní verzi Azure Functions a potom se pokusíte publikovat do Azure pomocí sady Visual Studio, zobrazí se dialogové okno s výzvou k aktualizaci na nejnovější verzi nebo zrušení publikování. Pokud se tomu chcete vyhnout, přidejte do souboru `.csproj` vlastnost `<DisableFunctionExtensionVersionUpdate>true</DisableFunctionExtensionVersionUpdate>`.

Když je k dispozici veřejně dostupná nová verze, zobrazí se výzva na portálu, která vám umožní přejít až k této verzi. Po přesunu na novou verzi můžete vždy použít nastavení aplikace `FUNCTIONS_EXTENSION_VERSION` a přejít zpět na předchozí verzi.

Následující tabulka uvádí `FUNCTIONS_EXTENSION_VERSION` hodnoty pro jednotlivé hlavní verze, které umožňují automatické aktualizace:

| Hlavní verze | hodnota `FUNCTIONS_EXTENSION_VERSION` |
| ------------- | ----------------------------------- |
| 3.x  | `~3` |
| 2.x  | `~2` |
| verze  | `~1` |

Změna verze modulu runtime způsobuje restartování aplikace Function App.

## <a name="view-and-update-the-current-runtime-version"></a>Zobrazení a aktualizace aktuální verze modulu runtime

Můžete změnit verzi modulu runtime, kterou používá aplikace Function App. Z důvodu potenciálních změn můžete změnit pouze verzi modulu runtime ještě před vytvořením funkcí ve vaší aplikaci Function App. 

> [!IMPORTANT]
> I když je verze modulu runtime určena nastavením `FUNCTIONS_EXTENSION_VERSION`, měli byste tuto změnu provést v Azure Portal a ne změnou nastavení přímo. Důvodem je to, že portál ověří vaše změny a v případě potřeby provede další související změny.

### <a name="from-the-azure-portal"></a>Pomocí webu Azure Portal

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

> [!NOTE]
> Pomocí Azure Portal nemůžete změnit verzi modulu runtime aplikace Function App, která již obsahuje funkce.

### <a name="view-and-update-the-runtime-version-using-azure-cli"></a>Z Azure CLI

Můžete také zobrazit a nastavit `FUNCTIONS_EXTENSION_VERSION` v rozhraní příkazového řádku Azure CLI.

>[!NOTE]
>Vzhledem k tomu, že jiná nastavení můžou být ovlivněná verzí modulu runtime, měli byste změnit verzi na portálu. Když změníte běhové verze, portál automaticky provede další potřebné aktualizace, jako je například verze Node. js a zásobník modulu runtime.  

Pomocí Azure CLI zobrazte aktuální verzi modulu runtime pomocí příkazu [AZ functionapp config appSettings set](/cli/azure/functionapp/config/appsettings) .

```azurecli-interactive
az functionapp config appsettings list --name <function_app> \
--resource-group <my_resource_group>
```

V tomto kódu nahraďte `<function_app>` názvem vaší aplikace Function App. Nahraďte `<my_resource_group>` také názvem skupiny prostředků pro aplikaci Function App. 

`FUNCTIONS_EXTENSION_VERSION` se zobrazí v následujícím výstupu, který byl zkrácen pro přehlednost:

```output
[
  {
    "name": "FUNCTIONS_EXTENSION_VERSION",
    "slotSetting": false,
    "value": "~2"
  },
  {
    "name": "FUNCTIONS_WORKER_RUNTIME",
    "slotSetting": false,
    "value": "dotnet"
  },
  
  ...
  
  {
    "name": "WEBSITE_NODE_DEFAULT_VERSION",
    "slotSetting": false,
    "value": "8.11.1"
  }
]
```

Nastavení `FUNCTIONS_EXTENSION_VERSION` ve Function App můžete aktualizovat pomocí příkazu [AZ functionapp config appSettings set](/cli/azure/functionapp/config/appsettings) .

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```

Nahraďte `<function_app>` názvem vaší aplikace Function App. Nahraďte `<my_resource_group>` také názvem skupiny prostředků pro aplikaci Function App. Také nahraďte `<version>` platnou verzí modulu runtime 1. x nebo `~2` pro verzi 2. x.

Tento příkaz můžete spustit z [Azure Cloud Shell](../cloud-shell/overview.md) výběrem možnosti **vyzkoušet** v předchozím příkladu kódu. Pomocí rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) můžete také spustit tento příkaz po provedení příkazu [AZ Login](/cli/azure/reference-index#az-login) pro přihlášení.



## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Cílení na modul runtime 2,0 ve vašem místním vývojovém prostředí](functions-run-local.md)

> [!div class="nextstepaction"]
> [Verze modulu runtime najdete v poznámkách k verzi.](https://github.com/Azure/azure-webjobs-sdk-script/releases)
