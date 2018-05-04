---
title: Azure doporučení služby Advisor výkonu | Microsoft Docs
description: Použití Advisor k optimalizaci výkonu vašich Azure nasazení.
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: 00abb5aafc6f3aec2e2dd7326a307bee74d97cc1
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="advisor-performance-recommendations"></a>Poradce při hodnocení výkonu doporučení

Azure doporučení výkonu služby Advisor napomáhají, a zvýšit rychlost a reakce důležitých podnikových aplikací. Výkon doporučení služby Advisor můžete získat **výkonu** Advisor řídicího panelu.

## <a name="improve-database-performance-with-sql-db-advisor"></a>Zlepšení výkonu databáze službou SQL DB Advisor

Advisor vám poskytne konzistentní, konsolidované zobrazení doporučení pro všechny prostředky Azure. Integruje se službou Advisor databáze SQL, aby vám doporučení pro zlepšení výkonu databáze SQL Azure. Poradce pro funkci SQL Database vyhodnocuje analýzou historii využití výkon vaší databáze SQL Azure. Potom nabízí doporučení, která jsou nejvhodnější pro spuštění typické zatížení databáze. 

> [!NOTE]
> Doporučení získáte databáze musí obsahovat o týden využití, a v daném týdnu musí být některé konzistentní aktivity. Poradce pro databáze SQL můžete optimalizovat snadněji konzistentní dotazu v případě vzorů než pro náhodné shluky aktivity.

Další informace o službě Advisor databáze SQL najdete v tématu [Poradce pro funkci SQL Database](https://azure.microsoft.com/documentation/articles/sql-database-advisor/).

## <a name="improve-redis-cache-performance-and-reliability"></a>Zlepšení výkonu Redis Cache a spolehlivosti

Advisor identifikuje instance služby Redis Cache kde výkon může být nepříznivě ovlivněn velké množství paměti, zatížení serveru, šířky pásma sítě nebo velký počet připojení klientů. Osvědčené postupy Advisor také poskytuje doporučení, která umožňuje vyhnout se možným problémům. Další informace o doporučení Redis Cache najdete v tématu [Redis Cache Advisor](https://azure.microsoft.com/documentation/articles/cache-configure/#redis-cache-advisor).


## <a name="improve-app-service-performance-and-reliability"></a>Zlepšení výkonu služby App Service a spolehlivosti

Azure Advisor integruje doporučení pro zlepšení prostředí aplikační služby a zjišťování možnosti příslušné platformy. Příklady App Services doporučení:
* Zjišťování instancí, kde se pomocí aplikace moduly runtime s možnostmi zmírnění vyčerpání paměti nebo prostředků procesoru.
* Zjišťování instancí, které tyto prostředky jako webové aplikace a databáze může zvýšit výkon a nižší náklady. 

Další informace o App Services doporučení najdete v tématu [osvědčené postupy pro službu Azure App Service](https://azure.microsoft.com/documentation/articles/app-service-best-practices/).

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Jak získat přístup k výkonu doporučení v Advisor

1. Přihlaste se k [portál Azure](https://portal.azure.com)a pak otevřete [Advisor](https://aka.ms/azureadvisordashboard).

2.  Na řídicím panelu služby Advisor, klikněte na **výkonu** kartě.

## <a name="next-steps"></a>Další postup

Další informace o doporučení služby Advisor najdete v tématu:

* [Úvod do služby Advisor](advisor-overview.md)
* [Začínáme se službou Advisor](advisor-get-started.md)
* [Náklady na doporučení služby Advisor](advisor-performance-recommendations.md)
* [Doporučení pro vysokou dostupnost služby Advisor](advisor-high-availability-recommendations.md)
* [Doporučení zabezpečení Advisor](advisor-security-recommendations.md)

