---
title: "Příklad víceklientského aplikace Azure SQL Database - Wingtip SaaS | Microsoft Docs"
description: "Další informace pomocí víceklientské ukázkovou aplikaci, která používá databázi SQL Azure, například adresář Wingtip SaaS"
keywords: kurz k sql database
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 11/12/2017
ms.author: sstein
ms.openlocfilehash: 451e1fc87fc5f626e78760d8cd5c4115ea02bb0d
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="introduction-to-a-multi-tenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>Úvod k aplikaci SaaS více klientů, která používá databázi za vzor klienta s databází SQL

*Wingtip SaaS* aplikace je víceklientské ukázkovou aplikaci. Aplikace používá databázi za klienta, vzor aplikací SaaS, k obsluhovat několik klientů. Aplikace umožňující prezentovat funkce databáze SQL Azure, které umožňují SaaS scénářů používání několika vzory návrhu a správu SaaS. Pro rychlé zprovoznění, nasadí aplikaci Wingtip SaaS za méně než pět minut!

Jsou k dispozici v aplikaci zdrojový kód a správu skriptů [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) úložiště GitHub. Než začnete, podívejte se [obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md) kroky, jak stáhnout a odblokování skripty správy Wingtip lístků.

## <a name="application-architecture"></a>Architektura aplikace

Aplikace Wingtip SaaS používá model databáze za klienta a elastické fondy SQL Pokud chcete maximalizovat efektivitu. Pro zřizování a mapování klientům svá data se používá databázi katalogu. Základní aplikace Wingtip SaaS používá fond s tři klienty ukázka plus databáze katalogu. Dokončení řadu SaaS Wingtip kurzy způsobit doplňky pro počáteční nasazení se zavedením analytické databáze mezidatabázové Správa schématu, atd.


![Architektura Wingtip SaaS](media/saas-dbpertenant-wingtip-app-overview/app-architecture.png)


Při průchodu přes kurzů k a práci s aplikací, je potřeba zaměřit se na vzory SaaS, které se vztahují na datové vrstvě. Jinými slovy je třeba se zaměřit na datovou vrstvu a ne na analýzu aplikace jako takové. Principy provádění těchto SaaS vzory je klíč k implementaci tyto vzory ve svých aplikacích při zvažování veškeré nezbytné úpravy pro vaše konkrétní obchodní požadavky.

## <a name="sql-database-wingtip-saas-tutorials"></a>Kurzy SaaS Wingtip databáze SQL

Po nasazení aplikace, prozkoumejte následující kurzy, které sestavení po počátečním nasazení. Tyto kurzy prozkoumejte běžných vzorů SaaS, které využít integrované funkce SQL Database, SQL Data Warehouse a jinými službami Azure. Kurzy zahrnují skripty prostředí PowerShell, s podrobné vysvětlení, které výrazně zjednodušit pochopení a implementace stejné vzorů správu SaaS ve svých aplikacích.


| Kurz | Popis |
|:--|:--|
| [Pokyny a tipů, například aplikace SaaS víceklientské Azure SQL Database](saas-tenancy-wingtip-app-guidance-tips.md) | **ZAČNĚTE ZDE!** Stažení a spuštění skriptů prostředí PowerShell pro přípravu částí aplikace. |
|[Nasazení a prozkoumejte Wingtip SaaS aplikace](saas-dbpertenant-get-started-deploy.md)|  Nasazení a prozkoumejte aplikace Wingtip SaaS k předplatnému Azure. |
|[Zřizování a katalog klientů](saas-dbpertenant-provision-and-catalog.md)| Zjistěte, jak se aplikace připojí klientům pomocí databáze katalogu a jak katalogu mapuje klienty na svá data. |
|[Sledování a správa výkonu](saas-dbpertenant-performance-monitoring.md)| Zjistěte, jak používat monitorování funkcí služby SQL Database a jak nastavit upozornění při překročení prahové hodnoty výkonu. |
|[Monitorování s analýzy protokolů (OMS)](saas-dbpertenant-log-analytics.md) | Další informace o použití [analýzy protokolů](../log-analytics/log-analytics-overview.md) monitorování velké množství prostředků, napříč více fondů. |
|[Obnovení jednoho klienta](saas-dbpertenant-restore-single-tenant.md)| Zjistěte, jak k obnovení databázi klienta do předchozího bodu v čase. Kroky k obnovení databázi paralelní ponechat stávající databázi klienta online, jsou také zahrnuté. |
|[Správa schématu databáze klienta](saas-tenancy-schema-management.md)| Zjistěte, jak aktualizovat schéma a aktualizovat referenční data mezi databázemi všechny klienta. |
|[Spuštění klienta mezi distribuovaných dotazů](saas-tenancy-cross-tenant-reporting.md) | Vytvoření databáze analýzy ad-hoc a spusťte v reálném čase distribuované dotazy mezi všechny klienty.  |
|[Spustit analýzy dat extrahovaných klienta](saas-tenancy-tenant-analytics.md) | Extrahování dat klienta do analytics databáze nebo datového skladu pro offline analytické dotazy. |


## <a name="next-steps"></a>Další postup

- [Obecné Rady a tipy při nasazení a používání příklad Wingtip lístky SaaS aplikace](saas-tenancy-wingtip-app-guidance-tips.md)

- [Nasazení aplikace Wingtip SaaS](saas-dbpertenant-get-started-deploy.md)
