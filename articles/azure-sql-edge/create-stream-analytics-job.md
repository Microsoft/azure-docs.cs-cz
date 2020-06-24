---
title: Vytvoření úlohy streamování T-SQL ve službě Azure SQL Edge (Preview)
description: Přečtěte si informace o vytváření úloh Stream Analytics ve službě Azure SQL Edge (Preview).
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: fc6ab2c9c844350e83674ed96a0e79289c7f5b43
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2020
ms.locfileid: "85255411"
---
# <a name="create-an-azure-stream-analytics-job-in-azure-sql-edge-preview"></a>Vytvoření úlohy Azure Stream Analytics v Azure SQL Edge (Preview) 

Tento článek vysvětluje, jak vytvořit úlohu streamování T-SQL ve službě Azure SQL Edge (Preview). Vytvoříte vstupní a výstupní objekty externího datového proudu a potom v rámci vytváření úlohy streamování definujete dotaz na úlohu streamování.

> [!NOTE]
> Pokud chcete povolit funkci streamování T-SQL ve službě Azure SQL Edge, povolte jako možnost spuštění TF 11515 nebo použijte příkaz [DBCC TRACEON]( https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) . Další informace o tom, jak povolit příznaky trasování pomocí souboru MSSQL. conf, najdete v tématu [Konfigurace použití souboru MSSQL. conf](configure.md#configure-by-using-an-mssqlconf-file).

## <a name="configure-the-external-stream-input-and-output-objects"></a>Konfigurace vstupních a výstupních objektů externího datového proudu

Streamování T-SQL využívá funkci externích zdrojů dat SQL Server k definování zdrojů dat přidružených k vstupům externích datových proudů a výstupům úlohy streamování. Pomocí následujících příkazů T-SQL vytvořte vstupní nebo výstupní objekt externího datového proudu:

- [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql)

- [CREATE EXTERNAL DATA SOURCE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql)

- [VYTVOŘIT externí datový proud (Transact-SQL)](#example-create-an-external-stream-object-to-azure-sql-database)

Pokud se navíc jako výstupní datový proud používá Azure SQL Edge, SQL Server nebo Azure SQL Database, budete potřebovat [vytvořit databázi s rozsahem pověření (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql). Tento příkaz T-SQL definuje přihlašovací údaje pro přístup k databázi.

### <a name="supported-input-and-output-stream-data-sources"></a>Podporované vstupní a výstupní datové proudy zdrojů dat

Azure SQL Edge aktuálně podporuje jenom následující zdroje dat jako vstupy a výstupy streamu.

| Typ zdroje dat | Vstup | Výstup | Description |
|------------------|-------|--------|------------------|
| Centrum Azure IoT Edge | Ano | Ano | Zdroj dat pro čtení a zápis streamovaná data do centra Azure IoT Edge. Další informace najdete v tématu [IoT Edge hub](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub).|
| SQL Database | N | Ano | Připojení zdroje dat, které zapisuje streamovaná data do SQL Database. Databáze může být místní databáze ve službě Azure SQL Edge nebo Vzdálená databáze ve SQL Server nebo Azure SQL Database.|
| Azure Blob Storage | N | Ano | Zdroj dat pro zápis dat do objektu BLOB v účtu úložiště Azure. |
| Kafka | Ano | N | Zdroj dat pro čtení dat streamování z tématu Kafka. Tento adaptér je v tuto chvíli dostupný jenom pro verze Intel nebo AMD serveru Azure SQL Edge. Není k dispozici pro verzi ARM64 serveru Azure SQL Edge.|

### <a name="example-create-an-external-stream-inputoutput-object-for-azure-iot-edge-hub"></a>Příklad: vytvoření objektu vstupu/výstupu externího datového proudu pro Azure IoT Edge hub

Následující příklad vytvoří objekt externího datového proudu pro Azure IoT Edge hub. Pokud chcete vytvořit zdroj dat vstupu a výstupu externího datového proudu pro Azure IoT Edge centrum, musíte nejprve vytvořit externí formát souboru pro rozložení dat, která se čtou nebo zapisují i vy.

1. Vytvořte formát souboru JSON typu external.

    ```sql
    Create External file format InputFileFormat
    WITH (  
       format_type = JSON,
    )
    go
    ```

2. Vytvořte externí zdroj dat pro centrum Azure IoT Edge. Následující skript T-SQL vytvoří připojení ke zdroji dat k rozbočovači IoT Edge, který běží na stejném hostiteli Docker jako Azure SQL Edge.

    ```sql
    CREATE EXTERNAL DATA SOURCE EdgeHubInput WITH (
    LOCATION = 'edgehub://'
    )
    go
    ```

3. Vytvořte objekt externího datového proudu pro Azure IoT Edge hub. Následující skript T-SQL vytvoří pro Centrum IoT Edge objekt datového proudu. V případě objektu datového proudu centra IoT Edge je parametr LOCATION názvem tématu IoT Edge centra nebo kanálu, do kterého se čte nebo zapisuje.

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

### <a name="example-create-an-external-stream-object-to-azure-sql-database"></a>Příklad: vytvoření objektu externího datového proudu pro Azure SQL Database

Následující příklad vytvoří objekt externího datového proudu pro místní databázi v Azure SQL Edge. 

1. Vytvořte v databázi hlavní klíč. Tento klíč je nutný k šifrování tajného kódu přihlašovacích údajů.

    ```sql
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<<Strong_Password_For_Master_Key_Encryption>>';
    ```

2. Vytvořte pověření v oboru databáze pro přístup ke zdroji SQL Server. Následující příklad vytvoří přihlašovací údaje k externímu zdroji dat s identitou = username a tajné = Password.

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL SQLCredential
    WITH IDENTITY = '<SQL_Login>', SECRET = '<SQL_Login_PASSWORD>'
    go
    ```

3. Vytvořte externí zdroj dat pomocí vytvoření externího zdroje dat. Následující příklad:

    * Vytvoří externí zdroj dat s názvem *LocalSQLOutput*.
    * Určuje externí zdroj dat (umístění = ' <vendor> :// <server> [: <port> ] '). V tomto příkladu odkazuje na místní instanci Azure SQL Edge.
    * Používá dříve vytvořené přihlašovací údaje.

    ```sql
    CREATE EXTERNAL DATA SOURCE LocalSQLOutput WITH (
    LOCATION = 'sqlserver://tcp:.,1433'
    ,CREDENTIAL = SQLCredential
    )
    go
    ```

4. Vytvořte objekt externího streamu. Následující příklad vytvoří objekt externího datového proudu odkazující na tabulku *dbo. TemperatureMeasurements*v *MySQLDatabase*databáze.

    ```sql
    CREATE EXTERNAL STREAM TemperatureMeasurements WITH (
    DATA_SOURCE = LocalSQLOutput,
    LOCATION = N'MySQLDatabase.dbo.TemperatureMeasurements',
    INPUT_OPTIONS = N'',
    OUTPUT_OPTIONS = N''
    )
    ```

## <a name="create-the-streaming-job-and-the-streaming-queries"></a>Vytvořit úlohu streamování a dotazy streamování

Pomocí `sys.sp_create_streaming_job` systémové uložené procedury definujte dotazy streamování a vytvořte úlohu streamování. `sp_create_streaming_job`Uložená procedura používá následující parametry:

- `job_name`: Název úlohy streamování. Názvy úloh streamování jsou v rámci instance jedinečné.
- `statement`: [Stream Analytics dotazování](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?)příkazů dotazu streamování založeného na jazyce.

Následující příklad vytvoří jednoduchou úlohu streamování s jedním dotazem pro streamování. Tento dotaz přečte vstupy z centra IoT Edge a zapisuje do `dbo.TemperatureMeasurements` databáze.

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob1',
@statement= N'Select * INTO TemperatureMeasurements from MyEdgeHubInput'
```

Následující příklad vytvoří složitější úlohu streamování s více různými dotazy. Tyto dotazy obsahují jednu z vestavěných `AnomalyDetection_ChangePoint` funkcí k identifikaci anomálií v datech o teplotě.

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

## <a name="start-stop-drop-and-monitor-streaming-jobs"></a>Spuštění, zastavení, zrušení a monitorování úloh streamování

Pokud chcete spustit úlohu streamování ve službě Azure SQL Edge, spusťte `sys.sp_start_streaming_job` uloženou proceduru. Uložená procedura vyžaduje jako vstup název úlohy streamování, která se má spustit.

```sql
exec sys.sp_start_streaming_job @name=N'StreamingJob1'
go
```

Pokud chcete zastavit úlohu streamování, spusťte `sys.sp_stop_streaming_job` uloženou proceduru. Uložená procedura vyžaduje, aby se jako vstup zastavil název úlohy streamování.

```sql
exec sys.sp_stop_streaming_job @name=N'StreamingJob1'
go
```

Pokud chcete odpojit úlohu streamování (nebo ji odstranit), spusťte `sys.sp_drop_streaming_job` uloženou proceduru. Uložená procedura vyžaduje jako vstup název úlohy streamování, která se má vyřadit.

```sql
exec sys.sp_drop_streaming_job @name=N'StreamingJob1'
go
```

Chcete-li získat aktuální stav úlohy streamování, spusťte `sys.sp_get_streaming_job` uloženou proceduru. Uložená procedura vyžaduje jako vstup název úlohy streamování, která se má vyřadit. Vypíše název a aktuální stav úlohy streamování.

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

Úloha streamování může mít některý z následujících stavů:

| Status | Popis |
|--------| ------------|
| Vytvořeno | Úloha streamování se vytvořila, ale ještě se nespustila. |
| Spouštění | Úloha streamování je ve fázi zahájení. |
| Období | Úloha streamování je spuštěná, ale není k dispozici žádný vstup ke zpracování. |
| Zpracování | Úloha streamování je spuštěná a zpracovává vstupy. Tento stav označuje stav v pořádku pro úlohu streamování. |
| Snížený výkon | Úloha streamování je spuštěná, ale během zpracování vstupu došlo k nějakým chybám, které nejsou závažné. Vstupní úloha bude pokračovat v běhu, ale zahodí vstupy, které nastanou chyby. |
| Zastaveno | Úloha streamování se zastavila. |
| Failed | Úloha streamování se nezdařila. To je obvykle indikace závažné chyby během zpracování. |

## <a name="next-steps"></a>Další kroky

- [Zobrazení metadat přidružených k úlohám streamování ve službě Azure SQL Edge (Preview)](streaming-catalog-views.md) 
- [Vytvoření externího datového proudu](create-external-stream-transact-sql.md)
