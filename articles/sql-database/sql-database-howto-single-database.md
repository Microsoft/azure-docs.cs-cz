---
title: Jak nakonfigurovat databázi SQL Azure – Singleton | Dokumentace Microsoftu
description: Zjistěte, jak konfigurovat a spravovat Azure SQL Database – izolované databáze.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 12/14/2018
ms.openlocfilehash: d34853220e423e73c6ca8cf7c76ba616b815b8bd
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2018
ms.locfileid: "53440650"
---
# <a name="how-to-use-single-database"></a>Jak používat izolované databáze

V této části najdete různé průvodce, skripty a vysvětlení, které vám mohou pomoci při správě a konfiguraci Azure SQL Database – izolované databáze.

## <a name="migrate"></a>Migrace

- [Migrace do SQL Database](sql-database-cloud-migrate.md) – přečtěte si víc o doporučených migrace procesů a nástrojů pro migraci do spravované Instance.
- Zjistěte, jak [Správa SQL database po migraci](sql-database-manage-after-migration.md).

## <a name="configure-features"></a>Konfigurace funkcí

- [Konfigurace transakční replikace](replication-to-sql-database.md) replikovat vaše data mezi databázemi.
- [Konfigurace zjišťování hrozeb](sql-database-threat-detection.md) umožňuje identifikovat podezřelé aktivity, jako je Injektáž SQL nebo přístupu z podezřelých míst Azure SQL Database.
- [Konfigurace dynamického maskování dat](sql-database-dynamic-data-masking-get-started-portal.md) chránit citlivá data.
- [Konfigurace uchovávání záloh](sql-database-long-term-backup-retention-configure.md) aby databáze mohla Udržovat zálohy na Azure Blob Storage. Jako alternativu je [konfigurace uchování záloh s využitím Azure vault (zastaralé)](sql-database-long-term-backup-retention-configure-vault.md) přístup.
- [Konfigurace geografické replikace](sql-database-geo-replication-portal.md) zachovat repliky databáze v jiné oblasti.
- [Konfigurace zabezpečení pro geografické repliky](sql-database-geo-replication-security-config.md).

## <a name="monitor-and-tune-your-database"></a>Monitorování a optimalizace databáze

- [Povolení automatického ladění](sql-database-automatic-tuning-enable.md) umožňuje optimalizovat výkon vaší úlohy Azure SQL Database.
- [Povolení e-mailová oznámení pro automatické ladění](sql-database-automatic-tuning-email-notifications.md) a získat informace o doporučení pro optimalizaci.
- [Použití doporučení k výkonu](sql-database-advisor-portal.md) a optimalizujte vaši databázi.
- [Vytvořit upozornění](sql-database-insights-alerts-portal.md) dostávat oznámení ze služby Azure SQL Database.
- [Řešení potíží s připojením](sql-database-troubleshoot-common-connection-issues.md) Pokud zjistíte nějaké problémy s připojením mezi aplikací a databází. Můžete také použít [Resource Health pro problémy s připojením](sql-database-resource-health.md).
- [Správa místo souboru](sql-database-file-space-management.md) monitorovat využití úložiště v databázi.

## <a name="query-distributed-data"></a>Dotazování na distribuovaná data

- [Dotazování na vertikálně dělená data](sql-database-elastic-query-getting-started-vertical.md) napříč několika databázemi.
- [Sestavy napříč vrstvou s horizontálním navýšením kapacity dat](sql-database-elastic-query-horizontal-partitioning.md).
- [Dotazování přes tabulky s různými schématy](sql-database-elastic-query-vertical-partitioning.md).

## <a name="elastic-database-jobs"></a>Úlohy elastické databáze

- [Vytvoření a správa](elastic-jobs-powershell.md) úlohy Elastic Database pomocí Powershellu.
- [Vytvoření a správa](elastic-jobs-tsql.md) úlohy Elastic Database pomocí jazyka Transact-SQL.
- [Migrace ze starého Elastických úloh](elastic-jobs-migrate.md).

## <a name="database-sharding"></a>Sharding databází

- [Klientská knihovna elastic database upgradu](sql-database-elastic-scale-upgrade-client-library.md).
- [Vytvoření horizontálně dělené aplikace](sql-database-elastic-scale-get-started.md).
- [Dotazování horizontálně dělených dat](sql-database-elastic-query-getting-started.md).
- Spustit [více horizontálních oddílů dotazy](sql-database-elastic-scale-multishard-querying.md).
- [Přesun horizontálně dělených dat](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
- [Konfigurace zabezpečení](sql-database-elastic-scale-split-merge-security-configuration.md) v horizontálních oddílů databáze.
- [Přidání horizontálního oddílu](sql-database-elastic-scale-add-a-shard.md) na aktuální nastavení od horizontálních oddílů databáze.
- [Odstraňování problémů s horizontálními oddíly mapy](sql-database-elastic-database-recovery-manager.md).
- [Migrace horizontálně dělené databáze](sql-database-elastic-convert-to-use-elastic-tools.md).
- [Vytvoření čítačů](sql-database-elastic-database-perf-counters.md).
- [Použití entity Frameworku](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) k dotazování na horizontálně dělená data.
- [Použití Dapper framework](sql-database-elastic-scale-working-with-dapper.md) k dotazování na horizontálně dělená data.

## <a name="next-steps"></a>Další postup
- Další informace o [postupy provede ve spravované instanci](sql-database-howto-managed-instance.md)
