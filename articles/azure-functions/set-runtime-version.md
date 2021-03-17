---
title: Jak cílit na verze modulu runtime Azure Functions
description: Azure Functions podporuje více verzí modulu runtime. Naučte se, jak určit běhovou verzi aplikace Function App hostované v Azure.
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: e9aa5546b5f07b724fe22bc1e20a2e97feb2aec2
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102435558"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Jak cílit na verze modulu runtime Azure Functions

Aplikace Function App běží na konkrétní verzi Azure Functions runtime. Existují tři hlavní verze: [3. x, 2. x a 1. x](functions-versions.md). Ve výchozím nastavení se aplikace Function App vytvoří ve verzi 3. x modulu runtime. Tento článek vysvětluje, jak nakonfigurovat aplikaci Function App v Azure tak, aby běžela na zvolené verzi. Informace o tom, jak nakonfigurovat místní vývojové prostředí pro konkrétní verzi, naleznete v tématu [Code and test Azure Functions lokálně](functions-run-local.md).

Postup ručního cílení na konkrétní verzi závisí na tom, zda používáte systém Windows nebo Linux.

## <a name="automatic-and-manual-version-updates"></a>Automatické a ruční aktualizace verze

_Tato část se nevztahuje na spuštění aplikace Function App [v systému Linux](#manual-version-updates-on-linux)._

Azure Functions umožňuje cílit na konkrétní verzi modulu runtime ve Windows pomocí `FUNCTIONS_EXTENSION_VERSION` nastavení aplikace ve Function App. Aplikace Function App je udržována v zadané hlavní verzi, dokud nebudete explicitně chtít přejít na novou verzi. Pokud zadáte pouze hlavní verzi, aplikace Function App se automaticky aktualizuje na nové podverze modulu runtime, jakmile budou k dispozici. Nové podverze by neměly zavádět podstatné změny. 

Pokud zadáte vedlejší verzi (například "2.0.12345"), aplikace Function App se přiřadí k dané konkrétní verzi, dokud ji explicitně nezměníte. Starší dílčí verze se pravidelně odstraňují z produkčního prostředí. Pokud se vaše podverze odebere, vaše aplikace Function App se vrátí k běhu na nejnovější verzi místo v sadě verzí v `FUNCTIONS_EXTENSION_VERSION` . V takovém případě byste měli rychle vyřešit všechny problémy s aplikací Function App, které vyžadují konkrétní dílčí verzi. Pak se můžete vrátit k cílení na hlavní verzi. Odebrání podverze jsou oznámeny v [App Service oznámení](https://github.com/Azure/app-service-announcements/issues).

> [!NOTE]
> Pokud připnete k určité hlavní verzi Azure Functions a potom se pokusíte publikovat do Azure pomocí sady Visual Studio, zobrazí se dialogové okno s výzvou k aktualizaci na nejnovější verzi nebo zrušení publikování. Pokud se tomu chcete vyhnout, přidejte `<DisableFunctionExtensionVersionUpdate>true</DisableFunctionExtensionVersionUpdate>` do `.csproj` souboru vlastnost.

Když je k dispozici veřejně dostupná nová verze, zobrazí se výzva na portálu, která vám umožní přejít až k této verzi. Po přesunu na novou verzi můžete `FUNCTIONS_EXTENSION_VERSION` nastavení aplikace vždy použít k přechodu zpět na předchozí verzi.

V následující tabulce jsou uvedeny `FUNCTIONS_EXTENSION_VERSION` hodnoty pro jednotlivé hlavní verze, které umožňují automatické aktualizace:

| Hlavní verze | `FUNCTIONS_EXTENSION_VERSION` osa |
| ------------- | ----------------------------------- |
| 3.x  | `~3` |
| 2.x  | `~2` |
| verze  | `~1` |

Změna verze modulu runtime způsobuje restartování aplikace Function App.

>[!NOTE]
>Aplikace funkcí .NET připnuté pro `~2.0` výslovný souhlas s automatickým upgradem na .NET Core 3,1. Další informace najdete v tématu [informace o funkcích v2. x](functions-dotnet-class-library.md#functions-v2x-considerations).  

## <a name="view-and-update-the-current-runtime-version"></a>Zobrazení a aktualizace aktuální verze modulu runtime

_Tato část se nevztahuje na spuštění aplikace Function App [v systému Linux](#manual-version-updates-on-linux)._

Můžete změnit verzi modulu runtime, kterou používá aplikace Function App. Z důvodu potenciálních změn můžete změnit pouze verzi modulu runtime ještě před vytvořením funkcí ve vaší aplikaci Function App. 

> [!IMPORTANT]
> I když je verze modulu runtime určena `FUNCTIONS_EXTENSION_VERSION` nastavením, je třeba tuto změnu provést pouze v Azure Portal a nikoli změnou nastavení přímo. Důvodem je to, že portál ověří vaše změny a v případě potřeby provede další související změny.

# <a name="portal"></a>[Azure Portal](#tab/portal)

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

> [!NOTE]
> Pomocí Azure Portal nemůžete změnit verzi modulu runtime aplikace Function App, která již obsahuje funkce.

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

Můžete také zobrazit a nastavit v rozhraní příkazového `FUNCTIONS_EXTENSION_VERSION` řádku Azure CLI.  

Pomocí Azure CLI zobrazte aktuální verzi modulu runtime pomocí příkazu [AZ functionapp config appSettings set](/cli/azure/functionapp/config/appsettings) .

```azurecli-interactive
az functionapp config appsettings list --name <function_app> \
--resource-group <my_resource_group>
```

V tomto kódu nahraďte `<function_app>` názvem vaší aplikace Function App. Nahraďte také `<my_resource_group>` názvem skupiny prostředků vaší aplikace Function App. 

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

`FUNCTIONS_EXTENSION_VERSION`Nastavení v aplikaci Function App můžete aktualizovat pomocí příkazu [AZ functionapp config appSettings set](/cli/azure/functionapp/config/appsettings) .

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP> \
--resource-group <RESOURCE_GROUP> \
--settings FUNCTIONS_EXTENSION_VERSION=<VERSION>
```

Nahraďte `<FUNCTION_APP>` názvem vaší aplikace Function App. Nahraďte také `<RESOURCE_GROUP>` názvem skupiny prostředků vaší aplikace Function App. Nahraďte také `<VERSION>` buď konkrétní verzí, nebo `~3` , `~2` nebo `~1` .

Výběrem možnosti **vyzkoušet** v předchozím příkladu kódu spusťte příkaz v [Azure Cloud Shell](../cloud-shell/overview.md). K provedení tohoto příkazu můžete také spustit [Azure CLI místně](/cli/azure/install-azure-cli) . Pokud používáte místně, musíte nejdřív spustit příkaz [AZ Login](/cli/azure/reference-index#az-login) , abyste se přihlásili.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pokud chcete kontrolovat Azure Functions runtime, použijte tuto rutinu: 

```powershell
Get-AzFunctionAppSetting -Name "<FUNCTION_APP>" -ResourceGroupName "<RESOURCE_GROUP>"
```

Nahraďte `<FUNCTION_APP>` názvem vaší aplikace Function App a `<RESOURCE_GROUP>` . Aktuální hodnota `FUNCTIONS_EXTENSION_VERSION` nastavení se vrátí v zatřiďovací tabulce.

Pomocí následujícího skriptu změňte modul runtime Functions:

```powershell
Update-AzFunctionAppSetting -Name "<FUNCTION_APP>" -ResourceGroupName "<RESOURCE_GROUP>" -AppSetting @{"FUNCTIONS_EXTENSION_VERSION" = "<VERSION>"} -Force
```

Stejně jako dřív nahraďte `<FUNCTION_APP>` názvem vaší aplikace Function App a `<RESOURCE_GROUP>` názvem skupiny prostředků. Nahraďte také `<VERSION>` konkrétní verzí nebo hlavní verzí, například `~2` nebo `~3` . V vrácené zatřiďovací tabulce můžete ověřit aktualizovanou hodnotu `FUNCTIONS_EXTENSION_VERSION` nastavení. 

---

Aplikace Function App se restartuje po provedení změny v nastavení aplikace.

## <a name="manual-version-updates-on-linux"></a>Ruční aktualizace verze v systému Linux

Pokud chcete aplikaci funkce systému Linux připnout na konkrétní verzi hostitele, zadejte adresu URL obrázku do pole ' LinuxFxVersion ' v konfiguraci lokality. Příklad: Pokud chceme připnout aplikaci funkcí Node 10 k vyslovení verze 3.0.13142 hostitele –

Pro **Linux App Service nebo elastické aplikace úrovně Premium** – nastavte `LinuxFxVersion` na `DOCKER|mcr.microsoft.com/azure-functions/node:3.0.13142-node10-appservice` .

Pro **aplikace pro Linux spotřeby** – `LinuxFxVersion` nastavte `DOCKER|mcr.microsoft.com/azure-functions/mesh:3.0.13142-node10` na.

# <a name="portal"></a>[Azure Portal](#tab/portal)

Zobrazení a úprava nastavení konfigurace webu pro aplikace Function app není v Azure Portal podporována. Místo toho použijte Azure CLI.

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

Pomocí rozhraní příkazového řádku Azure můžete zobrazit a nastavit `LinuxFxVersion` .  

Aktuální verzi modulu runtime zobrazíte pomocí příkazu [AZ functionapp config show](/cli/azure/functionapp/config) .

```azurecli-interactive
az functionapp config show --name <function_app> \
--resource-group <my_resource_group> --query 'linuxFxVersion' -o tsv
```

V tomto kódu nahraďte `<function_app>` názvem vaší aplikace Function App. Nahraďte také `<my_resource_group>` názvem skupiny prostředků vaší aplikace Function App. Aktuální hodnota `linuxFxVersion` je vrácena.

Pokud chcete aktualizovat `linuxFxVersion` nastavení v aplikaci Function App, použijte příkaz [AZ functionapp config set](/cli/azure/functionapp/config) .

```azurecli-interactive
az functionapp config set --name <FUNCTION_APP> \
--resource-group <RESOURCE_GROUP> \
--linux-fx-version <LINUX_FX_VERSION>
```

Nahraďte `<FUNCTION_APP>` názvem vaší aplikace Function App. Nahraďte také `<RESOURCE_GROUP>` názvem skupiny prostředků vaší aplikace Function App. Nahraďte také `<LINUX_FX_VERSION>` hodnotou konkrétního obrázku, jak je popsáno výše.

Tento příkaz můžete spustit z [Azure Cloud Shell](../cloud-shell/overview.md) výběrem možnosti **vyzkoušet** v předchozím příkladu kódu. Pomocí rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) můžete také spustit tento příkaz po provedení příkazu [AZ Login](/cli/azure/reference-index#az-login) pro přihlášení.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Azure PowerShell nelze použít k nastavení v `linuxFxVersion` tuto chvíli. Místo toho použijte Azure CLI.

---

Aplikace Function App se restartuje po provedení změny v konfiguraci lokality.

> [!NOTE]
> Pro aplikace spuštěné v plánu spotřeby `LinuxFxVersion` může nastavení na konkrétní obrázek prodloužit dobu studených časů spuštění. Důvodem je to, že připnutí na konkrétní obrázek zabraňuje funkcím v používání některých optimalizací spuštění. 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Cílení na modul runtime 2,0 ve vašem místním vývojovém prostředí](functions-run-local.md)

> [!div class="nextstepaction"]
> [Verze modulu runtime najdete v poznámkách k verzi.](https://github.com/Azure/azure-webjobs-sdk-script/releases)
