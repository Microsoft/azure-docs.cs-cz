---
title: Nastavení modulů IoT Edge v Azure SQL Edge
description: V třetí části tohoto kurzu Azure SQL Edge pro předpověď nečistot železa nastavíte IoT Edge moduly a připojení.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sourabha, sstein
ms.date: 09/22/2020
ms.openlocfilehash: 7b2432fda70e8f9a5fa8bc64ede846d977672e9e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90886476"
---
# <a name="set-up-iot-edge-modules-and-connections"></a>Nastavení IoT Edgech modulů a připojení

V druhé části tohoto kurzu pro předpověď nečistot železa ve službě Azure SQL Edge nastavíte následující IoT Edge moduly:

- Azure SQL Edge
- Modul IoT Edge generátoru dat

## <a name="specify-container-registry-credentials"></a>Zadat přihlašovací údaje registru kontejneru

Je potřeba zadat přihlašovací údaje k imagím modulu container Registry Host. Ty najdete v registru kontejnerů, které se vytvořily ve vaší skupině prostředků. Přejděte do části **přístupové klíče** . Poznamenejte si následující pole:

- Název registru
- Přihlašovací server
- Uživatelské jméno
- Heslo

Nyní zadejte pověření kontejneru v modulu IoT Edge.

1. Přejděte do služby IoT Hub, která byla vytvořena ve vaší skupině prostředků.

2. V části **IoT Edge** v části **Automatická správa zařízení**klikněte na **ID zařízení**. V tomto kurzu je ID `IronOrePredictionDevice` .

3. Vyberte část **set modules** .

4. V části **Container Registry přihlašovací údaje**zadejte následující hodnoty:

   _Pole_|_Hodnota_
   -------|-------
   Název|Název registru
   Adresa|Přihlašovací server
   Uživatelské jméno|Uživatelské jméno
   Heslo|Heslo
  
## <a name="deploy-the-data-generator-module"></a>Nasazení modulu generátoru dat

1. V části **IoT Edge** v části **Automatická správa zařízení**klikněte na **ID zařízení**. Pro tento kurz je ID `IronOrePredictionDevice` a pak klikněte na **nastavit moduly**.

2.  V části **IoT Edge moduly** na stránce **nastavit moduly na zařízení:** klikněte na **+ Přidat** a vyberte **IoT Edge modul**.

3. Zadejte platný název a identifikátor URI image pro modul IoT Edge.
   Identifikátor URI image najdete v registru kontejnerů ve skupině prostředků vytvořené v první části tohoto kurzu. V části **služby**vyberte část **úložiště** . Pro tento kurz vyberte úložiště s názvem `silicaprediction` . Vyberte příslušnou značku. Identifikátor URI image bude ve formátu:

   *přihlašovací server containerregistry* / *název úložiště*:*název značky*

   Například:

   ```
   ASEdemocontregistry.azurecr.io/silicaprediction:amd64
   ```

4. Ponechejte *zásady restartování* a *požadovaná pole stavu* tak, jak jsou.

5. Klikněte na **Přidat**.


## <a name="deploy-the-azure-sql-edge-module"></a>Nasazení modulu Edge Azure SQL

1. Nasaďte modul Azure SQL Edge kliknutím na **+ Přidat** a pak na **modul Marketplace**. 

2. V okně **IoT Edge modulu Marketplace** vyhledejte *Azure SQL Edge* a vyberte *Azure SQL Edge Developer*. 

