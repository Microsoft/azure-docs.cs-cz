---
title: Plány a kvóty služeb pro Azure jaře Cloud
description: Další informace o kvótách služby a plánech služeb pro jarní cloud Azure
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 8a7ba3c3b9c19b2084b6892b55ac417da38ab047
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "76278893"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Kvóty a plány služeb pro jarní Cloud v Azure

Všechny služby Azure nastavily výchozí limity a kvóty pro prostředky a funkce.  Během období Preview nabízí Azure jaře Cloud jenom jeden plán služby.

Tento článek podrobně popisuje kvóty služeb nabízené během období Preview.

## <a name="azure-spring-cloud-service-tiers-and-quotas"></a>Úrovně služeb a kvóty Azure pro jaře Cloud Service

Během období Preview nabízí Azure jaře Cloud jenom jednu úroveň služby.

Prostředek | Částka
------- | -------
Virtuální procesory | 4 na instanci služby
Memory (Paměť) | 8 GBytes na instanci služby
Instance služby Azure jaře Cloud Service na oblast na předplatné | 10
Celkový počet instancí aplikace na jednu instanci cloudové služby Azure na jaře | 500
Celkový počet instancí aplikace na jarní aplikaci | 20
Trvalé svazky | 10 × 50 GBytes

Když se dostanete k kvótě, obdržíte chybu 400 s upozorněním, že "kvóta překročila limit pro předplatné *vašeho* předplatného v oblasti *, kde se vytváří vaše jarní cloudová služba Azure*.

## <a name="next-steps"></a>Další kroky

Některé výchozí limity a kvóty je možné zvýšit. Pokud váš prostředek vyžaduje zvýšení, [vytvořte žádost o podporu](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).
