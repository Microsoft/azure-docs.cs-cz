---
title: Přesunutí dat z PostgreSQL pomocí Azure Data Factory
description: Přečtěte si, jak přesunout data z databáze PostgreSQL pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 888d9ebc-2500-4071-b6d1-0f6bd1b5997c
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 37c83e77cadae002ff701a08c4b36a86f7cab9a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281233"
---
# <a name="move-data-from-postgresql-using-azure-data-factory"></a>Přesunutí dat z PostgreSQL pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](data-factory-onprem-postgresql-connector.md)
> * [Verze 2 (aktuální verze)](../connector-postgresql.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [PostgreSQL konektor ve Verzi 2](../connector-postgresql.md).


Tento článek vysvětluje, jak použít aktivitu kopírování v Azure Data Factory k přesunutí dat z místní databáze PostgreSQL. Vychází z článku [Aktivity přesunu dat,](data-factory-data-movement-activities.md) který představuje obecný přehled přesunu dat s aktivitou kopírování.

Data z místního úložiště dat PostgreSQL můžete zkopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako jímky naleznete v [tématu podporovaná úložiště dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Data factory v současné době podporuje přesun dat z databáze PostgreSQL do jiných úložišť dat, ale ne pro přesun dat z jiných úložišť dat do databáze PostgreSQL.

## <a name="prerequisites"></a>Požadavky

Služba Data Factory podporuje připojení k místním zdrojům PostgreSQL pomocí brány pro správu dat. Podívejte [se na pohybující se data mezi místními lokacemi a cloudovým](data-factory-move-data-between-onprem-and-cloud.md) článkem, kde najdete informace o bráně pro správu dat a podrobné pokyny k nastavení brány.

Brána je vyžadována i v případě, že databáze PostgreSQL je hostovaná ve virtuálním počítači Azure IaaS. Bránu můžete nainstalovat na stejný virtuální počítač IaaS jako úložiště dat nebo na jiný virtuální počítač, pokud se brána může připojit k databázi.

> [!NOTE]
> Tipy týkající se řešení problémů s připojením nebo bránou najdete v [tématu Poradce při potížích](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) s bránou.

## <a name="supported-versions-and-installation"></a>Podporované verze a instalace
Pro připojení brány pro správu dat k databázi PostgreSQL nainstalujte [poskytovatele dat Ngpsql pro PostgreSQL](https://go.microsoft.com/fwlink/?linkid=282716) s verzí mezi 2.0.12 a 3.1.9 do stejného systému jako Brána pro správu dat. PostgreSQL verze 7.4 a vyšší je podporována.

## <a name="getting-started"></a>Začínáme
Můžete vytvořit kanál s aktivitou kopírování, která přesune data z místního úložiště dat PostgreSQL pomocí různých nástrojů nebo api.

- Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Viz [Kurz: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) pro rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním dat.
- K vytvoření kanálu můžete také použít následující nástroje:
  - Visual Studio
  - Azure PowerShell
  - Šablona Azure Resource Manageru
  - .NET API
  - REST API

    Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v tématu [Kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aktivity.

Bez ohledu na to, zda používáte nástroje nebo api, provedete následující kroky k vytvoření kanálu, který přesune data ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvořte **propojené služby** pro propojení vstupních a výstupních úložišť dat s vaší továrně dat.
2. Vytvořte **datové sady** představující vstupní a výstupní data pro operaci kopírování.
3. Vytvořte **kanál** s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup.

Při použití průvodce jsou automaticky vytvořeny definice JSON pro tyto entity Data Factory (propojené služby, datové sady a kanál). Při použití nástrojů nebo rozhraní API (s výjimkou rozhraní .NET API) definujete tyto entity Data Factory pomocí formátu JSON. Ukázka s definicemi JSON pro entity Data Factory, které se používají ke kopírování dat z místního úložiště dat PostgreSQL, najdete v [tématu JSON příklad: Kopírování dat z PostgreSQL do Azure Blob](#json-example-copy-data-from-postgresql-to-azure-blob) části tohoto článku.

V následujících částech jsou uvedeny podrobnosti o vlastnostech JSON, které se používají k definování entit Factory dat specifických pro úložiště dat PostgreSQL:

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb
V následující tabulce je uveden popis prvků JSON specifických pro propojenou službu PostgreSQL.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost type musí být nastavena **na: OnPremisesPostgreSql.** |Ano |
| server |Název postgresql serveru. |Ano |
| database |Název databáze PostgreSQL. |Ano |
| Schématu |Název schématu v databázi. Název schématu rozlišuje malá a velká písmena. |Ne |
| authenticationType |Typ ověřování používaný pro připojení k databázi PostgreSQL. Možné hodnoty jsou: Anonymní, Základní a Windows. |Ano |
| uživatelské jméno |Zadejte uživatelské jméno, pokud používáte základní ověřování nebo ověřování systému Windows. |Ne |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. |Ne |
| název brány |Název brány, kterou by měla služba Data Factory použít pro připojení k místní databázi PostgreSQL. |Ano |

## <a name="dataset-properties"></a>Vlastnosti datové sady
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování datových sad, naleznete v článku [Vytváření datových sad.](data-factory-create-datasets.md) Oddíly, jako je struktura, dostupnost a zásady datové sady JSON jsou podobné pro všechny typy datových sad.

Sekce typeProperties se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti dat. Oddíl typeProperties pro datovou sadu typu **RelationalTable** (která zahrnuje datovou sadu PostgreSQL) má následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky v instanci Databáze PostgreSQL, na kterou odkazuje propojená služba. TableName rozlišuje malá a velká písmena. |Ne (pokud je zadán **dotaz** **RelationalSource)** |

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivit, naleznete v článku [Vytváření kanálů.](data-factory-create-pipelines.md) Vlastnosti, jako je název, popis, vstupní a výstupní tabulky a zásady jsou k dispozici pro všechny typy aktivit.

Vzhledem k tomu, vlastnosti, které jsou k dispozici v typeProperties části aktivity se liší s každým typem aktivity. U aktivity kopírování se liší v závislosti na typech zdrojů a propadů.

Pokud je zdroj typu **RelationalSource** (který zahrnuje PostgreSQL), jsou v části typeProperties k dispozici následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query |Ke čtení dat použijte vlastní dotaz. |Řetězec dotazu SQL. Například: `"query": "select * from \"MySchema\".\"MyTable\""`. |Ne (pokud je zadán **název tabulky** **datové sady)** |

> [!NOTE]
> Názvy schémat a tabulek rozlišují malá a velká písmena. Uzavřete `""` je do (dvojité uvozovky) v dotazu.

**Příklad:**

 `"query": "select * from \"MySchema\".\"MyTable\""`

## <a name="json-example-copy-data-from-postgresql-to-azure-blob"></a>Příklad JSON: Kopírování dat z PostgreSQL do objektu Blob Azure
Tento příklad obsahuje ukázkové definice JSON, které můžete použít k vytvoření kanálu pomocí [Sady Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo Azure [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ukazují, jak zkopírovat data z databáze PostgreSQL do azure blob storage. Data však můžete zkopírovat do libovolného jímky [uvedené zde](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování v Azure Data Factory.

> [!IMPORTANT]
> Tato ukázka obsahuje úryvky JSON. Neobsahuje podrobné pokyny pro vytvoření datové továrny. Podrobné pokyny najdete v článku [přesunutí dat mezi místními umístěními a článkem cloudu.](data-factory-move-data-between-onprem-and-cloud.md)

Ukázka má následující entity datové továrny:

1. Propojená služba typu [OnPremisesPostgreSql](data-factory-onprem-postgresql-connector.md#linked-service-properties).
2. Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní [datová sada](data-factory-create-datasets.md) typu [RelationalTable](data-factory-onprem-postgresql-connector.md#dataset-properties).
4. Výstupní [datová sada](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [Relační zdroj](data-factory-onprem-postgresql-connector.md#copy-activity-properties) a [blobsink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka zkopíruje data z výsledku dotazu v databázi PostgreSQL do objektu blob každou hodinu. Vlastnosti JSON použité v těchto vzorcích jsou popsány v následujících částech.

Jako první krok nastavte bránu pro správu dat. Pokyny jsou v [přesunutí dat mezi místními umístěními a článkem cloudu.](data-factory-move-data-between-onprem-and-cloud.md)

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
**Propojená služba úložiště objektů blob Azure:**

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

Ukázka předpokládá, že jste vytvořili tabulku "MyTable" v PostgreSQL a obsahuje sloupec s názvem "časové razítko" pro data časových řad.

Nastavení `"external": true` informuje službu Data Factory, že datová sada je externí pro datovou továrnu a není vytvářena aktivitou v datové továrně.

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

**Výstupní datová sada objektu Blob Azure:**

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce a název souboru pro objekt blob jsou dynamicky vyhodnocovány na základě počátečního času zpracovávaného řezu. Cesta ke složce používá části počátečního času rok, měsíc, den a hodiny.

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

Kanál obsahuje aktivitu kopírování, která je nakonfigurována pro použití vstupních a výstupních datových sad a je naplánováno na spouštění každou hodinu. V definici kanálu JSON je **typ zdroje** nastaven na **RelationalSource** a typ **jímky** je nastaven na **Objekt blobSink**. Dotaz SQL zadaný pro vlastnost **dotazu** vybere data z tabulky public.usstates v databázi PostgreSQL.

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
Jak je uvedeno v [aktivitách přesunu dat](data-factory-data-movement-activities.md) článek Aktivita kopírování provádí automatické převody typů z typů zdrojů na typy jímek s následujícím dvoustupňovým přístupem:

1. Převod z nativních typů zdrojů na typ .NET
2. Převod z typu .NET na nativní typ jímky

Při přesouvání dat do PostgreSQL se používají následující mapování z typu PostgreSQL na typ .NET.

| Typ databáze PostgreSQL | PostgresSQL aliasy | Typ rozhraní .NET Framework |
| --- | --- | --- |
| abstime | |Datum a čas |
| bigint |int8 |Int64 |
| bigserial |pořad8 |Int64 |
| bit [(n)] | |Bajt[], Řetězec |
| bit mění [ (n) ] |varbit |Bajt[], Řetězec |
| Boolean |bool |Logická hodnota |
| Pole | |Bajt[], Řetězec |
| bytea | |Bajt[], Řetězec |
| znak [(n)] |char [(n)] |Řetězec |
| znak měnící se [(n)] |varchar [(n)] |Řetězec |
| Cid | |Řetězec |
| cidr | |Řetězec |
| kruh | |Bajt[], Řetězec |
| date | |Datum a čas |
| rozsah dat | |Řetězec |
| dvojitá přesnost |plovoucí8 |Double |
| inet | |Bajt[], Řetězec |
| intarry | |Řetězec |
| rozsah int4range | |Řetězec |
| rozsah int8range | |Řetězec |
| celé číslo |int, int4 |Int32 |
| interval [pole] [(p)] | |Časový interval |
| json | |Řetězec |
| jsonb | |Bajt[] |
| line | |Bajt[], Řetězec |
| Lseg (Např. | |Bajt[], Řetězec |
| macaddr | |Bajt[], Řetězec |
| Peníze | |Desetinné číslo |
| číselné [(p, s)] |desítkové [(p, s)] |Desetinné číslo |
| rozsah numrange | |Řetězec |
| Oid | |Int32 |
| cesta | |Bajt[], Řetězec |
| pg_lsn | |Int64 |
| Bod | |Bajt[], Řetězec |
| Mnohoúhelník | |Bajt[], Řetězec |
| reálná |plovoucí4 |Single |
| smallint |int2 |Int16 |
| smallserial |pořad2 |Int16 |
| Sériové |pořad4 |Int32 |
| text | |Řetězec |

## <a name="map-source-to-sink-columns"></a>Mapovat zdroj pro jímací sloupce
Další informace o mapování sloupců ve zdrojové datové sadě na sloupce v datové sadě jímky najdete [v tématu Mapování sloupců datových sad v Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Opakovatelné čtení ze relačních zdrojů
Při kopírování dat z úložišť relačních dat mějte na paměti opakovatelnost, abyste se vyhnuli nezamýšleným výsledkům. V Azure Data Factory můžete znovu spustit řez ručně. Můžete také nakonfigurovat zásady opakování pro datovou sadu tak, aby řez je znovu spustit, když dojde k selhání. Při opětovném spuštění řezu v obou směrech je třeba se ujistit, že stejná data jsou čtena bez ohledu na to, kolikrát je řez spuštěn. Viz [Opakovatelné čtení z relačních zdrojů](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Výkon a ladění
[V tématu Průvodce sledováním výkonu & optimalizací se](data-factory-copy-activity-performance.md) dozvíte o klíčových faktorech, které ovlivňují výkon přesunu dat (aktivita kopírování) ve Službě Azure Data Factory, a o různých způsobech jeho optimalizace.
