---
title: Příklad víceklientské aplikace Azure SQL Database - Wingtip SaaS | Microsoft Docs
description: Další informace pomocí víceklientské ukázkovou aplikaci, která používá databázi SQL Azure, například adresář Wingtip SaaS
keywords: kurz k sql database
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: ccac68fb22baed668ed786fd594eda122f2522ee
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34643912"
---
# <a name="introduction-to-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>Úvod do víceklientské aplikace SaaS, která využívá vzor databáze na klienta s databází SQL

Aplikace Wingtip SaaS je víceklientská ukázkovou aplikaci. Aplikace používá vzor aplikací SaaS databáze za klienta k obsluhovat několik klientů. Aplikace umožňující prezentovat funkce databáze SQL Azure, které umožňují SaaS scénáře pomocí několika vzory návrhu a správu SaaS. Pro rychlé zprovoznění, nasadí aplikaci Wingtip SaaS za méně než pět minut.

Jsou k dispozici v aplikaci zdrojový kód a správu skriptů [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) úložiště GitHub. Než začnete, najdete v článku [obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md) kroky, jak stáhnout a odblokování skripty správy Wingtip lístků.

## <a name="application-architecture"></a>Architektura aplikace

Aplikace Wingtip SaaS používá model databáze za klienta. Chcete-li maximalizovat efektivitu používá elastické fondy SQL. Pro zřizování a mapování klientům svá data se používá databázi katalogu. Základní aplikace Wingtip SaaS používá fond s tři klienty ukázka plus databáze katalogu. Servery katalogu a klientů zajištěných s aliasy DNS. Tyto aliasy slouží k udržování odkazů na aktivní prostředky využívané třídou Wingtip aplikace. Tyto aliasy jsou aktualizovány tak, aby odkazoval na obnovení prostředky v kurzech obnovení po havárii. Dokončení mnoho výsledků kurzy Wingtip SaaS v rozšíření do počáteční nasazení. Zavedly se doplňky, jako je například analytické databáze a schématu databáze mezi správy.


![Architektura Wingtip SaaS](media/saas-dbpertenant-wingtip-app-overview/app-architecture.png)


Projděte následující kurzy a fungovat s aplikací, které se vztahují na datové vrstvě soustřeďte na vzory SaaS. Jinými slovy zaměřit na datové vrstvě a nemáte overanalyze aplikace. Principy provádění těchto SaaS vzory je klíč k implementaci tyto vzory ve svých aplikacích. Zvažte také veškeré nezbytné úpravy pro vaše konkrétní obchodní požadavky.

## <a name="sql-database-wingtip-saas-tutorials"></a>Kurzy SaaS Wingtip databáze SQL

Poté, co nasadíte aplikaci, prozkoumejte následující kurzy, které vychází z počátečního nasazení. Tyto kurzy prozkoumejte běžných vzorů SaaS, které využít integrované funkce SQL Database, Azure SQL Data Warehouse a jinými službami Azure. Kurzy zahrnují skriptů prostředí PowerShell s podrobné vysvětlení. Vysvětlení zjednodušit pochopení a provádění stejné vzory správu SaaS ve svých aplikacích.


| Kurz | Popis |
|:--|:--|
| [Pokyny a tipy pro databáze SQL příklad víceklientské aplikace SaaS](saas-tenancy-wingtip-app-guidance-tips.md) | Stažení a spuštění skriptů prostředí PowerShell pro přípravu částí aplikace. |
|[Nasazení a prozkoumejte Wingtip SaaS aplikace](saas-dbpertenant-get-started-deploy.md)|  Nasazení a prozkoumejte aplikace Wingtip SaaS s předplatným Azure. |
|[Zřizování a katalog klientů](saas-dbpertenant-provision-and-catalog.md)| Zjistěte, jak se aplikace připojí klientům pomocí databáze katalogu a jak katalogu mapuje klienty na svá data. |
|[Sledování a správa výkonu](saas-dbpertenant-performance-monitoring.md)| Naučte se používat monitorování funkcí služby SQL Database a nastavit upozornění při překročení prahové hodnoty výkonu. |
|[Monitorování s Azure Log Analytics](saas-dbpertenant-log-analytics.md) | Další informace o použití [analýzy protokolů](../log-analytics/log-analytics-overview.md) monitorování velké množství prostředků napříč více fondů. |
|[Obnovení jednoho klienta](saas-dbpertenant-restore-single-tenant.md)| Zjistěte, jak k obnovení databázi klienta do předchozího bodu v čase. Také zjistěte, jak k obnovení databázi paralelní, což zanechá databázi existující klient online. |
|[Správa schématu databáze klienta](saas-tenancy-schema-management.md)| Zjistěte, jak aktualizovat schéma a aktualizovat referenční data mezi databázemi všechny klienta. |
|[Spuštění klienta mezi distribuovaných dotazů](saas-tenancy-cross-tenant-reporting.md) | Vytvoření databáze analýzy ad-hoc a spusťte v reálném čase distribuované dotazy mezi všechny klienty.  |
|[Spustit analýzy dat extrahovaných klienta](saas-tenancy-tenant-analytics.md) | Extrahování dat klienta do analytics databáze nebo datového skladu pro offline analytické dotazy. |


## <a name="next-steps"></a>Další postup

- [Obecné Rady a tipy při nasazení a používání příklad Wingtip lístky SaaS aplikace](saas-tenancy-wingtip-app-guidance-tips.md)
- [Nasazení aplikace Wingtip SaaS](saas-dbpertenant-get-started-deploy.md)
