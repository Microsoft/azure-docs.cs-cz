---
title: Přesunutí dat z MySQL pomocí Azure Data Factory
description: Přečtěte si, jak přesunout data z databáze MySQL pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 452f4fce-9eb5-40a0-92f8-1e98691bea4c
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/06/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 90fccba016a3db9ff85f8ec7c8fd426ef3c896a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281285"
---
# <a name="move-data-from-mysql-using-azure-data-factory"></a>Přesunutí dat z MySQL pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](data-factory-onprem-mysql-connector.md)
> * [Verze 2 (aktuální verze)](../connector-mysql.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si [téma MySQL konektor ve Verzi 2](../connector-mysql.md).


Tento článek vysvětluje, jak použít aktivitu kopírování v Azure Data Factory k přesunutí dat z místní databáze MySQL. Vychází z článku [Aktivity přesunu dat,](data-factory-data-movement-activities.md) který představuje obecný přehled přesunu dat s aktivitou kopírování.

Data z místního úložiště dat MySQL můžete zkopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako jímky naleznete v tabulce [Podporovaná úložiště dat.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Data factory v současné době podporuje pouze přesouvání dat z úložiště dat MySQL do jiných úložišť dat, ale ne pro přesun dat z jiných úložišť dat do úložiště dat MySQL. 

## <a name="prerequisites"></a>Požadavky
Služba Data Factory podporuje připojení k místním zdrojům MySQL pomocí brány pro správu dat. Podívejte [se na pohybující se data mezi místními lokacemi a cloudovým](data-factory-move-data-between-onprem-and-cloud.md) článkem, kde najdete informace o bráně pro správu dat a podrobné pokyny k nastavení brány.

Brána je vyžadována i v případě, že databáze MySQL je hostovaná ve virtuálním počítači Azure IaaS (VM). Bránu můžete nainstalovat na stejný virtuální počítač jako úložiště dat nebo na jiný virtuální počítač, pokud se brána může připojit k databázi.

> [!NOTE]
> Tipy týkající se řešení problémů s připojením nebo bránou najdete v [tématu Poradce při potížích](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) s bránou.

## <a name="supported-versions-and-installation"></a>Podporované verze a instalace
Aby se brána pro správu dat připojila k databázi MySQL, musíte nainstalovat [mysql konektor/NET pro Microsoft Windows](https://dev.mysql.com/downloads/connector/net/) (verze mezi 6.6.5 a 6.10.7) do stejného systému jako brána pro správu dat. Tento 32bitový ovladač je kompatibilní s 64bitovou bránou pro správu dat. MySQL verze 5.1 a vyšší je podporována.

> [!TIP]
> Pokud stisknete chybu v části "Ověřování se nezdařilo, protože vzdálená strana uzavřela datový proud přenosu.", zvažte upgrade MySQL Connector/NET na vyšší verzi.

## <a name="getting-started"></a>Začínáme
Můžete vytvořit kanál s aktivitou kopírování, která přesouvá data z místního úložiště dat Cassandra pomocí různých nástrojů nebo rozhraní API. 

- Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Viz [Kurz: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) pro rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním dat. 
- K vytvoření kanálu můžete taky použít následující nástroje: **Visual Studio**, **Azure PowerShell**, **Šablona Azure Resource Manager**, Rozhraní **.NET API**a REST **API**. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v tématu [Kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aktivity. 

Bez ohledu na to, zda používáte nástroje nebo api, provedete následující kroky k vytvoření kanálu, který přesune data ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvořte **propojené služby** pro propojení vstupních a výstupních úložišť dat s vaší továrně dat.
2. Vytvořte **datové sady** představující vstupní a výstupní data pro operaci kopírování. 
3. Vytvořte **kanál** s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup. 

Při použití průvodce jsou automaticky vytvořeny definice JSON pro tyto entity Data Factory (propojené služby, datové sady a kanál). Při použití nástrojů nebo rozhraní API (s výjimkou rozhraní .NET API) definujete tyto entity Data Factory pomocí formátu JSON.  Ukázka s definicemi JSON pro entity Data Factory, které se používají ke kopírování dat z místního úložiště dat MySQL, najdete v [tématu JSON příklad: Kopírování dat z MySQL do Azure Blob](#json-example-copy-data-from-mysql-to-azure-blob) části tohoto článku. 

V následujících částech jsou uvedeny podrobnosti o vlastnostech JSON, které se používají k definování entit Data Factory specifických pro úložiště dat MySQL:

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb
V následující tabulce je uveden popis prvků JSON specifických pro propojenou službu MySQL.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost type musí být nastavena **na: OnPremisesMySql** |Ano |
| server |Název serveru MySQL. |Ano |
| database |Název databáze MySQL. |Ano |
| Schématu |Název schématu v databázi. |Ne |
| authenticationType |Typ ověřování používaný pro připojení k databázi MySQL. Možné hodnoty `Basic`jsou: . |Ano |
| userName |Zadejte uživatelské jméno pro připojení k databázi MySQL. |Ano |
| heslo |Zadejte heslo pro zadaný uživatelský účet. |Ano |
| název brány |Název brány, kterou by měla služba Data Factory použít pro připojení k místní databázi MySQL. |Ano |

## <a name="dataset-properties"></a>Vlastnosti datové sady
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování datových sad, naleznete v článku [Vytváření datových sad.](data-factory-create-datasets.md) Oddíly, jako je struktura, dostupnost a zásady datové sady JSON, jsou podobné pro všechny typy datových sad (Azure SQL, Azure blob, Tabulka Azure atd.).

Sekce **typeProperties** se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti dat. Oddíl typeProperties pro datovou sadu typu **RelationalTable** (která obsahuje datovou sadu MySQL) má následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky v instanci Databáze MySQL, na kterou odkazuje propojená služba. |Ne (pokud je zadán **dotaz** **RelationalSource)** |

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivit, naleznete v článku [Vytváření kanálů.](data-factory-create-pipelines.md) Vlastnosti, jako je název, popis, vstupní a výstupní tabulky, jsou zásady jsou k dispozici pro všechny typy aktivit.

Vzhledem k tomu, vlastnosti, které jsou k dispozici v **typeProperties** části aktivity se liší s každým typem aktivity. U aktivity kopírování se liší v závislosti na typech zdrojů a propadů.

Pokud zdroj v aktivitě kopírování je typu **RelationalSource** (který zahrnuje MySQL), následující vlastnosti jsou k dispozici v typeProperties části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query |Ke čtení dat použijte vlastní dotaz. |Řetězec dotazu SQL. Příklad: vyberte * z MyTable. |Ne (pokud je zadán **název tabulky** **datové sady)** |


## <a name="json-example-copy-data-from-mysql-to-azure-blob"></a>Příklad JSON: Kopírování dat z MySQL do objektu blob Azure
Tento příklad obsahuje ukázkové definice JSON, které můžete použít k vytvoření kanálu pomocí [Sady Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo Azure [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ukazuje, jak zkopírovat data z místní databáze MySQL do úložiště objektů blob Azure. Data však můžete zkopírovat do libovolného jímky [uvedené zde](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování v Azure Data Factory.

> [!IMPORTANT]
> Tato ukázka obsahuje úryvky JSON. Neobsahuje podrobné pokyny pro vytvoření datové továrny. Podrobné pokyny najdete v článku [přesunutí dat mezi místními umístěními a článkem cloudu.](data-factory-move-data-between-onprem-and-cloud.md)

Ukázka má následující entity datové továrny:

1. Propojená služba typu [OnPremisesMySql](data-factory-onprem-mysql-connector.md#linked-service-properties).
2. Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní [datová sada](data-factory-create-datasets.md) typu [RelationalTable](data-factory-onprem-mysql-connector.md#dataset-properties).
4. Výstupní [datová sada](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, který používá [Relační zdroj](data-factory-onprem-mysql-connector.md#copy-activity-properties) a [blobsink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka zkopíruje data z výsledku dotazu v databázi MySQL do objektu blob každou hodinu. Vlastnosti JSON použité v těchto vzorcích jsou popsány v následujících částech.

Jako první krok nastavte bránu pro správu dat. Pokyny jsou v [přesunutí dat mezi místními umístěními a článkem cloudu.](data-factory-move-data-between-onprem-and-cloud.md)

**Služba propojená s MySQL:**

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

**Propojené služby Azure Storage:**

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

Ukázka předpokládá, že jste vytvořili tabulku "MyTable" v MySQL a obsahuje sloupec s názvem "timestampcolumn" pro data časových řad.

Nastavení "externí": "true" informuje službu Data Factory, že tabulka je externí pro datovou továrnu a není vyráběna aktivitou v datové továrně.

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

**Výstupní datová sada objektu Blob Azure:**

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce pro objekt blob je dynamicky vyhodnocována na základě počátečního času zpracovávaného řezu. Cesta ke složce používá části počátečního času rok, měsíc, den a hodiny.

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

Kanál obsahuje aktivitu kopírování, která je nakonfigurována pro použití vstupních a výstupních datových sad a je naplánována na každou hodinu. V definici kanálu JSON je **typ zdroje** nastaven na **RelationalSource** a typ **jímky** je nastaven na **Objekt blobSink**. Dotaz SQL zadaný pro vlastnost **dotazu** vybere data za poslední hodinu ke kopírování.

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
Jak je uvedeno v článku [aktivity přesunu dat,](data-factory-data-movement-activities.md) copy aktivita provádí převody automatického typu z typů zdrojů na typy jímek s následujícím dvoustupňovým přístupem:

1. Převod z nativních typů zdrojů na typ .NET
2. Převod z typu .NET na nativní typ jímky

Při přesouvání dat do MySQL se používají následující mapování z typů MySQL na typy .NET.

| Typ databáze MySQL | Typ rozhraní .NET Framework |
| --- | --- |
| bigint nepodepsaný |Desetinné číslo |
| bigint |Int64 |
| bitové |Desetinné číslo |
| blob |Bajt[] |
| bool |Logická hodnota |
| char |Řetězec |
| date |Datum a čas |
| datetime |Datum a čas |
| decimal |Desetinné číslo |
| dvojitá přesnost |Double |
| double |Double |
| enum |Řetězec |
| float |Single |
| int nepodepsané |Int64 |
| int |Int32 |
| celé číslo nepodepsané |Int64 |
| celé číslo |Int32 |
| dlouhý varbinary |Bajt[] |
| dlouhý varchar |Řetězec |
| longblob |Bajt[] |
| dlouhý text |Řetězec |
| mediumblob |Bajt[] |
| mediumint nepodepsané |Int64 |
| střední int |Int32 |
| střední text |Řetězec |
| numerické |Desetinné číslo |
| reálná |Double |
| set |Řetězec |
| smallint nepodepsané |Int32 |
| smallint |Int16 |
| text |Řetězec |
| time |TimeSpan |
| časové razítko |Datum a čas |
| tinyblob |Bajt[] |
| tinyint nepodepsané |Int16 |
| tinyint |Int16 |
| tinytext |Řetězec |
| varchar |Řetězec |
| year |Int |

## <a name="map-source-to-sink-columns"></a>Mapovat zdroj pro jímací sloupce
Další informace o mapování sloupců ve zdrojové datové sadě na sloupce v datové sadě jímky najdete [v tématu Mapování sloupců datových sad v Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Opakovatelné čtení ze relačních zdrojů
Při kopírování dat z úložišť relačních dat mějte na paměti opakovatelnost, abyste se vyhnuli nezamýšleným výsledkům. V Azure Data Factory můžete znovu spustit řez ručně. Můžete také nakonfigurovat zásady opakování pro datovou sadu tak, aby řez je znovu spustit, když dojde k selhání. Při opětovném spuštění řezu v obou směrech je třeba se ujistit, že stejná data jsou čtena bez ohledu na to, kolikrát je řez spuštěn. Viz [Opakovatelné čtení z relačních zdrojů](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Výkon a ladění
[V tématu Průvodce sledováním výkonu & optimalizací se](data-factory-copy-activity-performance.md) dozvíte o klíčových faktorech, které ovlivňují výkon přesunu dat (aktivita kopírování) ve Službě Azure Data Factory, a o různých způsobech jeho optimalizace.
