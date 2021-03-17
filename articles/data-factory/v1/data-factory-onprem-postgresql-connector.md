---
title: Přesun dat z PostgreSQL pomocí Azure Data Factory
description: Přečtěte si informace o tom, jak přesouvat data z databáze PostgreSQL pomocí Azure Data Factory.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
robots: noindex
ms.openlocfilehash: 8ca8770944a12c6e0dd3e4f95d286758ebee5f9e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100387270"
---
# <a name="move-data-from-postgresql-using-azure-data-factory"></a>Přesun dat z PostgreSQL pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](data-factory-onprem-postgresql-connector.md)
> * [Verze 2 (aktuální verze)](../connector-postgresql.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [konektor PostgreSQL v v2](../connector-postgresql.md).


Tento článek vysvětluje, jak pomocí aktivity kopírování v Azure Data Factory přesouvat data z místní databáze PostgreSQL. Sestavuje se podle článku [aktivity přesunu dat](data-factory-data-movement-activities.md) , který prezentuje obecný přehled přesunu dat s aktivitou kopírování.

Data z místního úložiště dat PostgreSQL můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných jako jímky aktivitou kopírování najdete v části [podporovaná úložiště dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Data Factory v současné době podporuje přesun dat z databáze PostgreSQL do jiných úložišť dat, ale ne pro přesun dat z jiných úložišť dat do databáze PostgreSQL.

## <a name="prerequisites"></a>Požadavky

Služba Data Factory podporuje připojení k místním zdrojům PostgreSQL pomocí brány Správa dat. Další informace o Správa dat bráně a podrobné pokyny k nastavení brány najdete v tématu [přesun dat mezi místními umístěními a v cloudovém](data-factory-move-data-between-onprem-and-cloud.md) článku.

Brána je vyžadována i v případě, že je databáze PostgreSQL hostována ve virtuálním počítači Azure IaaS. Bránu můžete nainstalovat na stejný virtuální počítač s IaaS jako úložiště dat nebo na jiný virtuální počítač, pokud se brána může připojit k databázi.

> [!NOTE]
> Tipy k odstraňování potíží souvisejících s připojením nebo bránou najdete v tématu řešení potíží s [bránou](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) .

## <a name="supported-versions-and-installation"></a>Podporované verze a instalace
Aby se Správa dat brána připojovala k databázi PostgreSQL, nainstalujte [poskytovatele dat Ngpsql pro PostgreSQL](https://go.microsoft.com/fwlink/?linkid=282716) s verzí mezi 2.0.12 a 3.1.9 ve stejném systému jako správa dat bránu. PostgreSQL verze 7,4 a vyšší je podporována.

## <a name="getting-started"></a>Začínáme
Můžete vytvořit kanál s aktivitou kopírování, která přesouvá data z místního úložiště dat PostgreSQL pomocí různých nástrojů nebo rozhraní API.

- Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním dat najdete v tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) .
- K vytvoření kanálu můžete také použít následující nástroje:
  - Visual Studio
  - Azure PowerShell
  - Šablona Azure Resource Manageru
  - .NET API
  - REST API

    Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v [kurzu kopírování aktivit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Bez ohledu na to, jestli používáte nástroje nebo rozhraní API, provedete následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvořte **propojené služby** , které propojí vstupní a výstupní úložiště dat s datovou továrnou.
2. Vytvořte datové **sady** , které reprezentují vstupní a výstupní data pro operaci kopírování.
3. Vytvořte **kanál** s aktivitou kopírování, která převezme datovou sadu jako vstup a datovou sadu jako výstup.

Při použití Průvodce se automaticky vytvoří definice JSON pro tyto Entity Data Factory (propojené služby, datové sady a kanál). Pokud používáte nástroje/rozhraní API (s výjimkou rozhraní .NET API), definujete tyto Data Factory entit pomocí formátu JSON. Ukázku s definicemi JSON pro Entity Data Factory, které se používají ke kopírování dat z místního úložiště dat PostgreSQL, najdete v části [JSON example: kopírování dat z PostgreSQL do objektu blob Azure](#json-example-copy-data-from-postgresql-to-azure-blob) v tomto článku.

Následující části obsahují podrobné informace o vlastnostech JSON, které se používají k definování Data Factory entit specifických pro úložiště dat PostgreSQL:

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Následující tabulka uvádí popis pro prvky JSON specifické pro propojenou službu PostgreSQL.

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| typ |Vlastnost Type musí být nastavená na: **OnPremisesPostgreSql** . |Yes |
| server |Název serveru PostgreSQL. |Yes |
| database |Název databáze PostgreSQL |Yes |
| schema |Název schématu v databázi. V názvu schématu se rozlišují velká a malá písmena. |No |
| authenticationType |Typ ověřování, který se používá pro připojení k databázi PostgreSQL. Možné hodnoty jsou: anonymní, základní a Windows. |Yes |
| username |Pokud používáte základní ověřování nebo ověřování systému Windows, zadejte uživatelské jméno. |No |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. |No |
| gatewayName |Název brány, kterou by služba Data Factory měla použít pro připojení k místní databázi PostgreSQL. |Yes |

## <a name="dataset-properties"></a>Vlastnosti datové sady
Úplný seznam sekcí & vlastností dostupných pro definování datových sad naleznete v článku [vytvoření datových sad](data-factory-create-datasets.md) . Oddíly, jako jsou struktura, dostupnost a zásady pro datovou sadu JSON, jsou podobné pro všechny typy datových sad.

Oddíl typeProperties se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti dat. Oddíl typeProperties pro datovou sadu **relačních** objektů typu (což zahrnuje PostgreSQL DataSet) má následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| tableName |Název tabulky instance databáze PostgreSQL, na kterou odkazuje propojená služba Vlastnost tableName rozlišuje velká a malá písmena. |Ne (Pokud je zadán **dotaz** na **RelationalSource** ) |

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam sekcí & vlastností dostupných pro definování aktivit najdete v článku [vytvoření kanálů](data-factory-create-pipelines.md) . Pro všechny typy aktivit jsou k dispozici vlastnosti, jako je název, popis, vstupní a výstupní tabulka a zásada.

V takovém případě se vlastnosti dostupné v části typeProperties v aktivitě liší podle typu aktivity. U aktivity kopírování se liší v závislosti na typech zdrojů a jímky.

Pokud je zdroj typu **RelationalSource** (který zahrnuje PostgreSQL), jsou v části typeProperties k dispozici následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| query |Pomocí vlastního dotazu můžete číst data. |Řetězec dotazu SQL. Příklad: `"query": "select * from \"MySchema\".\"MyTable\""`. |Ne (Pokud je zadaný **TableName** **objektu DataSet** ) |

> [!NOTE]
> V názvech schémat a tabulek se rozlišují velká a malá písmena. Uzavřete je do `""` dotazu (dvojité uvozovky).

**Příklad:**

 `"query": "select * from \"MySchema\".\"MyTable\""`

## <a name="json-example-copy-data-from-postgresql-to-azure-blob"></a>Příklad JSON: kopírování dat z PostgreSQL do Azure Blob
Tento příklad poskytuje ukázkové definice JSON, které můžete použít k vytvoření kanálu pomocí sady [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ukazují, jak kopírovat data z databáze PostgreSQL do Azure Blob Storage. Data však lze zkopírovat do kterékoli z těchto umyvadel, které jsou [zde](data-factory-data-movement-activities.md#supported-data-stores-and-formats) uvedeny, pomocí aktivity kopírování v Azure Data Factory.

> [!IMPORTANT]
> Tato ukázka poskytuje fragmenty kódu JSON. Nezahrnuje podrobné pokyny k vytvoření datové továrny. Podrobné pokyny najdete v článku [přesun dat mezi místními umístěními a v cloudu](data-factory-move-data-between-onprem-and-cloud.md) .

Ukázka má následující Entity Data Factory:

1. Propojená služba typu [OnPremisesPostgreSql](data-factory-onprem-postgresql-connector.md#linked-service-properties).
2. Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní [datová sada](data-factory-create-datasets.md) typu [relačních](data-factory-onprem-postgresql-connector.md#dataset-properties)objektů.
4. Výstupní [datová sada](data-factory-create-datasets.md) typu [azureblobu](data-factory-azure-blob-connector.md#dataset-properties).
5. [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [RelationalSource](data-factory-onprem-postgresql-connector.md#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka kopíruje data z výsledků dotazu v PostgreSQL databázi do objektu BLOB každou hodinu. Vlastnosti JSON použité v těchto ukázkách jsou popsány v oddílech následujících po ukázkách.

Jako první krok nastavte bránu pro správu dat. Pokyny najdete v článku [přesun dat mezi místními umístěními a cloudem](data-factory-move-data-between-onprem-and-cloud.md) .

**Propojená služba PostgreSQL:**

```json
{
    "name": "OnPremPostgreSqlLinkedService",
    "properties": {
        "type": "OnPremisesPostgreSql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```
**Propojená služba úložiště objektů BLOB v Azure:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
        }
    }
}
```
**Vstupní datová sada PostgreSQL:**

Ukázka předpokládá, že jste v PostgreSQL vytvořili tabulku "MyTable" a obsahuje sloupec s názvem "časové razítko" pro data časových řad.

Nastavení `"external": true` informuje službu Data Factory o tom, že datová sada je externí pro datovou továrnu a není vytvořená aktivitou v datové továrně.

```json
{
    "name": "PostgreSqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremPostgreSqlLinkedService",
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

Data se zapisují do nového objektu BLOB každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce a název souboru pro objekt BLOB jsou dynamicky vyhodnocovány na základě počátečního času zpracovávaného řezu. Cesta ke složce používá části rok, měsíc, den a hodiny v počátečním čase.

```json
{
    "name": "AzureBlobPostgreSqlDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/postgresql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

Kanál obsahuje aktivitu kopírování, která je nakonfigurovaná tak, aby používala vstupní a výstupní datové sady a má naplánované spuštění na každou hodinu. V definici JSON kanálu je typ **zdroje** nastavený na **RelationalSource** a typ **jímky** je nastavený na **BlobSink**. Dotaz SQL zadaný pro vlastnost **dotazu** vybere data z veřejné tabulky. usstates v databázi PostgreSQL.

```json
{
    "name": "CopyPostgreSqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "select * from \"public\".\"usstates\""
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "inputs": [
                    {
                        "name": "PostgreSqlDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobPostgreSqlDataSet"
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
                "name": "PostgreSqlToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```
## <a name="type-mapping-for-postgresql"></a>Mapování typů pro PostgreSQL
Jak je uvedeno v článku aktivity [přesunu dat](data-factory-data-movement-activities.md) , provádí se automatické převody typů ze zdrojových typů do typů jímky s následujícím přístupem ke dvěma krokům:

1. Převod z nativních zdrojových typů na typ .NET
2. Převést z typu .NET na nativní typ jímky

Při přesunu dat na PostgreSQL se z typu PostgreSQL na typ .NET použijí následující mapování.

| Typ databáze PostgreSQL | PostgresSQL aliasy | Typ rozhraní .NET Framework |
| --- | --- | --- |
| abstime | |Datum a čas |
| bigint |int8 |Int64 |
| bigserial |serial8 |Int64 |
| bit [(n)] | |Byte [], řetězec |
| bitové proměnlivost [(n)] |varbit |Byte [], řetězec |
| boolean |bool |Logická hodnota |
| seznam | |Byte [], řetězec |
| Byte | |Byte [], řetězec |
| znak [(n)] |Char [(n)] |Řetězec |
| proměnlivé znaky [(n)] |varchar [(n)] |Řetězec |
| identifikátor | |Řetězec |
| cidr | |Řetězec |
| kruh | |Byte [], řetězec |
| date | |Datum a čas |
| daterange | |Řetězec |
| Dvojitá přesnost |float8 |dvojité |
| inet | |Byte [], řetězec |
| intarry | |Řetězec |
| int4range | |Řetězec |
| int8range | |Řetězec |
| integer |int, int4 |Int32 |
| interval [pole] [(p)] | |Časový interval |
| json | |Řetězec |
| jsonb | |Byte [] |
| line | |Byte [], řetězec |
| lseg | |Byte [], řetězec |
| macaddr | |Byte [], řetězec |
| papír | |Decimal |
| číslice [(p, s)] |Decimal [(p, s)] |Decimal |
| numrange | |Řetězec |
| identifikátor | |Int32 |
| program | |Byte [], řetězec |
| pg_lsn | |Int64 |
| Vyberte | |Byte [], řetězec |
| postupně | |Byte [], řetězec |
| real |float4 |Jednoduché |
| smallint |int2 |Int16 |
| smallserial |serial2 |Int16 |
| sér |serial4 |Int32 |
| text | |Řetězec |

## <a name="map-source-to-sink-columns"></a>Mapovat zdroj na sloupce jímky
Další informace o mapování sloupců ve zdrojové datové sadě na sloupce v datové sadě jímky najdete v tématu [mapování sloupců datové sady v Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Opakované čtení z relačních zdrojů
Při kopírování dat z relačních úložišť dat mějte na paměti, že se vyhnete nezamýšleným výsledkům. V Azure Data Factory můžete řez znovu spustit ručně. Můžete také nakonfigurovat zásady opakování pro datovou sadu, aby se řez znovu opakoval, když dojde k selhání. Při opětovném spuštění řezu v obou případech je nutné zajistit, že stejná data budou čtena bez ohledu na to, kolikrát je řez spuštěn. Viz [opakované čtení z relačních zdrojů](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Výkon a optimalizace
Další informace o klíčových faktorech, které mají vliv na výkon přesunu dat (aktivita kopírování) v Azure Data Factory a různých způsobech jejich optimalizace, najdete v tématu [Průvodce optimalizací aktivity kopírování &](data-factory-copy-activity-performance.md) .
