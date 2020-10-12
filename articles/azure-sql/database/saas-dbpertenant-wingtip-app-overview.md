---
title: Víceklientské aplikace – příklad – Wingtip SaaS
description: Naučte se používat ukázkovou víceklientské aplikaci, která používá Azure SQL Database, příklad Wingtip SaaS
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/24/2018
ms.openlocfilehash: d291adcf53469825f32b664cb7f4ffdb0bbf244b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89441013"
---
# <a name="introduction-to-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-azure-sql-database"></a>Úvod do víceklientské aplikace SaaS, která používá model databáze na tenanta s Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Aplikace Wingtip SaaS je ukázková víceklientské aplikace. Aplikace používá pro obsluhu více tenantů vzor aplikace SaaS pro jednotlivé klienty. Aplikace prezentuje funkce Azure SQL Database, které umožňují scénáře SaaS pomocí několika vzorů návrhu a správy SaaS. Pokud chcete rychle začít pracovat, aplikace Wingtip SaaS se nasadí za méně než pět minut.

Zdrojový kód aplikace a skripty pro správu jsou k dispozici v úložišti GitHub [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) . Než začnete, přečtěte si [Obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md) ke stažení a odblokování skriptů pro správu lístků Wingtip Ticket.

## <a name="application-architecture"></a>Architektura aplikace

Aplikace Wingtip SaaS používá model databáze na tenanta. Pomocí elastických fondů SQL maximalizuje efektivitu. Pro zřizování a mapování klientů na svá data se používá databáze katalogu. Základní aplikace Wingtip SaaS používá fond se třemi ukázkovými klienty, a navíc databázi katalogu. Katalog a servery tenanta byly zřízeny s aliasy DNS. Tyto aliasy slouží k údržbě odkazů na aktivní prostředky používané aplikací Wingtip. Tyto aliasy se aktualizují tak, aby odkazovaly na prostředky obnovení v kurzech zotavení po havárii. Výsledkem řady SaaS kurzů Wingtip je přidání doplňků k počátečnímu nasazení. Zavádí se doplňky, jako jsou analytické databáze a Správa schémat mezi databázemi.


![Architektura Wingtip SaaS](./media/saas-dbpertenant-wingtip-app-overview/app-architecture.png)


Při procházení kurzů a práci s aplikací se zaměřte na vzory SaaS, které se vztahují k datové vrstvě. Jinými slovy, zaměřte se na datovou vrstvu a samotnou aplikaci neprovádějte. Princip implementace těchto SaaS vzorů je klíč k implementaci těchto vzorů ve vašich aplikacích. Zvažte také všechny nezbytné úpravy pro konkrétní obchodní požadavky.

## <a name="sql-database-wingtip-saas-tutorials"></a>Kurzy SQL Database Wingtip SaaS

Po nasazení aplikace si Prozkoumejte následující kurzy, které se sestavují při počátečním nasazení. Tyto kurzy projednávají běžné SaaS vzory, které využívají integrované funkce SQL Database, Azure synapse Analytics (dřív SQL Data Warehouse) a další služby Azure. Mezi kurzy patří skripty PowerShellu s podrobnými vysvětleními. Vysvětlení zjednodušují porozumění a implementaci stejných vzorů správy SaaS ve vašich aplikacích.


| Kurz | Description |
|:--|:--|
| [Doprovodné materiály a tipy pro SQL Database víceklientské aplikace SaaS App](saas-tenancy-wingtip-app-guidance-tips.md) | Stáhněte a spusťte PowerShellové skripty pro přípravu částí aplikace. |
|[Nasazení a zkoumání aplikace Wingtip SaaS](../../sql-database/saas-dbpertenant-get-started-deploy.md)|  Nasaďte a prozkoumejte aplikaci Wingtip SaaS s vaším předplatným Azure. |
|[Zřízení a katalog tenantů](../../sql-database/saas-dbpertenant-provision-and-catalog.md)| Naučte se, jak se aplikace připojuje ke klientům pomocí databáze katalogu a jak katalog mapuje klienty na svá data. |
|[Monitorování a Správa výkonu](../../sql-database/saas-dbpertenant-performance-monitoring.md)| Naučte se používat monitorovací funkce SQL Database a nastavovat výstrahy při překročení prahových hodnot výkonu. |
|[Monitorování pomocí protokolů služby Azure Monitor](../../sql-database/saas-dbpertenant-log-analytics.md) | Naučte se používat [protokoly Azure monitor](../../azure-monitor/log-query/log-query-overview.md) k monitorování velkých objemů prostředků napříč několika fondy. |
|[Obnovení jednoho tenanta](../../sql-database/saas-dbpertenant-restore-single-tenant.md)| Přečtěte si, jak obnovit databázi tenanta k předchozímu bodu v čase. Naučíte se také, jak obnovit do paralelní databáze, která opustí stávající databázi tenanta online. |
|[Spravovat schéma databáze tenanta](saas-tenancy-schema-management.md)| Naučte se aktualizovat schéma a aktualizovat referenční data napříč všemi databázemi tenanta. |
|[Spustit distribuované dotazy mezi klienty](saas-tenancy-cross-tenant-reporting.md) | Vytvořte databázi ad hoc Analytics a spouštějte distribuované dotazy v reálném čase napříč všemi klienty.  |
|[Spustit analýzu pro extrahovaná data tenanta](saas-tenancy-tenant-analytics.md) | Extrahujte data tenanta do analytické databáze nebo datového skladu pro offline analytické dotazy. |


## <a name="next-steps"></a>Další kroky

- [Obecné pokyny a tipy pro nasazení a používání aplikace SaaS lístky Wingtip Tickets](saas-tenancy-wingtip-app-guidance-tips.md)
- [Nasazení aplikace Wingtip SaaS](../../sql-database/saas-dbpertenant-get-started-deploy.md)
