---
title: Vytvoření úlohy streamování T-SQL ve službě Azure SQL Edge (Preview)
description: Další informace o vytváření úloh Stream Analytics ve službě Azure SQL Edge (Preview)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 7db7f9548a3daa86a53dd37fbe088661e8b7b17e
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83685172"
---
# <a name="create-stream-analytics-job-in-azure-sql-edge-preview"></a>Vytvoření úlohy Stream Analytics v Azure SQL Edge (Preview) 

Tento článek vysvětluje, jak vytvořit úlohu streamování T-SQL ve službě Azure SQL Edge (Preview). Chcete-li vytvořit úlohu streamování v SQL Edge, je nutné provést následující kroky.

1. Vytvoření vstupních a výstupních objektů externího datového proudu
2. V rámci vytváření úlohy streamování definujte dotaz na úlohu streamování.

> [!NOTE]
> Pokud chcete povolit funkci streamování T-SQL ve službě Azure SQL Edge, povolte jako možnost spuštění TF 11515 nebo použijte příkaz [DBCC TRACEON]( https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) . Další informace o tom, jak povolit příznaky trasování pomocí souboru MSSQL. conf, najdete v tématu [Konfigurace použití souboru MSSQL. conf](configure.md#configure-using-mssqlconf-file). Tento požadavek se odebere v budoucích aktualizacích Azure SQL Edge (Preview).

## <a name="configure-an-external-stream-input-and-output-object"></a>Konfigurace vstupního a výstupního objektu externího datového proudu

Streamování T-SQL využívá funkci externích zdrojů dat SQL Server k definování zdrojů dat přidružených k vstupům externích datových proudů a výstupům úlohy streamování. K vytvoření vstupního nebo výstupního objektu externího datového proudu jsou vyžadovány následující příkazy T-SQL.

[CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql)

[CREATE EXTERNAL DATA SOURCE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql)

[VYTVOŘIT externí datový proud (Transact-SQL)](#example-create-an-external-stream-object-sql-database)

V případě, že se jako výstupní datový proud používá SQL Edge (nebo SQL Server Azure SQL), je pro definování přihlašovacích údajů pro přístup k databázi SQL potřeba příkaz T-SQL s [rozsahem příkazu CREATE DATABASE scoped Credential (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql) .

### <a name="supported-input-and-output-stream-data-sources"></a>Podporované vstupní a výstupní datové proudy zdrojů dat

Azure SQL Edge aktuálně podporuje jenom následující zdroje dat jako vstupy a výstupy streamu.

| Typ zdroje dat | Vstup | Výstup | Popis |
|------------------|-------|--------|------------------|
| Centrum Azure IoT Edge | Ano | Ano | Zdroj dat pro čtení a zápis streamování dat do centra Azure IoT Edge. Další informace o centru Azure IoT Edge najdete v [centru IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub)|
| SQL Database | Ne | Ano | Připojení zdroje dat, které zapisuje streamovaná data do SQL Database. SQL Database může být místní databáze SQL Edge nebo vzdálený SQL Server nebo Azure SQL Database|
| Azure Blob Storage | Ne | Ano | Zdroj dat pro zápis dat do objektu BLOB v účtu úložiště Azure. |
| Kafka | Ano | Ne | Zdroj dat pro čtení dat streamování z tématu Kafka. Tento adaptér je teď dostupný jenom pro Azure SQL Edge verze Intel/AMD a není k dispozici pro ARM64 verzi SQL Edge.|

### <a name="example-create-an-external-stream-inputoutput-object-for-azure-iot-edge-hub"></a>Příklad: vytvoření objektu vstupu/výstupu externího datového proudu pro Azure IoT Edge hub

Následující příklad vytvoří externí objekt datového proudu pro centrum Edge. Pokud chcete vytvořit zdroj dat vstupu a výstupu externího datového proudu pro centrum Azure IoT Edge, musíte nejprve vytvořit externí formát souboru pro SQL, abyste pochopili, že se data čtou/napíší i vy.

1. Vytvořte formát externího souboru s typem formátu JSON.

    ```sql
    Create External file format InputFileFormat
    WITH (  
       format_type = JSON,
    )
    go
    ```

2. Vytvořte externí zdroj dat pro Centrum IoT Edge. Níže uvedený skript T-SQL vytvoří připojení zdroje dat k hraničnímu rozbočovači běžícímu na stejném hostiteli Docker jako SQL Edge.

    ```sql
    CREATE EXTERNAL DATA SOURCE EdgeHubInput WITH (
    LOCATION = 'edgehub://'
    )
    go
    ```

3. Vytvořte objekt externího datového proudu pro Centrum IoT Edge. Níže uvedený skript T-SQL vytvořte objekt Stream pro centrum Edge. V případě objektu datového proudu hraničního centra je parametr LOCATION názvem tématu nebo kanálu, do kterého se čte nebo zapisuje.

    ```sql
    CREATE EXTERNAL STREAM MyTempSensors WITH (
    DATA_SOURCE = EdgeHubInput,
    FILE_FORMAT = InputFileFormat,
    LOCATION = N'TemperatureSensors',
    INPUT_OPTIONS = N'',
    OUTPUT_OPTIONS = N''
    )
    go
    ```

### <a name="example-create-an-external-stream-object-sql-database"></a>Příklad: vytvoření objektu externího datového proudu SQL Database

Následující příklad vytvoří objekt externího datového proudu k místní databázi SQL Edge. 

1. Vytvořte v databázi hlavní klíč. Tento klíč je nutný k šifrování tajného kódu přihlašovacích údajů.

    ```sql
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<<Strong_Password_For_Master_Key_Encryption>>';
    ```

2. Vytvořte databázi s rozsahem pověření pro přístup ke zdroji SQL Server. Následující příklad vytvoří přihlašovací údaje k externímu zdroji dat pomocí IDENTITY = ' username ' a TAJNÉho klíče = ' password '.

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL SQLCredential
    WITH IDENTITY = '<SQL_Login>', SECRET = '<SQL_Login_PASSWORD>'
    go
    ```

3. Vytvořte externí zdroj dat pomocí vytvoření externího zdroje dat. Následující příklad:

    * Vytvoří externí zdroj dat s názvem LocalSQLOutput.
    * Určuje externí zdroj dat (umístění = ' <vendor> :// <server> [: <port> ] '). V příkladu odkazuje na místní instanci SQL Edge.
    * Nakonec v příkladu se používá dříve vytvořené přihlašovací údaje.

    ```sql
    CREATE EXTERNAL DATA SOURCE LocalSQLOutput WITH (
    LOCATION = 'sqlserver://tcp:.,1433'
    ,CREDENTIAL = SQLCredential
    )
    go
    ```

4. Vytvořte objekt externího streamu. Následující příklad vytvoří objekt externího datového proudu odkazující na tabulku *dbo. TemperatureMeasurements* v databázi *MySQLDatabase*.

    ```sql
    CREATE EXTERNAL STREAM TemperatureMeasurements WITH (
    DATA_SOURCE = LocalSQLOutput,
    LOCATION = N'MySQLDatabase.dbo.TemperatureMeasurements',
    INPUT_OPTIONS = N'',
    OUTPUT_OPTIONS = N''
    )
    ```

## <a name="create-the-streaming-job-and-the-streaming-queries"></a>Vytvořit úlohu streamování a dotazy streamování

Pomocí systémové uložené procedury **Sys. sp_create_streaming_job** můžete definovat dotazy streamování a vytvořit úlohu streamování. **Sp_create_streaming_job** uložená procedura používá dva parametry.

- job_name – název úlohy streamování. Názvy úloh streamování jsou v rámci instance jedinečné.
- statement-Stream Analytics příkazy dotazu streamování založené na [jazyce dotazu](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?) .

Následující příklad vytvoří jednoduchou úlohu streamování s jedním dotazem pro streamování. Tento dotaz přečte vstupy z hraničního centra a zapíše je do *dbo. TemperatureMeasurements* v databázi.

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob1',
@statement= N'Select * INTO TemperatureMeasurements from MyEdgeHubInput'
```

Následující příklad vytvoří složitější úlohu streamování s více různými dotazy, včetně dotazu, který používá integrovanou funkci AnomalyDetection_ChangePoint k identifikaci anomálií v datech teploty.

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob2', @statement=
N' Select * INTO TemperatureMeasurements1 from MyEdgeHubInput1

Select * Into TemperatureMeasurements2 from MyEdgeHubInput2

Select * Into TemperatureMeasurements3 from MyEdgeHubInput3

SELECT
Timestamp as [Time],
[Temperature] As [Temperature],
GetRecordPropertyValue(AnomalyDetection_ChangePoint(Temperature, 80, 1200) OVER(LIMIT DURATION(minute, 20)), ''Score'') as ChangePointScore,
GetRecordPropertyValue(AnomalyDetection_ChangePoint(Temperature, 80, 1200) OVER(LIMIT DURATION(minute, 20)), ''IsAnomaly'') as IsChangePointAnomaly,
INTO TemperatureAnomalies FROM MyEdgeHubInput2;
'
go
```

## <a name="start-stop-drop-and-monitor-streaming-jobs"></a>Spuštění, zastavení, odpojení a monitorování úloh streamování

Pokud chcete spustit úlohu streamování v SQL Edge, spusťte uloženou proceduru **Sys. sp_start_streaming_job** . Uložená procedura vyžaduje, aby se jako vstup spouštěla stejná úloha streamování.

```sql
exec sys.sp_start_streaming_job @name=N'StreamingJob1'
go
```

Pokud chcete zastavit úlohu streamování v SQL Edge, spusťte uloženou proceduru **Sys. sp_stop_streaming_job** . Uložená procedura vyžaduje, aby se jako vstup zastavila stejná úloha streamování.

```sql
exec sys.sp_stop_streaming_job @name=N'StreamingJob1'
go
```

Pokud chcete vyřadit (nebo odstranit) úlohu streamování v SQL Edge, spusťte uloženou proceduru **Sys. sp_drop_streaming_job** . Uložená procedura vyžaduje jako vstup stejnou úlohu streamování, která se má vyřadit.

```sql
exec sys.sp_drop_streaming_job @name=N'StreamingJob1'
go
```

Chcete-li získat aktuální stav úlohy streamování v SQL Edge, spusťte uloženou proceduru **Sys. sp_get_streaming_job** . Úložná procedura vyžaduje, aby se stejná úloha streamování vynechal jako vstup a výstupem je název a aktuální stav úlohy streamování.

```sql
exec sys.sp_get_streaming_job @name=N'StreamingJob1'
        WITH RESULT SETS
(
       (
       name nvarchar(256),
       status nvarchar(256)
       )
)
```

Úloha streamování může být v některém z následujících stavů:

| Status | Popis |
|--------| ------------|
| Vytvořeno | Úloha streamování se vytvořila, ale ještě se nespustila. |
| Spouštění | Úloha streamování je ve fázi zahájení. |
| Období | Úloha streamování je spuštěná, ale neexistuje žádný vstup ke zpracování. |
| Zpracování | Úloha streamování je spuštěná a zpracovává vstupy. Tento stav indikuje stav v pořádku pro úlohu streamování. |
| Snížený výkon | Úloha streamování je spuštěná, ale během zpracování vstupu došlo k některému nezávažnému vstupu/výstupu nebo chybě serializace/zrušení serializace. Vstupní úloha bude pokračovat v běhu, ale odstraní vstupy, u kterých dojde k chybám. |
| Zastaveno | Úloha streamování se zastavila. |
| Failed | Úloha streamování se nezdařila. Většinou je známa závažná chyba během zpracování. |

## <a name="next-steps"></a>Další kroky

- [Zobrazení metadat přidružených k úlohám streamování ve službě Azure SQL Edge (Preview)](streaming-catalog-views.md) 
- [Vytvoření externího datového proudu](create-external-stream-transact-sql.md)
