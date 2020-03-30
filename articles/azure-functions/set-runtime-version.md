---
title: Jak cílit na verze runtime Azure Functions
description: Azure Functions podporuje více verzí runtime. Přečtěte si, jak určit runtime verzi aplikace funkce hostované v Azure.
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: 5a71338b1b9735d7e7494dc2667bd7addf5d4a53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77151951"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Jak cílit na verze runtime Azure Functions

Aplikace funkce běží na konkrétní verzi runtime Funkce Azure. Existují tři hlavní verze: [1.x, 2.x a 3.x](functions-versions.md). Ve výchozím nastavení jsou aplikace funkcí vytvářeny ve verzi 2.x běhu. Tento článek vysvětluje, jak nakonfigurovat aplikaci funkcí v Azure tak, aby se spouštěla ve zvolené verzi. Informace o tom, jak nakonfigurovat prostředí místního vývoje pro konkrétní verzi, naleznete v [tématu Kód a testování funkcí Azure místně](functions-run-local.md).

## <a name="automatic-and-manual-version-updates"></a>Automatické a ruční aktualizace verzí

Funkce Azure umožňuje cílit na konkrétní verzi runtime pomocí nastavení `FUNCTIONS_EXTENSION_VERSION` aplikace v aplikaci funkce. Aplikace funkce je uložena na zadanou hlavní verzi, dokud se explicitně nerozhodnete přejít na novou verzi.

Pokud zadáte pouze hlavní verzi, aplikace funkce se automaticky aktualizuje na nové dílčí verze runtime, jakmile budou k dispozici. Nové dílčí verze by neměly zavádět změny. Pokud zadáte dílčí verzi (například "2.0.12345"), aplikace funkce je připnutá k této konkrétní verzi, dokud ji explicitně nezměníte.

> [!NOTE]
> Pokud připnete konkrétní verzi Azure Functions a pak se pokusíte publikovat do Azure pomocí Visual Studia, zobrazí se dialogové okno s výzvou k aktualizaci na nejnovější verzi nebo zrušení publikování. Chcete-li tomu `<DisableFunctionExtensionVersionUpdate>true</DisableFunctionExtensionVersionUpdate>` zabránit, `.csproj` přidejte vlastnost do souboru.

Pokud je nová verze veřejně dostupná, výzva na portálu vám dává možnost přejít na tuto verzi. Po přechodu na novou verzi můžete `FUNCTIONS_EXTENSION_VERSION` vždy použít nastavení aplikace k přechodu zpět na předchozí verzi.

V následující tabulce `FUNCTIONS_EXTENSION_VERSION` jsou uvedeny hodnoty pro každou hlavní verzi pro povolení automatických aktualizací:

| Hlavní verze | `FUNCTIONS_EXTENSION_VERSION`Hodnotu |
| ------------- | ----------------------------------- |
| 3.x  | `~3` |
| 2.x  | `~2` |
| 1.x  | `~1` |

Změna verze runtime způsobí restartování aplikace funkce.

## <a name="view-and-update-the-current-runtime-version"></a>Zobrazení a aktualizace aktuální verze runtime

Můžete změnit verzi runtime používanou vaší funkční aplikací. Z důvodu potenciálu narušujících změn můžete změnit pouze verzi runtime před vytvořením všech funkcí v aplikaci funkce. 

> [!IMPORTANT]
> Přestože je runtime verze `FUNCTIONS_EXTENSION_VERSION` určena nastavením, měli byste provést tuto změnu na webu Azure Portal a ne přímozměnou nastavení. Důvodem je, že portál ověří změny a provede další související změny podle potřeby.

### <a name="from-the-azure-portal"></a>Pomocí webu Azure Portal

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

> [!NOTE]
> Pomocí portálu Azure nemůžete změnit verzi runtime pro aplikaci funkce, která už obsahuje funkce.

### <a name="from-the-azure-cli"></a><a name="view-and-update-the-runtime-version-using-azure-cli"></a>Z příkazového příkazového příkazu Kazi

Můžete také zobrazit a `FUNCTIONS_EXTENSION_VERSION` nastavit z azure cli.

>[!NOTE]
>Vzhledem k tomu, že další nastavení může být ovlivněna verzí runtime, měli byste změnit verzi na portálu. Portál automaticky provede další potřebné aktualizace, jako je například verze Node.js a zásobník za běhu, když změníte verze runtime.  

Pomocí příkazového příkazu KONP Azure zobrazte aktuální verzi runtime pomocí příkazu [nastavit nastavení konfigurace aplikace az functionapp.](/cli/azure/functionapp/config/appsettings)

```azurecli-interactive
az functionapp config appsettings list --name <function_app> \
--resource-group <my_resource_group>
```

V tomto kódu `<function_app>` nahraďte názvem aplikace funkce. Nahraďte `<my_resource_group>` také název skupiny prostředků pro vaši aplikaci funkce. 

Zobrazí se `FUNCTIONS_EXTENSION_VERSION` následující výstup, který byl zkrácen pro přehlednost:

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

`FUNCTIONS_EXTENSION_VERSION` Nastavení v aplikaci funkce můžete aktualizovat pomocí příkazu [az functionapp config appsettings set.](/cli/azure/functionapp/config/appsettings)

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```

Nahraďte `<function_app>` název aplikace funkce. Nahraďte `<my_resource_group>` také název skupiny prostředků pro vaši aplikaci funkce. Nahraďte `<version>` také platnou verzí runtime 1.x nebo `~2` pro verzi 2.x.

Tento příkaz můžete spustit z [prostředí Azure Cloud Shell](../cloud-shell/overview.md) výběrem **Vyzkoušet** v předchozí ukázce kódu. Můžete také použít [Azure CLI místně](/cli/azure/install-azure-cli) ke spuštění tohoto příkazu po spuštění [az přihlášení](/cli/azure/reference-index#az-login) k přihlášení.



## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Cílení na runtime 2.0 ve vašem místním vývojovém prostředí](functions-run-local.md)

> [!div class="nextstepaction"]
> [Viz Poznámky k verzi pro verze za běhu](https://github.com/Azure/azure-webjobs-sdk-script/releases)
