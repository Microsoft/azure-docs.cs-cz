---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: 1c2196f1f834002b76dbea555b54a5162655ec1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77205749"
---
## <a name="local-settings-file"></a>Soubor místního nastavení

Soubor local.settings.json ukládá nastavení aplikací, připojovací řetězce a nastavení používaná nástroji pro místní vývoj. Nastavení v souboru local.settings.json se používá pouze v případě, že spouštěte projekty místně. Soubor místního nastavení má tuto strukturu:

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

Tato nastavení jsou podporována při místním spuštění projektů:

| Nastavení      | Popis                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | Pokud je toto `true`nastavení nastaveno na , jsou všechny hodnoty zašifrovány klíčem místního počítače. Používá `func settings` se s příkazy. Výchozí hodnota `false`je . |
| **`Values`** | Pole nastavení aplikace a připojovacích řetězců používaných v případě, že projekt běží místně. Tyto dvojice klíč-hodnota (řetězec řetězec) odpovídají nastavení aplikace ve vaší [`AzureWebJobsStorage`]aplikaci funkce v Azure, jako je . Mnoho aktivačních událostí a vazeb má vlastnost, která odkazuje `Connection` na nastavení aplikace připojovacího řetězce, například pro [aktivační událost úložiště objektů blob](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#configuration). Pro tyto vlastnosti potřebujete nastavení aplikace `Values` definované v poli. <br/>[`AzureWebJobsStorage`]je požadované nastavení aplikace pro jiné aktivační události než HTTP. <br/>Verze 2.x a vyšší funkce runtime`FUNCTIONS_WORKER_RUNTIME`vyžaduje [ ] nastavení, které je generováno pro váš projekt základní nástroje. <br/> Když máte [emulátor úložiště Azure](../articles/storage/common/storage-use-emulator.md) nainstalován místně [`AzureWebJobsStorage`] a `UseDevelopmentStorage=true`nastavíte na , Základní nástroje používá emulátor. Emulátor je užitečný během vývoje, ale před nasazením byste měli otestovat s připojením skutečného úložiště.<br/> Hodnoty musí být řetězce a nikoli JSON objekty nebo pole. Nastavení názvů nemůže obsahovat`:`dvojtečku (`__`) nebo dvojité podtržení ( ). Tyto znaky jsou vyhrazeny runtime.  |
| **`Host`** | Nastavení v této části přizpůsobit proces hostitele funkce při spuštění projektů místně. Tato nastavení jsou oddělená od nastavení host.json, které platí také při spuštění projektů v Azure. |
| **`LocalHttpPort`** | Nastaví výchozí port používaný při spuštění`func host start` `func run`místního hostitele functions ( a ). Možnost `--port` příkazového řádku má přednost před tímto nastavením. |
| **`CORS`** | Definuje počátky povolené pro [sdílení prostředků mezi zdroji (CORS).](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) Počátky jsou dodávány jako seznam oddělený čárkami bez mezer. Je podporována\*hodnota zástupných symbolů ( ), která umožňuje požadavky z libovolného původu. |
| **`CORSCredentials`** |  Pokud je `true`nastavena na , umožňuje `withCredentials` požadavky. |
| **`ConnectionStrings`** | Kolekce. Nepoužívejte tuto kolekci pro připojovací řetězce používané vazby funkce. Tato kolekce se používá pouze rozhraní, které obvykle `ConnectionStrings` získat připojovací řetězce z části konfiguračního souboru, jako [je entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Připojovací řetězce v tomto objektu jsou přidány do prostředí s typem zprostředkovatele [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx). Položky v této kolekci se do Azure nepublikují s jinýmnastavením aplikace. Tyto hodnoty je nutné `Connection strings` explicitně přidat do kolekce nastavení aplikace funkce. Pokud vytváříte [`SqlConnection`](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) v kódu funkce, měli byste uložit hodnotu připojovacího řetězce s ostatními připojeními v nastavení **aplikace** na portálu. |

["AzureWebJobsStorage"]: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
