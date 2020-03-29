---
title: Migrace dat z Amazonu S3 do Azure Data Lake Storage Gen2
description: Zjistěte, jak pomocí šablony řešení migrovat data z AmazonS3 pomocí tabulky externího ovládacího prvku k uložení seznamu oddílů na AWS S3 s Azure Data Factory.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/07/2019
ms.openlocfilehash: e918fe01426202746f0225d25304b9c1b26cb74b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74927323"
---
# <a name="migrate-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Migrace dat z Amazonu S3 do Azure Data Lake Storage Gen2

Pomocí šablon můžete migrovat petabajty dat skládajících se ze stovek milionů souborů z Amazonu S3 do Azure Data Lake Storage Gen2. 

 > [!NOTE]
 > Pokud chcete zkopírovat malý svazek dat z AWS S3 do Azure (například méně než 10 TB), je efektivnější a jednodušší používat [nástroj Azure Data Factory Copy Data .](copy-data-tool.md) Šablona popsaná v tomto článku je víc než to, co potřebujete.

## <a name="about-the-solution-templates"></a>Šablony řešení

Datový oddíl se doporučuje zejména při migraci více než 10 TB dat. Chcete-li rozdělit data, využijte nastavení "prefix" k filtrování složek a souborů na AmazonS3 podle názvu a pak každá úloha kopírování ADF může kopírovat jeden oddíl najednou. Pro lepší propustnost můžete spustit více úloh kopírování ADF současně.

Migrace dat obvykle vyžaduje jednorázovou historickou migraci dat a pravidelnou synchronizaci změn z AWS S3 do Azure. Níže jsou dvě šablony, kde jedna šablona pokrývá jednorázovou migraci historických dat a druhá šablona zahrnuje synchronizaci změn z AWS S3 do Azure.

### <a name="for-the-template-to-migrate-historical-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Pro šablonu pro migraci historických dat z Amazon S3 do Azure Data Lake Storage Gen2

Tato šablona (*název šablony: migrace historických dat z AWS S3 do Azure Data Lake Storage Gen2*) předpokládá, že jste napsali seznam oddílů v tabulce externího ovládacího prvku v Azure SQL Database. Tak to bude používat *vyhledávací* aktivity načíst seznam oddílů z tabulky externího ovládacího prvku, iterát přes každý oddíl a aby každý adf kopie úlohy kopie jeden oddíl najednou. Po dokončení jakékoli úlohy kopírování použije *aktivitu uložené procedury* k aktualizaci stavu kopírování jednotlivých oddílů v řídicí tabulce.

Šablona obsahuje pět aktivit:
- **Vyhledávání načte** oddíly, které nebyly zkopírovány do Azure Data Lake Storage Gen2 z tabulky externího ovládacího prvku. Název tabulky je *s3_partition_control_table* a dotaz pro načtení dat z tabulky je *"SELECT PartitionPrefix from s3_partition_control_table Where SuccessOrFailure = 0"*.
- **ForEach** získá seznam oddílů z *aktivity vyhledávání* a itetuje každý oddíl na *TriggerCopy* aktivity. BatchCount můžete *batchCount* nastavit tak, aby souběžně spouštěl více úloh kopírování ADF. V této šabloně jsme nastavili 2.
- **Příkaz ExecutePipeline** provede kanál *CopyFolderPartitionFromS3.* Důvod, proč jsme vytvořit jiný kanál, aby se každá kopie kopie úlohy kopírování oddíl je, protože to bude snadné znovu spustit neúspěšné kopie úlohy znovu načíst tento konkrétní oddíl znovu z AWS S3. Všechny ostatní úlohy kopírování načítání jiných oddílů nebudou ovlivněny.
- **Zkopírujte** kopie jednotlivých oddílů z AWS S3 do Azure Data Lake Storage Gen2.
- **SqlServerStoredProcedure** aktualizovat stav kopírování jednotlivých oddílů v tabulce ovládacího prvku.

Šablona obsahuje dva parametry:
- **AWS_S3_bucketName** je název vašeho bloku na AWS S3, ze kterého chcete migrovat data. Pokud chcete migrovat data z více bloků na AWS S3, můžete přidat ještě jeden sloupec v tabulce externího ovládacího prvku pro uložení názvu bloku pro každý oddíl a také aktualizovat kanál pro načtení dat z tohoto sloupce odpovídajícím způsobem.
- **Azure_Storage_fileSystem** je název vašeho fileSystem na Azure Data Lake Storage Gen2, kam chcete migrovat data.

