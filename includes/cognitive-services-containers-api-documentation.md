---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2019
ms.openlocfilehash: 94e95864d8bac2d6dc0ff690a2a8f53bd2db5a40
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2019
ms.locfileid: "58522502"
---
## <a name="validate-container-is-running"></a>Ověřit spuštění kontejneru 

Existuje několik způsobů, jak ověřit kontejneru běží: 

|Žádost|Účel|
|--|--|
|`http://localhost:5000/`|Poskytuje kontejner domovskou stránku.|
|`http://localhost:5000/status`|Vyžádaný with GET, ověření kontejneru běží bez způsobení dotazu koncový bod. To lze použít pro Kubernetes [sondy aktivity a připravenosti](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/).|
|`http://localhost:5000/swagger`|Kontejner, poskytuje kompletní dokumentaci pro koncové body a také `Try it now` funkce. Tato funkce umožňuje zadat nastavení do webového formuláře HTML a proveďte dotaz bez nutnosti psát jakýkoli kód. Jakmile je k dispozici dotaz vrací hodnotu, například příkaz CURL k předvedení hlavičky protokolu HTTP a textu formátu potřebném. |

![Domovská stránka kontejneru](./media/cognitive-services-containers-api-documentation/container-webpage.png)
