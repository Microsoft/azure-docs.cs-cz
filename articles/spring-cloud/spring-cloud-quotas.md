---
title: Plány a kvóty služeb pro Azure jaře Cloud
description: Další informace o kvótách služby a plánech služeb pro jarní cloud Azure
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 20ebeb23fe09ba4fd70a724828afadfaa3901abd
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "101095658"
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
> Sazby uvedené pro celkový počet instancí aplikace na instanci služby platí pro aplikace nebo nasazení v zastaveném stavu. Odstraňte prosím aplikace nebo nasazení, která se nepoužívají.

## <a name="next-steps"></a>Další kroky

Některá výchozí omezení lze zvýšit. Pokud vaše instalace vyžaduje zvýšení, [vytvořte žádost o podporu](../azure-portal/supportability/how-to-create-azure-support-request.md).