3. Kliknutím na nově přidaný modul *Azure SQL Edge* v části **IoT Edge moduly** nakonfigurujte modul Azure SQL Edge. Další informace o možnostech konfigurace najdete v tématu [nasazení Azure SQL Edge](https://docs.microsoft.com/azure/azure-sql-edge/deploy-portal).

4. Přidejte `MSSQL_PACKAGE` proměnnou prostředí do nasazení modulu *Azure SQL Edge* a zadejte adresu URL souboru DACPAC databáze vytvořeného v kroku 8 první [části](tutorial-deploy-azure-resources.md) tohoto kurzu.

5. Kliknout na **aktualizovat**

6. Na stránce **nastavit moduly na zařízení** klikněte na **další: trasy >**.

7. V podokně trasy na stránce **nastavit moduly na zařízení** zadejte trasy pro modul pro IoT Edge komunikaci centra, jak je popsáno níže. Nezapomeňte aktualizovat názvy modulů v níže uvedených definicích tras.

   ```
   FROM /messages/modules/<your_data_generator_module>/outputs/IronOreMeasures INTO
   BrokeredEndpoint("/modules/<your_azure_sql_edge_module>/inputs/IronOreMeasures")
   ```

   Například:

   ```
   FROM /messages/modules/ASEDataGenerator/outputs/IronOreMeasures INTO BrokeredEndpoint("/modules/AzureSQLEdge/inputs/IronOreMeasures")
   ```


7. Na stránce **nastavit moduly na zařízení** klikněte na **Další: zkontrolovat + vytvořit >**

8. Na stránce **nastavit moduly na zařízení** klikněte na **vytvořit** .

## <a name="create-and-start-the-t-sql-streaming-job-in-azure-sql-edge"></a>Vytvořte a spusťte úlohu streamování T-SQL ve službě Azure SQL Edge.

1. Otevřete Azure Data Studio.

2. Na kartě **Vítejte** spusťte nové připojení s následujícími podrobnostmi:

   |_Pole_|_Hodnota_|
   |-------|-------|
   |Typ připojení| Microsoft SQL Server|
   |Server|Veřejná IP adresa zmíněná na virtuálním počítači, který se vytvořil pro tuto ukázku|
   |Uživatelské jméno|sa|
   |Heslo|Silné heslo, které se použilo při vytváření instance Azure SQL Edge|
   |databáze|Výchozí|
   |Skupina serverů|Výchozí|
   |Název (nepovinný)|Zadání volitelného názvu|

3. Klikněte na **připojit** .

4. Na kartě Nabídka **soubor** otevřete nový Poznámkový blok nebo použijte klávesovou zkratku CTRL + N.

5. V okně Nový dotaz spusťte skript níže a vytvořte úlohu streamování T-SQL. Před spuštěním skriptu se ujistěte, že jste změnili následující proměnné. 
   - *SQL_SA_Password:* Hodnota MSSQL_SA_PASSWORD zadaná při nasazení modulu Azure SQL Edge. 
   
   ```sql
   Use IronOreSilicaPrediction
   Go

   Declare @SQL_SA_Password varchar(200) = '<SQL_SA_Password>'
   declare @query varchar(max) 

   /*
   Create Objects Required for Streaming
   */

   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'MyStr0ng3stP@ssw0rd';

   If NOT Exists (select name from sys.external_file_formats where name = 'JSONFormat')
   Begin
      CREATE EXTERNAL FILE FORMAT [JSONFormat]  
      WITH ( FORMAT_TYPE = JSON)
   End 


   If NOT Exists (select name from sys.external_data_sources where name = 'EdgeHub')
   Begin
      Create EXTERNAL DATA SOURCE [EdgeHub] 
      With(
         LOCATION = N'edgehub://'
      )
   End 

   If NOT Exists (select name from sys.external_streams where name = 'IronOreInput')
   Begin
      CREATE EXTERNAL STREAM IronOreInput WITH 
      (
         DATA_SOURCE = EdgeHub,
         FILE_FORMAT = JSONFormat,
         LOCATION = N'IronOreMeasures'
       )
   End


   If NOT Exists (select name from sys.database_scoped_credentials where name = 'SQLCredential')
   Begin
       set @query = 'CREATE DATABASE SCOPED CREDENTIAL SQLCredential
                 WITH IDENTITY = ''sa'', SECRET = ''' + @SQL_SA_Password + ''''
       Execute(@query)
   End 

   If NOT Exists (select name from sys.external_data_sources where name = 'LocalSQLOutput')
   Begin
      CREATE EXTERNAL DATA SOURCE LocalSQLOutput WITH (
      LOCATION = 'sqlserver://tcp:.,1433',CREDENTIAL = SQLCredential)
   End

   If NOT Exists (select name from sys.external_streams where name = 'IronOreOutput')
   Begin
      CREATE EXTERNAL STREAM IronOreOutput WITH 
      (
         DATA_SOURCE = LocalSQLOutput,
         LOCATION = N'IronOreSilicaPrediction.dbo.IronOreMeasurements'
      )
   End

   EXEC sys.sp_create_streaming_job @name=N'IronOreData',
   @statement= N'Select * INTO IronOreOutput from IronOreInput'

   exec sys.sp_start_streaming_job @name=N'IronOreData'
   ```

6. Pomocí následujícího dotazu ověřte, zda jsou data z modulu generování dat vysílána do databáze. 

   ```sql
   Select Top 10 * from dbo.IronOreMeasurements
   order by timestamp desc
   ```


V tomto kurzu jsme nasadili modul generátoru dat a modul SQL Edge. Pak jsme vytvořili úlohu streamování pro streamování dat generovaných modulem generování dat do SQL. 

## <a name="next-steps"></a>Další kroky

- [Nasazení modelu ML na Edge Azure SQL pomocí ONNX](tutorial-run-ml-model-on-sql-edge.md)
