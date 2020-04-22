---
title: Poznámky k vydání pro Microsoft Azure Backup Server v3
description: Tento článek obsahuje informace o známých problémech a řešeních pro Microsoft Azure Backup Server (MABS) v3.
ms.topic: conceptual
ms.date: 11/22/2018
ms.asset: 0c4127f2-d936-48ef-b430-a9198e425d81
ms.openlocfilehash: 03863f7598da06bb36cbb7497d7c773f811a004e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685609"
---
# <a name="release-notes-for-microsoft-azure-backup-server"></a>Poznámky k vydání pro microsoft azure backup server

Tento článek obsahuje známé problémy a řešení pro Microsoft Azure Backup Server (MABS) V3.

## <a name="backup-and-recovery-fails-for-clustered-workloads"></a>Zálohování a obnovení se nezdaří pro clusterované úlohy

**Popis:** Zálohování a obnovení se nezdaří pro clusterované zdroje dat, jako je například cluster Hyper-V nebo cluster SQL (SQL Always On) nebo Exchange ve skupině dostupnosti databáze (DAG) po upgradu MABS V2 na MABS V3.

**Řešení:** Chcete-li tomu zabránit, otevřete sql server management studio (SSMS)) a spusťte následující skript SQL v db dpm:

```sql
    IF EXISTS (SELECT * FROM dbo.sysobjects
        WHERE id = OBJECT_ID(N'[dbo].[tbl_PRM_DatasourceLastActiveServerMap]')
        AND OBJECTPROPERTY(id, N'IsUserTable') = 1)
        DROP TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap]
        GO

        CREATE TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap] (
            [DatasourceId]          [GUID]          NOT NULL,
            [ActiveNode]            [nvarchar](256) NULL,
            [IsGCed]                [bit]           NOT NULL
            ) ON [PRIMARY]
        GO

        ALTER TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap] ADD
    CONSTRAINT [pk__tbl_PRM_DatasourceLastActiveServerMap__DatasourceId] PRIMARY KEY NONCLUSTERED
        (
            [DatasourceId]
        )  ON [PRIMARY],

    CONSTRAINT [DF_tbl_PRM_DatasourceLastActiveServerMap_IsGCed] DEFAULT
        (
            0
        ) FOR [IsGCed]
    GO
```

## <a name="upgrade-to-mabs-v3-fails-in-russian-locale"></a>Upgrade na MABS V3 selže v ruském národním prostředí

**Popis:** Upgrade z MABS V2 na MABS V3 v ruském národním prostředí selže s kódem chyby **4387**.

**Řešení:** Provedení následujících kroků k upgradu na MABS V3 pomocí ruského instalačního balíčku:

1. [Zálohujte](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server?view=sql-server-2017#SSMSProcedure) databázi SQL a odinstalujte MABS V2 (zvolte zachování chráněných dat během odinstalace).
2. Upgradujte na SQL 2017 (Enterprise) a odinstalujte sestavy jako součást upgradu.
3. [Instalace](https://docs.microsoft.com/sql/reporting-services/install-windows/install-reporting-services?view=sql-server-2017#install-your-report-server) SQL Server Reporting Services (SSRS).
4. [Instalace](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) SQL Server Management Studio (SSMS).
5. Konfigurace reportingu pomocí parametrů, jak jsou popsány v [konfiguraci SSRS pomocí SQL 2017](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#upgrade-mabs).
6. [Instalace](backup-azure-microsoft-azure-backup.md) MABS V3.
7. [Obnovit](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms?view=sql-server-2017) SQL pomocí SSMS a spustit DPM-Sync nástroj, jak je popsáno [zde](https://docs.microsoft.com/system-center/dpm/back-up-the-dpm-server?view=sc-dpm-2019#using-dpmsync).
8. Aktualizujte vlastnost DataBaseVersion v tabulce dbo.tbl_DLS_GlobalSetting pomocí následujícího příkazu:

```sql
        UPDATE dbo.tbl_DLS_GlobalSetting
        set PropertyValue = '13.0.415.0'
        where PropertyName = 'DatabaseVersion'
```

9. Spusťte službu MSDPM.

## <a name="next-steps"></a>Další kroky

[Co je nového v MABS V3](backup-mabs-whats-new-mabs.md)
