---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 00cc63f53388ab7bea05a0b55784247f63477684
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704194"
---
## <a name="validate-that-a-container-is-running"></a>Ověřit, jestli je spuštěná kontejneru 

Existuje několik způsobů, jak ověřit, že je kontejner spuštěný. 

|Žádost|Účel|
|--|--|
|`http://localhost:5000/`|Poskytuje kontejner domovskou stránku.|
|`http://localhost:5000/status`|Vyžádáno with GET, ověřte, že je kontejner spuštěný bez způsobení dotazu koncový bod. Tento požadavek může být použit pro Kubernetes [sondy aktivity a připravenosti](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/).|
|`http://localhost:5000/swagger`|Kontejner, poskytuje kompletní dokumentaci pro koncové body a `Try it now` funkce. Pomocí této funkce můžete zadat nastavení do webové formuláře HTML a provést dotaz bez nutnosti psát jakýkoli kód. Po dotaz vrátí, poskytuje příkazu CURL k příkladu prokázat, že hlavičky protokolu HTTP a text formátu, který vyžaduje. |

![Domovská stránka kontejneru](./media/cognitive-services-containers-api-documentation/container-webpage.png)
