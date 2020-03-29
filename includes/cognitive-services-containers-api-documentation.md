---
author: IEvangelist
ms.author: dapine
ms.date: 08/22/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 081155c8984f39cc9cc8e905eb108c07dee98d2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "70034433"
---
## <a name="validate-that-a-container-is-running"></a>Ověření, zda je spuštěn kontejner 

Existuje několik způsobů, jak ověřit, že kontejner je spuštěn. Vyhledejte *externí IP* adresu a exponovaný port daného kontejneru a otevřete svůj oblíbený webový prohlížeč. Pomocí různých adres URL požadavků níže ověřte, zda je kontejner spuštěn. Níže uvedené adresy URL s `http://localhost:5000`ukázkovým požadavkem na žádosti jsou , ale konkrétní kontejner se může lišit. Mějte na paměti, že se budete spoléhat na *externí IP* adresu kontejneru a exponovaný port.

| Adresa URL požadavku | Účel |
|--|--|
| `http://localhost:5000/` | Kontejner poskytuje domovskou stránku. |
| `http://localhost:5000/status` | Požadováno s HTTP GET, k ověření, že kontejner je spuštěn bez vyvolání dotazu koncového bodu. Tento požadavek lze použít pro [sondy](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)Kubernetes živosti a připravenosti . |
| `http://localhost:5000/swagger` | Kontejner poskytuje úplnou sadu dokumentace pro koncové body a **vyzkoušejte to** funkce. Pomocí této funkce můžete zadat nastavení do webového formuláře HTML a vytvořit dotaz, aniž byste museli psát jakýkoli kód. Po vrácení dotazu je k dispozici příkaz CURL, který předvede hlavičky PROTOKOLU HTTP a požadovaný formát těla. |

![Domovská stránka kontejneru](./media/cognitive-services-containers-api-documentation/container-webpage.png)
