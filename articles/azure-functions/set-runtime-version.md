---
title: Jak se zaměřit na verze modulu runtime Azure Functions
description: Služba Azure Functions podporuje více verzí modulu runtime. Zjistěte, jak určit verzi modulu runtime aplikace funkcí hostované v Azure.
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: glenga
ms.openlocfilehash: a0f66f5a1ba64c955fe0669d3ed215ee7c2895c0
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52498393"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Jak se zaměřit na verze modulu runtime Azure Functions

Aplikace function app se spouští na konkrétní verzi modulu runtime Azure Functions. Existují dvě hlavní verze: [1.x a 2.x](functions-versions.md). Ve výchozím nastavení, funkce aplikace, které jsou vytvořeny verze 2.x modulu runtime. Tento článek vysvětluje, jak nakonfigurovat aplikaci function app v Azure pro spouštění na zvolené verzi. Informace o tom, jak nakonfigurovat místní vývojové prostředí pro konkrétní verzi, najdete v části [kódu a testování Azure Functions místně](functions-run-local.md).

> [!NOTE]
> Nelze změnit verzi modulu runtime aplikace function App, který má jednu nebo více funkcí. Můžete změnit verzi modulu runtime by měl pomocí webu Azure portal.

## <a name="automatic-and-manual-version-updates"></a>Verze automatické a ruční aktualizace

Funkce umožňuje cílit na konkrétní verzi modulu runtime s použitím `FUNCTIONS_EXTENSION_VERSION` nastavení aplikace function app. Aplikace function app se ukládají na zadaný hlavní verze, dokud se nerozhodnete explicitně přesunout na novou verzi.

Pokud zadáte jenom hlavní verzi ("~ 2" pro 2.x nebo "~ 1" pro 1.x), aplikace function app se automaticky aktualizují na nový dílčí verze modulu runtime, jakmile budou k dispozici. Nový dílčí verze není zavést rozbíjející změny. Pokud zadáte vedlejší verze (například "2.0.12345"), aplikace function app se Připne na tuto konkrétní verzi, dokud ji explicitně nezměníte.

Když je veřejně dostupná nová verze, výzvu na portálu poskytuje možnost Přesunout nahoru na tuto verzi. Po přesunutí na novou verzi, můžete kdykoli použít `FUNCTIONS_EXTENSION_VERSION` nastavení aplikace, které chcete vrátit k předchozí verzi.

Ke změně verze modulu runtime způsobí, že se aplikace function app k restartování.

Hodnoty můžete nastavit `FUNCTIONS_EXTENSION_VERSION` nastavení, které povolí automatické aktualizace aplikací jsou aktuálně "~ 1" pro modul runtime verze 1.x a "~ 2" pro 2.x.

## <a name="view-and-update-the-current-runtime-version"></a>Zobrazit a aktualizovat aktuální verze modulu runtime

Můžete změnit verzi modulu runtime používané jste aplikaci function app. Z důvodu potenciál rozbíjející změny v by měl změnit pouze verze modulu runtime, než vytvoříte všechny funkce v aplikaci function app. I když se verze modulu runtime závisí `FUNCTIONS_EXTENSION_VERSION` nastavení, by měl provedení této změny na webu Azure Portal a není to změnou nastavení přímo. Je to proto, že na portálu ověří změny a provádět další související změny podle potřeby.

### <a name="from-the-azure-portal"></a>Na webu Azure Portal

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

### <a name="view-and-update-the-runtime-version-using-azure-cli"></a>V Azure CLI

Můžete také zobrazit a nastavit `FUNCTIONS_EXTENSION_VERSION` z příkazového řádku Azure.

>[!NOTE]
>Protože jiné nastavení může mít vliv na verzi modulu runtime, měli byste změnit verzi na portálu. Na portálu automaticky provede další potřebné aktualizace, jako je například Node.js verze a modulu runtime zásobníku při změně verze modulu runtime.  

Pomocí Azure CLI, zobrazit aktuální verze modulu runtime s [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set) příkazu.

```azurecli-interactive
az functionapp config appsettings list --name <function_app> \
--resource-group <my_resource_group>
```

V tomto kódu nahraďte `<function_app>` s názvem aplikace function App. Také nahraďte `<my_resource_group>` s názvem skupiny prostředků pro vaši aplikaci function app. 

Zobrazí `FUNCTIONS_EXTENSION_VERSION` v následujícím výstupu, který byl zkrácen pro přehlednost:

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

Můžete aktualizovat `FUNCTIONS_EXTENSION_VERSION` nastavení aplikace function App s [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set) příkazu.

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```

Nahraďte `<function_app>` s názvem aplikace function App. Také nahraďte `<my_resource_group>` s názvem skupiny prostředků pro vaši aplikaci function app. Také nahraďte `<version>` s platnou verzi modul runtime verze 1.x nebo `~2` verze 2.x.

Tento příkaz můžete spustit [Azure Cloud Shell](../cloud-shell/overview.md) výběrem **vyzkoušet** v předchozím příkladu kódu. Můžete také použít [rozhraní příkazového řádku Azure místně](/cli/azure/install-azure-cli) k provedení tohoto příkazu po provedení [az login](/cli/azure/reference-index#az-login) k přihlášení.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Cílový modul runtime 2.0 ve vašem místním vývojovém prostředí](functions-run-local.md)

> [!div class="nextstepaction"]
> [Zobrazit poznámky k verzím modulu runtime](https://github.com/Azure/azure-webjobs-sdk-script/releases)
