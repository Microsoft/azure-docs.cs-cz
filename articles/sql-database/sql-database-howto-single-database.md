---
title: Jak nakonfigurovat Azure SQL Database – Single | Microsoft Docs
description: Informace o tom, jak nakonfigurovat a spravovat Azure SQL Database-Single Database
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
ms.date: 02/08/2019
ms.openlocfilehash: 2117a811f977230dd9c9eecf6ea09b9b7deda3be
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568040"
---
# <a name="how-to-use-a-single-database-in-azure-sql-database"></a>Jak používat izolovanou databázi v Azure SQL Database

V této části najdete různé příručky, skripty a vysvětlení, které vám pomůžou spravovat a konfigurovat izolovanou databázi v Azure SQL Database

## <a name="migrate"></a>Migrace

- [Migrace na SQL Database](sql-database-single-database-migrate.md) – Přečtěte si o doporučeném procesu migrace a nástrojích pro migraci do spravované instance.
- Naučte se [Spravovat databázi SQL po migraci](sql-database-manage-after-migration.md).

## <a name="configure-features"></a>Konfigurace funkcí

- [Nakonfigurujte transakční replikaci](replication-to-sql-database.md) pro replikaci data mezi databázemi.
- [Nakonfigurujte detekci hrozeb](sql-database-threat-detection.md) , abyste umožnili Azure SQL Database identifikovat podezřelé aktivity, jako je například injektáže SQL nebo přístup z podezřelých umístění.
- [Nakonfigurujte dynamické maskování dat](sql-database-dynamic-data-masking-get-started-portal.md) pro ochranu citlivých dat.
- [Nakonfigurujte uchovávání záloh](sql-database-long-term-backup-retention-configure.md) pro databázi, abyste zachovali zálohy na Azure Blob Storage. Alternativně je [možné nakonfigurovat uchovávání záloh pomocí Azure trezoru (nepoužívané)](sql-database-long-term-backup-retention-configure-vault.md) .
- [Nakonfigurujte geografickou replikaci](sql-database-geo-replication-portal.md) a udržujte repliku databáze v jiné oblasti.
- [Nakonfigurujte zabezpečení pro geografické repliky](sql-database-geo-replication-security-config.md).

## <a name="monitor-and-tune-your-database"></a>Monitorování a optimalizace databáze

- [Povolením automatického ladění](sql-database-automatic-tuning-enable.md) umožníte Azure SQL Database optimalizovat výkon svého zatížení.
- [Povolením e-mailových oznámení pro automatické ladění](sql-database-automatic-tuning-email-notifications.md) získáte informace o doporučeních pro ladění.
- [Použijte doporučení k výkonu](sql-database-advisor-portal.md) a optimalizujte databázi.
- [Vytvořte výstrahy](sql-database-insights-alerts-portal.md) , abyste získali oznámení od Azure SQL Database.
- [Řešení potíží s](sql-database-troubleshoot-common-connection-issues.md) připojením, pokud si všimnete nějakých problémů s připojením mezi aplikacemi a databází. [Pro problémy s připojením](sql-database-resource-health.md)můžete také použít Resource Health.
- [Spravujte místo v souboru](sql-database-file-space-management.md) , abyste mohli monitorovat využití úložiště ve vaší databázi.

## <a name="query-distributed-data"></a>Dotazování na distribuovaná data

- [Dotazování vertikálně dělených dat](sql-database-elastic-query-getting-started-vertical.md) napříč více databázemi.
- [Sestava napříč datovou vrstvou s více instancemi](sql-database-elastic-query-horizontal-partitioning.md)
- [Dotaz napříč tabulkami s různými schématy](sql-database-elastic-query-vertical-partitioning.md)

## <a name="elastic-database-jobs"></a>Úlohy elastické databáze

- [Vytvoření a Správa](elastic-jobs-powershell.md) Elastic Database úlohy pomocí prostředí PowerShell.
- [Vytvoření a Správa](elastic-jobs-tsql.md) Elastic Database úlohy pomocí jazyka Transact-SQL.
- [Migrujte ze staré elastické úlohy](elastic-jobs-migrate.md).

## <a name="database-sharding"></a>Sharding databází

- [Upgradujte klientskou knihovnu elastické databáze](sql-database-elastic-scale-upgrade-client-library.md).
- [Vytvoření aplikace horizontálně dělené](sql-database-elastic-scale-get-started.md)
- [Dotazování horizontálně horizontálně dělené dat](sql-database-elastic-query-getting-started.md).
- Spusťte [dotazy multi-horizontálních oddílů](sql-database-elastic-scale-multishard-querying.md).
- [Přesune data horizontálně dělené](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
- [Nakonfigurujte zabezpečení](sql-database-elastic-scale-split-merge-security-configuration.md) v databázi horizontálních oddílů.
- [Přidá horizontálních oddílů](sql-database-elastic-scale-add-a-shard.md) k aktuální sadě horizontálních oddílů databáze.
- [Opravte problémy s mapou horizontálních oddílů](sql-database-elastic-database-recovery-manager.md).
- [Migrujte horizontálně DĚLENÉ DB](sql-database-elastic-convert-to-use-elastic-tools.md).
- [Vytvořte čítače](sql-database-elastic-database-perf-counters.md).
- [Použijte Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) k dotazování horizontálně dělené dat.
- [Použijte dapperem Framework](sql-database-elastic-scale-working-with-dapper.md) pro dotazování dat horizontálně dělené.

## <a name="next-steps"></a>Další postup
- Přečtěte si další informace o [průvodcích pro spravovanou instanci](sql-database-howto-managed-instance.md)