### <a name="for-the-template-to-copy-changed-files-only-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Pro šablonu ke kopírování změněných souborů pouze z Amazon S3 do Azure Data Lake Storage Gen2

Tato šablona (*název šablony: zkopírujte rozdílová data z AWS S3 do Azure Data Lake Storage Gen2*) používá LastModifiedTime každého souboru ke kopírování nových nebo aktualizovaných souborů jenom z AWS S3 do Azure. Uvědomte si, zda vaše soubory nebo složky již byly časově rozděleny s informacemi o timeslice jako součást názvu souboru nebo složky na AWS S3 (například /yyyy/mm/dd/file.csv), můžete přejít do tohoto [kurzu,](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md) abyste získali výkonnější přístup pro přírůstkové načítání nových souborů. Tato šablona předpokládá, že jste v Azure SQL Database napsali seznam oddílů v tabulce externího ovládacího prvku. Tak to bude používat *vyhledávací* aktivity načíst seznam oddílů z tabulky externího ovládacího prvku, iterát přes každý oddíl a aby každý adf kopie úlohy kopie jeden oddíl najednou. Když každá úloha kopírování začne kopírovat soubory z AWS S3, spoléhá se na Vlastnost LastModifiedTime k identifikaci a zkopírování pouze nové nebo aktualizované soubory. Po dokončení jakékoli úlohy kopírování použije *aktivitu uložené procedury* k aktualizaci stavu kopírování jednotlivých oddílů v řídicí tabulce.

Šablona obsahuje sedm aktivit:
- **Vyhledávání** načte oddíly z tabulky externího ovládacího prvku. Název tabulky je *s3_partition_delta_control_table* a dotaz pro načtení dat z tabulky je *"select distinct PartitionPrefix from s3_partition_delta_control_table"*.
- **ForEach** získá seznam oddílů z *aktivity vyhledávání* a iterates každý oddíl *triggerDeltaCopy* aktivity. BatchCount můžete *batchCount* nastavit tak, aby souběžně spouštěl více úloh kopírování ADF. V této šabloně jsme nastavili 2.
- **Spuštění kanálu** spustí kanál *DeltaCopyFolderPartitionFromS3.* Důvod, proč jsme vytvořit jiný kanál, aby se každá kopie kopie úlohy kopírování oddíl je, protože to bude snadné znovu spustit neúspěšné kopie úlohy znovu načíst tento konkrétní oddíl znovu z AWS S3. Všechny ostatní úlohy kopírování načítání jiných oddílů nebudou ovlivněny.
- **Vyhledávání načte** poslední dobu spuštění úlohy kopírování z tabulky externího ovládacího prvku, aby bylo možné identifikovat nové nebo aktualizované soubory prostřednictvím lastmodifiedtime. Název tabulky je *s3_partition_delta_control_table* a dotaz pro načtení dat z tabulky je *"select max(JobRunTime) jako LastModifiedTime z s3_partition_delta_control_table kde PartitionPrefix = '@{pipeline().parameters.prefixStr}' a SuccessOrFailure = 1"*.
- **Zkopírujte** kopie nových nebo změněných souborů jenom pro každý oddíl z AWS S3 do Azure Data Lake Storage Gen2. Vlastnost *modifiedDatetimeStart* je nastavena na poslední dobu běhu úlohy kopírování. Vlastnost *modifiedDatetimeEnd* je nastavena na aktuální dobu běhu úlohy kopírování. Uvědomte si, že čas je použit pro časové pásmo UTC.
- **SqlServerStoredProcedure** aktualizovat stav kopírování jednotlivých oddílů a kopírování běhu v tabulce ovládacího prvku, když se to podaří. Sloupec SuccessOrFailure je nastavena na 1.
- **SqlServerStoredProcedure** aktualizovat stav kopírování jednotlivých oddílů a kopírování běhu v tabulce ovládacího prvku, když se nezdaří. Sloupec SuccessOrFailure je nastavena na 0.

