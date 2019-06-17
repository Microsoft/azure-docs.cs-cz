---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2019
ms.openlocfilehash: f4925401235aedb341a7e29ca36b079126647f7b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66124330"
---
## <a name="validate-that-a-container-is-running"></a>Ověřit, jestli je spuštěná kontejneru 

Existuje několik způsobů, jak ověřit, že je kontejner spuštěný. 

|Žádost|Účel|
|--|--|
|`http://localhost:5000/`|Poskytuje kontejner domovskou stránku.|
|`http://localhost:5000/status`|Vyžádáno with GET, ověřte, že je kontejner spuštěný bez způsobení dotazu koncový bod. Tento požadavek může být použit pro Kubernetes [sondy aktivity a připravenosti](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/).|
|`http://localhost:5000/swagger`|Kontejner, poskytuje kompletní dokumentaci pro koncové body a `Try it now` funkce. Pomocí této funkce můžete zadat nastavení do webové formuláře HTML a provést dotaz bez nutnosti psát jakýkoli kód. Po dotaz vrátí, poskytuje příkazu CURL k příkladu prokázat, že hlavičky protokolu HTTP a text formátu, který vyžaduje. |

![Domovská stránka kontejneru](./media/cognitive-services-containers-api-documentation/container-webpage.png)
