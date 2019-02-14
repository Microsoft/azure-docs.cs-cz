---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: aa93fb9779e6cda41df52f7979e42348948544ea
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56246799"
---
`Logging` Nastavení spravovat podporu protokolování ASP.NET Core pro váš kontejner. Pro váš kontejner, který používáte pro aplikace ASP.NET Core můžete použít stejný konfigurační nastavení a hodnoty. 

Podporuje následující zprostředkovatele protokolování kontejneru:

|Poskytovatel|Účel|
|--|--|
|[Console](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)|ASP.NET Core `Console` zprostředkovatele. Všechna nastavení konfigurace ASP.NET Core a výchozí hodnoty pro tohoto zprostředkovatele protokolování jsou podporovány.|
|[Ladění](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)|ASP.NET Core `Debug` zprostředkovatele. Všechna nastavení konfigurace ASP.NET Core a výchozí hodnoty pro tohoto zprostředkovatele protokolování jsou podporovány.|
|[Disk](#disk-logging)|JSON zprostředkovatele. Tento zprostředkovatel protokolování zapíše data protokolu připojení výstupu.|

### <a name="disk-logging"></a>Disk protokolování

`Disk` Protokolování zprostředkovatel podporuje následující nastavení:  

| Název | Typ dat | Popis |
|------|-----------|-------------|
| `Format` | Řetězec | Výstupní formát souborů protokolu.<br/> **Poznámka:** Tato hodnota musí být nastavená na `json` povolit zprostředkovatele. Pokud tato hodnota je spustit bez úkolu připojení výstupu při vytvoření instance kontejneru, dojde k chybě. |
| `MaxFileSize` | Integer | Maximální velikost v megabajtech (MB), soubor protokolu. Když velikost aktuálního souboru protokolu splňuje nebo překročí tuto hodnotu, nový soubor protokolu je spuštěn poskytovatel protokolování. Pokud není zadána hodnota -1, velikost souboru protokolu je omezen pouze maximální velikost souboru, pokud existuje, pro výstupní připojení. Výchozí hodnota je 1. |

Další informace o konfiguraci protokolování podpora ASP.NET Core najdete v tématu [konfigurační soubor nastavení](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1).
