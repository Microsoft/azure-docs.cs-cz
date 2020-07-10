---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: 1c2196f1f834002b76dbea555b54a5162655ec1c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "77205749"
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
| **`IsEncrypted`** | Pokud je toto nastavení nastaveno na hodnotu `true` , všechny hodnoty budou šifrovány pomocí klíče místního počítače. Používá se s `func settings` příkazy. Výchozí hodnota je `false` . |
| **`Values`** | Pole nastavení aplikace a připojovacích řetězců, které se použijí, když je projekt spuštěn místně. Tyto páry klíč-hodnota (řetězcové řetězce) odpovídají nastavení aplikace ve vaší aplikaci Function App v Azure, jako je [`AzureWebJobsStorage`] . Mnoho triggerů a vazeb má vlastnost, která odkazuje na nastavení aplikace připojovacího řetězce, jako `Connection` je například [Trigger služby Blob Storage](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#configuration). U těchto vlastností potřebujete nastavení aplikace definované v poli `Values` . <br/>[`AzureWebJobsStorage`]je požadované nastavení aplikace pro triggery jiné než HTTP. <br/>Verze 2. x a vyšší modulu runtime funkcí vyžaduje `FUNCTIONS_WORKER_RUNTIME` nastavení [], které je pro váš projekt vygenerované základními nástroji. <br/> Pokud máte [emulátor úložiště Azure](../articles/storage/common/storage-use-emulator.md) nainstalovaný místně a nastavíte [`AzureWebJobsStorage`] na `UseDevelopmentStorage=true` , základní nástroje používají emulátor. Emulátor je užitečný během vývoje, ale před nasazením je třeba otestovat pomocí skutečného připojení úložiště.<br/> Hodnoty musí být řetězce, nikoli objekty nebo pole JSON. Názvy nastavení nemůžou obsahovat dvojtečku ( `:` ) nebo dvojitou vlnovku ( `__` ). Tyto znaky jsou vyhrazeny modulem runtime.  |
| **`Host`** | Nastavení v této části přizpůsobuje hostitelský proces Functions, když spouštíte projekty místně. Tato nastavení jsou oddělená od host.jsnastavení, která platí i při spuštění projektů v Azure. |
| **`LocalHttpPort`** | Nastaví výchozí port, který se používá při spuštění místního hostitele funkcí ( `func host start` a `func run` ). `--port`Možnost příkazového řádku má přednost před tímto nastavením. |
| **`CORS`** | Definuje zdroje povolené pro [sdílení prostředků mezi zdroji (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Zdroje jsou zadány jako čárkami oddělený seznam bez mezer. Je podporována znaková hodnota ( \* ), která umožňuje požadavky z libovolného původu. |
| **`CORSCredentials`** |  Pokud je nastavena na `true` , umožňuje `withCredentials` požadavky. |
| **`ConnectionStrings`** | Kolekce. Tuto kolekci nepoužívejte pro připojovací řetězce používané vašimi vazbami funkcí. Tato kolekce je používána pouze rozhraními, která obvykle získávají připojovací řetězce z `ConnectionStrings` oddílu konfiguračního souboru, například [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Připojovací řetězce v tomto objektu jsou přidány do prostředí s typem zprostředkovatele [System. data. SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx). Položky v této kolekci se nepublikují do Azure s dalšími nastaveními aplikací. Tyto hodnoty musíte explicitně přidat do `Connection strings` kolekce nastavení aplikace Function App. Pokud vytváříte [`SqlConnection`](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) v kódu funkce, měli byste uložit hodnotu připojovacího řetězce s ostatními připojeními v **nastavení aplikace** na portálu. |

[AzureWebJobsStorage]: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
