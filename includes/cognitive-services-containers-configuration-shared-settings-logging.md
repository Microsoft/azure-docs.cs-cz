---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 2bfb700ac5c220b780c05c8d415a4506c7a2f871
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96001149"
---
`Logging`Nastavení spravovat ASP.NET Core podporu protokolování pro váš kontejner. Můžete použít stejné nastavení konfigurace a hodnoty pro váš kontejner, který používáte pro aplikaci ASP.NET Core. 

Kontejner podporuje následující zprostředkovatele protokolování:

|Poskytovatel|Účel|
|--|--|
|[Konzola](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)|`Console`Zprostředkovatel protokolování ASP.NET Core. Všechna nastavení konfigurace ASP.NET Core a výchozí hodnoty pro tohoto zprostředkovatele protokolování jsou podporovány.|
|[Ladění](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)|`Debug`Zprostředkovatel protokolování ASP.NET Core. Všechna nastavení konfigurace ASP.NET Core a výchozí hodnoty pro tohoto zprostředkovatele protokolování jsou podporovány.|
|[Disk](#disk-logging)|Zprostředkovatel protokolování JSON. Tento zprostředkovatel protokolování zapisuje data protokolu do výstupního připojení.|

Tento příkaz kontejneru ukládá do výstupního připojení informace o protokolování ve formátu JSON:

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

Tento příkaz kontejneru zobrazuje informace o ladění s předponou `dbug` , zatímco je spuštěný kontejner:

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

`Disk`Zprostředkovatel protokolování podporuje následující nastavení konfigurace:

| Name | Datový typ | Popis |
|------|-----------|-------------|
| `Format` | Řetězec | Výstupní formát pro soubory protokolu.<br/> **Poznámka:** Tato hodnota musí být nastavena na hodnotu `json` pro povolení poskytovatele protokolování. Pokud je tato hodnota zadána bez zadání výstupního připojení při vytváření instance kontejneru, dojde k chybě. |
| `MaxFileSize` | Integer | Maximální velikost souboru protokolu v megabajtech (MB). Když velikost aktuálního souboru protokolu splňuje nebo překračuje tuto hodnotu, spustí zprostředkovatel protokolování nový soubor protokolu. Je-li zadána hodnota-1, velikost souboru protokolu je omezena pouze maximální velikostí souboru (pokud existuje) pro připojení pro výstup. Výchozí hodnota je 1. |

Další informace o konfiguraci podpory protokolování ASP.NET Core najdete v tématu [nastavení souboru konfigurace](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1).