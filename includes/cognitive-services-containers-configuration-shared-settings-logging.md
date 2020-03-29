---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 873fd8cbc211f098c93b8fb3fbe701e4a34d8487
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "68320517"
---
Nastavení `Logging` spravují ASP.NET podporu protokolování jádra pro váš kontejner. Můžete použít stejné nastavení konfigurace a hodnoty pro váš kontejner, který používáte pro ASP.NET základní aplikace. 

Kontejner podporuje následující zprostředkovatele protokolování:

|Poskytovatel|Účel|
|--|--|
|[Konzola](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)|Zprostředkovatel protokolování ASP.NET Core. `Console` Jsou podporovány všechny ASP.NET nastavení konfigurace jádra a výchozí hodnoty pro tohoto poskytovatele protokolování.|
|[Ladění](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)|Zprostředkovatel protokolování ASP.NET Core. `Debug` Jsou podporovány všechny ASP.NET nastavení konfigurace jádra a výchozí hodnoty pro tohoto poskytovatele protokolování.|
|[Disk](#disk-logging)|Zprostředkovatel protokolování JSON. Tento poskytovatel protokolování zapisuje data protokolu do výstupního připojení.|

Tento příkaz kontejneru ukládá informace o protokolování ve formátu JSON do výstupního připojení:

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
Logging:Disk:Format=json
```

Tento příkaz kontejneru zobrazuje informace o `dbug`ladění s předponou , když je kontejner spuštěn:

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
Logging:Console:LogLevel:Default=Debug
```

### <a name="disk-logging"></a>Protokolování disku

Poskytovatel `Disk` protokolování podporuje následující nastavení konfigurace:

| Name (Název) | Datový typ | Popis |
|------|-----------|-------------|
| `Format` | Řetězec | Výstupní formát pro soubory protokolu.<br/> **Poznámka:** Tato hodnota musí `json` být nastavena tak, aby umožňovala zprostředkovatele protokolování. Pokud je tato hodnota zadána bez zadání výstupního připojení při vytváření instancí kontejneru, dojde k chybě. |
| `MaxFileSize` | Integer | Maximální velikost souboru protokolu v megabajtech (MB). Pokud velikost aktuálního souboru protokolu splňuje nebo překračuje tuto hodnotu, je nový soubor protokolu spuštěn zprostředkovatelem protokolování. Pokud -1 je zadán, velikost souboru protokolu je omezena pouze maximální velikost souboru, pokud existuje, pro výstup připojení. Výchozí hodnota je 1. |

Další informace o konfiguraci podpory ASP.NET protokolování jádra naleznete v [tématu Nastavení konfigurace souboru](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1).

