---
title: Poznámky k verzi pro Microsoft Azure Backup Server V3
description: Tento článek poskytuje informace o známých problémech a alternativním řešení pro Microsoft Azure Backup Server (MABS) v3.
ms.topic: conceptual
ms.date: 06/03/2020
ms.asset: 0c4127f2-d936-48ef-b430-a9198e425d81
ms.openlocfilehash: 87bc415c125a387d98ac88255d77fb1867564acf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91254257"
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

1. [Zálohujte](/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server#SSMSProcedure) databázi SQL a odinstalujte MABS v2 (při odinstalaci si můžete zachovat chráněná data).
2. Upgradujte na SQL 2017 (Enterprise) a odinstalujte hlášení v rámci upgradu.
3. [Nainstalovat](/sql/reporting-services/install-windows/install-reporting-services#install-your-report-server) SQL Server Reporting Services (SSRS).
4. [Nainstalovat](/sql/ssms/download-sql-server-management-studio-ssms) SQL Server Management Studio (SSMS).
5. Nakonfigurujte vytváření sestav pomocí parametrů popsaných v [konfiguraci služby SSRS pomocí SQL 2017](./backup-azure-microsoft-azure-backup.md#upgrade-mabs).
6. [Nainstalovat](backup-azure-microsoft-azure-backup.md) MABS V3.
7. [Obnovit](/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms) SQL pomocí SSMS a spusťte DPM-Sync Tool, jak je popsáno [zde](/system-center/dpm/back-up-the-dpm-server#using-dpmsync).
8. Aktualizujte vlastnost ' verze databáze ' v tabulce dbo.tbl_DLS_GlobalSetting pomocí následujícího příkazu:

    ```sql
            UPDATE dbo.tbl_DLS_GlobalSetting
            set PropertyValue = '13.0.415.0'
            where PropertyName = 'DatabaseVersion'
    ```

9. Spusťte službu MSDPM.

## <a name="after-installing-ur1-the-mabs-reports-arent-updated-with-new-rdl-files"></a>Po instalaci UR1 se sestavy MABS neaktualizují s novými soubory RDL.

**Popis**: v UR1 je problém formátování sestavy MABS vyřešen aktualizovanými soubory RDL. Nové soubory RDL nejsou automaticky nahrazeny existujícími soubory.

**Alternativní řešení**: Pokud chcete nahradit soubory RDL, postupujte podle následujících kroků:

1. Na počítači s MABS otevřete adresu URL webového portálu SQL Reporting Services.
1. V adrese URL webového portálu se složka DPMReports nachází ve formátu **`DPMReports_<GUID>`**

    >[!NOTE]
    >Tato konvence vytváření názvů vždy obsahuje jenom jednu složku. Pokud se MABS upgraduje z předchozí verze, může to být také jiná starší složka, ale nebudete ji moct otevřít.

    ![DPMReports složka](./media/backup-mabs-release-notes-v3/dpm-reports-folder.png)

1. Vyberte a otevřete **`DPMReports_<GUID>`** složku. Jednotlivé soubory sestav budou uvedeny, jak je uvedeno níže.

    ![Seznam jednotlivých souborů sestav](./media/backup-mabs-release-notes-v3/individual-report-files.png)

1. Vyberte soubory sestav, které nekončí **sestavou**, klikněte pravým tlačítkem na **možnost** a vyberte **Spravovat**.

    ![Výběr možnosti spravovat pro soubory sestav](./media/backup-mabs-release-notes-v3/manage-files.png)

1. Na stránce Nový vyberte možnost **nahradit** k nahrazení souborů nejnovějšími soubory sestav.

    Nejnovější soubory sestav najdete v cestě. `<MABS Installation Directory>\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin\DpmReports`

    Příklad: `C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin\DpmReports`

    ![Nahradit soubory nejnovějšími soubory sestav](./media/backup-mabs-release-notes-v3/replace-files.png)

    Po nahrazení souborů se ujistěte, že **název** a **Popis** jsou nedotčeny a nejsou prázdné.

1. Po nahrazení souborů restartujte služby MABS a použijte soubory sestav.

## <a name="next-steps"></a>Další kroky

[Co je nového v MABS](backup-mabs-whats-new-mabs.md)
