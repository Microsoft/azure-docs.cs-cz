---
title: Zpráva k vydání verze pro Microsoft Azure Backup serveru v3
description: Tento článek obsahuje informace o známých problémů a řešení pro MABS v3.
services: backup
author: JYOTHIRMAISURI
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 11/22/2018
ms.author: v-jysur
ms.asset: 0c4127f2-d936-48ef-b430-a9198e425d81
ms.openlocfilehash: 1d077fc8a853f145d271fa69d453ea2ea70ab696
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2018
ms.locfileid: "52319474"
---
# <a name="release-notes-for-microsoft-azure-backup-server"></a>Zpráva k vydání verze pro Microsoft Azure Backup serveru
Tento článek obsahuje známé problémy a řešení pro Microsoft Azure Backup Server (MABS) V3.

##  <a name="backup-and-recovery-fails-for-clustered-workloads"></a>Zálohování a obnovení u clusterovaných úloh selže

**Popis:** zálohování a obnovení po upgradu MABS V2 na MABS V3 selže pro clusterovaná data zdroje jako cluster Hyper-V nebo clusteru serveru SQL (SQL Always On) nebo Exchange ve skupině dostupnosti databáze (DAG).

**Obejít:** Chcete-li tomu zabránit, otevřete SQL Server Management Studio (SSMS)) a spusťte následující skript SQL v databázi aplikace DPM:


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


##  <a name="upgrade-to-mabs-v3-fails-in-russian-locale"></a>Selhání upgradu na MABS V3 v Ruské národního prostředí

**Popis:** Upgrade z MABS V2 na V3 MABS v národním prostředí ruština selže s kódem chyby **4387**.

**Alternativní řešení:** proveďte následující kroky pro upgrade na MABS V3 pomocí ruština nainstalovat balíček:

1.  [Zálohování](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server?view=sql-server-2017#SSMSProcedure) SQL databázi a odinstalujte MABS V2 (zvolte uchovat chráněná data během odinstalace).
2.  Upgrade na SQL 2017 (podnikových) a odinstalujte vykazování jako součást upgradu.
3. [Nainstalujte](https://docs.microsoft.com/sql/reporting-services/install-windows/install-reporting-services?view=sql-server-2017#install-your-report-server) SQL Server Reporting Services (SSRS).
4.  [Nainstalujte](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017#ssms-installation-tips-and-issues-ssms-1791) SQL Server Management Studio (SSMS).
5.  Konfigurace generování sestav pomocí parametrů, jak je uvedeno v [konfigurace služby SSRS 2017 SQL](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#upgrade-mabs).
6.  [Nainstalujte](backup-azure-microsoft-azure-backup.md) MABS V3.
7. [Obnovení](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms?view=sql-server-2017) SQL s použitím aplikace SSMS a spuštění aplikace DPM synchronizační nástroj služby, jak je popsáno [tady](https://docs.microsoft.com/it-it/previous-versions/system-center/data-protection-manager-2010/ff634215(v=technet.10)).
8.  Umožňuje aktualizujte vlastnost "DataBaseVersion" v tabulce dbo.tbl_DLS_GlobalSetting pomocí následujícího příkazu:

        UPDATE dbo.tbl_DLS_GlobalSetting
        set PropertyValue = '13.0.415.0'
        where PropertyName = 'DatabaseVersion'


9.  Spusťte službu MSDPM.


## <a name="next-steps"></a>Další postup

[Co je nového ve verzi 3 MABS](backup-mabs-whats-new-mabs.md)
