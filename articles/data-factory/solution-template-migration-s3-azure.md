---
title: Migrace dat ze služby Amazon S3 do Azure Data Lake Storage Gen2
description: Naučte se používat šablonu řešení k migraci dat ze služby Amazon S3 pomocí tabulky externích ovládacích prvků k uložení seznamu oddílů v AWS S3 pomocí Azure Data Factory.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/07/2019
ms.openlocfilehash: c1fd4cb248abdc219c6ee5d098e10c329826c160
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100361979"
---
# <a name="migrate-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Migrace dat ze služby Amazon S3 do Azure Data Lake Storage Gen2

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Pomocí šablon můžete migrovat petabajty dat sestávající ze stovek milionů souborů z Amazon S3 do Azure Data Lake Storage Gen2. 

 > [!NOTE]
 > Pokud chcete zkopírovat malý objem dat z AWS S3 do Azure (například méně než 10 TB), je efektivnější a snadno používat [nástroj Azure Data Factory kopírování dat](copy-data-tool.md). Šablona popsaná v tomto článku je větší, než co potřebujete.

## <a name="about-the-solution-templates"></a>O šablonách řešení

Datový oddíl se doporučuje hlavně při migraci více než 10 TB dat. Chcete-li rozdělit data na oddíly, pomocí nastavení "prefix" vyfiltrujte složky a soubory v Amazon S3 podle názvu a potom jednotlivé úlohy kopírování ADF mohou kopírovat jeden oddíl po druhém. Pro zajištění lepší propustnosti můžete souběžně spustit více úloh kopírování přes ADF.

Migrace dat normálně vyžaduje jednorázovou migraci historických dat a pravidelné synchronizaci změn z AWS S3 do Azure. Níže jsou uvedené dvě šablony, kde jedna šablona pokrývá jednorázovou migraci historických dat a další šablona pokrývá synchronizaci změn z AWS S3 do Azure.

### <a name="for-the-template-to-migrate-historical-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Pro šablonu migrace historických dat ze služby Amazon S3 na Azure Data Lake Storage Gen2

Tato šablona (*název šablony: migrace historických dat z AWS S3 na Azure Data Lake Storage Gen2*) předpokládá, že jste v Azure SQL Database napsali seznam oddílů v tabulce externích ovládacích prvků. Proto bude používat aktivitu *vyhledávání* k načtení seznamu oddílů z tabulky externích ovládacích prvků, iterovat přes jednotlivé oddíly a vytvořit každou úlohu kopírování ADF v jednom okamžiku kopii jednoho oddílu. Po dokončení jakékoli úlohy kopírování se pomocí aktivity *uložená procedura* aktualizuje stav kopírování všech oddílů v řídicí tabulce.

Šablona obsahuje pět aktivit:
- **Vyhledávání** načte oddíly, které nebyly zkopírovány do Azure Data Lake Storage Gen2 z externí tabulky ovládacích prvků. Název tabulky je *s3_partition_control_table* a dotaz na načtení dat z tabulky je *"SELECT PARTITIONPREFIX from s3_partition_control_table WHERE SuccessOrFailure = 0"*.
- **Foreach** získá seznam oddílů z aktivity *vyhledávání* a projde každý oddíl do aktivity *TriggerCopy* . Můžete nastavit, aby *batchCount* spouštěla více úloh kopírování ADF současně. V této šabloně jsme nastavili 2.
- **ExecutePipeline** spustí kanál *CopyFolderPartitionFromS3* . Důvodem je, že vytvoříme další kanál, aby bylo možné každou úlohu kopírování zkopírovat oddíl, protože to usnadňuje opakované načtení tohoto konkrétního oddílu z AWS S3. Všechny ostatní úlohy kopírování načítající jiné oddíly nebudou mít vliv na.
- **Kopírovat** zkopíruje každý oddíl ze AWS S3 do Azure Data Lake Storage Gen2.
- **SqlServerStoredProcedure** aktualizovat stav kopírování všech oddílů v řídicí tabulce.

Šablona obsahuje dva parametry:
- **AWS_S3_bucketName** je název vašeho kontejneru v AWS S3, ze kterého chcete migrovat data. Pokud chcete migrovat data z několika intervalů v AWS S3, můžete do své tabulky externích ovládacích prvků přidat další sloupec pro uložení názvu sady pro každý oddíl a také aktualizovat svůj kanál, aby se odpovídajícím způsobem načetla data z tohoto sloupce.
- **Azure_Storage_fileSystem** je název systému souborů v Azure Data Lake Storage Gen2, do kterého chcete migrovat data.

### <a name="for-the-template-to-copy-changed-files-only-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Aby šablona zkopírovala změněné soubory pouze ze služby Amazon S3 do Azure Data Lake Storage Gen2

