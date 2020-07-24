---
title: Plány a kvóty služeb pro Azure jaře Cloud
description: Další informace o kvótách služby a plánech služeb pro jarní cloud Azure
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: cda22c63a70c5121e6a6972c66bdc0a4bb5158fc
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87089460"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Kvóty a plány služeb pro jarní Cloud v Azure

Všechny služby Azure nastavily výchozí limity a kvóty pro prostředky a funkce.   Azure jaře Cloud nabízí dvě cenové úrovně: Basic a Standard. V tomto článku budeme podrobná omezení pro obě úrovně.

## <a name="azure-spring-cloud-service-tiers-and-limits"></a>Omezení a úrovně služeb cloudu Azure

| Prostředek | Basic | Standard
------- | ------- | -------
Virtuální procesory | 1 na instanci služby | 4 na instanci služby
Paměť | 2 GB na instanci služby | 8 GB na instanci služby
Instance služby Azure jaře Cloud Service na oblast na předplatné | 10 | 10
Celkový počet instancí aplikace na jednu instanci cloudové služby Azure na jaře | 25 | 500
Trvalé svazky | 1 GB/aplikace x 10 | aplikace 50 GB/aplikace x 10


Během období Preview nabízí Azure jaře Cloud jenom jednu úroveň služby. Pokud dosáhnete limitu, obdržíte chybu 400 s názvem "kvóta překročila limit pro předplatné *vašeho* předplatného v oblasti *, kde je vytvořena vaše jarní cloudová služba Azure*.

## <a name="next-steps"></a>Další kroky

Některá výchozí omezení lze zvýšit. Pokud vaše instalace vyžaduje zvýšení, [vytvořte žádost o podporu](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).
