---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: c505909599b6b69719de1cb9224db52d2f524b2b
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74935956"
---
## <a name="local-settings-file"></a>Soubor místního nastavení

Soubor Local. Settings. JSON ukládá nastavení aplikace, připojovací řetězce a nastavení, které používají místní vývojové nástroje. Nastavení v souboru Local. Settings. JSON se použijí pouze v případě, že používáte projekty místně. Soubor místních nastavení má tuto strukturu:

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "<language worker>",
    "AzureWebJobsStorage": "<connection-string>",
    "AzureWebJobsDashboard": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>"
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
| **`IsEncrypted`** | Když je toto nastavení nastavené na `true`, všechny hodnoty se šifrují pomocí klíče místního počítače. Používá se s příkazy `func settings`. Výchozí hodnota je `false`. |
| **`Values`** | Pole nastavení aplikace a připojovacích řetězců, které se použijí, když je projekt spuštěn místně. Tyto páry klíč-hodnota (řetězcové řetězce) odpovídají nastavení aplikace ve vaší aplikaci Function App v Azure, například [`AzureWebJobsStorage`]. Mnoho triggerů a vazeb má vlastnost, která odkazuje na nastavení aplikace připojovacího řetězce, například `Connection` pro [Trigger služby Blob Storage](../articles/azure-functions/functions-bindings-storage-blob.md#trigger---configuration). U těchto vlastností potřebujete nastavení aplikace definované v `Values` poli. <br/>[`AzureWebJobsStorage`] je požadované nastavení aplikace pro triggery jiné než HTTP. <br/>Verze 2. x a vyšší modulu runtime funkcí vyžaduje nastavení [`FUNCTIONS_WORKER_RUNTIME`], které je vygenerováno pro váš projekt pomocí základních nástrojů. <br/> Pokud máte [emulátor úložiště Azure](../articles/storage/common/storage-use-emulator.md) nainstalovaný místně a nastavili jste [`AzureWebJobsStorage`] na `UseDevelopmentStorage=true`, nástroj Core Tools používá emulátor. Emulátor je užitečný během vývoje, ale před nasazením je třeba otestovat pomocí skutečného připojení úložiště.<br/> Hodnoty musí být řetězce, nikoli objekty nebo pole JSON. Názvy nastavení nemůžou obsahovat dvojtečku (`:`) nebo dvojité podtržení (`__`). Tyto znaky jsou vyhrazeny modulem runtime.  |
| **`Host`** | Nastavení v této části přizpůsobuje hostitelský proces Functions, když spouštíte projekty místně. Tato nastavení jsou oddělená od nastavení Host. JSON, která platí také při spuštění projektů v Azure. |
| **`LocalHttpPort`** | Nastaví výchozí port, který se používá při spuštění místního hostitele Functions (`func host start` a `func run`). Možnost příkazového řádku `--port` má přednost před tímto nastavením. |
| **`CORS`** | Definuje zdroje povolené pro [sdílení prostředků mezi zdroji (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Zdroje jsou zadány jako čárkami oddělený seznam bez mezer. Je podporována znaková hodnota (\*), která umožňuje požadavky z libovolného původu. |
| **`CORSCredentials`** |  Pokud je nastaveno na `true`, umožňuje `withCredentials` požadavky. |
| **`ConnectionStrings`** | Kolekce. Tuto kolekci nepoužívejte pro připojovací řetězce používané vašimi vazbami funkcí. Tato kolekce je používána pouze rozhraními, které obvykle získávají připojovací řetězce z oddílu `ConnectionStrings` konfiguračního souboru, například [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Připojovací řetězce v tomto objektu jsou přidány do prostředí s typem zprostředkovatele [System. data. SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx). Položky v této kolekci se nepublikují do Azure s dalšími nastaveními aplikací. Tyto hodnoty musíte explicitně přidat do kolekce `Connection strings` nastavení aplikace Function App. Pokud vytváříte [`SqlConnection`](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) v kódu funkce, měli byste uložit hodnotu připojovacího řetězce s dalšími připojeními v **nastavení aplikace** na portálu. |

[`AzureWebJobsStorage`]: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
