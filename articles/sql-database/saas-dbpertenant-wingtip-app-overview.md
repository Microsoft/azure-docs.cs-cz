---
title: Příklad víceklientské aplikace Azure SQL Database – SaaS aplikace Wingtip | Dokumentace Microsoftu
description: Zjistěte, s použitím ukázkové víceklientské aplikaci, která používá Azure SQL Database, příklad SaaS aplikace Wingtip
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: dd67e49a80c403e807b24276cf14b9e8c47037b9
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47055505"
---
# <a name="introduction-to-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>Úvod do víceklientské aplikace SaaS, která používá vzor databáze na tenanta s využitím SQL Database

Aplikace SaaS aplikace Wingtip je ukázková víceklientské aplikace. Aplikace používá vzorec SaaS aplikace databází na tenanta k obsluhovat několik klientů. Aplikace prezentuje funkce služby Azure SQL Database, které umožňují scénáře SaaS pomocí několika vzorů návrhu a správy SaaS. Rychlé zprovoznění, nasadí SaaS aplikace Wingtip za méně než pět minut.

Skripty zdrojový kód a správu aplikací jsou k dispozici v [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) úložiště GitHub. Než začnete, podívejte se [obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md) pokyny ke stažení a odblokování Wingtip Tickets skripty pro správu.

## <a name="application-architecture"></a>Architektura aplikace

SaaS aplikace Wingtip používá model databáze na tenanta. Využívá SQL elastické fondy pro maximalizaci efektivity. Pro zřizování a mapování tenantů na svá data se používá databáze katalogu. Základní aplikace SaaS aplikace Wingtip používá fond se třemi ukázkovými tenanty a databází katalogu. Servery katalogu a tenanta byly zajišťovaný pomocí aliasů DNS. Tyto aliasy slouží k udržování odkaz na aktivní prostředky využívané třídou aplikaci Wingtip. Tyto aliasy jsou aktualizovány tak, aby odkazoval na obnovení prostředky v kurzech pro zotavení po havárii. Dokončuje se mnoho výsledků kurzy SaaS aplikace Wingtip v doplňky pro počáteční nasazení. Zavedení doplňky, jako je například analytické databáze a Správa schématu mezi databázemi.


![Architektura SaaS aplikace Wingtip](media/saas-dbpertenant-wingtip-app-overview/app-architecture.png)


Projděte následující kurzy a pracovat s aplikací, jejich vztah k datové vrstvě zaměřte na vzorce SaaS. Jinými slovy zaměřte se na datové vrstvě a není overanalyze samotné aplikace. Vzory znalost implementace těchto SaaS je klíčem k implementaci těchto vzorců v aplikacích. Zvažte také jakýchkoli modifikací nezbytných pro vaše konkrétní obchodní požadavky.

## <a name="sql-database-wingtip-saas-tutorials"></a>Kurzy SaaS aplikace Wingtip databáze SQL

Poté, co nasadíte aplikaci, si projděte následující kurzy, které vycházejí z počátečního nasazení. Tyto kurzy prozkoumat běžné vzory SaaS, které využívají integrované funkce služby SQL Database, Azure SQL Data Warehouse a dalšími službami Azure. Kurzy zahrnují skripty prostředí PowerShell s podrobnými vysvětleními. Vysvětlení zjednodušují pochopení a provádění stejné vzorce správy SaaS ve vašich aplikacích.


| Kurz | Popis |
|:--|:--|
| [Pokyny a tipy pro SQL Database příklad víceklientské aplikace SaaS](saas-tenancy-wingtip-app-guidance-tips.md) | Stáhnout a spustit skripty prostředí PowerShell pro přípravu částí aplikace. |
|[Nasazení a zkoumání SaaS aplikace Wingtip aplikace](saas-dbpertenant-get-started-deploy.md)|  Nasazení a zkoumání SaaS aplikace Wingtip aplikace ve vašem předplatném Azure. |
|[Zřízení a katalog tenantů](saas-dbpertenant-provision-and-catalog.md)| Zjistěte, jak aplikace připojuje klientům pomocí databáze katalogů a jak katalog mapuje tenanty ke svým datům. |
|[Monitorování a správa výkonu](saas-dbpertenant-performance-monitoring.md)| Zjistěte, jak použít monitorování funkcí služby SQL Database a nastavit upozornění při překročení prahové hodnoty výkonu. |
|[Monitorování pomocí služby Azure Log Analytics](saas-dbpertenant-log-analytics.md) | Další informace o použití [Log Analytics](../log-analytics/log-analytics-overview.md) k monitorování velkého množství prostředků u více fondů. |
|[Obnovení jednoho tenanta](saas-dbpertenant-restore-single-tenant.md)| Zjistěte, jak obnovit databáze tenantů do předchozího bodu v čase. Naučte se obnovit databázi paralelní, což zanechá databázi existujícího tenanta online. |
|[Správa schémat databází tenantů](saas-tenancy-schema-management.md)| Zjistěte, jak aktualizovat schéma a aktualizace referenčních dat napříč všemi databázemi tenantů. |
|[Spouštění distribuovaných dotazů napříč tenanty](saas-tenancy-cross-tenant-reporting.md) | Vytvořit analytickou databázi ad hoc a spouštění distribuovaných dotazů v reálném čase ve všech tenantech.  |
|[Spouštět analýzy na data extrahovaná tenanta](saas-tenancy-tenant-analytics.md) | Extrahování dat klienta do analytics databázi ani na datový sklad pro offline analytických dotazů. |


## <a name="next-steps"></a>Další postup

- [Obecné pokyny a tipy při nasazování a pomocí příkladu aplikace SaaS aplikace Wingtip Tickets](saas-tenancy-wingtip-app-guidance-tips.md)
- [Nasazení aplikace SaaS aplikace Wingtip](saas-dbpertenant-get-started-deploy.md)
