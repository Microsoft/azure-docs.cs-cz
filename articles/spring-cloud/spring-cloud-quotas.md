---
title: Plány a kvóty služeb pro Azure jaře Cloud
description: Další informace o kvótách služby a plánech služeb pro jarní cloud Azure
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: jeconnoc
ms.openlocfilehash: 0518b13ea228b4834a095a9bf126b131e70a5f45
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851549"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Kvóty a plány služeb pro jarní Cloud v Azure

Všechny služby Azure nastavily výchozí limity a kvóty pro prostředky a funkce.  Během období Preview nabízí Azure jaře Cloud jenom jeden plán služby.

Tento článek podrobně popisuje kvóty služeb nabízené během období Preview.

## <a name="azure-spring-cloud-service-tiers-and-quotas"></a>Úrovně služeb a kvóty Azure pro jaře Cloud Service

Během období Preview nabízí Azure jaře Cloud jenom jednu úroveň služby.

Prostředek | Částka
------- | -------
vCPU | 4 na instanci služby
Paměť | 8 GBytes na instanci služby
Instance služby Azure jaře Cloud Service na oblast na předplatné | 10
Celkový počet instancí aplikace na jednu instanci cloudové služby Azure na jaře | 500
Celkový počet instancí aplikace na jarní aplikaci | 20
Trvalé svazky | 10 × 50 GBytes

Když se dostanete k kvótě, obdržíte chybu 400 s upozorněním, že "kvóta překročila limit pro předplatné *vašeho* předplatného v oblasti *, kde se vytváří vaše jarní cloudová služba Azure*.

## <a name="next-steps"></a>Další kroky

Některé výchozí limity a kvóty je možné zvýšit. Pokud váš prostředek vyžaduje zvýšení, [vytvořte žádost o podporu](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request).
