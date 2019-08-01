---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: fef5cd38461fec67790fb67faf8e466d46b247fc
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68669583"
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
| **`IsEncrypted`** | Pokud je toto nastavení nastaveno na `true`hodnotu, všechny hodnoty budou šifrovány pomocí klíče místního počítače. Používá se `func settings` s příkazy. Výchozí hodnota je `false`. |
| **`Values`** | Pole nastavení aplikace a připojovacích řetězců, které se použijí, když je projekt spuštěn místně. Tyto páry klíč-hodnota (řetězcové řetězce) odpovídají nastavení aplikace ve vaší aplikaci Function App v Azure, jako [`AzureWebJobsStorage`]je. Mnoho triggerů a vazeb má vlastnost, která odkazuje na nastavení aplikace připojovacího řetězce, `Connection` jako je například [Trigger služby Blob Storage](../articles/azure-functions/functions-bindings-storage-blob.md#trigger---configuration). U těchto vlastností potřebujete nastavení aplikace definované v `Values` poli. <br/>[`AzureWebJobsStorage`]je požadované nastavení aplikace pro triggery jiné než HTTP. <br/>Verze 2. x modulu runtime Functions vyžaduje nastavení [`FUNCTIONS_WORKER_RUNTIME`], které je vygenerováno pro váš projekt pomocí základních nástrojů. <br/> Pokud máte [emulátor úložiště Azure](../articles/storage/common/storage-use-emulator.md) nainstalovaný místně a nastavíte [`AzureWebJobsStorage`] na `UseDevelopmentStorage=true`, základní nástroje používají emulátor. Emulátor je užitečný během vývoje, ale před nasazením je třeba otestovat pomocí skutečného připojení úložiště.<br/> Hodnoty musí být řetězce, nikoli objekty nebo pole JSON. Názvy nastavení nemůžou obsahovat dvojtečku`:`() nebo dvojitou vlnovku (`__`). Tyto znaky jsou vyhrazeny modulem runtime.  |
| **`Host`** | Nastavení v této části přizpůsobuje hostitelský proces Functions, když spouštíte projekty místně. Tato nastavení jsou oddělená od nastavení Host. JSON, která platí také při spuštění projektů v Azure. |
| **`LocalHttpPort`** | Nastaví výchozí port, který se používá při spuštění místního hostitele funkcí`func host start` ( `func run`a). Možnost `--port` příkazového řádku má přednost před tímto nastavením. |
| **`CORS`** | Definuje zdroje povolené pro [sdílení prostředků mezi zdroji (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Zdroje jsou zadány jako čárkami oddělený seznam bez mezer. Je podporována znaková\*hodnota (), která umožňuje požadavky z libovolného původu. |
| **`CORSCredentials`** |  Pokud je nastavena `true`na, `withCredentials` umožňuje požadavky. |
| **`ConnectionStrings`** | Kolekce. Tuto kolekci nepoužívejte pro připojovací řetězce používané vašimi vazbami funkcí. Tato kolekce je používána pouze rozhraními, která obvykle získávají připojovací řetězce z `ConnectionStrings` oddílu konfiguračního souboru, například [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Připojovací řetězce v tomto objektu jsou přidány do prostředí s typem zprostředkovatele [System. data. SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx). Položky v této kolekci se nepublikují do Azure s dalšími nastaveními aplikací. Tyto hodnoty musíte explicitně přidat do `Connection strings` kolekce nastavení aplikace Function App. Pokud vytváříte [`SqlConnection`](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) v kódu funkce, měli byste uložit hodnotu připojovacího řetězce s ostatními připojeními v **nastavení aplikace** na portálu. |

[`AzureWebJobsStorage`]: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