Šablona obsahuje dva parametry:
- **AWS_S3_bucketName** je název vašeho bloku na AWS S3, ze kterého chcete migrovat data. Pokud chcete migrovat data z více bloků na AWS S3, můžete přidat ještě jeden sloupec v tabulce externího ovládacího prvku pro uložení názvu bloku pro každý oddíl a také aktualizovat kanál pro načtení dat z tohoto sloupce odpovídajícím způsobem.
- **Azure_Storage_fileSystem** je název vašeho fileSystem na Azure Data Lake Storage Gen2, kam chcete migrovat data.

## <a name="how-to-use-these-two-solution-templates"></a>Jak používat tyto dvě šablony řešení

### <a name="for-the-template-to-migrate-historical-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Pro šablonu pro migraci historických dat z Amazon S3 do Azure Data Lake Storage Gen2 

1. Vytvořte tabulku ovládacích prvku v Azure SQL Database pro uložení seznamu oddílů AWS S3. 

    > [!NOTE]
    > Název tabulky je s3_partition_control_table.
    > Schéma řídicí tabulky je PartitionPrefix a SuccessOrFailure, kde PartitionPrefix je nastavení předpony v S3 filtrovat složky a soubory v Amazon S3 podle názvu a SuccessOrFailure je stav kopírování každého oddílu: 0 znamená, že tento oddíl má nebylzkopírovaný do Azure a 1 znamená, že tento oddíl byl úspěšně zkopírován do Azure.
    > V řídicí tabulce je definováno 5 oddílů a výchozí stav kopírování jednotlivých oddílů je 0.

    ```sql
    CREATE TABLE [dbo].[s3_partition_control_table](
        [PartitionPrefix] [varchar](255) NULL,
        [SuccessOrFailure] [bit] NULL
    )

    INSERT INTO s3_partition_control_table (PartitionPrefix, SuccessOrFailure)
    VALUES
    ('a', 0),
    ('b', 0),
    ('c', 0),
    ('d', 0),
    ('e', 0);
    ```

2. Vytvořte uloženou proceduru ve stejné tabulce Azure SQL Database for control. 

    > [!NOTE]
    > Název uložené procedury je sp_update_partition_success. Bude vyvolána aktivitou SqlServerStoredProcedure v kanálu ADF.

    ```sql
    CREATE PROCEDURE [dbo].[sp_update_partition_success] @PartPrefix varchar(255)
    AS
    BEGIN
    
        UPDATE s3_partition_control_table
        SET [SuccessOrFailure] = 1 WHERE [PartitionPrefix] = @PartPrefix
    END
    GO
    ```

3. Přejděte na šablonu **Migrate historických dat z AWS S3 do šablony Azure Data Lake Storage Gen2.** Zadejte připojení k tabulce externího ovládacího prvku, AWS S3 jako úložiště zdrojů dat a Azure Data Lake Storage Gen2 jako cílové úložiště. Uvědomte si, že tabulka externího ovládacího prvku a uložená procedura odkazují na stejné připojení.

    ![Vytvoření nového připojení](media/solution-template-migration-s3-azure/historical-migration-s3-azure1.png)

4. Vyberte **Použít tuto šablonu**.

    ![Použít tuto šablonu](media/solution-template-migration-s3-azure/historical-migration-s3-azure2.png)
    
5. Uvidíte, že byly vytvořeny 2 kanály a 3 datové sady, jak je znázorněno v následujícím příkladu:

    ![Kontrola kanálu](media/solution-template-migration-s3-azure/historical-migration-s3-azure3.png)

6. Vyberte **Ladit**, zadejte **parametry**a pak vyberte **Dokončit**.

    ![Klikněte na **Ladění**](media/solution-template-migration-s3-azure/historical-migration-s3-azure4.png)

7. Zobrazí se výsledky, které jsou podobné v následujícím příkladu:

    ![Zkontrolujte výsledek](media/solution-template-migration-s3-azure/historical-migration-s3-azure5.png)


### <a name="for-the-template-to-copy-changed-files-only-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Pro šablonu ke kopírování změněných souborů pouze z Amazon S3 do Azure Data Lake Storage Gen2