Tato šablona (*název šablony: kopírovat rozdílová data z AWS S3 do Azure Data Lake Storage Gen2*) používá časposledníúpravy každého souboru ke zkopírování nových nebo aktualizovaných souborů pouze z AWS S3 do Azure. Počítejte s tím, že vaše soubory nebo složky již byly rozděleny s timeslice informacemi jako součást názvu souboru nebo složky v AWS S3 (například/yyyy/MM/DD/file.csv), můžete přejít k tomuto [kurzu](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md) a získat tak další postup pro přírůstkové načítání nových souborů. Tato šablona předpokládá, že jste v Azure SQL Database vytvořili seznam oddílů v externí tabulce ovládacích prvků. Proto bude používat aktivitu *vyhledávání* k načtení seznamu oddílů z tabulky externích ovládacích prvků, iterovat přes jednotlivé oddíly a vytvořit každou úlohu kopírování ADF v jednom okamžiku kopii jednoho oddílu. Když Každá úloha kopírování začne kopírovat soubory ze AWS S3, spoléhá se na vlastnost Časposledníúpravy, která identifikuje a zkopíruje pouze nové nebo aktualizované soubory. Po dokončení jakékoli úlohy kopírování se pomocí aktivity *uložená procedura* aktualizuje stav kopírování všech oddílů v řídicí tabulce.

Šablona obsahuje sedm aktivit:
- **Vyhledávání** načte oddíly z externí tabulky ovládacích prvků. Název tabulky je *s3_partition_delta_control_table* a dotaz pro načtení dat z tabulky je *"vybrat odlišné PartitionPrefix z s3_partition_delta_control_table"*.
- **Foreach** získá seznam oddílů z aktivity *vyhledávání* a projde každý oddíl do aktivity *TriggerDeltaCopy* . Můžete nastavit, aby *batchCount* spouštěla více úloh kopírování ADF současně. V této šabloně jsme nastavili 2.
- **ExecutePipeline** spustí kanál *DeltaCopyFolderPartitionFromS3* . Důvodem je, že vytvoříme další kanál, aby bylo možné každou úlohu kopírování zkopírovat oddíl, protože to usnadňuje opakované načtení tohoto konkrétního oddílu z AWS S3. Všechny ostatní úlohy kopírování načítající jiné oddíly nebudou mít vliv na.
- **Vyhledávání** načte poslední čas spuštění úlohy kopírování z tabulky externích ovládacích prvků tak, aby se nové nebo aktualizované soubory mohly identifikovat pomocí časposledníúpravy. Název tabulky je *s3_partition_delta_control_table* a dotaz pro načtení dat z tabulky je *"SELECT Max (JobRunTime) as časposledníúpravy from s3_partition_delta_control_table WHERE PartitionPrefix = ' @ {Pipeline (). Parameters. prefixStr} ' a SuccessOrFailure = 1"*.
- **Kopírování** zkopíruje nové nebo změněné soubory pouze pro každý oddíl ze AWS S3 do Azure Data Lake Storage Gen2. Vlastnost *modifiedDatetimeStart* je nastavená na čas posledního spuštění úlohy kopírování. Vlastnost *modifiedDatetimeEnd* je nastavena na aktuální čas spuštění úlohy kopírování. Počítejte s tím, že čas se aplikuje na časové pásmo UTC.
- **SqlServerStoredProcedure** aktualizovat stav kopírování jednotlivých oddílů a kopírovat dobu běhu v tabulce ovládacích prvků, když je úspěšná. Sloupec SuccessOrFailure je nastaven na hodnotu 1.
- **SqlServerStoredProcedure** aktualizovat stav kopírování jednotlivých oddílů a kopírovat dobu běhu v kontrolní tabulce, pokud se nezdařila. Sloupec SuccessOrFailure je nastaven na hodnotu 0.

Šablona obsahuje dva parametry:
- **AWS_S3_bucketName** je název vašeho kontejneru v AWS S3, ze kterého chcete migrovat data. Pokud chcete migrovat data z několika intervalů v AWS S3, můžete do své tabulky externích ovládacích prvků přidat další sloupec pro uložení názvu sady pro každý oddíl a také aktualizovat svůj kanál, aby se odpovídajícím způsobem načetla data z tohoto sloupce.
- **Azure_Storage_fileSystem** je název systému souborů v Azure Data Lake Storage Gen2, do kterého chcete migrovat data.

## <a name="how-to-use-these-two-solution-templates"></a>Jak používat tyto dvě šablony řešení

### <a name="for-the-template-to-migrate-historical-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Pro šablonu migrace historických dat ze služby Amazon S3 na Azure Data Lake Storage Gen2 

1. Vytvořte v Azure SQL Database tabulku ovládacího prvku pro uložení seznamu oddílů AWS S3. 

    > [!NOTE]
    > Název tabulky je s3_partition_control_table.
    > Schéma řídicí tabulky je PartitionPrefix a SuccessOrFailure, kde PartitionPrefix je nastavení předpony ve S3, aby vyfiltroval složky a soubory v Amazon S3 podle názvu a SuccessOrFailure je stav kopírování jednotlivých oddílů: 0 znamená, že se tento oddíl nezkopíroval do Azure a 1 znamená, že se tento oddíl zkopíroval do Azure úspěšně.
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

2. Vytvořte uloženou proceduru pro stejný Azure SQL Database pro kontrolní tabulku. 

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

