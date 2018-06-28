---
title: Přesun dat z databáze MySQL pomocí Azure Data Factory | Microsoft Docs
description: Další informace o tom, jak přesunout data z databáze MySQL pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 452f4fce-9eb5-40a0-92f8-1e98691bea4c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/06/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 34de57188dffb7375889ed9ed89a759238b035ac
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37046880"
---
# <a name="move-data-from-mysql-using-azure-data-factory"></a>Přesun dat pomocí Azure Data Factory z databáze MySQL
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](data-factory-onprem-mysql-connector.md)
> * [Verze 2 (aktuální verze)](../connector-mysql.md)

> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory. Pokud používáte aktuální verze služby Data Factory, přečtěte si téma [MySQL konektoru V2](../connector-mysql.md).


Tento článek vysvětluje, jak pomocí aktivity kopírování v Azure Data Factory pro přesun dat z databáze MySQL na místě. Vychází [aktivity přesunu dat](data-factory-data-movement-activities.md) článek, který představuje obecný přehled přesun dat s aktivitou kopírování.

Z úložiště dat MySQL místní může kopírovat data do úložiště dat žádné podporované jímky. Seznam úložišť dat jako jímky nepodporuje aktivitě kopírování najdete v tématu [podporovanými úložišti dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabulky. Objekt pro vytváření dat aktuálně podporuje pouze přesunutí dat z jiného úložiště dat MySQL jiným úložištím dat, ale ne pro přesun dat z jiných úložišť dat k úložišti dat MySQL. 

## <a name="prerequisites"></a>Požadavky
Služba data Factory podporuje připojení k místním zdrojům MySQL pomocí Brána pro správu dat. V tématu [přesouvání dat mezi místní umístění a cloudem](data-factory-move-data-between-onprem-and-cloud.md) článku se dozvíte o Brána pro správu dat a podrobné pokyny o nastavení brány.

Vyžaduje se brána, i když je databáze MySQL hostované ve virtuálním počítači Azure IaaS (VM). Bránu můžete nainstalovat na stejný virtuální počítač jako úložiště dat nebo na jiný virtuální počítač, dokud brána se může připojit k databázi.

> [!NOTE]
> V tématu [potíží brány](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) tipy k řešení potíží s připojení nebo brány související s problémy.

## <a name="supported-versions-and-installation"></a>Podporované verze a instalaci
Pro bránu pro správu dat pro připojení k databázi MySQL, je potřeba nainstalovat [MySQL Connector/Net pro Microsoft Windows](https://dev.mysql.com/downloads/connector/net/) (verze mezi 6.6.5 a 6.10.7) ve stejném systému jako brána pro správu dat. Tento ovladač 32bitové je kompatibilní s Brána pro správu dat 64 bitů. MySQL verze 5.1 a vyšší je podporovaná.

> [!TIP]
> Jestli jste nedosáhli chyba "Ověřování se nezdařilo, protože je uzavřený vzdálené strany přenosu datového proudu.", zvažte MySQL Connector/Net upgradu na vyšší verzi.

## <a name="getting-started"></a>Začínáme
Vytvoření kanálu s aktivitou kopírování, který přesouvá data z úložiště místní Cassandra data pomocí různých nástrojů nebo rozhraní API. 

- Nejjednodušší způsob, jak vytvořit kanál je použití **Průvodce kopírováním**. V tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) podrobný rychlé vytvoření kanálu pomocí Průvodce kopírováním data. 
- Tyto nástroje můžete také použít k vytvoření kanálu: **portál Azure**, **Visual Studio**, **prostředí Azure PowerShell**, **šablony Azure Resource Manageru** , **.NET API**, a **rozhraní REST API**. V tématu [kurzu aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) podrobné pokyny k vytvoření kanálu s aktivitou kopírování. 

Jestli používáte nástroje nebo rozhraní API, je třeba provést následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat podřízený:

1. Vytvoření **propojené služby** propojení vstupní a výstupní data ukládá do data factory.
2. Vytvoření **datové sady** představují vstupní a výstupní data pro kopírování. 
3. Vytvoření **kanálu** s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup. 

Když použijete průvodce, jsou automaticky vytvoří definice JSON pro tyto entity služby Data Factory (propojené služby, datové sady a kanál). Při použití nástroje nebo rozhraní API (s výjimkou .NET API), definujete tyto entity služby Data Factory pomocí formátu JSON.  Příklad s definicemi JSON entit služby Data Factory, které se používají ke zkopírování dat z úložiště dat místní MySQL, naleznete v tématu [JSON příklad: kopírování dat z databáze MySQL do Azure Blob](#json-example-copy-data-from-mysql-to-azure-blob) tohoto článku. 

Následující části obsahují podrobnosti o vlastnostech formátu JSON, které slouží k určení konkrétní entity služby Data Factory k úložišti dat MySQL:

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Následující tabulka obsahuje popis JSON elementy, které jsou specifické pro službu MySQL propojený.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost typu musí být nastavena na: **OnPremisesMySql** |Ano |
| server |Název serveru databáze MySQL. |Ano |
| databáze |Název databáze MySQL. |Ano |
| Schéma |Název schématu v databázi. |Ne |
| authenticationType. |Typ ověřování používaný pro připojení k databázi MySQL. Možné hodnoty jsou: `Basic`. |Ano |
| uživatelské jméno |Zadejte uživatelské jméno pro připojení k databázi MySQL. |Ano |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali. |Ano |
| gatewayName |Název brány, kterou služba Data Factory měla použít pro připojení k místní databázi MySQL. |Ano |

## <a name="dataset-properties"></a>Vlastnosti datové sady
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [vytváření datových sad](data-factory-create-datasets.md) článku. Oddíly, jako je například struktura, dostupnost a zásad JSON datové sady jsou podobné pro všechny typy datovou sadu (Azure SQL Azure blob, tabulky Azure, atd.).

**Rámci typeProperties** oddílu se liší pro jednotlivé typy datovou sadu a informace o umístění dat v úložišti dat. Rámci typeProperties část datové sady typ **RelationalTable** (což zahrnuje datová sada MySQL) má následující vlastnosti

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky instance databáze MySQL na kterou odkazuje propojená služba. |Ne (Pokud **dotazu** z **RelationalSource** je zadána) |

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [vytváření kanálů](data-factory-create-pipelines.md) článku. Vlastnosti, například název, popis, vstupní a výstupní tabulky, jsou zásady jsou dostupné pro všechny typy aktivit.

Vzhledem k tomu, vlastnosti dostupné ve **rámci typeProperties** části aktivity se liší podle každý typ aktivity. Pro aktivitu kopírování budou lišit v závislosti na typech zdrojů a jímky.

Pokud je zdroj v aktivitě kopírování typu **RelationalSource** (která zahrnuje MySQL), následující vlastnosti jsou k dispozici v rámci typeProperties části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query |Čtení dat pomocí vlastního dotazu. |Řetězec dotazu SQL. Příklad: vybrat * z MyTable. |Ne (Pokud **tableName** z **datovou sadu** je zadána) |


## <a name="json-example-copy-data-from-mysql-to-azure-blob"></a>Příklad JSON: kopírování dat z databáze MySQL do objektu Blob Azure
Tento příklad obsahuje ukázkové JSON definice, které můžete použít k vytvoření kanálu pomocí [portál Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) nebo [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [prostředí Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ukazuje, jak zkopírovat data z místní databáze MySQL do Azure Blob Storage. Však lze zkopírovat data do jakéhokoli z jímky uvádí [sem](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování v Azure Data Factory.

> [!IMPORTANT]
> Tato ukázka obsahuje fragmenty kódu JSON. Podrobné pokyny pro vytvoření objektu pro vytváření dat neobsahuje. V tématu [přesouvání dat mezi místní umístění a cloudem](data-factory-move-data-between-onprem-and-cloud.md) podrobné pokyny najdete v článku.

Ukázka má následující entity objektu pro vytváření dat:

1. Propojené služby typu [OnPremisesMySql](data-factory-onprem-mysql-connector.md#linked-service-properties).
2. Propojené služby typu [azurestorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstup [datovou sadu](data-factory-create-datasets.md) typu [RelationalTable](data-factory-onprem-mysql-connector.md#dataset-properties).
4. Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [RelationalSource](data-factory-onprem-mysql-connector.md#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka zkopíruje data z výsledku dotazu v databázi MySQL do objektu blob každou hodinu. Vlastnostech JSON použitých ve tyto ukázky jsou popsané v části následující ukázky.

Jako první krok nastavte Brána pro správu dat. Tyto pokyny jsou v [přesouvání dat mezi místní umístění a cloudem](data-factory-move-data-between-onprem-and-cloud.md) článku.

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

Příkladu se předpokládá, jste vytvořili tabulku "MyTable" v MySQL a obsahuje sloupec s názvem "timestampcolumn" pro data časové řady.

Nastavení "externí": "PRAVDA" informuje služba Data Factory, v tabulce je externí k objektu pro vytváření dat a není vyprodukované aktivitu v datové továrně.

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

**Azure Blob výstupní datovou sadu:**

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodiny, interval: 1). Cesta ke složce pro tento objekt blob je vyhodnocován dynamicky podle času zahájení řezu, které jsou zpracovávány. Cesta ke složce používá rok, měsíc, den a čas částí čas spuštění.

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

Kanál obsahuje aktivitu kopírování, který je nakonfigurovaný na použití vstupní a výstupní datové sady a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **RelationalSource** a **podřízený** je typ nastaven na **BlobSink**. Zadané pro dotaz SQL **dotazu** vlastnost vybere data za poslední hodinu pro kopírování.

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


### <a name="type-mapping-for-mysql"></a>Mapování typu pro databázi MySQL
Jak je uvedeno v [aktivity přesunu dat](data-factory-data-movement-activities.md) článku aktivita kopírování provádí automatické typ převody z typů zdroje do jímky typů s následující postup ve dvou krocích:

1. Převést na typ .NET typy nativní zdrojů
2. Převést na typ jímky nativní typ formátu .NET

Při přesunu dat do databáze MySQL, se používají následující mapování z typů MySQL na typy .NET.

| Typ databáze MySQL | Typ rozhraní .NET framework |
| --- | --- |
| bigint bez znaménka |Decimal |
| bigint |Int64 |
| Bit |Decimal |
| Objekt blob |Byte] |
| BOOL |Logická hodnota |
| Char |Řetězec |
| datum |Datum a čas |
| datetime |Datum a čas |
| Decimal |Decimal |
| Dvojitá přesnost |Double |
| double |Double |
| výčet |Řetězec |
| float |Jednoduchá |
| int bez znaménka |Int64 |
| celá čísla |Int32 |
| celé číslo bez znaménka |Int64 |
| integer |Int32 |
| dlouhé varbinary |Byte] |
| dlouhé varchar |Řetězec |
| longblob |Byte] |
| LONGTEXT |Řetězec |
| mediumblob |Byte] |
| mediumint bez znaménka |Int64 |
| mediumint |Int32 |
| mediumtext |Řetězec |
| číselné |Decimal |
| skutečné |Double |
| Nastavení |Řetězec |
| smallint bez znaménka |Int32 |
| smallint |Int16 |
| text |Řetězec |
| time |Časový interval |
| časové razítko |Datum a čas |
| tinyblob |Byte] |
| tinyint bez znaménka |Int16 |
| tinyint |Int16 |
| tinytext |Řetězec |
| varchar |Řetězec |
| rok |Int |

## <a name="map-source-to-sink-columns"></a>Mapování zdroje jímky sloupců
Další informace o mapování sloupců v datové sadě zdrojového sloupce v datové sadě podřízený najdete v tématu [mapování sloupců datovou sadu v Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Opakovatelných číst z relační zdrojů
Při kopírování dat z relačních dat ukládá, uvědomte si, aby se zabránilo neúmyslnému výstupy opakovatelnosti. V Azure Data Factory může řez znovu ručně. Zásady opakovaných pokusů pro datovou sadu můžete také nakonfigurovat tak, aby řez se znovu spustí, když dojde k chybě. Řez se znovu spustí, buď způsobem, musíte zajistit, že stejná data je pro čtení bez ohledu na to kolikrát řez je spustit. V tématu [Repeatable číst z relačními zdroji](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Výkon a ladění
V tématu [výkonu kopie aktivity & ladění průvodce](data-factory-copy-activity-performance.md) Další informace o klíčových faktorů, že dopad výkon přesun dat (aktivita kopírování) v Azure Data Factory a různé způsoby, jak optimalizovat ho.