1. Vytvořte tabulku ovládacích prvku v Azure SQL Database pro uložení seznamu oddílů AWS S3. 

    > [!NOTE]
    > Název tabulky je s3_partition_delta_control_table.
    > Schéma řídicí tabulky je PartitionPrefix, JobRunTime a SuccessOrFailure, kde PartitionPrefix je nastavení předpony v S3 pro filtrování složek a souborů v Amazon S3 podle názvu, JobRunTime je datetime hodnota při spuštění úlohy kopírování a SuccessOrFailure je stav kopírování jednotlivých oddílů: 0 znamená, že tento oddíl nebyl zkopírován do Azure a 1 znamená, že tento oddíl byl úspěšně zkopírován do Azure.
    > V řídicí tabulce je definováno 5 oddílů. Výchozí hodnota jobruntime může být čas při jednorázové historické migrace dat. ADF kopírování aktivity zkopírují soubory na AWS S3, které byly naposledy změněny po této době. Výchozí stav kopírování jednotlivých oddílů je 1.

    ```sql
    CREATE TABLE [dbo].[s3_partition_delta_control_table](
        [PartitionPrefix] [varchar](255) NULL,
        [JobRunTime] [datetime] NULL,
        [SuccessOrFailure] [bit] NULL
        )

    INSERT INTO s3_partition_delta_control_table (PartitionPrefix, JobRunTime, SuccessOrFailure)
    VALUES
    ('a','1/1/2019 12:00:00 AM',1),
    ('b','1/1/2019 12:00:00 AM',1),
    ('c','1/1/2019 12:00:00 AM',1),
    ('d','1/1/2019 12:00:00 AM',1),
    ('e','1/1/2019 12:00:00 AM',1);
    ```

2. Vytvořte uloženou proceduru ve stejné tabulce Azure SQL Database for control. 

    > [!NOTE]
    > Název uložené procedury je sp_insert_partition_JobRunTime_success. Bude vyvolána aktivitou SqlServerStoredProcedure v kanálu ADF.

    ```sql
        CREATE PROCEDURE [dbo].[sp_insert_partition_JobRunTime_success] @PartPrefix varchar(255), @JobRunTime datetime, @SuccessOrFailure bit
        AS
        BEGIN
            INSERT INTO s3_partition_delta_control_table (PartitionPrefix, JobRunTime, SuccessOrFailure)
            VALUES
            (@PartPrefix,@JobRunTime,@SuccessOrFailure)
        END
        GO
    ```


3. Přejděte na šablonu **Kopírovat delta z AWS S3 do šablony Azure Data Lake Storage Gen2.** Zadejte připojení k tabulce externího ovládacího prvku, AWS S3 jako úložiště zdrojů dat a Azure Data Lake Storage Gen2 jako cílové úložiště. Uvědomte si, že tabulka externího ovládacího prvku a uložená procedura odkazují na stejné připojení.

    ![Vytvoření nového připojení](media/solution-template-migration-s3-azure/delta-migration-s3-azure1.png)

4. Vyberte **Použít tuto šablonu**.

    ![Použít tuto šablonu](media/solution-template-migration-s3-azure/delta-migration-s3-azure2.png)
    
5. Uvidíte, že byly vytvořeny 2 kanály a 3 datové sady, jak je znázorněno v následujícím příkladu:

    ![Kontrola kanálu](media/solution-template-migration-s3-azure/delta-migration-s3-azure3.png)

6. Vyberte **Ladit**, zadejte **parametry**a pak vyberte **Dokončit**.

    ![Klikněte na **Ladění**](media/solution-template-migration-s3-azure/delta-migration-s3-azure4.png)

7. Zobrazí se výsledky, které jsou podobné v následujícím příkladu:

    ![Zkontrolujte výsledek](media/solution-template-migration-s3-azure/delta-migration-s3-azure5.png)

8. Můžete také zkontrolovat výsledky z ovládací tabulky dotazem *"select * from s3_partition_delta_control_table"*, zobrazí se výstup podobný následujícímu příkladu:

    ![Zkontrolujte výsledek](media/solution-template-migration-s3-azure/delta-migration-s3-azure6.png)
    
## <a name="next-steps"></a>Další kroky

- [Kopírování souborů z několika kontejnerů](solution-template-copy-files-multiple-containers.md)
- [Přesunutí souborů](solution-template-move-files.md)