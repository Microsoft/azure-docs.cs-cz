---
title: Jak nakonfigurovat jednu databázi
description: Zjistěte, jak konfigurovat a spravovat Azure SQL Database – jednu databázi
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
ms.date: 01/14/2020
ms.openlocfilehash: 4283e1a2c92b7fe738fb57a8103cea1deb0015a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76027713"
---
# <a name="how-to-use-a-single-database-in-azure-sql-database"></a>Použití jedné databáze v Azure SQL Database

V této části najdete různé příručky, skripty a vysvětlení, které vám pomůžou spravovat a konfigurovat jednu databázi v Azure SQL Database.

## <a name="migrate"></a>Migrace

- [Migrace do databáze SQL](sql-database-single-database-migrate.md) – informace o doporučeném procesu migrace a nástrojích pro migraci do spravované instance.
- Přečtěte si, jak [spravovat databázi SQL po migraci](sql-database-manage-after-migration.md).

## <a name="configure-features"></a>Konfigurace funkcí

- [Nakonfigurujte transakční replikaci](replication-to-sql-database.md) tak, aby replikovala datum mezi databázemi.
- [Nakonfigurujte detekci hrozeb](sql-database-threat-detection.md) tak, aby služba Azure SQL Database identifikovala podezřelé aktivity, jako je vkládání SQL nebo přístup z podezřelých umístění.
- [Nakonfigurujte dynamické maskování dat,](sql-database-dynamic-data-masking-get-started-portal.md) abyste ochránili citlivá data.
- [Nakonfigurujte uchovávání záloh](sql-database-long-term-backup-retention-configure.md) pro databázi, abyste udrželi zálohy v azure blob storage. Alternativně je [konfigurace uchovávání záloh pomocí azure trezoru (zastaralé)](sql-database-long-term-backup-retention-configure-vault.md) přístup.
- [Nakonfigurujte geografickou replikaci](sql-database-geo-replication-portal.md) tak, aby byla replika databáze uchována v jiné oblasti.
- [Konfigurace zabezpečení pro geografické repliky](sql-database-geo-replication-security-config.md).

## <a name="monitor-and-tune-your-database"></a>Sledování a ladění databáze

- [Povolte automatické ladění,](sql-database-automatic-tuning-enable.md) aby Azure SQL Database optimalizovala výkon vašeho pracovního vytížení.
- [Povolte e-mailová oznámení pro automatické ladění,](sql-database-automatic-tuning-email-notifications.md) abyste získali informace o doporučeních pro ladění.
- [Použijte doporučení pro výkon](sql-database-advisor-portal.md) a optimalizujte databázi.
- [Vytvořte výstrahy](sql-database-insights-alerts-portal.md) pro získání oznámení z Azure SQL Database.
- [Poradce při potížích s připojením,](troubleshoot-connectivity-issues-microsoft-azure-sql-database.md) pokud zjistíte některé problémy s připojením mezi aplikacemi a databází. Stav prostředků můžete také použít [pro problémy s připojením](sql-database-resource-health.md).
- [Spravujte místo v souborech](sql-database-file-space-management.md) pro sledování využití úložiště v databázi.

## <a name="query-distributed-data"></a>Dotazování na distribuovaná data

- [Dotaz svisle rozdělená data](sql-database-elastic-query-getting-started-vertical.md) ve více databázích.
- [Sestavy napříč vrstvou dat na horizontálním navýšení kapacity](sql-database-elastic-query-horizontal-partitioning.md).
- [Dotaz napříč tabulkami s různými schématy](sql-database-elastic-query-vertical-partitioning.md).

## <a name="elastic-database-jobs"></a>Úlohy elastické databáze

- [Vytváření a správa](elastic-jobs-powershell.md) Úlohy elastické databáze pomocí prostředí PowerShell.
- [Vytváření a správa](elastic-jobs-tsql.md) Elastické databázové úlohy pomocí Transact-SQL.
- [Migrace ze staré elastické úlohy](elastic-jobs-migrate.md).

## <a name="database-sharding"></a>Sharding databází

- [Inovujte klientskou knihovnu elastické databáze](sql-database-elastic-scale-upgrade-client-library.md).
- [Vytvořte roztřepenou aplikaci](sql-database-elastic-scale-get-started.md).
- [Dotaz na horizontálně horizontálně horizontálně horizontálně horizontálně horizontálně horizontálně horizontálně horizontálně horizontálně horizontálně horizontálně](sql-database-elastic-query-getting-started.md)
- [Spusťte dotazy s více oddíly](sql-database-elastic-scale-multishard-querying.md).
- [Přesunutí roztřesených dat](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
- [Konfigurace zabezpečení](sql-database-elastic-scale-split-merge-security-configuration.md) v databázových šmejdech.
- [Přidejte šiřidla](sql-database-elastic-scale-add-a-shard.md) do aktuální sady šiříšků databáze.
- [Oprava problémů s mapou úlomků](sql-database-elastic-database-recovery-manager.md).
- [Migrace oddílových oddílů DB](sql-database-elastic-convert-to-use-elastic-tools.md).
- [Vytvořte čítače](sql-database-elastic-database-perf-counters.md).
- [Pomocí rozhraní entity](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) můžete dotazovat na rozšivělá data.
- [Pomocí architektury Dapper](sql-database-elastic-scale-working-with-dapper.md) dotazunat a rozdělená data.

## <a name="next-steps"></a>Další kroky
- Další informace o [postupech pro spravovanou instanci](sql-database-howto-managed-instance.md)
