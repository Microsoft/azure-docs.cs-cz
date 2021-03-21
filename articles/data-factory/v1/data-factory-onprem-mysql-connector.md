---
title: Přesun dat z MySQL pomocí Azure Data Factory
description: Přečtěte si informace o tom, jak přesunout data z databáze MySQL pomocí Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/06/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 83c39435d2249981a45798ffe0717054fa7b0717
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100387321"
---
# <a name="move-data-from-mysql-using-azure-data-factory"></a>Přesun dat z MySQL pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](data-factory-onprem-mysql-connector.md)
> * [Verze 2 (aktuální verze)](../connector-mysql.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [MySQL Connector ve verzi v2](../connector-mysql.md).


Tento článek vysvětluje, jak pomocí aktivity kopírování v Azure Data Factory přesouvat data z místní databáze MySQL. Sestavuje se podle článku [aktivity přesunu dat](data-factory-data-movement-activities.md) , který prezentuje obecný přehled přesunu dat s aktivitou kopírování.

Data z místního úložiště dat MySQL můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, která aktivita kopírování podporuje jako jímky, najdete v tabulce [podporovaná úložiště dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Data Factory aktuálně podporuje pouze přesouvání dat z úložiště dat MySQL do jiných úložišť dat, ale ne pro přesun dat z jiných úložišť dat do úložiště dat MySQL. 

## <a name="prerequisites"></a>Předpoklady
Služba Data Factory podporuje připojení k místním zdrojům MySQL pomocí brány Správa dat. Další informace o Správa dat bráně a podrobné pokyny k nastavení brány najdete v tématu [přesun dat mezi místními umístěními a v cloudovém](data-factory-move-data-between-onprem-and-cloud.md) článku.

Brána je vyžadována i v případě, že je databáze MySQL hostována ve virtuálním počítači Azure s IaaS (VM). Bránu můžete nainstalovat na stejný virtuální počítač jako úložiště dat nebo na jiný virtuální počítač, pokud se brána může připojit k databázi.

> [!NOTE]
> Tipy k odstraňování potíží souvisejících s připojením nebo bránou najdete v tématu řešení potíží s [bránou](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) .

## <a name="supported-versions-and-installation"></a>Podporované verze a instalace
Aby se Správa dat brána připojovala k databázi MySQL, musíte nainstalovat [konektor MySQL/NET pro Microsoft Windows](https://dev.mysql.com/downloads/connector/net/) (verze mezi 6.6.5 a 6.10.7) na stejném systému jako správa dat bránu. Tento ovladač 32 je kompatibilní s 64 bitovou Správa dat bránou. Je podporována databáze MySQL verze 5,1 a vyšší.

> [!TIP]
> Pokud se zobrazí chyba "ověření se nezdařilo, protože Vzdálená strana zavřela datový proud přenosu", zvažte možnost upgradovat konektor MySQL/NET na vyšší verzi.

## <a name="getting-started"></a>Začínáme
Můžete vytvořit kanál s aktivitou kopírování, která přesouvá data z místního úložiště dat Cassandra pomocí různých nástrojů nebo rozhraní API. 

- Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním dat najdete v tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) . 
- K vytvoření kanálu můžete také použít následující nástroje: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager template**, **.NET API** a **REST API**. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v [kurzu kopírování aktivit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) . 

Bez ohledu na to, jestli používáte nástroje nebo rozhraní API, provedete následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvořte **propojené služby** , které propojí vstupní a výstupní úložiště dat s datovou továrnou.
2. Vytvořte datové **sady** , které reprezentují vstupní a výstupní data pro operaci kopírování. 
3. Vytvořte **kanál** s aktivitou kopírování, která převezme datovou sadu jako vstup a datovou sadu jako výstup. 

Při použití Průvodce se automaticky vytvoří definice JSON pro tyto Entity Data Factory (propojené služby, datové sady a kanál). Pokud používáte nástroje/rozhraní API (s výjimkou rozhraní .NET API), definujete tyto Data Factory entit pomocí formátu JSON.  Ukázku s definicemi JSON pro Entity Data Factory, které se používají ke kopírování dat z místního úložiště dat MySQL, najdete v části [JSON example: kopírování dat z MySQL do Azure Blob](#json-example-copy-data-from-mysql-to-azure-blob) tohoto článku. 

Následující části obsahují podrobné informace o vlastnostech JSON, které se používají k definování Data Factory entit specifických pro úložiště dat MySQL:

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Následující tabulka uvádí popis pro prvky JSON specifické pro propojenou službu MySQL.

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| typ |Vlastnost Type musí být nastavená na: **OnPremisesMySql** . |Yes |
| server |Název serveru MySQL |Yes |
| database |Název databáze MySQL |Yes |
| schema |Název schématu v databázi. |No |
| authenticationType |Typ ověřování, který se používá pro připojení k databázi MySQL. Možné hodnoty jsou: `Basic` . |Yes |
| userName |Zadejte uživatelské jméno pro připojení k databázi MySQL. |Yes |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali. |Yes |
| gatewayName |Název brány, kterou by služba Data Factory měla použít pro připojení k místní databázi MySQL. |Yes |

## <a name="dataset-properties"></a>Vlastnosti datové sady
Úplný seznam sekcí & vlastností dostupných pro definování datových sad naleznete v článku [vytvoření datových sad](data-factory-create-datasets.md) . Oddíly, jako je například struktura, dostupnost a zásada pro datovou sadu JSON, jsou podobné pro všechny typy datových sad (Azure SQL, Azure Blob, tabulka Azure atd.).

Oddíl **typeProperties** se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti dat. Oddíl typeProperties pro datovou sadu **relačních** objektů typu (což zahrnuje datovou sadu MySQL) má následující vlastnosti.

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| tableName |Název tabulky v instanci databáze MySQL, na kterou odkazuje propojená služba |Ne (Pokud je zadán **dotaz** na **RelationalSource** ) |

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam sekcí & vlastností dostupných pro definování aktivit najdete v článku [vytvoření kanálů](data-factory-create-pipelines.md) . Vlastnosti, jako je název, popis, vstupní a výstupní tabulky, jsou zásady dostupné pro všechny typy aktivit.

V takovém případě se vlastnosti dostupné v části **typeProperties** v aktivitě liší podle typu aktivity. U aktivity kopírování se liší v závislosti na typech zdrojů a jímky.

Pokud je zdroj v aktivitě kopírování typu **RelationalSource** (který zahrnuje MySQL), jsou v části typeProperties k dispozici následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| query |Pomocí vlastního dotazu můžete číst data. |Řetězec dotazu SQL. Příklad: SELECT * FROM MyTable. |Ne (Pokud je zadaný **TableName** **objektu DataSet** ) |


## <a name="json-example-copy-data-from-mysql-to-azure-blob"></a>Příklad JSON: kopírování dat z MySQL do Azure Blob
Tento příklad poskytuje ukázkové definice JSON, které můžete použít k vytvoření kanálu pomocí sady [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ukazuje, jak kopírovat data z místní databáze MySQL do Azure Blob Storage. Data však lze zkopírovat do kterékoli z těchto umyvadel, které jsou [zde](data-factory-data-movement-activities.md#supported-data-stores-and-formats) uvedeny, pomocí aktivity kopírování v Azure Data Factory.

> [!IMPORTANT]
> Tato ukázka poskytuje fragmenty kódu JSON. Nezahrnuje podrobné pokyny k vytvoření datové továrny. Podrobné pokyny najdete v článku [přesun dat mezi místními umístěními a v cloudu](data-factory-move-data-between-onprem-and-cloud.md) .

Ukázka má následující Entity Data Factory:

1. Propojená služba typu [OnPremisesMySql](data-factory-onprem-mysql-connector.md#linked-service-properties).
2. Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní [datová sada](data-factory-create-datasets.md) typu [relačních](data-factory-onprem-mysql-connector.md#dataset-properties)objektů.
4. Výstupní [datová sada](data-factory-create-datasets.md) typu [azureblobu](data-factory-azure-blob-connector.md#dataset-properties).
5. [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [RelationalSource](data-factory-onprem-mysql-connector.md#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka kopíruje data z výsledku dotazu v databázi MySQL do objektu BLOB po hodinách. Vlastnosti JSON použité v těchto ukázkách jsou popsány v oddílech následujících po ukázkách.

Jako první krok nastavte bránu pro správu dat. Pokyny najdete v článku [přesun dat mezi místními umístěními a cloudem](data-factory-move-data-between-onprem-and-cloud.md) .

**Propojená služba MySQL:**

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

**Vstupní datová sada MySQL:**

Ukázka předpokládá, že jste v MySQL vytvořili tabulku "MyTable" a obsahuje sloupec s názvem "timestampcolumn" pro data časové řady.

Nastavení "externí": "true" informuje službu Data Factory o tom, že je tabulka externí pro objekt pro vytváření dat, a není vytvořena aktivitou v datové továrně.

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

Data se zapisují do nového objektu BLOB každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce pro objekt BLOB je dynamicky vyhodnocována na základě počátečního času zpracovávaného řezu. Cesta ke složce používá části rok, měsíc, den a hodiny v počátečním čase.

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

Kanál obsahuje aktivitu kopírování, která je nakonfigurovaná tak, aby používala vstupní a výstupní datové sady a má naplánované spuštění každou hodinu. V definici JSON kanálu je typ **zdroje** nastavený na **RelationalSource** a typ **jímky** je nastavený na **BlobSink**. Dotaz SQL zadaný pro vlastnost **dotazu** vybere data během uplynulé hodiny ke zkopírování.

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


### <a name="type-mapping-for-mysql"></a>Mapování typů pro MySQL
Jak je uvedeno v článku [aktivity přesunu dat](data-factory-data-movement-activities.md) , aktivita kopírování provádí automatické převody typů ze zdrojových typů do typů jímky s následujícím dvěma kroky:

1. Převod z nativních zdrojových typů na typ .NET
2. Převést z typu .NET na nativní typ jímky

Při přesunu dat do MySQL se z typů MySQL do typů .NET používají následující mapování.

| Typ databáze MySQL | Typ rozhraní .NET Framework |
| --- | --- |
| bigint bez znaménka |Decimal |
| bigint |Int64 |
| bit |Decimal |
| blob |Byte [] |
| bool |Logická hodnota |
| char |Řetězec |
| date |Datum a čas |
| datetime |Datum a čas |
| decimal |Decimal |
| Dvojitá přesnost |dvojité |
| double |dvojité |
| enum |Řetězec |
| float |Jednoduché |
| celé číslo bez znaménka |Int64 |
| int |Int32 |
| celé číslo bez znaménka |Int64 |
| integer |Int32 |
| Long varbinary |Byte [] |
| Long varchar |Řetězec |
| longblob |Byte [] |
| longtext |Řetězec |
| mediumblob |Byte [] |
| mediumint bez znaménka |Int64 |
| mediumint |Int32 |
| mediumtext |Řetězec |
| numerické |Decimal |
| real |dvojité |
| set |Řetězec |
| typ smallint bez znaménka |Int32 |
| smallint |Int16 |
| text |Řetězec |
| time |TimeSpan |
| časové razítko |Datum a čas |
| tinyblob |Byte [] |
| typ tinyint bez znaménka |Int16 |
| tinyint |Int16 |
| tinytext |Řetězec |
| varchar |Řetězec |
| year |Int |

## <a name="map-source-to-sink-columns"></a>Mapovat zdroj na sloupce jímky
Další informace o mapování sloupců ve zdrojové datové sadě na sloupce v datové sadě jímky najdete v tématu [mapování sloupců datové sady v Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Opakované čtení z relačních zdrojů
Při kopírování dat z relačních úložišť dat mějte na paměti, že se vyhnete nezamýšleným výsledkům. V Azure Data Factory můžete řez znovu spustit ručně. Můžete také nakonfigurovat zásady opakování pro datovou sadu, aby se řez znovu opakoval, když dojde k selhání. Při opětovném spuštění řezu v obou případech je nutné zajistit, že stejná data budou čtena bez ohledu na to, kolikrát je řez spuštěn. Viz [opakované čtení z relačních zdrojů](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Výkon a optimalizace
Další informace o klíčových faktorech, které mají vliv na výkon přesunu dat (aktivita kopírování) v Azure Data Factory a různých způsobech jejich optimalizace, najdete v tématu [Průvodce optimalizací aktivity kopírování &](data-factory-copy-activity-performance.md) .
