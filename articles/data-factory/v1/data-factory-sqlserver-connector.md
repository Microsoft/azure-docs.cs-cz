---
title: Přesun dat do a z SQL serveru | Dokumentace Microsoftu
description: Další informace o tom, jak přesunout data z databáze serveru SQL Server, který je místně nebo ve Virtuálním počítači Azure pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 864ece28-93b5-4309-9873-b095bbe6fedd
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: a8283ebe135c5204dd64d8955295fdece38e0ebe
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54023494"
---
# <a name="move-data-to-and-from-sql-server-on-premises-or-on-iaas-azure-vm-using-azure-data-factory"></a>Přesun dat do a z místního SQL serveru nebo na IaaS (virtuální počítač Azure) pomocí služby Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](data-factory-sqlserver-connector.md)
> * [Verze 2 (aktuální verze)](../connector-sql-server.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [konektor SQL Server ve verzi V2](../connector-sql-server.md).

Tento článek vysvětluje, jak použít aktivitu kopírování v Azure Data Factory k přesunu dat do a z k místní databázi SQL serveru. Je nástavbou [aktivity přesunu dat](data-factory-data-movement-activities.md) článek, který nabízí obecný přehled o přesun dat pomocí aktivity kopírování. 

## <a name="supported-scenarios"></a>Podporované scénáře
Data můžete kopírovat **z databáze serveru SQL Server** ukládá následující data:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Může kopírovat data z následujících datových skladů **k databázi systému SQL Server**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-sql-server-versions"></a>Podporované verze systému SQL Server
Tato podpora konektoru systému SQL Server kopírování dat z/do následující verze instanci hostované v místním nebo v Azure IaaS pomocí ověřování SQL a ověřování Windows: SQL Server 2016, SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008, SQL Server 2005

## <a name="enabling-connectivity"></a>Povolení připojení
Koncepty a kroky potřebné pro připojení SQL Server je hostovaný na místním nebo ve virtuálních počítačích Azure IaaS (Infrastructure-as--Service) jsou stejné. V obou případech budete muset použít bránu správy dat pro připojení.

Zobrazit [přesun dat mezi místními umístěními a cloudem](data-factory-move-data-between-onprem-and-cloud.md) článku se dozvíte o brána správy dat a podrobné pokyny o nastavení brány. Nastavení instance brány je nezbytný předpoklad pro připojení k serveru SQL Server.

Když bránu můžete nainstalovat na stejném v místním počítači nebo instanci virtuálního počítače v cloudu jako SQL Server pro zajištění lepšího výkonu, doporučujeme, že je nainstalujete na samostatných počítačích. Máte brány a systému SQL Server na samostatných počítačích snižuje kolize prostředků.

## <a name="getting-started"></a>Začínáme
Vytvoření kanálu s aktivitou kopírování, který přesouvá data z databáze systému SQL Server v místním pomocí různých nástrojů a rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Zobrazit [kurzu: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním data.

Tyto nástroje můžete také použít k vytvoření kanálu: **Azure portal**, **sady Visual Studio**, **prostředí Azure PowerShell**, **šablony Azure Resource Manageru**, **rozhraní .NET API**a  **Rozhraní REST API**. Zobrazit [kurz aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) podrobné pokyny k vytvoření kanálu s aktivitou kopírování. 

Ať už používáte, nástrojů nebo rozhraní API, proveďte následující kroky k vytvoření kanálu pro přesouvání dat ze zdrojového úložiště dat do úložiště dat jímky: 

1. Vytvoření **služby data factory**. Datová továrna může obsahovat jeden nebo víc kanálů. 
2. Vytvoření **propojené služby** propojení vstupní a výstupní data ukládá do služby data factory. Pokud jsou kopírování dat z databáze SQL serveru do Azure blob storage, například vytvoříte dvě propojené služby propojení databáze systému SQL Server a účet úložiště Azure pro vytváření dat. Vlastnosti propojené služby, které jsou specifické pro databázi systému SQL Server, naleznete v tématu [vlastnostem propojených služeb](#linked-service-properties) oddílu. 
3. Vytvoření **datových sad** k představují vstupní a výstupní data pro operaci kopírování. V příkladu uvedených v posledním kroku vytvoříte datovou sadu, která zadejte tabulky SQL ve vaší databázi SQL serveru, který obsahuje vstupní data. A vytvořte jinou datovou sadu, která zadejte kontejner objektů blob a složku obsahující data zkopírovaná z databáze SQL serveru. Vlastnosti datové sady, které jsou specifické pro databázi systému SQL Server, naleznete v tématu [vlastnosti datové sady](#dataset-properties) oddílu.
4. Vytvoření **kanálu** s aktivitou kopírování, která přijímá jako vstupní datovou sadu a datovou sadu jako výstup. V příkladu již bylo zmíněno dříve pomocí SqlSource jako zdroj a BlobSink jako jímka pro aktivitu kopírování. Podobně pokud kopírujete z Azure Blob Storage do databáze SQL serveru, můžete použít BlobSource a SqlSink v aktivitě kopírování. Kopírovat vlastnosti aktivity, které jsou specifické pro databázi systému SQL Server, naleznete v tématu [vlastnosti aktivity kopírování](#copy-activity-properties) oddílu. Podrobnosti o tom, jak používat úložiště dat jako zdroj nebo jímku klikněte na odkaz v předchozí části datového úložiště. 

Při použití Průvodce definice JSON pro tyto entity služby Data Factory (propojené služby, datové sady a kanál) se automaticky vytvoří za vás. Při použití nástroje a rozhraní API (s výjimkou rozhraní .NET API), můžete definovat tyto entity služby Data Factory ve formátu JSON.  Ukázky s definicemi JSON entit služby Data Factory, které se používají ke kopírování dat do a z místní databáze systému SQL Server najdete v tématu [JSON příklady](#json-examples-for-copying-data-from-and-to-sql-server) části tohoto článku. 

Následující části obsahují podrobnosti o vlastnostech JSON, které se používají k definování entit služby Data Factory, které jsou specifické pro SQL Server: 

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Vytvoření propojené služby typu **OnPremisesSqlServer** propojení k místní databázi SQL serveru do služby data factory. Následující tabulka obsahuje popis JSON elementy, které jsou specifické pro místní propojené služby SQL serveru.

Následující tabulka obsahuje popis JSON elementy, které jsou specifické pro propojenou službu SQL serveru.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost type by měla být nastavená na: **OnPremisesSqlServer**. |Ano |
| připojovací řetězec |Zadejte připojovací řetězec informace potřebné pro připojení k místní databázi SQL serveru pomocí ověřování SQL nebo ověřování Windows. |Ano |
| Název brány |Název brány, který služba Data Factory měla použít pro připojení k místní databázi SQL serveru. |Ano |
| uživatelské jméno |Pokud používáte ověřování Windows, zadejte uživatelské jméno. Příklad: **domainname\\uživatelské jméno**. |Ne |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. |Ne |

Můžete k šifrování přihlašovacích údajů a **New-AzureRmDataFactoryEncryptValue** rutiny a jejich použití v připojovacím řetězci, jak je znázorněno v následujícím příkladu (**EncryptedCredential** vlastnost):  

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

### <a name="samples"></a>Ukázky
**JSON pro použití ověřování SQL**

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties":
    {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
**JSON pro použití ověřování Windows**

Brána správy dat se zosobnit zadaný uživatelský účet pro připojení k místní databázi systému SQL Server. 

```json
{
     "Name": " MyOnPremisesSQLDB",
     "Properties":
     {
         "type": "OnPremisesSqlServer",
         "typeProperties": {
             "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
             "username": "<domain\\username>",
             "password": "<password>",
             "gatewayName": "<gateway name>"
        }
     }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady
V ukázkách, jste použili datové sady typu **SqlServerTable** představující tabulky v databázi serveru SQL Server.  

Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [vytváření datových sad](data-factory-create-datasets.md) článku. Oddíly, jako je například struktura, dostupnost a zásad JSON datové sady jsou podobné pro všechny typy datovou sadu (SQL Server, Azure blob, tabulky Azure, atd.).

V části typeProperties se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti. **TypeProperties** části datové sady typu **SqlServerTable** má následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky nebo zobrazení v instanci databáze SQL serveru, která je propojená služba odkazuje na. |Ano |

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Pokud přesouváte data z databáze SQL serveru, nastavíte typ zdroje v aktivitě kopírování do **SqlSource**. Podobně pokud přesouváte data do databáze SQL serveru, je nastavit typ jímky v aktivitě kopírování do **SqlSink**. Tato část obsahuje seznam vlastností, které jsou podporované SqlSource a SqlSink.

Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [vytváření kanálů](data-factory-create-pipelines.md) článku. Vlastnosti, jako je název, popis, vstupní a výstupní tabulky a zásady jsou k dispozici pro všechny typy aktivit.

> [!NOTE]
> Aktivita kopírování přijímá pouze jeden vstup a vytvoří jenom jeden výstup.

Vzhledem k tomu, vlastnosti v části typeProperties aktivity se liší s jednotlivými typu aktivity. Pro aktivitu kopírování se liší v závislosti na typy zdroje a jímky.

### <a name="sqlsource"></a>SqlSource
Pokud je zdroj v aktivitě kopírování typu **SqlSource**, následující vlastnosti jsou k dispozici v **typeProperties** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| sqlReaderQuery |Použijte vlastní dotaz číst data. |Řetězec dotazu SQL. Příklad: vybrat * z MyTable. Může odkazovat více tabulek z databáze odkazuje vstupní datové sady. Pokud není zadán, příkaz SQL, která se spustí: Vyberte MyTable. |Ne |
| sqlReaderStoredProcedureName |Název uložené procedury, která čte data ze zdrojové tabulky. |Název uložené procedury. Příkaz SELECT v uložené proceduře musí být poslední příkaz jazyka SQL. |Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru. |Páry název/hodnota. Názvy a použití malých a velkých parametry musí odpovídat názvům a použití malých a velkých parametrů uložené procedury. |Ne |

Pokud **sqlReaderQuery** je určená pro SqlSource, spustí aktivita kopírování tohoto dotazu na zdroji databázi systému SQL Server se mají získat data.

Alternativně můžete zadat uložené procedury tak, že zadáte **sqlReaderStoredProcedureName** a **storedProcedureParameters** (Pokud uložená procedura parametry).

Pokud nezadáte sqlReaderQuery nebo sqlReaderStoredProcedureName, sloupce definované v části struktura slouží k sestavení dotazu select pro spuštění na databázi SQL serveru. Pokud struktura nemá žádné definice datové sady, vyberou se všechny sloupce z tabulky.

> [!NOTE]
> Při použití **sqlReaderStoredProcedureName**, je stále potřeba zadat hodnotu **tableName** vlastnost v datové sadě JSON. Neexistují žádné ověření, ale provedeny v této tabulce.

### <a name="sqlsink"></a>SqlSink
**SqlSink** podporuje následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| writeBatchTimeout |Čekací doba pro dávkové operace insert dokončit před vypršením časového limitu. |Časový interval<br/><br/> Příklad: "00: 30:00" (30 minut). |Ne |
| WriteBatchSize |Vloží data do tabulky SQL writeBatchSize dosáhne velikosti vyrovnávací paměti. |Celé číslo (počet řádků) |Ne (výchozí: 10000) |
| sqlWriterCleanupScript |Zadejte dotaz pro aktivitu kopírování ke spuštění tak, že po vyčištění dat určitý řez. Další informace najdete v tématu [opakovatelné kopírování](#repeatable-copy) oddílu. |Příkaz dotazu. |Ne |
| sliceIdentifierColumnName |Zadejte název sloupce pro aktivitu kopírování k vyplnění s identifikátorem automaticky generovány řez, který se používá k vyčištění dat určitý řez, kdy se znovu spustit. Další informace najdete v tématu [opakovatelné kopírování](#repeatable-copy) oddílu. |Název sloupce pro sloupec s datovým typem binary(32). |Ne |
| sqlWriterStoredProcedureName |Název uložené procedury, která definuje, jak použít zdroj dat do cílové tabulky, například na upsertuje proveďte nebo transformace pomocí vlastní obchodní logikou. <br/><br/>Mějte na paměti, bude tuto uloženou proceduru **za batch**. Pokud budete chtít provádět operace, která pouze spustí jednou a nemá nic dělat se zdrojovými daty, třeba delete nebo truncate, použijte `sqlWriterCleanupScript` vlastnost. |Název uložené procedury. |Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru. |Páry název/hodnota. Názvy a použití malých a velkých parametry musí odpovídat názvům a použití malých a velkých parametrů uložené procedury. |Ne |
| sqlWriterTableType |Zadejte název tabulky typu použitého v uložené proceduře. Aktivitu kopírování, která zpřístupňuje data přesouvá do dočasné tabulky s tímto typem tabulky. Uloženou proceduru kód pak sloučit data kopírovaná s existujícími daty. |Zadejte název tabulky. |Ne |


## <a name="json-examples-for-copying-data-from-and-to-sql-server"></a>Příklady JSON pro kopírování dat z a do systému SQL Server
Následující příklady popisují ukázkový JSON definice, které můžete použít k vytvoření kanálu pomocí [webu Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) nebo [sady Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [prostředí Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Následující ukázky ukazují, jak kopírovat data do a z SQL serveru a Azure Blob Storage. Nicméně je možné zkopírovat data **přímo** z libovolného zdroje do libovolné jímky uvedeno [tady](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivit kopírování ve službě Azure Data Factory.     

## <a name="example-copy-data-from-sql-server-to-azure-blob"></a>Příklad: Kopírování dat z SQL serveru do objektů Blob v Azure
Následující příklad ukazuje:

1. Propojené služby typu [OnPremisesSqlServer](#linked-service-properties).
2. Propojené služby typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní hodnota [datovou sadu](data-factory-create-datasets.md) typu [SqlServerTable](#dataset-properties).
4. Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [SqlSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka zkopíruje data časových řad z tabulky SQL serveru do objektu blob Azure každou hodinu. Vlastnostech JSON použitých v tyto ukázky jsou popsány v části podle ukázky.

Jako první krok instalační program brány správy dat. Pokyny jsou v [přesun dat mezi místními umístěními a cloudem](data-factory-move-data-between-onprem-and-cloud.md) článku.

**Propojené služby SQL serveru**
```json
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
    }
  }
}
```
**Objekt Blob propojená služba Azure storage**

```json
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Vstupní datové sady SQL Server**

Ukázka předpokládá vytvoříte tabulku "MyTable" v systému SQL Server a obsahuje sloupec s názvem "timestampcolumn" pro data časových řad. Můžete zadat dotaz přes více tabulek v rámci stejné databáze pomocí jedné datové sady, ale jedné tabulky musí být použito pro typeProperty tableName datové sady.

Nastavení "externí": "PRAVDA" informuje služby Data Factory, že datová sada je externí do služby data factory a není vytvořen aktivitou ve službě data factory.

```json
{
  "name": "SqlServerInput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Výstupní datová sada Azure Blob**

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce pro objekt blob se dynamicky vyhodnocuje na základě doby spuštění řez, který se právě zpracovává. Cesta ke složce používá rok, měsíc, den a části hodin čas spuštění.

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Kanál s aktivitou kopírování**

Kanálu obsahujícího aktivitu kopírování, kterou Pokud chcete použít tyto vstupní a výstupní datové sady a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **SqlSource** a **jímky** je typ nastaven na **BlobSink**. Zadaná pro dotaz SQL **SqlReaderQuery** vlastnost vybere data za poslední hodinu pro kopírování.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2016-06-01T18:00:00",
    "end":"2016-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[  
      {
        "name": "SqlServertoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": " SqlServerInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
     ]
   }
}
```
V tomto příkladu **sqlReaderQuery** je určená pro SqlSource. Tento dotaz spustí aktivita kopírování na zdroji databázi systému SQL Server se mají získat data. Alternativně můžete zadat uložené procedury tak, že zadáte **sqlReaderStoredProcedureName** a **storedProcedureParameters** (Pokud uložená procedura parametry). SqlReaderQuery může odkazovat více tabulek v databázi odkazují vstupní datové sady. Není omezena pouze tabulky jako typeProperty tableName datové sady.

Pokud nezadáte sqlReaderQuery nebo sqlReaderStoredProcedureName, sloupce definované v části struktura slouží k sestavení dotazu select pro spuštění na databázi SQL serveru. Pokud struktura nemá žádné definice datové sady, vyberou se všechny sloupce z tabulky.

Zobrazit [zdroje Sql](#sqlsource) části a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) pro seznam vlastností, které jsou podporované SqlSource a BlobSink.

## <a name="example-copy-data-from-azure-blob-to-sql-server"></a>Příklad: Kopírování dat z objektů Blob v Azure do SQL serveru
Následující příklad ukazuje:

1. Propojené služby typu [OnPremisesSqlServer](#linked-service-properties).
2. Propojené služby typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní hodnota [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Výstup [datovou sadu](data-factory-create-datasets.md) typu [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties).
5. [Kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) a [SqlSink](#sql-server-copy-activity-type-properties).

Ukázka zkopíruje časových řad dat z Azure blob do systému SQL Server tabulky každou hodinu. Vlastnostech JSON použitých v tyto ukázky jsou popsány v části podle ukázky.

**Propojené služby SQL serveru**

```json
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
    }
  }
}
```
**Objekt Blob propojená služba Azure storage**

```json
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Azure vstupní datová sada objektu Blob**

Data je převzata z nového objektu blob každou hodinu (frekvence: hodina, interval: 1). Název složky a cesta k souboru pro tento objekt blob se dynamicky vyhodnocuje podle času spuštění řezu, který se právě zpracovává. Cesta ke složce používá rok, měsíc a den čas spuštění a název souboru používá hodinová část času zahájení. "externí": "PRAVDA" nastavení informuje služby Data Factory, že datová sada je externí do služby data factory a není vytvořen aktivitou ve službě data factory.

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Výstupní datová sada SQL Server**

Ukázce kopíruje data do tabulky s názvem "MyTable" v systému SQL Server. Vytvoření tabulky v SQL serveru s stejný počet sloupců tak, jak očekáváte objektů Blob CSV soubor obsahovat. Nové řádky do tabulky přidají každou hodinu.

```json
{
  "name": "SqlServerOutput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Kanál s aktivitou kopírování**

Kanálu obsahujícího aktivitu kopírování, kterou Pokud chcete použít tyto vstupní a výstupní datové sady a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **BlobSource** a **jímky** je typ nastaven na **SqlSink**.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[  
      {
        "name": "AzureBlobtoSQL",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": " SqlServerOutput "
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource",
            "blobColumnSeparators": ","
          },
          "sink": {
            "type": "SqlSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
      ]
   }
}
```

## <a name="troubleshooting-connection-issues"></a>Řešení potíží s připojením
1. Konfigurace SQL serveru tak, aby přijímal vzdálená připojení. Spuštění **SQL Server Management Studio**, klikněte pravým tlačítkem na **server**a klikněte na tlačítko **vlastnosti**. Vyberte **připojení** ze seznamu a kontrola **povolit vzdálená připojení k serveru**.

    ![Povolit vzdálená připojení](./media/data-factory-sqlserver-connector/AllowRemoteConnections.png)

    Zobrazit [nakonfigurovat možnosti konfigurace serveru vzdáleného přístupu](https://msdn.microsoft.com/library/ms191464.aspx) podrobné pokyny.
2. Spuštění **Správce konfigurace systému SQL Server**. Rozbalte **síťová konfigurace systému SQL Server** pro instanci a vyberte **protokoly pro MSSQLSERVER**. Měli byste vidět protokoly v pravém podokně. Povolte protokol TCP/IP kliknutím pravým tlačítkem myši **TCP/IP** a kliknete na **povolit**.

    ![Povolte protokol TCP/IP](./media/data-factory-sqlserver-connector/EnableTCPProptocol.png)

    Zobrazit [povolení nebo zakázání síťového protokolu serveru](https://msdn.microsoft.com/library/ms191294.aspx) podrobnosti a alternativní způsoby povolení protokolu TCP/IP.
3. Ve stejném okně, dvakrát klikněte na panel **TCP/IP** spustit **vlastností protokolu TCP/IP** okna.
4. Přepněte **IP adresy** kartu. Posuňte se dolů viz **IPAll** oddílu. Poznamenejte si ** TCP Port ** (výchozí hodnota je **1433**).
5. Vytvoření **pravidla pro bránu Windows Firewall** na počítači za účelem povolený příchozí provoz přes tento port.  
6. **Ověření připojení**: Pro připojení k SQL serveru pomocí plně kvalifikovaného názvu, použijte SQL Server Management Studio z různých počítačů. Například: "<machine>.<domain>.Corp.<company>.com,1433."

   > [!IMPORTANT]

   > Zobrazit [přesun dat mezi místním zdrojům a cloudem pomocí brány správy dat](data-factory-move-data-between-onprem-and-cloud.md) podrobné informace.
   >
   > Naleznete v tématu [potíží brány](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) tipy k řešení potíží s připojení/bránou související problémy.
   >
   >


## <a name="identity-columns-in-the-target-database"></a>Sloupce identity v cílové databázi
Tato část poskytuje příklad, který kopíruje data ze zdrojové tabulky s žádný sloupec identity do cílové tabulky se sloupcem identity.

**Zdrojová tabulka:**

```sql
create table dbo.SourceTbl
(
       name varchar(100),
       age int
)
```
**Cílová tabulka:**

```sql
create table dbo.TargetTbl
(
       identifier int identity(1,1),
       name varchar(100),
       age int
)
```

Všimněte si, že cílová tabulka obsahuje sloupec identity.

**Zdroj definice JSON datové sady**

```json
{
    "name": "SampleSource",
    "properties": {
        "published": false,
        "type": " SqlServerTable",
        "linkedServiceName": "TestIdentitySQL",
        "typeProperties": {
            "tableName": "SourceTbl"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```
**Určení definice JSON datové sady**

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "published": false,
        "type": "AzureSqlTable",
        "linkedServiceName": "TestIdentitySQLSource",
        "typeProperties": {
            "tableName": "TargetTbl"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": false,
        "policy": {}
    }
}
```

Všimněte si, že jako zdrojové a cílové tabulce mají jiné schéma (cílový má další sloupec s identitou). V tomto scénáři budete muset zadat **struktura** vlastnost v definici datové sady target, která neobsahuje sloupec identity.

## <a name="invoke-stored-procedure-from-sql-sink"></a>Vyvolání uložené procedury SQL jímky
Zobrazit [volání uložené procedury pro SQL jímky v aktivitě kopírování](data-factory-invoke-stored-procedure-from-copy-activity.md) článku příklad volání uložené procedury z SQL jímky v aktivitě kopírování kanálu.

## <a name="type-mapping-for-sql-server"></a>Mapování typu pro SQL server
Jak je uvedeno v [aktivity přesunu dat](data-factory-data-movement-activities.md) článku, aktivita kopírování provádí automatické typ převody z typů zdroje do jímky typy s přístupem následující krok 2:

1. Převést na typ formátu .NET typy nativních zdrojů
2. Převést z typu .NET native jímky typu

Při přesouvání dat do a z SQL serveru, se používají následující mapování z typu SQL pro typ formátu .NET a naopak.

Mapování je stejná jako SQL Server mapování datového typu pro technologii ADO.NET.

| Typ databázového stroje SQL serveru | Typ rozhraní .NET framework |
| --- | --- |
| bigint |Int64 |
| Binární |Byte] |
| Bit |Logická hodnota |
| Char |Řetězec, Char] |
| date |DateTime |
| Datum a čas |DateTime |
| datetime2 |DateTime |
| DateTimeOffset |DateTimeOffset |
| Decimal |Decimal |
| Atribut FILESTREAM (varbinary(max)) |Byte] |
| Float |Double |
| image |Byte] |
| int |Datový typ Int32 |
| peníze |Decimal |
| nchar |Řetězec, Char] |
| ntext |Řetězec, Char] |
| Číselné |Decimal |
| nvarchar |Řetězec, Char] |
| Real |Jednoduchá |
| ROWVERSION |Byte] |
| smalldatetime |DateTime |
| smallint |Int16 |
| Smallmoney |Decimal |
| SQL_VARIANT |Objekt * |
| text |Řetězec, Char] |
| time |Časový interval |
| časové razítko |Byte] |
| tinyint |Bajt |
| UniqueIdentifier |Guid |
| varbinary |Byte] |
| varchar |Řetězec, Char] |
| xml |XML |

