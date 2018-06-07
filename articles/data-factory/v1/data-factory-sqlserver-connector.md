---
title: Přesun dat do a z SQL serveru | Microsoft Docs
description: Další informace o tom, jak přesunout data z databáze serveru SQL Server, který je místně nebo v virtuálního počítače Azure pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 864ece28-93b5-4309-9873-b095bbe6fedd
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 67a91f8b61c1a0783d2075a13cc8cf2701496c88
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34622376"
---
# <a name="move-data-to-and-from-sql-server-on-premises-or-on-iaas-azure-vm-using-azure-data-factory"></a>Přesun dat do a z místní SQL Server nebo na IaaS (virtuální počítač Azure) pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](data-factory-sqlserver-connector.md)
> * [Verze 2 – Preview](../connector-sql-server.md)

> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory, která je obecně dostupná (GA). Pokud používáte verze 2 služby Data Factory, který je ve verzi preview, najdete v části [konektor serveru SQL Server v V2](../connector-sql-server.md).

Tento článek vysvětluje, jak pomocí aktivity kopírování v Azure Data Factory pro přesun dat z databáze SQL Server na místě. Vychází [aktivity přesunu dat](data-factory-data-movement-activities.md) článek, který představuje obecný přehled přesun dat s aktivitou kopírování. 

## <a name="supported-scenarios"></a>Podporované scénáře
Může kopírovat data **z databáze systému SQL Server** ukládá do následující data:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Může kopírovat data z následujících datových úložišť **k databázi systému SQL Server**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-sql-server-versions"></a>Podporované verze systému SQL Server
Tato podpora konektoru systému SQL Server kopírování dat z/do následující verze instance hostovaná místně nebo v Azure IaaS pomocí ověřování SQL a ověřování systému Windows: SQL Server 2016, SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008, SQL Server 2005

## <a name="enabling-connectivity"></a>Povolení připojení
Koncepty a kroky potřebné pro připojení s SQL serveru hostované místně nebo ve virtuálních počítačích Azure IaaS (infrastruktura jako služba) jsou stejné. V obou případech budete muset použít Brána pro správu dat pro připojení k síti.

V tématu [přesouvání dat mezi místní umístění a cloudem](data-factory-move-data-between-onprem-and-cloud.md) článku se dozvíte o Brána pro správu dat a podrobné pokyny o nastavení brány. Nastavení instance brány je nezbytný předpoklad pro připojení k systému SQL Server.

Při bránu nainstalovat na stejný na místním počítači nebo instanci cloudu virtuálního počítače jako Server SQL pro lepší výkon, doporučujeme vám nainstalovat na samostatné počítače. S brány a SQL Server na samostatné počítače snižuje kolize prostředků.

## <a name="getting-started"></a>Začínáme
Vytvoření kanálu s aktivitou kopírování, který přesouvá data z databáze SQL serveru místní pomocí různých nástrojů nebo rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál je použití **Průvodce kopírováním**. V tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) podrobný rychlé vytvoření kanálu pomocí Průvodce kopírováním data.

Tyto nástroje můžete také použít k vytvoření kanálu: **portál Azure**, **Visual Studio**, **prostředí Azure PowerShell**, **šablony Azure Resource Manageru**, **.NET API**, a **REST API**. V tématu [kurzu aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) podrobné pokyny k vytvoření kanálu s aktivitou kopírování. 

Jestli používáte nástroje nebo rozhraní API, je třeba provést následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat podřízený: 

