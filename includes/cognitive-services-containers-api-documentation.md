---
author: IEvangelist
ms.author: dapine
ms.date: 08/22/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 081155c8984f39cc9cc8e905eb108c07dee98d2e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "70034433"
---
## <a name="validate-that-a-container-is-running"></a>Ověření, jestli je kontejner spuštěný 

Existuje několik způsobů, jak ověřit, zda je kontejner spuštěn. Vyhledejte *externí IP* adresu a vystavený port příslušného kontejneru a otevřete svůj oblíbený webový prohlížeč. K ověření, že je kontejner spuštěný, použijte různé adresy URL žádostí níže. Níže uvedené příklady adres URL žádostí se `http://localhost:5000`můžou lišit, ale váš konkrétní kontejner se může lišit. Pamatujte na to, že budete spoléhat na *externí IP* adresu vašeho kontejneru a vystavený port.

| Adresa URL požadavku | Účel |
|--|--|
| `http://localhost:5000/` | Kontejner poskytuje domovskou stránku. |
| `http://localhost:5000/status` | Požadováno pomocí HTTP GET, aby se ověřilo, jestli je kontejner spuštěný, aniž by to způsobilo dotaz na koncový bod. Tento požadavek se dá použít k Kubernetesi [živých a připravených sond](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/). |
| `http://localhost:5000/swagger` | Kontejner poskytuje úplnou sadu dokumentace pro koncové body a funkci **vyzkoušet si ji** . Pomocí této funkce můžete zadat nastavení do webového formuláře HTML a vytvořit dotaz bez nutnosti psát jakýkoli kód. Jakmile se dotaz vrátí, je k dispozici vzorový příkaz SLOŽENÉho příkazu, který předvádí hlavičku protokolu HTTP a požadovaný formát textu. |

![Domovská stránka kontejneru](./media/cognitive-services-containers-api-documentation/container-webpage.png)