## <a name="mapping-source-to-sink-columns"></a>Mapování zdroje do jímky sloupce
Pokud chcete namapovat sloupce ze zdrojové datové sady na sloupce z datové sady jímky, najdete v článku [mapování sloupců v datové sadě ve službě Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-copy"></a>Opakovatelné kopírování
Kopírování dat do databáze SQL serveru, aktivita kopírování připojí data do tabulky jímky ve výchozím nastavení. Místo toho provést funkcí UPSERT, najdete v článku [Repeatable zapisovat do SqlSink](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink) článku. 

Při kopírování dat z relačních dat ukládá, mějte opakovatelnosti aby se zabránilo neúmyslnému výsledků. Ve službě Azure Data Factory můžete znovu spustit řezu ručně. Zásady opakování pro datovou sadu můžete také nakonfigurovat tak, aby určitý řez se znovu spustí, když dojde k chybě. V obou případech se znovu spustí určitý řez, musíte zajistit, že stejná data je pro čtení bez ohledu na to kolikrát spustit určitý řez. Zobrazit [Repeatable z relačních zdrojů](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Výkon a optimalizace
Zobrazit [výkonem aktivity kopírování & Průvodci optimalizací](data-factory-copy-activity-performance.md) Další informace o klíčových faktorů této ovlivnit výkon přesouvání dat (aktivita kopírování) ve službě Azure Data Factory a různé způsoby, jak optimalizovat.
