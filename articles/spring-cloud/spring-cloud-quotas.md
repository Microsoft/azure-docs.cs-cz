---
title: Plány a kvóty služeb pro Azure jaře Cloud
description: Další informace o kvótách služby a plánech služeb pro jarní cloud Azure
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 9/27/2019
ms.author: jeconnoc
ms.openlocfilehash: 89934ee0ab0c901a904a1a5ac2fb620185571a23
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038779"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Kvóty a plány služeb pro jarní Cloud v Azure

Všechny služby Azure nastavily výchozí limity a kvóty pro prostředky a funkce.  Během období Preview nabízí Azure jaře Cloud jenom jeden plán služby.

Tento článek podrobně popisuje kvóty služeb nabízené během období Preview.

## <a name="azure-spring-cloud-service-tiers-and-quotas"></a>Úrovně služeb a kvóty Azure pro jaře Cloud Service

Během období Preview nabízí Azure jaře Cloud jenom jednu úroveň služby.

Prostředek | Částka
------- | -------
vCPU | 4
Paměť | 8 GBytes
Předplatné Azure jaře Cloud | 1\. místo
Instance služby Azure jaře Cloud Service na oblast na předplatné | 2
Celkový počet instancí aplikace na jednu instanci cloudové služby Azure na jaře | 50
Celkový počet instancí aplikace na jarní aplikaci | 20
Trvalé svazky | 10 × 50 GBytes

Když se dostanete k kvótě, obdržíte chybu 400 s upozorněním, že "kvóta překročila limit pro předplatné *vašeho* předplatného v oblasti *, kde se vytváří vaše jarní cloudová služba Azure*.

## <a name="next-steps"></a>Další kroky

Některé výchozí limity a kvóty je možné zvýšit. Pokud váš prostředek vyžaduje zvýšení, pošlete nám svůj požadavek: azure-spring-cloud@service.microsoft.com.