3. Pokud chcete Azure Data Lake Storage Gen2 šablonu, přejdete na **migrace historických dat z AWS S3** . Zadejte připojení do tabulky externích ovládacích prvků, AWS S3 jako úložiště zdrojů dat a Azure Data Lake Storage Gen2 jako cílové úložiště. Mějte na paměti, že externí řídicí tabulka a uložená procedura odkazují na stejné připojení.

    ![Snímek obrazovky, který zobrazuje migraci historických dat z AWS S3 na šablonu Azure Data Lake Storage Gen2.](media/solution-template-migration-s3-azure/historical-migration-s3-azure1.png)

4. Vyberte **Použít tuto šablonu**.

    ![Snímek obrazovky, který zvýrazní tlačítko použít tuto šablonu.](media/solution-template-migration-s3-azure/historical-migration-s3-azure2.png)
    
5. Uvidíte, že se vytvořily dva kanály a 3 datové sady, jak je znázorněno v následujícím příkladu:

    ![Snímek obrazovky zobrazující dva kanály a tři datové sady, které byly vytvořeny pomocí šablony.](media/solution-template-migration-s3-azure/historical-migration-s3-azure3.png)

6. Přejděte do kanálu "BulkCopyFromS3" a vyberte **ladit**, zadejte **parametry**. Pak vyberte **Finish** (Dokončit).

    ![Snímek obrazovky, který ukazuje, kde vybrat ladění a zadat parametry před výběrem dokončit.](media/solution-template-migration-s3-azure/historical-migration-s3-azure4.png)

7. Zobrazí se výsledky podobné následujícímu příkladu:

    ![Snímek obrazovky zobrazující vrácené výsledky](media/solution-template-migration-s3-azure/historical-migration-s3-azure5.png)


### <a name="for-the-template-to-copy-changed-files-only-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Aby šablona zkopírovala změněné soubory pouze ze služby Amazon S3 do Azure Data Lake Storage Gen2

1. Vytvořte v Azure SQL Database tabulku ovládacího prvku pro uložení seznamu oddílů AWS S3. 

    > [!NOTE]
    > Název tabulky je s3_partition_delta_control_table.
    > Schéma řídicí tabulky je PartitionPrefix, JobRunTime a SuccessOrFailure, kde PartitionPrefix je nastavení předpony ve S3, aby vyfiltroval složky a soubory v Amazon S3 podle názvu, JobRunTime je hodnota DateTime při spuštění kopírování úloh a SuccessOrFailure je stavem kopírování jednotlivých oddílů: 0 znamená to, že se tento oddíl do Azure úspěšně zkopíroval.
    > V řídicí tabulce je definováno 5 oddílů. Výchozí hodnota pro JobRunTime může být čas, kdy se spustí jednorázová migrace historických dat. Aktivita kopírování ADF zkopíruje soubory do AWS S3, které byly po uplynutí této doby naposledy změněny. Výchozí stav kopírování jednotlivých oddílů je 1.

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

2. Vytvořte uloženou proceduru pro stejný Azure SQL Database pro kontrolní tabulku. 

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


3. Pokud chcete Azure Data Lake Storage Gen2 šablonu, přejdete do části **kopírování rozdílových dat z AWS S3** . Zadejte připojení do tabulky externích ovládacích prvků, AWS S3 jako úložiště zdrojů dat a Azure Data Lake Storage Gen2 jako cílové úložiště. Mějte na paměti, že externí řídicí tabulka a uložená procedura odkazují na stejné připojení.

    ![Vytvoření nového připojení](media/solution-template-migration-s3-azure/delta-migration-s3-azure1.png)

4. Vyberte **Použít tuto šablonu**.

    ![Použít tuto šablonu](media/solution-template-migration-s3-azure/delta-migration-s3-azure2.png)
    
5. Uvidíte, že se vytvořily dva kanály a 3 datové sady, jak je znázorněno v následujícím příkladu:

    ![Kontrola kanálu](media/solution-template-migration-s3-azure/delta-migration-s3-azure3.png)

6.  Přejděte do kanálu "DeltaCopyFromS3" a vyberte **ladit** a zadejte **parametry**. Pak vyberte **Finish** (Dokončit).

    ![Klikněte na * * ladit * *.](media/solution-template-migration-s3-azure/delta-migration-s3-azure4.png)

7. Zobrazí se výsledky podobné následujícímu příkladu:

    ![Kontrola výsledku](media/solution-template-migration-s3-azure/delta-migration-s3-azure5.png)

8. Můžete také kontrolovat výsledky z tabulky Control pomocí dotazu *"SELECT * from s3_partition_delta_control_table"*, zobrazí se výstup podobný následujícímu příkladu:

    ![Snímek obrazovky, který zobrazuje výsledky z tabulky ovládacích prvků po spuštění dotazu.](media/solution-template-migration-s3-azure/delta-migration-s3-azure6.png)
    
## <a name="next-steps"></a>Další kroky

- [Kopírování souborů z několika kontejnerů](solution-template-copy-files-multiple-containers.md)
- [Přesunutí souborů](solution-template-move-files.md)
