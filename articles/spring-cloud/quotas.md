---
title: Plány a kvóty služeb pro Azure jaře Cloud
description: Další informace o kvótách služby a plánech služeb pro jarní cloud Azure
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: b02ccb3acb4546e08e7d58159ab9d85bca2d0eed
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878199"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Kvóty a plány služeb pro jarní Cloud v Azure

**Tento článek se týká:** ✔️ Java ✔️ C #

Všechny služby Azure nastavily výchozí limity a kvóty pro prostředky a funkce.   Azure jaře Cloud nabízí dvě cenové úrovně: Basic a Standard. V tomto článku budeme podrobná omezení pro obě úrovně.

## <a name="azure-spring-cloud-service-tiers-and-limits"></a>Omezení a úrovně služeb cloudu Azure

| Prostředek | Obor | Basic | Standard
------- | ------- | -------
Virtuální procesory | na instanci aplikace | 1 | 4
Memory (Paměť) | na instanci aplikace | 2 GB | 8 GB
Instance služby Azure jaře Cloud Service | podle oblasti na předplatné | 10 | 10
Celkový počet instancí aplikace | instance cloudové služby na jaře Azure | 25 | 500
Vlastní domény | instance cloudové služby na jaře Azure | 0 | 25 
Trvalé svazky | instance cloudové služby na jaře Azure | 1 GB/aplikace x 10 | aplikace 50 GB/aplikace x 10

> [!TIP]
> Omezení uvedená pro celkový počet instancí aplikace na instanci služby platí pro aplikace a nasazení v jakémkoli stavu, včetně stavu Zastaveno. Odstraňte prosím aplikace nebo nasazení, které se nepoužívají.

## <a name="next-steps"></a>Další kroky

Některá výchozí omezení lze zvýšit. Pokud vaše instalace vyžaduje zvýšení, [vytvořte žádost o podporu](../azure-portal/supportability/how-to-create-azure-support-request.md).
