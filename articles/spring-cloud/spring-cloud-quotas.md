---
title: Plány a kvóty služeb pro Azure jaře Cloud
description: Další informace o kvótách služby a plánech služeb pro jarní cloud Azure
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 496f2e812a102e85fea92a535552daaaadf5f31e
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92093425"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Kvóty a plány služeb pro jarní Cloud v Azure

**Tento článek se týká:** ✔️ Java ✔️ C #

Všechny služby Azure nastavily výchozí limity a kvóty pro prostředky a funkce.   Azure jaře Cloud nabízí dvě cenové úrovně: Basic a Standard. V tomto článku budeme podrobná omezení pro obě úrovně.

## <a name="azure-spring-cloud-service-tiers-and-limits"></a>Omezení a úrovně služeb cloudu Azure

| Prostředek | Basic | Standard
------- | ------- | -------
Virtuální procesory | 1 na instanci služby | 4 na instanci služby
Paměť | 2 GB na instanci služby | 8 GB na instanci služby
Instance služby Azure jaře Cloud Service na oblast na předplatné | 10 | 10
Celkový počet instancí aplikace na jednu instanci cloudové služby Azure na jaře | 25 | 500
Trvalé svazky | 1 GB/aplikace x 10 | aplikace 50 GB/aplikace x 10

## <a name="next-steps"></a>Další kroky

Některá výchozí omezení lze zvýšit. Pokud vaše instalace vyžaduje zvýšení, [vytvořte žádost o podporu](../azure-portal/supportability/how-to-create-azure-support-request.md).