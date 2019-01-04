---
title: Přesun dat z databáze MySQL pomocí Azure Data Factory | Dokumentace Microsoftu
description: Další informace o tom, jak přesunout data z databáze MySQL pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 452f4fce-9eb5-40a0-92f8-1e98691bea4c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/06/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: de1263d68e96a23bd6b5eca4297e74b56ba22e40
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54021629"
---
# <a name="move-data-from-mysql-using-azure-data-factory"></a>Přesun dat z MySQL pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](data-factory-onprem-mysql-connector.md)
> * [Verze 2 (aktuální verze)](../connector-mysql.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [konektor MySQL ve verzi V2](../connector-mysql.md).


Tento článek vysvětluje, jak použít aktivitu kopírování v Azure Data Factory k přesunu dat z místní databáze MySQL. Je nástavbou [aktivity přesunu dat](data-factory-data-movement-activities.md) článek, který nabízí obecný přehled o přesun dat pomocí aktivity kopírování.

Kopírování dat z do místního úložiště dat MySQL do jakékoli podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako jímky, najdete v článku [podporovanými úložišti dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabulky. Data factory aktuálně podporuje pouze přesouvá data z úložiště dat MySQL do jiných úložišť dat, ale ne pro přesun dat z jiných úložišť dat do úložiště dat MySQL. 

## <a name="prerequisites"></a>Požadavky
Služba data Factory podporuje připojení k místním zdrojům MySQL pomocí brány správy dat. Zobrazit [přesun dat mezi místními umístěními a cloudem](data-factory-move-data-between-onprem-and-cloud.md) článku se dozvíte o brána správy dat a podrobné pokyny o nastavení brány.

I v případě, že je hostitelem databáze MySQL na virtuálním počítači Azure IaaS (VM) je potřeba brána. Bránu můžete nainstalovat na stejný virtuální počítač jako úložiště dat nebo jinému virtuálnímu počítači, tak dlouho, dokud brána lze připojit k databázi.

> [!NOTE]
> Naleznete v tématu [potíží brány](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) tipy k řešení potíží s připojení/bránou související problémy.

## <a name="supported-versions-and-installation"></a>Podporované verze a instalace
Pro bránu správy dat pro připojení k databázi MySQL, je potřeba nainstalovat [MySQL Connector/Net pro Microsoft Windows](https://dev.mysql.com/downloads/connector/net/) (verze mezi 6.6.5 a 6.10.7) ve stejném systému jako brána pro správu dat. Tento ovladač 32 bitů je kompatibilní s brána správy dat 64-bit. MySQL verze 5.1 a novější se podporuje.

> [!TIP]
> Pokud dosáhnete chyba "ověření se nezdařilo, protože vzdálená strana zavřela datový proud přenosu.", zvažte možnost upgradovat na vyšší verzi MySQL Connector/Net.

## <a name="getting-started"></a>Začínáme
Vytvoření kanálu s aktivitou kopírování, který přesouvá data z úložiště dat místní Cassandra pomocí různých nástrojů a rozhraní API. 

- Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Zobrazit [kurzu: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním data. 
- Tyto nástroje můžete také použít k vytvoření kanálu: **Azure portal**, **sady Visual Studio**, **prostředí Azure PowerShell**, **šablony Azure Resource Manageru**, **rozhraní .NET API**a  **Rozhraní REST API**. Zobrazit [kurz aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) podrobné pokyny k vytvoření kanálu s aktivitou kopírování. 

Ať už používáte, nástrojů nebo rozhraní API, proveďte následující kroky k vytvoření kanálu pro přesouvání dat ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvoření **propojené služby** propojení vstupní a výstupní data ukládá do služby data factory.
2. Vytvoření **datových sad** k představují vstupní a výstupní data pro operaci kopírování. 
3. Vytvoření **kanálu** s aktivitou kopírování, která přijímá jako vstupní datovou sadu a datovou sadu jako výstup. 

Při použití Průvodce definice JSON pro tyto entity služby Data Factory (propojené služby, datové sady a kanál) se automaticky vytvoří za vás. Při použití nástroje a rozhraní API (s výjimkou rozhraní .NET API), můžete definovat tyto entity služby Data Factory ve formátu JSON.  Tady je příklad s definice JSON entit služby Data Factory, které se používají ke kopírování dat z úložiště dat MySQL v místním, naleznete v tématu [příklad JSON: Kopírování dat z databáze MySQL do objektů Blob v Azure](#json-example-copy-data-from-mysql-to-azure-blob) části tohoto článku. 

Následující části obsahují podrobnosti o vlastnostech JSON, které se používají k definování entit služby Data Factory konkrétní do úložiště dat MySQL:

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Následující tabulka obsahuje popis JSON elementy, které jsou specifické pro MySQL propojené služby.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost type musí být nastavená na: **OnPremisesMySql** |Ano |
| server |Název serveru MySQL. |Ano |
| databáze |Název databáze MySQL. |Ano |
| schéma |Název schématu databáze. |Ne |
| authenticationType. |Typ ověřování používaný pro připojení k databázi MySQL. Možné hodnoty jsou: `Basic`. |Ano |
| uživatelské jméno |Zadejte uživatelské jméno pro připojení k databázi MySQL. |Ano |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali. |Ano |
| Název brány |Název brány, který služba Data Factory měla použít pro připojení k místní databázi MySQL. |Ano |

## <a name="dataset-properties"></a>Vlastnosti datové sady
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [vytváření datových sad](data-factory-create-datasets.md) článku. Oddíly, jako je například struktura, dostupnost a zásad JSON datové sady jsou podobné pro všechny datové sady typy (Azure SQL, Azure blob, tabulky Azure, atd.).

**TypeProperties** oddílu se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti. TypeProperties části datové sady typu **RelationalTable** (což zahrnuje datová sada MySQL) má následující vlastnosti

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky instance databáze MySQL, propojená služba odkazuje na. |Ne (Pokud **dotazu** z **RelationalSource** určena) |

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [vytváření kanálů](data-factory-create-pipelines.md) článku. Vlastnosti, jako je název, popis, vstupní a výstupní tabulky, jsou zásady jsou k dispozici pro všechny typy aktivit.

Vzhledem k tomu, k dispozici ve vlastnosti **typeProperties** části aktivity se liší s jednotlivými typu aktivity. Pro aktivitu kopírování se liší v závislosti na typy zdroje a jímky.

Když zdroj v aktivitě kopírování je typu **RelationalSource** (která zahrnuje MySQL), v části typeProperties jsou k dispozici následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query |Použijte vlastní dotaz číst data. |Řetězec dotazu SQL. Příklad: vybrat * z MyTable. |Ne (Pokud **tableName** z **datovou sadu** určena) |


## <a name="json-example-copy-data-from-mysql-to-azure-blob"></a>Příklad JSON: Kopírování dat z databáze MySQL do objektů Blob v Azure
V tomto příkladu obsahuje ukázky JSON definice, které můžete použít k vytvoření kanálu pomocí [webu Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) nebo [sady Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [prostředí Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ukazuje, jak kopírovat data z místní databáze MySQL do služby Azure Blob Storage. Ale data je možné zkopírovat do libovolné jímky uvedeno [tady](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivit kopírování ve službě Azure Data Factory.

> [!IMPORTANT]
> Tato ukázka poskytuje fragmenty kódu JSON. Neobsahuje podrobné pokyny pro vytvoření datové továrny. Zobrazit [přesun dat mezi místními umístěními a cloudu](data-factory-move-data-between-onprem-and-cloud.md) najdete podrobné pokyny.

Ukázka obsahuje následující entit datové továrny:

1. Propojené služby typu [OnPremisesMySql](data-factory-onprem-mysql-connector.md#linked-service-properties).
2. Propojené služby typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní hodnota [datovou sadu](data-factory-create-datasets.md) typu [RelationalTable](data-factory-onprem-mysql-connector.md#dataset-properties).
4. Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [RelationalSource](data-factory-onprem-mysql-connector.md#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázce kopíruje data z výsledků dotazu v databázi MySQL do objektu blob po hodinách. Vlastnostech JSON použitých v tyto ukázky jsou popsány v části podle ukázky.

Jako první krok instalační program brány správy dat. Pokyny jsou v [přesun dat mezi místními umístěními a cloudem](data-factory-move-data-between-onprem-and-cloud.md) článku.

**MySQL propojené služby:**

```JSON
    {
      "name": "OnPremMySqlLinkedService",
      "properties": {
        "type": "OnPremisesMySql",
        "typeProperties": {
          "server": "<server name>",
          "database": "<database name>",
          "schema": "<schema name>",
          "authenticationType": "<authentication type>",
          "userName": "<user name>",
          "password": "<password>",
          "gatewayName": "<gateway>"
        }
      }
    }
```

**Propojená služba Azure Storage:**

```JSON
    {
      "name": "AzureStorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }
```

**MySQL vstupní datové sady:**

Příkladu se předpokládá vytvoříte tabulku "MyTable" v MySQL a obsahuje sloupec s názvem "timestampcolumn" pro data časových řad.

Nastavení "externí": "PRAVDA" informuje služby Data Factory, že v tabulce je externí do služby data factory a není vytvořen aktivitou ve službě data factory.

```JSON
    {
        "name": "MySqlDataSet",
        "properties": {
            "published": false,
            "type": "RelationalTable",
            "linkedServiceName": "OnPremMySqlLinkedService",
            "typeProperties": {},
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
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

**Výstupní datová sada Azure Blob:**

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce pro objekt blob se dynamicky vyhodnocuje na základě doby spuštění řez, který se právě zpracovává. Cesta ke složce používá rok, měsíc, den a části hodin čas spuštění.

```JSON
    {
        "name": "AzureBlobMySqlDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/mysql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
                "format": {
                    "type": "TextFormat",
                    "rowDelimiter": "\n",
                    "columnDelimiter": "\t"
                },
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
                ]
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
```

**Kanál s aktivitou kopírování:**

Kanálu obsahujícího aktivitu kopírování, který je nakonfigurován na použití vstupních a výstupních datových sad a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **RelationalSource** a **jímky** je typ nastaven na **BlobSink**. Zadaná pro dotaz SQL **dotazu** vlastnost vybere data za poslední hodinu pro kopírování.

```JSON
    {
        "name": "CopyMySqlToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "MySqlDataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobMySqlDataSet"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "MySqlToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }
```


### <a name="type-mapping-for-mysql"></a>Mapování typu for MySQL
Jak je uvedeno v [aktivity přesunu dat](data-factory-data-movement-activities.md) článku, aktivita kopírování provádí automatické typ převody z typů zdroje do jímky typy s následující dvoukrokový přístup:

1. Převést na typ formátu .NET typy nativních zdrojů
2. Převést z typu .NET native jímky typu

Při přesouvání dat k MySQL, se používají následující mapování z typů MySQL na typy .NET.

| Typ databáze MySQL | Typ rozhraní .NET framework |
| --- | --- |
| bigint bez znaménka |Desítkově |
| bigint |Int64 |
| Bit |Desítkově |
| blob |Byte] |
| BOOL |Logická hodnota |
| Char |Řetězec |
| date |Datum a čas |
| datetime |Datum a čas |
| decimal |Desítkově |
| dvojitou přesností |Double |
| double |Double |
| Výčet |Řetězec |
| float |Jednoduchá |
| int unsigned |Int64 |
| int |Datový typ Int32 |
| celé číslo bez znaménka |Int64 |
| integer |Datový typ Int32 |
| dlouhé varbinary |Byte] |
| Long varchar |Řetězec |
| longblob |Byte] |
| LONGTEXT |Řetězec |
| mediumblob |Byte] |
| mediumint bez znaménka |Int64 |
| mediumint |Datový typ Int32 |
| mediumtext |Řetězec |
| Číselné |Desítkově |
| Real |Double |
| set |Řetězec |
| smallint bez znaménka |Datový typ Int32 |
| smallint |Int16 |
| text |Řetězec |
| time |Časový interval |
| časové razítko |Datum a čas |
| tinyblob |Byte] |
| tinyint bez znaménka |Int16 |
| tinyint |Int16 |
| tinytext |Řetězec |
| varchar |Řetězec |
| za rok |Int |

## <a name="map-source-to-sink-columns"></a>Mapování zdroje do jímky sloupce
Další informace o mapování sloupců v datové sadě zdroje do sloupců v datové sadě jímky, najdete v článku [mapování sloupců v datové sadě ve službě Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Opakovatelné čtení z relačních zdrojů
Při kopírování dat z relačních dat ukládá, mějte opakovatelnosti aby se zabránilo neúmyslnému výsledků. Ve službě Azure Data Factory můžete znovu spustit řezu ručně. Zásady opakování pro datovou sadu můžete také nakonfigurovat tak, aby určitý řez se znovu spustí, když dojde k chybě. V obou případech se znovu spustí určitý řez, musíte zajistit, že stejná data je pro čtení bez ohledu na to kolikrát spustit určitý řez. Zobrazit [Repeatable z relačních zdrojů](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Výkon a optimalizace
Zobrazit [výkonem aktivity kopírování & Průvodci optimalizací](data-factory-copy-activity-performance.md) Další informace o klíčových faktorů této ovlivnit výkon přesouvání dat (aktivita kopírování) ve službě Azure Data Factory a různé způsoby, jak optimalizovat.
