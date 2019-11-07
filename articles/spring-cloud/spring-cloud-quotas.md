---
title: Plány a kvóty služeb pro Azure jaře Cloud
description: Další informace o kvótách služby a plánech služeb pro jarní cloud Azure
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: jeconnoc
ms.openlocfilehash: 41a2b1a7d9aa5089ba2ee73cd3c5c5c5e31f5225
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607676"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Kvóty a plány služeb pro jarní Cloud v Azure

Všechny služby Azure nastavily výchozí limity a kvóty pro prostředky a funkce.  Během období Preview nabízí Azure jaře Cloud jenom jeden plán služby.

Tento článek podrobně popisuje kvóty služeb nabízené během období Preview.

## <a name="azure-spring-cloud-service-tiers-and-quotas"></a>Úrovně služeb a kvóty Azure pro jaře Cloud Service

Během období Preview nabízí Azure jaře Cloud jenom jednu úroveň služby.

Prostředek | Částka
------- | -------
Virtuální procesory | 4
Memory (Paměť) | 8 GBytes
Předplatné Azure jaře Cloud | 1
Instance služby Azure jaře Cloud Service na oblast na předplatné | 2
Celkový počet instancí aplikace na jednu instanci cloudové služby Azure na jaře | 50
Celkový počet instancí aplikace na jarní aplikaci | 20
Trvalé svazky | 10 × 50 GBytes

Když se dostanete k kvótě, obdržíte chybu 400 s upozorněním, že "kvóta překročila limit pro předplatné *vašeho* předplatného v oblasti *, kde se vytváří vaše jarní cloudová služba Azure*.

## <a name="next-steps"></a>Další kroky

Některé výchozí limity a kvóty je možné zvýšit. Pokud váš prostředek vyžaduje zvýšení, [vytvořte žádost o podporu](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request).
