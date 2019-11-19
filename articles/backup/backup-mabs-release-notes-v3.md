---
title: Poznámky k verzi pro Microsoft Azure Backup Server V3
description: Tento článek poskytuje informace o známých problémech a alternativním řešení pro Microsoft Azure Backup Server (MABS) v3.
ms.topic: conceptual
ms.date: 11/22/2018
ms.asset: 0c4127f2-d936-48ef-b430-a9198e425d81
ms.openlocfilehash: b47d83e0e3714f3f035397acaadeac9cda39d12a
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172279"
---
# <a name="release-notes-for-microsoft-azure-backup-server"></a>Poznámky k verzi pro Microsoft Azure Backup Server

V tomto článku jsou uvedené známé problémy a řešení pro Microsoft Azure Backup Server (MABS) v3.

## <a name="backup-and-recovery-fails-for-clustered-workloads"></a>Zálohování a obnova se u clusterových úloh nezdařila.

**Popis:** Zálohování a obnovení se u zdrojů clusterovaných dat, jako je cluster Hyper-V nebo cluster SQL (Always On SQL Server), nezdařila, nebo Exchange ve skupině dostupnosti databáze (DAG) po upgradu MABS v2 na MABS v3.

**Alternativní řešení:** Chcete-li tomu zabránit, otevřete SQL Server Management Studio (SSMS)) a spusťte následující skript SQL v databázi aplikace DPM:

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

## <a name="upgrade-to-mabs-v3-fails-in-russian-locale"></a>Upgrade na MABS V3 se nezdařil v ruštině národním prostředí.

**Popis:** Upgrade z MABS v2 na MABS V3 v ruštině locale se nezdařil s kódem chyby **4387**.

**Alternativní řešení:** Proveďte následující kroky, pokud chcete upgradovat na MABS V3 pomocí nástroje Ruština Install Package:

1. [Zálohujte](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server?view=sql-server-2017#SSMSProcedure) databázi SQL a odinstalujte MABS v2 (při odinstalaci si můžete zachovat chráněná data).
2. Upgradujte na SQL 2017 (Enterprise) a odinstalujte hlášení v rámci upgradu.
3. [Nainstalovat](https://docs.microsoft.com/sql/reporting-services/install-windows/install-reporting-services?view=sql-server-2017#install-your-report-server) SQL Server Reporting Services (SSRS).
4. [Nainstalovat](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) SQL Server Management Studio (SSMS).
5. Nakonfigurujte vytváření sestav pomocí parametrů popsaných v [konfiguraci služby SSRS pomocí SQL 2017](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#upgrade-mabs).
6. [Nainstalovat](backup-azure-microsoft-azure-backup.md) MABS V3.
7. [Obnovit](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms?view=sql-server-2017) SQL pomocí SSMS a spusťte nástroj DPM-Sync, jak je popsáno [zde](https://docs.microsoft.com/previous-versions/system-center/data-protection-manager-2010/ff634215(v=technet.10)).
8. Aktualizujte vlastnost ' verze databáze ' v tabulce dbo. tbl_DLS_GlobalSetting pomocí následujícího příkazu:

```sql
        UPDATE dbo.tbl_DLS_GlobalSetting
        set PropertyValue = '13.0.415.0'
        where PropertyName = 'DatabaseVersion'
```

9. Spusťte službu MSDPM.

## <a name="next-steps"></a>Další kroky

[Co je nového v MABS V3](backup-mabs-whats-new-mabs.md)
