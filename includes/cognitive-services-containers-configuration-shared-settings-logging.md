---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: a2a5935079a339e85713e9cbcd0f32c211cabbb5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729851"
---
`Logging`Nastavení spravovat ASP.NET Core podporu protokolování pro váš kontejner. Můžete použít stejné nastavení konfigurace a hodnoty pro váš kontejner, který používáte pro aplikaci ASP.NET Core. 

Kontejner podporuje následující zprostředkovatele protokolování:

|Poskytovatel|Účel|
|--|--|
|[Konzola](/aspnet/core/fundamentals/logging/#console-provider)|`Console`Zprostředkovatel protokolování ASP.NET Core. Všechna nastavení konfigurace ASP.NET Core a výchozí hodnoty pro tohoto zprostředkovatele protokolování jsou podporovány.|
|[Ladění](/aspnet/core/fundamentals/logging/#debug-provider)|`Debug`Zprostředkovatel protokolování ASP.NET Core. Všechna nastavení konfigurace ASP.NET Core a výchozí hodnoty pro tohoto zprostředkovatele protokolování jsou podporovány.|
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

Další informace o konfiguraci podpory protokolování ASP.NET Core najdete v tématu [nastavení souboru konfigurace](/aspnet/core/fundamentals/logging/).