1. Vytvoření **objekt pro vytváření dat**. Objekt pro vytváření dat může obsahovat jeden nebo víc kanálů. 
2. Vytvoření **propojené služby** propojení vstupní a výstupní data ukládá do data factory. Pokud jsou kopírování dat z databáze SQL serveru do Azure blob storage, například vytvoříte dvě propojené služby propojení databáze systému SQL Server a účet úložiště Azure pro vytváření dat. Vlastnosti propojené služby, které jsou specifické pro databázi systému SQL Server, najdete v části [propojené vlastnosti služby](#linked-service-properties) části. 
3. Vytvoření **datové sady** představují vstupní a výstupní data pro kopírování. V příkladu uvedených v posledním kroku vytvoříte datové sady určete tabulku SQL v databázi systému SQL Server, který obsahuje vstupní data. A vytvořte jinou datovou sadu, která zadejte kontejner objektů blob a složky, která obsahuje data zkopírovat z databáze serveru SQL. Vlastnosti datové sady, které jsou specifické pro databázi systému SQL Server, najdete v části [vlastnosti datové sady](#dataset-properties) části.
4. Vytvoření **kanálu** s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup. V příkladu již bylo zmíněno dříve použijete SqlSource jako zdroj a BlobSink jako jímku pro aktivitu kopírování. Podobně pokud kopírujete z Azure Blob Storage do databáze serveru SQL, použijte BlobSource a SqlSink v aktivitě kopírování. Kopírovat vlastnosti aktivity, které jsou specifické pro databáze serveru SQL, najdete v části [zkopírovat vlastnosti aktivity](#copy-activity-properties) části. Podrobnosti o tom, jak používat úložiště dat jako zdroj nebo jímka klikněte na odkaz v předchozí části pro data store. 

Když použijete průvodce, jsou automaticky vytvoří definice JSON pro tyto entity služby Data Factory (propojené služby, datové sady a kanál). Při použití nástroje nebo rozhraní API (s výjimkou .NET API), definujete tyto entity služby Data Factory pomocí formátu JSON.  Ukázky s definicemi JSON entit služby Data Factory, které se používají ke zkopírování dat do nebo z místní databáze systému SQL Server naleznete v části [JSON příklady](#json-examples-for-copying-data-from-and-to-sql-server) tohoto článku. 

Následující části obsahují podrobnosti o vlastnostech formátu JSON, které slouží k určení konkrétní entity služby Data Factory k systému SQL Server: 

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Vytvoření propojené služby typu **onpremisessqlserver** propojit místní databázi systému SQL Server do služby data factory. Následující tabulka obsahuje popis specifické pro službu SQL serveru propojená místní elementy JSON.

Následující tabulka obsahuje popis JSON elementy, které jsou specifické pro SQL Server propojené služby.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost typu musí být nastavená na: **onpremisessqlserver**. |Ano |
| připojovací řetězec |Zadejte připojovací řetězec informace potřebné pro připojení k místní databázi systému SQL Server pomocí ověřování SQL nebo ověřování systému Windows. |Ano |
| gatewayName |Název brány, kterou služba Data Factory měla použít pro připojení k místní databázi systému SQL Server. |Ano |
| uživatelské jméno |Zadejte uživatelské jméno, pokud používáte ověřování systému Windows. Příklad: **domainname\\uživatelské jméno**. |Ne |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. |Ne |

Můžete šifrovat přihlašovací údaje pomocí **New-AzureRmDataFactoryEncryptValue** rutiny a jak je znázorněno v následujícím příkladu je využít v připojovacím řetězci (**EncryptedCredential** vlastnost):  

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

### <a name="samples"></a>Ukázky
**JSON pro pomocí ověřování SQL.**

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
**JSON pro použití ověřování systému Windows**

Brána pro správu dat se zosobnit zadaný uživatelský účet pro připojení k místní databázi systému SQL Server. 

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
V ukázky, můžete použít datovou sadu typu **SqlServerTable** představují tabulky v databázi systému SQL Server.  

Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [vytváření datových sad](data-factory-create-datasets.md) článku. Oddíly, jako je například struktura, dostupnost a zásad JSON datové sady jsou podobné pro všechny typy datovou sadu (SQL Server, objektů blob v Azure, Azure table atd.).

V rámci typeProperties části se liší pro jednotlivé typy datovou sadu a poskytuje informace o umístění dat v úložišti. **Rámci typeProperties** části datové sady typu **SqlServerTable** má následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky nebo zobrazení instance databáze serveru SQL, kterou propojená služba odkazuje. |Ano |

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Pokud přesouváte data z databáze systému SQL Server, nastavíte typ zdroje v aktivitě kopírování do **SqlSource**. Podobně pokud přesouváte data do databáze serveru SQL, nastavíte typ jímky v aktivitě kopírování do **SqlSink**. Tato část obsahuje seznam vlastností, které jsou podporované SqlSource a SqlSink.

Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [vytváření kanálů](data-factory-create-pipelines.md) článku. Vlastnosti, například název, popis, vstupní a výstupní tabulky a zásad jsou dostupné pro všechny typy aktivit.

> [!NOTE]
> Aktivita kopírování přijímá pouze jeden vstup a vytváří jenom jeden výstup.

Vzhledem k tomu, vlastnosti dostupné v rámci typeProperties části aktivity se liší podle každý typ aktivity. Pro aktivitu kopírování budou lišit v závislosti na typech zdrojů a jímky.

### <a name="sqlsource"></a>SqlSource
Pokud zdroj v aktivitě kopírování je typu **SqlSource**, následující vlastnosti jsou k dispozici v **rámci typeProperties** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| sqlReaderQuery |Čtení dat pomocí vlastního dotazu. |Řetězec dotazu SQL. Příklad: vybrat * z MyTable. Může odkazovat více tabulek z databáze odkazuje vstupní datové sady. Pokud není zadaný příkaz jazyka SQL, která se provedla: Vyberte možnost z MyTable. |Ne |
| sqlReaderStoredProcedureName |Název uložené procedury, který čte data ze zdrojové tabulky. |Název uložené procedury. Poslední příkaz jazyka SQL musí být příkaz SELECT v uložené proceduře. |Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru. |Páry název/hodnota. Názvy a malá a velká písmena parametry musí odpovídat názvům a malá a velká písmena parametry uložené procedury. |Ne |

Pokud **sqlReaderQuery** je zadán pro SqlSource, aktivitě kopírování spustí tento dotaz na zdroji databáze systému SQL Server získat data.

Alternativně můžete zadat uložené procedury zadáním **sqlReaderStoredProcedureName** a **storedProcedureParameters** (Pokud uložená procedura přebírá parametry).

Pokud nezadáte sqlReaderQuery nebo sqlReaderStoredProcedureName, sloupce definované v části struktura slouží k vytvoření dotazu vyberte možnost spustit v databázi SQL serveru. Pokud definice datové sady nemá strukturu, jsou vybrány všechny sloupce z tabulky.

> [!NOTE]
> Při použití **sqlReaderStoredProcedureName**, stále je třeba zadat hodnotu pro **tableName** vlastnost v datové sadě JSON. Neexistují žádné ověření, ale adresovat této tabulky.

### <a name="sqlsink"></a>SqlSink
**SqlSink** podporuje následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| writeBatchTimeout |Počkejte, než čas na dokončení předtím, než vyprší časový limit operace dávkové vložení. |Časový interval<br/><br/> Příklad: "00: 30:00" (30 minut). |Ne |
| writeBatchSize |Vloží data do tabulky SQL, když velikost vyrovnávací paměti dosáhne writeBatchSize. |Celé číslo (počet řádků) |Ne (výchozí: 10000) |
| sqlWriterCleanupScript |Zadejte dotaz aktivity kopírování provést tak, aby se vyčistit data určitý řez. Další informace najdete v tématu [opakovatelných kopie](#repeatable-copy) části. |Příkaz dotazu. |Ne |
| sliceIdentifierColumnName |Zadejte název sloupce pro aktivitu kopírování vyplníte identifikátor automaticky generovány řez, který se používá k vyčištění dat určitý řez při spusťte znovu. Další informace najdete v tématu [opakovatelných kopie](#repeatable-copy) části. |Název sloupce sloupce s datovým typem binary(32). |Ne |
| sqlWriterStoredProcedureName |Název uložené procedury, která definuje, jak použít zdroj dat do cílové tabulky, například na proveďte upserts nebo transformace pomocí vlastní obchodní logiku. <br/><br/>Tuto uloženou proceduru bude **vyvolat na jednu dávku**. Pokud budete chtít provést operaci, která pouze se spustí jednou a nijak nesouvisí se zdrojovými daty, například odstranit nebo zkrátit proveďte pomocí `sqlWriterCleanupScript` vlastnost. |Název uložené procedury. |Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru. |Páry název/hodnota. Názvy a malá a velká písmena parametry musí odpovídat názvům a malá a velká písmena parametry uložené procedury. |Ne |
| sqlWriterTableType |Zadejte název typu tabulky má být použit v uložené proceduře. Aktivita kopírování zpřístupní přesouvání dat v dočasné tabulce s tímto typem tabulky. Uložená procedura kód pak sloučit data kopírovány s existujícími daty. |Zadejte název tabulky. |Ne |


## <a name="json-examples-for-copying-data-from-and-to-sql-server"></a>Příklady JSON pro kopírování dat z a do systému SQL Server
Následující příklady poskytují ukázka JSON definice, které můžete použít k vytvoření kanálu pomocí [portál Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) nebo [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [prostředí Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Následující ukázky ukazují, jak ke zkopírování dat do a z SQL serveru a Azure Blob Storage. Nicméně je možné zkopírovat data **přímo** ze všech zdrojů do jakéhokoli z jímky uvádí [sem](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování v Azure Data Factory.     

## <a name="example-copy-data-from-sql-server-to-azure-blob"></a>Příklad: Kopírování dat z SQL serveru do objektu Blob Azure
Následující příklad ukazuje:

1. Propojené služby typu [onpremisessqlserver](#linked-service-properties).
2. Propojené služby typu [azurestorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstup [datovou sadu](data-factory-create-datasets.md) typu [SqlServerTable](#dataset-properties).
4. Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [SqlSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka zkopíruje data časové řady z tabulky serveru SQL Server do objektu blob Azure každou hodinu. Vlastnostech JSON použitých ve tyto ukázky jsou popsané v části následující ukázky.

Jako první krok nastavte Brána pro správu dat. Tyto pokyny jsou v [přesouvání dat mezi místní umístění a cloudem](data-factory-move-data-between-onprem-and-cloud.md) článku.

**Služba SQL Server propojené**
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

Příkladu se předpokládá, jste vytvořili tabulku "MyTable" v systému SQL Server a obsahuje sloupec s názvem "timestampcolumn" pro data časové řady. Můžete dotazovat přes více tabulek v rámci stejné databáze pomocí jednu datovou sadu, ale musí používat jednu tabulku pro typeProperty tableName datovou sadu.

Nastavení "externí": "PRAVDA" informuje služba Data Factory, datová sada je externí k objektu pro vytváření dat a není vyprodukované aktivitu v datové továrně.

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

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodiny, interval: 1). Cesta ke složce pro tento objekt blob je vyhodnocován dynamicky podle času zahájení řezu, které jsou zpracovávány. Cesta ke složce používá rok, měsíc, den a čas částí čas spuštění.

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

Kanál obsahuje aktivitu kopírování, která je konfigurovaná pro používání těchto vstupní a výstupní datové sady a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **SqlSource** a **podřízený** je typ nastaven na **BlobSink**. Zadané pro dotaz SQL **SqlReaderQuery** vlastnost vybere data za poslední hodinu pro kopírování.

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
V tomto příkladu **sqlReaderQuery** je zadán pro SqlSource. Aktivita kopírování spustí tento dotaz na zdroji databáze systému SQL Server získat data. Alternativně můžete zadat uložené procedury zadáním **sqlReaderStoredProcedureName** a **storedProcedureParameters** (Pokud uložená procedura přebírá parametry). SqlReaderQuery může odkazovat více tabulek v databázi odkazuje vstupní datové sady. Se neomezuje jenom do tabulky, nastavte jako typeProperty tableName datovou sadu.

Pokud nezadáte sqlReaderQuery nebo sqlReaderStoredProcedureName, sloupce definované v části struktura slouží k vytvoření dotazu vyberte možnost spustit v databázi SQL serveru. Pokud definice datové sady nemá strukturu, jsou vybrány všechny sloupce z tabulky.

Najdete v článku [zdroje Sql](#sqlsource) části a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) pro seznam vlastností, které jsou podporované SqlSource a BlobSink.

## <a name="example-copy-data-from-azure-blob-to-sql-server"></a>Příklad: Kopírování dat z objektu Blob Azure do SQL serveru
Následující příklad ukazuje:

1. Propojené služby typu [onpremisessqlserver](#linked-service-properties).
2. Propojené služby typu [azurestorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Výstup [datovou sadu](data-factory-create-datasets.md) typu [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties).
5. [Kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) a [SqlSink](#sql-server-copy-activity-type-properties).

Kopie ukázka časové řady dat z Azure blob do systému SQL Server tabulky každou hodinu. Vlastnostech JSON použitých ve tyto ukázky jsou popsané v části následující ukázky.

**Služba SQL Server propojené**

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
**Azure vstupní datovou sadu objektu Blob**

Data je převzata z nového objektu blob každou hodinu (frekvence: hodiny, interval: 1). Název složky a cesta k souboru pro tento objekt blob se vyhodnocují dynamicky podle času zahájení řezu, které jsou zpracovávány. Cesta ke složce používá rok, měsíc a den součástí čas spuštění a název souboru používá hodinu součástí čas spuštění. "externí": "PRAVDA" nastavení informuje služba Data Factory, že datová sada je externí k objektu pro vytváření dat a není vyprodukované aktivitu v datové továrně.

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
**Výstupní datové sady SQL Server**

Ukázka zkopíruje data na tabulku s názvem "MyTable" v systému SQL Server. Podle očekávání souboru CSV objektů Blob tak, aby obsahovala, vytvořte v systému SQL Server s stejný počet sloupců v tabulce. Nové záznamy se přidají do tabulky každou hodinu.

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

Kanál obsahuje aktivitu kopírování, která je konfigurovaná pro používání těchto vstupní a výstupní datové sady a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **BlobSource** a **podřízený** je typ nastaven na **SqlSink**.

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

## <a name="troubleshooting-connection-issues"></a>Odstraňování potíží s připojením
1. Konfigurace systému SQL Server tak, aby přijímal vzdálená připojení. Spusťte **SQL Server Management Studio**, klikněte pravým tlačítkem na **server**a klikněte na tlačítko **vlastnosti**. Vyberte **připojení** ze seznamu a zkontrolujte **povolit vzdálená připojení k serveru**.

    ![Povolit vzdálená připojení](./media/data-factory-sqlserver-connector/AllowRemoteConnections.png)

    V tématu [konfigurovat možnosti konfigurace serveru pro vzdálený přístup](https://msdn.microsoft.com/library/ms191464.aspx) podrobné pokyny.
2. Spusťte **Správce konfigurace systému SQL Server**. Rozbalte položku **konfigurace sítě serveru SQL Server** pro instanci a vyberte **protokoly pro MSSQLSERVER**. Měli byste vidět protokolů v pravém podokně. Povolte protokol TCP/IP kliknutím pravým tlačítkem na **TCP/IP** a kliknutím na **povolit**.

    ![Povolte protokol TCP/IP](./media/data-factory-sqlserver-connector/EnableTCPProptocol.png)

    V tématu [povolit nebo zakázat síťový protokol serveru](https://msdn.microsoft.com/library/ms191294.aspx) podrobnosti a alternativní způsoby povolení protokolu TCP/IP.
3. V rámci stejného časového období, klikněte dvakrát na **TCP/IP** spustíte **vlastností protokolu TCP/IP** okno.
4. Přepnout **IP adresy** kartě. Posuňte se dolů a najdete **IPAll** části. Poznamenejte si ** TCP Port ** (výchozí hodnota je **1433**).
5. Vytvoření **pravidlo pro bránu Windows Firewall** na počítači, aby povolit příchozí přenosy pomocí tohoto portu.  
6. **Ověření připojení**: pro připojení k serveru SQL pomocí plně kvalifikovaný název, použijte SQL Server Management Studio z jiný počítač. Například: "<machine>.<domain>.Corp.<company>.com,1433."

   > [!IMPORTANT]

   > V tématu [přesun dat mezi místní zdroje a cloudu s Brána pro správu dat](data-factory-move-data-between-onprem-and-cloud.md) podrobné informace.
   >
   > V tématu [potíží brány](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) tipy k řešení potíží s připojení nebo brány související s problémy.
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
**Cílové tabulky:**

```sql
create table dbo.TargetTbl
(
       identifier int identity(1,1),
       name varchar(100),
       age int
)
```

Všimněte si, že cílová tabulka obsahuje sloupec identity.

**Definice JSON datové sady zdroje**

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
**Cílový definici JSON datové sady**

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

Všimněte si, že jako zdrojové a cílové tabulky jiné schéma (cíl má sloupec s identitou). V tomto scénáři budete muset zadat **struktura** vlastnost v definici datové sady cíl, který neobsahuje sloupec identity.

## <a name="invoke-stored-procedure-from-sql-sink"></a>Volání uložené procedury jímku SQL
V tématu [vyvolat uloženou proceduru SQL jímka v aktivitě kopírování](data-factory-invoke-stored-procedure-from-copy-activity.md) článku příklad volání uložené procedury z jímku SQL při aktivitě kopírování kanálu.

## <a name="type-mapping-for-sql-server"></a>Mapování typu pro SQL server
Jak je uvedeno v [aktivity přesunu dat](data-factory-data-movement-activities.md) článku aktivita kopírování provádí automatické typ převody z typů zdroje do jímky typů s následující postup krok 2:

1. Převést na typ .NET typy nativní zdrojů
2. Převést na typ jímky nativní typ formátu .NET

Při přesunu dat do a z SQL serveru, se používají následující mapování z typu SQL na typ .NET a naopak.

Mapování je stejný jako mapování SQL Server datového typu pro technologii ADO.NET.

| Typ databázového stroje SQL Server | Typ rozhraní .NET framework |
| --- | --- |
| bigint |Int64 |
| Binární |Byte] |
| Bit |Logická hodnota |
| Char |Řetězec, Char] |
| datum |DateTime |
| Datum a čas |DateTime |
| datetime2 |DateTime |
| Datový typ DateTimeOffset |Datový typ DateTimeOffset |
| Decimal |Decimal |
| Atribut FILESTREAM (varbinary(max)) |Byte] |
| Float |Double |
| Bitové kopie |Byte] |
| celá čísla |Int32 |
| peníze |Decimal |
| nchar |Řetězec, Char] |
| ntext |Řetězec, Char] |
| číselné |Decimal |
| nvarchar |Řetězec, Char] |
| skutečné |Jednoduchá |
| ROWVERSION |Byte] |
| smalldatetime |DateTime |
| smallint |Int16 |
| Smallmoney |Decimal |
| SQL_VARIANT |Objekt * |
| text |Řetězec, Char] |
| time |Časový interval |
| časové razítko |Byte] |
| tinyint |Bajtů |
| Typ UniqueIdentifier |Guid |
| varbinary |Byte] |
| varchar |Řetězec, Char] |
| xml |XML |

## <a name="mapping-source-to-sink-columns"></a>Mapování zdroje jímky sloupců
Mapování sloupců z datové sady zdroje na sloupce ze sady jímku dat naleznete v tématu [mapování sloupců datovou sadu v Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-copy"></a>Opakovatelných kopie
Při kopírování dat do databáze serveru SQL, připojí aktivitě kopírování dat do tabulky jímky ve výchozím nastavení. Místo toho provést UPSERT, najdete v tématu [Repeatable zapisovat do SqlSink](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink) článku. 

Při kopírování dat z relačních dat ukládá, uvědomte si, aby se zabránilo neúmyslnému výstupy opakovatelnosti. V Azure Data Factory může řez znovu ručně. Zásady opakovaných pokusů pro datovou sadu můžete také nakonfigurovat tak, aby řez se znovu spustí, když dojde k chybě. Řez se znovu spustí, buď způsobem, musíte zajistit, že stejná data je pro čtení bez ohledu na to kolikrát řez je spustit. V tématu [Repeatable číst z relačními zdroji](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Výkon a ladění
V tématu [výkonu kopie aktivity & ladění průvodce](data-factory-copy-activity-performance.md) Další informace o klíčových faktorů, že dopad výkon přesun dat (aktivita kopírování) v Azure Data Factory a různé způsoby, jak optimalizovat ho.
