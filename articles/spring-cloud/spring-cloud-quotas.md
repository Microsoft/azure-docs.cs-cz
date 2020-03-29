---
title: Plány služeb a kvóty pro Azure Spring Cloud
description: Informace o kvótách služeb a plánech služeb pro Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 8a7ba3c3b9c19b2084b6892b55ac417da38ab047
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278893"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Kvóty a plány služeb pro Azure Spring Cloud

Všechny služby Azure nastavují výchozí limity a kvóty pro prostředky a funkce.  Azure Spring Cloud nabízí během období předběžné verze jenom jeden plán služeb.

Tento článek podrobně popisuje kvóty služeb nabízené během aktuálního období náhledu.

## <a name="azure-spring-cloud-service-tiers-and-quotas"></a>Úrovně a kvóty služeb Azure Spring Cloud

Během období náhledu Azure Spring Cloud nabízí jenom jednu úroveň služeb.

Prostředek | Částka
------- | -------
Virtuální procesory | 4 na instanci služby
Memory (Paměť) | 8 Bajtů na instanci služby
Instance služeb Azure Spring Cloud na oblast na předplatné | 10
Celkový počet instancí aplikací na instanci služby Azure Spring Cloud | 500
Celkový počet instancí aplikací na jarní aplikaci | 20
Trvalé svazky | 10 x 50 gbajtů

Když dosáhnete kvóty, zobrazí se chyba 400 s nápisem: "Kvóta překračuje limit předplatného *pro vaše předplatné* v *oblasti, kde se vaše služba Azure Spring Cloud vytvoří*.

## <a name="next-steps"></a>Další kroky

Některé výchozí limity a kvóty je možné zvýšit. Pokud váš prostředek vyžaduje zvýšení, [vytvořte žádost o podporu](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).
