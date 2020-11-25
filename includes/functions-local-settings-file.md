---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: d944d1d3e9c72471fab2435430a7d13e1770e807
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010448"
---
## <a name="local-settings-file"></a>Soubor místního nastavení

local.settings.jsv souboru ukládá nastavení aplikace, připojovací řetězce a nastavení, které používají místní vývojové nástroje. Nastavení v local.settings.jsv souboru se používají pouze v případě, že používáte projekty místně. Soubor místních nastavení má tuto strukturu:

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "<language worker>",
    "AzureWebJobsStorage": "<connection-string>",
    "AzureWebJobsDashboard": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>",
    "AzureWebJobs.HttpExample.Disabled": "true"
  },
  "Host": {
    "LocalHttpPort": 7071,
    "CORS": "*",
    "CORSCredentials": false
  },
  "ConnectionStrings": {
    "SQLConnectionString": "<sqlclient-connection-string>"
  }
}
```

Tato nastavení jsou podporována při spuštění projektů místně:

| Nastavení      | Popis                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | Pokud je toto nastavení nastaveno na hodnotu `true` , všechny hodnoty budou šifrovány pomocí klíče místního počítače. Používá se s `func settings` příkazy. Výchozí hodnota je `false`. Můžete chtít šifrovat local.settings.jsv souboru na místním počítači, když obsahuje tajné kódy, jako jsou například připojovací řetězce služby. Hostitel při spuštění automaticky dešifruje nastavení. `func settings decrypt`Před pokusem o čtení místně zašifrovaného nastavení použijte příkaz. |
| **`Values`** | Pole nastavení aplikace a připojovacích řetězců, které se použijí, když je projekt spuštěn místně. Tyto páry klíč-hodnota (řetězcové řetězce) odpovídají nastavení aplikace ve vaší aplikaci Function App v Azure, jako je [`AzureWebJobsStorage`] . Mnoho triggerů a vazeb má vlastnost, která odkazuje na nastavení aplikace připojovacího řetězce, jako `Connection` je například [Trigger služby Blob Storage](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#configuration). U těchto vlastností potřebujete nastavení aplikace definované v poli `Values` . Seznam běžně používaných nastavení najdete v následující tabulce. <br/>Hodnoty musí být řetězce, nikoli objekty nebo pole JSON. Názvy nastavení nemůžou obsahovat dvojtečku ( `:` ) nebo dvojitou vlnovku ( `__` ). Znaky dvojitého podtržení jsou rezervovány modulem runtime a dvojtečka je vyhrazena pro [vkládání závislostí](../articles/azure-functions/functions-dotnet-dependency-injection.md#working-with-options-and-settings). |
| **`Host`** | Nastavení v této části přizpůsobuje hostitelský proces Functions, když spouštíte projekty místně. Tato nastavení jsou oddělená od host.jsnastavení, která platí i při spuštění projektů v Azure. |
| **`LocalHttpPort`** | Nastaví výchozí port, který se používá při spuštění místního hostitele funkcí ( `func host start` a `func run` ). `--port`Možnost příkazového řádku má přednost před tímto nastavením. |
| **`CORS`** | Definuje zdroje povolené pro [sdílení prostředků mezi zdroji (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Zdroje jsou zadány jako čárkami oddělený seznam bez mezer. Je podporována znaková hodnota ( \* ), která umožňuje požadavky z libovolného původu. |
| **`CORSCredentials`** |  Pokud je nastavena na `true` , umožňuje `withCredentials` požadavky. |
| **`ConnectionStrings`** | Kolekce. Tuto kolekci nepoužívejte pro připojovací řetězce používané vašimi vazbami funkcí. Tato kolekce je používána pouze rozhraními, která obvykle získávají připojovací řetězce z `ConnectionStrings` oddílu konfiguračního souboru, například [Entity Framework](/ef/ef6/). Připojovací řetězce v tomto objektu jsou přidány do prostředí s typem zprostředkovatele [System. data. SqlClient](/dotnet/api/system.data.sqlclient). Položky v této kolekci se nepublikují do Azure s dalšími nastaveními aplikací. Tyto hodnoty musíte explicitně přidat do `Connection strings` kolekce nastavení aplikace Function App. Pokud vytváříte [`SqlConnection`](/dotnet/api/system.data.sqlclient.sqlconnection) v kódu funkce, měli byste uložit hodnotu připojovacího řetězce s ostatními připojeními v **nastavení aplikace** na portálu. |

Při místním spuštění může být v poli zahrnuto následující nastavení aplikace **`Values`** :

| Nastavení | Hodnoty | Popis |
|-----|-----|-----|
|**`AzureWebJobsStorage`**| Připojovací řetězec účtu úložiště nebo<br/>`UseDevelopmentStorage=true`| Obsahuje připojovací řetězec pro účet úložiště Azure. Vyžaduje se při použití triggerů než HTTP. Další informace najdete v [`AzureWebJobsStorage`] referenčních informacích.<br/>Pokud máte [emulátor Azure Storage](../articles/storage/common/storage-use-emulator.md) nainstalovaný místně a nastavíte [`AzureWebJobsStorage`] pro `UseDevelopmentStorage=true` , základní nástroje používají emulátor. Emulátor je užitečný během vývoje, ale před nasazením je třeba otestovat pomocí skutečného připojení úložiště.| 
|**`AzureWebJobs.<FUNCTION_NAME>.Disabled`**| `true`\|`false` | Chcete-li zakázat funkci při místním spuštění, přidejte `"AzureWebJobs.<FUNCTION_NAME>.Disabled": "true"` do kolekce, kde `<FUNCTION_NAME>` je název funkce. Další informace najdete v tématu [zakázání funkcí v Azure Functions](../articles/azure-functions/disable-function.md#localsettingsjson) |
|**`FUNCTIONS_WORKER_RUNTIME`** | `dotnet`<br/>`node`<br/>`java`<br/>`powershell`<br/>`python`| Označuje cílový jazyk modulu runtime Functions. Vyžadováno pro verzi 2. x a vyšší z modulu runtime Functions. Toto nastavení je vygenerováno pro váš projekt pomocí základních nástrojů. Další informace najdete v [`FUNCTIONS_WORKER_RUNTIME`](../articles/azure-functions/functions-app-settings.md#functions_worker_runtime) referenčních informacích.|
| **`FUNCTIONS_WORKER_RUNTIME_VERSION`** | `~7` |Indikuje, že se má používat PowerShell 7 při místním spuštění. Pokud není nastavená, použije se PowerShell Core 6. Toto nastavení se používá jenom v případě, že je spuštěný místně. Při spuštění v Azure se verze modulu runtime PowerShellu určuje podle `powerShellVersion` nastavení konfigurace lokality, které se dá [nastavit na portálu](../articles/azure-functions/functions-reference-powershell.md#changing-the-powershell-version). | 

[AzureWebJobsStorage]: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage