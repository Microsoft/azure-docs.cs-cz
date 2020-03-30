---
title: Příklad víceklientské aplikace – Wingtip SaaS
description: Naučte se pomocí ukázkové víceklientské aplikace, která používá Azure SQL Database, wingtip SaaS příklad
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/24/2018
ms.openlocfilehash: 46cdcd5f768278dbc729f48e450c68a63be604be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256494"
---
# <a name="introduction-to-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>Úvod do víceklientské aplikace SaaS, která používá vzor databáze na klienta s databází SQL Database

Wingtip SaaS aplikace je ukázková víceklientská aplikace. Aplikace používá vzor aplikace SaaS pro databázi na klienta k poskytování služeb více klientům. Aplikace představuje funkce Azure SQL Database, které umožňují scénáře SaaS pomocí několika vzorů návrhu a správy SaaS. Aplikace Wingtip SaaS se nasazuje za méně než pět minut, aby se rychle zprovozněla.

Zdrojový kód aplikace a skripty pro správu jsou k dispozici v úložišti [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub. Než začnete, podívejte se na [obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md) pro kroky ke stažení a odblokování skriptů pro správu wingtip lístků.

## <a name="application-architecture"></a>Architektura aplikace

Aplikace Wingtip SaaS používá model databáze na tenanta. Používá elastické fondy SQL k maximalizaci efektivity. Pro zřizování a mapování klientů na jejich data se používá databáze katalogu. Základní aplikace Wingtip SaaS používá fond se třemi ukázkovými klienty a databázi katalogu. Katalog a klient servery byly zřízeny s aliasy DNS. Tyto aliasy se používají k udržování odkazu na aktivní prostředky používané aplikací Wingtip. Tyto aliasy jsou aktualizovány tak, aby ukazovaly na prostředky pro obnovení v kurzech zotavení po havárii. Dokončení mnoha kurzů Wingtip SaaS má za následek doplňky k počátečnímu nasazení. Jsou zavedeny doplňky, jako jsou analytické databáze a správa schématu mezi databázemi.


![Architektura Wingtip SaaS](media/saas-dbpertenant-wingtip-app-overview/app-architecture.png)


Při procházení kurzů a práci s aplikací se zaměřte na vzory SaaS, které se vztahují k datové vrstvě. Jinými slovy, zaměřte se na datovou vrstvu a neanalyzujte nadměrnou analýzu samotné aplikace. Pochopení implementace těchto vzorů SaaS je klíčem k implementaci těchto vzorů ve vašich aplikacích. Zvažte také všechny nezbytné úpravy pro vaše konkrétní obchodní požadavky.

## <a name="sql-database-wingtip-saas-tutorials"></a>Sql Database Wingtip SaaS výukové programy

Po nasazení aplikace prozkoumejte následující kurzy, které vycházejí z počátečního nasazení. Tyto kurzy prozkoumat běžné vzory SaaS, které využívají integrované funkce SQL Database, Azure SQL Data Warehouse a další služby Azure. Kurzy zahrnují skripty prostředí PowerShell s podrobným vysvětlením. Vysvětlení zjednodušují pochopení a implementaci stejných vzorů správy SaaS ve vašich aplikacích.


| Kurz | Popis |
|:--|:--|
| [Pokyny a tipy pro sql database víceklientské aplikace SaaS příklad](saas-tenancy-wingtip-app-guidance-tips.md) | Stáhněte a spusťte skripty prostředí PowerShell a připravte části aplikace. |
|[Nasazení a prozkoumání aplikace Wingtip SaaS](saas-dbpertenant-get-started-deploy.md)|  Nasazujte a prozkoumejte aplikaci Wingtip SaaS pomocí předplatného Azure. |
|[Zřízení a katalogu klientů](saas-dbpertenant-provision-and-catalog.md)| Zjistěte, jak se aplikace připojuje k klientům pomocí databáze katalogu a jak katalog mapuje klienty na jejich data. |
|[Sledování a správa výkonu](saas-dbpertenant-performance-monitoring.md)| Naučte se používat funkce monitorování databáze SQL a nastavit výstrahy při překročení prahových hodnot výkonu. |
|[Monitorování pomocí protokolů služby Azure Monitor](saas-dbpertenant-log-analytics.md) | Zjistěte, jak pomocí [protokolů Azure Monitor](../log-analytics/log-analytics-overview.md) umonitorovat velké množství prostředků ve více fondech. |
|[Obnovení jednoho tenanta](saas-dbpertenant-restore-single-tenant.md)| Zjistěte, jak obnovit databázi klienta do předchozího bodu v čase. Také se dozvíte, jak obnovit paralelní databáze, která ponechá existující databázi klienta online. |
|[Správa schématu databáze klienta](saas-tenancy-schema-management.md)| Zjistěte, jak aktualizovat schéma a aktualizovat referenční data ve všech databázích klientů. |
|[Spuštění distribuovaných dotazů mezi klienty](saas-tenancy-cross-tenant-reporting.md) | Vytvořte databázi analýzy ad hoc a spouštějte distribuované dotazy v reálném čase napříč všemi klienty.  |
|[Spuštění analýzy na extrahovaných datech klienta](saas-tenancy-tenant-analytics.md) | Extrahujte data klienta do analytické databáze nebo datového skladu pro dotazy offline analýzy. |


## <a name="next-steps"></a>Další kroky

- [Obecné pokyny a tipy při nasazování a používání příkladu aplikace Wingtip Tickets SaaS](saas-tenancy-wingtip-app-guidance-tips.md)
- [Nasazení aplikace Wingtip SaaS](saas-dbpertenant-get-started-deploy.md)
