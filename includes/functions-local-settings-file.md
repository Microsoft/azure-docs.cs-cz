---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: e319356d555f26354ea29dc7be068bf6168abb17
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455152"
---
## <a name="local-settings-file"></a>Soubor místního nastavení

Souboru local.settings.json ukládá nastavení aplikace, připojovacích řetězců a nastavení používané místní vývojové nástroje. Nastavení v souboru local.settings.json používají pouze při místním spuštění. Místní nastavení soubor má následující strukturu:

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

Při místním spuštění, podporovány jsou následující nastavení:

| Nastavení      | Popis                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | Pokud je nastavena na `true`, všechny hodnoty jsou šifrované pomocí klíče místního počítače. Použít s `func settings` příkazy. Výchozí hodnota je `false`. |
| **`Values`** | Pole nastavení aplikace a připojovacích řetězců použité při místním spuštění. Tyto páry klíč hodnota (řetězec řetězec) odpovídají nastavení aplikace ve vaší aplikaci function app v Azure, jako například [ `AzureWebJobsStorage` ]. Řada triggerů a vazeb mají vlastnost, která odkazuje na nastavení aplikace řetězec připojení, jako například `Connection` pro [aktivační událost objektů Blob storage](../articles/azure-functions/functions-bindings-storage-blob.md#trigger---configuration). Pro tyto vlastnosti definované v nastavení aplikace potřebujete `Values` pole. <br/>[`AzureWebJobsStorage`] Požadovaná aplikace nastavení pro aktivační události než HTTP. <br/>Verze 2.x modul runtime služby Functions vyžaduje [`FUNCTIONS_WORKER_RUNTIME`] nastavení, který je generován základní nástroje pro váš projekt. <br/> Pokud máte [emulátoru úložiště Azure](../articles/storage/common/storage-use-emulator.md) nainstalovaný místně, můžete nastavit [ `AzureWebJobsStorage` ] k `UseDevelopmentStorage=true` a základní nástroje pomocí emulátoru. To je užitečné při vývoji, ale měli byste otestovat připojení k skutečného úložiště před nasazením.<br/> Hodnoty musí být řetězce a nikoli objekty JSON nebo pole. Názvy nastavení nesmí obsahovat dvojtečku (`:`) ani dvojitou podtržení (`__`); tyto jsou vyhrazeny modulem runtime.  |
| **`Host`** | Nastavení v této části přizpůsobit funkce hostitelský proces, při místním spuštění. Toto jsou oddělené od host.json nastavení, která se vztahují také při spuštění v Azure. |
| **`LocalHttpPort`** | Nastaví výchozí port použitý při spuštění místního hostitele funkce (`func host start` a `func run`). `--port` Možnost příkazového řádku má přednost před tuto hodnotu. |
| **`CORS`** | Určuje původ, odkud můžou pro [prostředků mezi zdroji (CORS) pro sdílení obsahu](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Zdroje jsou dodávány jako seznam oddělený čárkami bez mezer. Hodnota zástupného znaku (\*) je podporován, umožňující žádosti z původu. |
| **`CORSCredentials`** |  Nastavte na hodnotu true, chcete-li povolit `withCredentials` požadavky. |
| **`ConnectionStrings`** | Nepoužívejte připojovací řetězce, používá funkce vazby této kolekce. Tato kolekce používá pouze rozhraní, které obvykle získat připojovací řetězce z `ConnectionStrings` část konfigurační soubor, třeba [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Připojovací řetězce v tomto objektu jsou přidány do prostředí s typem zprostředkovatele [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx). Položky v této kolekci nejsou publikovány do Azure s jinými nastaveními aplikace. Musíte explicitně přidat tyto hodnoty `Connection strings` kolekce vaše nastavení aplikace function app. Pokud vytváříte [ `SqlConnection` ](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) v kódu funkce, měli byste uložit hodnotu připojovacího řetězce v **nastavení aplikace** na portálu u vašich připojení. |

[`AzureWebJobsStorage`]: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
