---
title: Data služby a nástroje matice migrace – Azure | Dokumentace Microsoftu
description: Další informace o služby a nástroje jsou k dispozici pro migraci databáze a pro podporu různých fázích procesu migrace.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: douglasl
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 12/13/2018
ms.openlocfilehash: 9887860470632c37a586cb3029e2ef7d455a3a9c
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53713279"
---
# <a name="services-and-tools-available-for-data-migration-scenarios"></a>Služby a nástroje, které jsou k dispozici pro scénáře migrace dat

Tento článek obsahuje matici Microsoftu a třetích stran služeb a nástrojů, které vám pomohou s různými databáze a scénáře migrace dat a speciální úkoly.

Následující tabulky Identifikujte služby a nástroje, které se můžete úspěšně naplánovat migraci dat a dokončete její různých fází.

> [!NOTE]
> V následujících tabulkách položky s hvězdičkou (*) představují nástroje třetích stran.

## <a name="business-justification-phase"></a>Obchodní odůvodnění fáze

| **Zdroj** | **Cíl** | **Zjišťování /**<br/>**Inventář** | **Cíl a skladové položky**<br/>**Doporučení** | **Celkové náklady na vlastnictví a návratnosti investic a**<br/>**Obchodní případ** |
| --- | --- | --- | --- | --- |
| SQL Server | Azure SQL DB | [MAP Toolkit](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize *](https://www.cloudamize.com/) | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloud Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize *](https://www.cloudamize.com/) | [Kalkulačka TCO](https://azure.microsoft.com/pricing/tco/calculator/) |
| SQL Server | Azure SQL DB MI | [MAP Toolkit](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize *](https://www.cloudamize.com/) | [Cloud Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize *](https://www.cloudamize.com/) | [Kalkulačka TCO](https://azure.microsoft.com/pricing/tco/calculator/) |
| SQL Server | Virtuální počítač Azure SQL | [MAP Toolkit](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize *](https://www.cloudamize.com/) | [Cloud Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize *](https://www.cloudamize.com/) | [Kalkulačka TCO](https://azure.microsoft.com/pricing/tco/calculator/) |
| SQL Server | SQL DATA WAREHOUSE |  |  | [Kalkulačka TCO](https://azure.microsoft.com/pricing/tco/calculator/) |
| SQL VZDÁLENÉ PLOCHY | Virtuální počítač Azure SQL DB, USA, |  | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [Kalkulačka TCO](https://azure.microsoft.com/pricing/tco/calculator/) |
| Oracle | Virtuální počítač Azure SQL DB, USA, | [MAP Toolkit](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [MigVisor *](https://www.migvisor.com/) |  |
| Oracle | SQL DATA WAREHOUSE | [MAP Toolkit](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) |  |  |
| Oracle | Azure DB for PostgreSQL |  |  |  |
| MongoDB | Databáze Cosmos | [Cloudamize *](https://www.cloudamize.com/) | [Cloudamize *](https://www.cloudamize.com/) |  |
| Cassandra | Databáze Cosmos |  |  |  |
| MySQL | Virtuální počítač Azure SQL DB, USA, | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [Cloud Atlas *](https://www.unifycloud.com/cloud-migration-tool/) | [Kalkulačka TCO](https://azure.microsoft.com/pricing/tco/calculator/) |
| MySQL | Azure DB for MySQL | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) |  | [Kalkulačka TCO](https://azure.microsoft.com/pricing/tco/calculator/) |
| RDS MySQL | Azure DB for MySQL |  |  | [Kalkulačka TCO](https://azure.microsoft.com/pricing/tco/calculator/) |
| PostgreSQL | Azure DB for PostgreSQL | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) |  | [Kalkulačka TCO](https://azure.microsoft.com/pricing/tco/calculator/) |
| PostgreSQL vzdálené plochy | Azure DB for PostgreSQL |  |  | [Kalkulačka TCO](https://azure.microsoft.com/pricing/tco/calculator/) |
| DB2 | Virtuální počítač Azure SQL DB, USA, |  |  |  |
| Access | Virtuální počítač Azure SQL DB, USA, |  |  |  |
| Sybase | Virtuální počítač Azure SQL DB, USA, |  |  |  |
| | | | | |

## <a name="pre-migration-phase"></a>Před migrací fáze

| **Zdroj** | **Cíl** | **Přístup k datům aplikace**<br/>**Posouzení vrstvy** | **Database**<br/>**Posouzení** | **Výkon**<br/>**Posouzení** |
| --- | --- | --- | --- | --- |
| SQL Server | Azure SQL DB |  | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloud Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize *](https://www.cloudamize.com/) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Cloudamize *](https://www.cloudamize.com/) |
| SQL Server | Azure SQL DB MI |  | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloud Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize *](https://www.cloudamize.com/) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Cloudamize *](https://www.cloudamize.com/) |
| SQL Server | Virtuální počítač Azure SQL |  | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloud Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize *](https://www.cloudamize.com/) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Cloudamize *](https://www.cloudamize.com/) |
| SQL Server | SQL DATA WAREHOUSE |  |  |  |
| SQL VZDÁLENÉ PLOCHY | Virtuální počítač Azure SQL DB, USA, |  | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090) |
| Oracle | Virtuální počítač Azure SQL DB, USA, |  | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [Simora *](http://www.simora.co.uk/) |
| Oracle | SQL DATA WAREHOUSE |  | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [Simora *](http://www.simora.co.uk/) |
| Oracle | Azure DB for PostgreSQL |  |  |  |
| MongoDB | Databáze Cosmos |  | [Cloudamize *](https://www.cloudamize.com/) | [Cloudamize *](https://www.cloudamize.com/) |
| Cassandra | Databáze Cosmos |  |  |  |
| MySQL | Virtuální počítač Azure SQL DB, USA, |  | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Cloud Atlas *](https://www.unifycloud.com/cloud-migration-tool/) |  |
| MySQL | Azure DB for MySQL |  |  |  |
| RDS MySQL | Azure DB for MySQL |  |  |  |
| PostgreSQL | Azure DB for PostgreSQL |  |  |  |
| PostgreSQL vzdálené plochy | Azure DB for PostgreSQL |  |  |  |
| DB2 | Virtuální počítač Azure SQL DB, USA, |  | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Access | Virtuální počítač Azure SQL DB, USA, |  | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Sybase | Virtuální počítač Azure SQL DB, USA, |  | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| | | | | |

## <a name="migration-phase"></a>Fáze migrace

| **Zdroj** | **Cíl** | **schéma** | **Data**<br/>**(Offline)** | **Data**<br/>**(Online)** |
| --- | --- | --- | --- | --- |
| SQL Server | Azure SQL DB | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloudamize *](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize *](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize *](https://www.cloudamize.com/)<br/>[Řešení Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| SQL Server | Azure SQL DB MI | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize *](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize *](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize *](https://www.cloudamize.com/)<br/>[Řešení Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| SQL Server | Virtuální počítač Azure SQL | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloudamize *](https://www.cloudamize.com/) | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloudamize *](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize *](https://www.cloudamize.com/)<br/>[Řešení Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| SQL Server | SQL DATA WAREHOUSE |  |  |  |
| SQL VZDÁLENÉ PLOCHY | Virtuální počítač Azure SQL DB, USA, | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [DMS](https://azure.microsoft.com/services/database-migration/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Řešení Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oracle | Virtuální počítač Azure SQL DB, USA, | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[SharePlex *](https://www.quest.com/products/shareplex/) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[SharePlex *](https://www.quest.com/products/shareplex/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[SharePlex *](https://www.quest.com/products/shareplex/)<br/>[Řešení Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oracle | SQL DATA WAREHOUSE | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |
| Oracle | Azure DB for PostgreSQL |  |  |  |
| MongoDB | Databáze Cosmos | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize *](https://www.cloudamize.com/)<br/>[Imanis Data *](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize *](https://www.cloudamize.com/)<br/>[Imanis Data *](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [Cloudamize *](https://www.cloudamize.com/)<br/>[Imanis Data *](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Cassandra | Databáze Cosmos | [Imanis Data *](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [Imanis Data *](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [Imanis Data *](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) |
| MySQL | Virtuální počítač Azure SQL DB, USA, | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Řešení Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| MySQL | Azure DB for MySQL | [MySQL s výpisem paměti *](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) | [DMS](https://azure.microsoft.com/services/database-migration/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Řešení Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| RDS MySQL | Azure DB for MySQL | [MySQL s výpisem paměti *](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) | [DMS](https://azure.microsoft.com/services/database-migration/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Řešení Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| PostgreSQL | Azure DB for PostgreSQL | [PG výpisu stavu systému *](https://www.postgresql.org/docs/11/static/app-pgdump.html) | [DMS](https://azure.microsoft.com/services/database-migration/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Řešení Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| PostgreSQL vzdálené plochy | Azure DB for PostgreSQL | [PG výpisu stavu systému *](https://www.postgresql.org/docs/11/static/app-pgdump.html) | [DMS](https://azure.microsoft.com/services/database-migration/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Řešení Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| DB2 | Virtuální počítač Azure SQL DB, USA, | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Řešení Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Access | Virtuální počítač Azure SQL DB, USA, | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |
| Sybase | Virtuální počítač Azure SQL DB, USA, | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Řešení Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| | | | | |

## <a name="post-migration-phase"></a>Fáze po migraci

| **Zdroj** | **Cíl** | **Optimalizovat** |
| --- | --- | --- |
| SQL Server | Azure SQL DB | [Cloud Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize *](https://www.cloudamize.com/) |
| SQL Server | Azure SQL DB MI | [Cloud Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize *](https://www.cloudamize.com/) |
| SQL Server | Virtuální počítač Azure SQL | [Cloud Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize *](https://www.cloudamize.com/) |
| SQL Server | SQL DATA WAREHOUSE |  |
| SQL VZDÁLENÉ PLOCHY | Virtuální počítač Azure SQL DB, USA, |  |
| Oracle | Virtuální počítač Azure SQL DB, USA, |  |
| Oracle | SQL DATA WAREHOUSE |  |
| Oracle | Azure DB for PostgreSQL |  |
| MongoDB | Databáze Cosmos | [Cloudamize *](https://www.cloudamize.com/) |
| Cassandra | Databáze Cosmos |  |
| MySQL | Virtuální počítač Azure SQL DB, USA, |  |
| MySQL | Azure DB for MySQL |  |
| RDS MySQL | Azure DB for MySQL |  |
| PostgreSQL | Azure DB for PostgreSQL |  |
| PostgreSQL vzdálené plochy | Azure DB for PostgreSQL |  |
| DB2 | Virtuální počítač Azure SQL DB, USA, |  |
| Access | Virtuální počítač Azure SQL DB, USA, |  |
| Sybase | Virtuální počítač Azure SQL DB, USA, |  |
| | | |

## <a name="next-steps"></a>Další postup

Přehled služby Azure Database Migration Service, najdete v článku [co je Azure Database Migration Service](dms-overview.md).
