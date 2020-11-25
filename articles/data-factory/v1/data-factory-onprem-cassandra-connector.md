---
title: Přesun dat z Cassandra pomocí Data Factory
description: Přečtěte si, jak přesunout data z místní databáze Cassandra pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 085cc312-42ca-4f43-aa35-535b35a102d5
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0f96680f1ea91434c84d6606e3637c68c1cb5a84
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019629"
---
# <a name="move-data-from-an-on-premises-cassandra-database-using-azure-data-factory"></a>Přesun dat z místní databáze Cassandra pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](data-factory-onprem-cassandra-connector.md)
> * [Verze 2 (aktuální verze)](../connector-cassandra.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [konektor Cassandra v v2](../connector-cassandra.md).

Tento článek vysvětluje, jak pomocí aktivity kopírování v Azure Data Factory přesouvat data z místní databáze Cassandra. Sestavuje se podle článku [aktivity přesunu dat](data-factory-data-movement-activities.md) , který prezentuje obecný přehled přesunu dat s aktivitou kopírování.

Data z místního úložiště dat Cassandra můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, která aktivita kopírování podporuje jako jímky, najdete v tabulce [podporovaná úložiště dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Data Factory aktuálně podporuje jenom přesun dat z úložiště Cassandra data do jiných úložišť dat, ale ne pro přesun dat z jiných úložišť dat do úložiště dat Cassandra.

## <a name="supported-versions"></a>Podporované verze
Konektor Cassandra podporuje následující verze Cassandra: 2. x a 3. x. V případě aktivity spuštěné v místním prostředí Integration Runtime se Cassandra 3. x podporuje od verze IR 3,7 a vyšší.

## <a name="prerequisites"></a>Požadavky
Aby se služba Azure Data Factory mohla připojit k místní databázi Cassandra, musíte nainstalovat Správa dat bránu do stejného počítače, který hostuje databázi, nebo na samostatném počítači, abyste se vyhnuli konkurenčním prostředkům s databází. Správa dat Gateway je komponenta, která připojuje místní zdroje dat ke cloudovým službám zabezpečeným a spravovaným způsobem. Podrobnosti o Správa dat bráně najdete v článku o [Správa dat brány](data-factory-data-management-gateway.md) . Podrobné pokyny týkající se nastavení brány a datového kanálu pro přesun dat najdete v článku [o přesunu dat z místního prostředí do cloudu](data-factory-move-data-between-onprem-and-cloud.md) .

Bránu musíte použít pro připojení k databázi Cassandra, i když je databáze hostovaná v cloudu, například na virtuálním počítači Azure s IaaS. Y můžete mít bránu na stejném virtuálním počítači, který hostuje databázi, nebo na samostatném virtuálním počítači, pokud se brána může připojit k databázi.

Při instalaci brány se automaticky nainstaluje ovladač Microsoft Cassandra ODBC, který slouží k připojení k databázi Cassandra. Proto nemusíte při kopírování dat z databáze Cassandra ručně instalovat žádný ovladač na počítač brány.

> [!NOTE]
> Tipy k odstraňování potíží souvisejících s připojením nebo bránou najdete v tématu řešení potíží s [bránou](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) .

## <a name="getting-started"></a>Začínáme
Můžete vytvořit kanál s aktivitou kopírování, která přesouvá data z místního úložiště dat Cassandra pomocí různých nástrojů nebo rozhraní API.

- Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním dat najdete v tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) .
- K vytvoření kanálu můžete také použít následující nástroje: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager template**, **.NET API** a **REST API**. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v [kurzu kopírování aktivit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Bez ohledu na to, jestli používáte nástroje nebo rozhraní API, provedete následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvořte **propojené služby** , které propojí vstupní a výstupní úložiště dat s datovou továrnou.
2. Vytvořte datové **sady** , které reprezentují vstupní a výstupní data pro operaci kopírování.
3. Vytvořte **kanál** s aktivitou kopírování, která převezme datovou sadu jako vstup a datovou sadu jako výstup.

Při použití Průvodce se automaticky vytvoří definice JSON pro tyto Entity Data Factory (propojené služby, datové sady a kanál). Pokud používáte nástroje/rozhraní API (s výjimkou rozhraní .NET API), definujete tyto Data Factory entit pomocí formátu JSON. Ukázku s definicemi JSON pro Entity Data Factory, které se používají ke kopírování dat z místního úložiště dat Cassandra, najdete v části [JSON example: kopírování dat z Cassandra do objektu blob Azure](#json-example-copy-data-from-cassandra-to-azure-blob) v tomto článku.

Následující části obsahují podrobné informace o vlastnostech JSON, které se používají k definování Data Factory entit specifických pro úložiště dat Cassandra:

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Následující tabulka uvádí popis pro prvky JSON specifické pro propojenou službu Cassandra.

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| typ |Vlastnost Type musí být nastavená na: **OnPremisesCassandra** . |Yes |
| Hostitel |Jedna nebo víc IP adres nebo názvů hostitelů Cassandra serverů.<br/><br/>Zadejte čárkami oddělený seznam IP adres nebo názvů hostitelů pro připojení ke všem serverům současně. |Yes |
| port |Port TCP, který server Cassandra používá k naslouchání klientským připojením. |Ne, výchozí hodnota: 9042 |
| authenticationType |Basic nebo Anonymous |Yes |
| username |Zadejte uživatelské jméno pro uživatelský účet. |Ano, pokud je authenticationType nastaveno na Basic. |
| heslo |Zadejte heslo pro uživatelský účet. |Ano, pokud je authenticationType nastaveno na Basic. |
| gatewayName |Název brány, který se používá pro připojení k místní databázi Cassandra. |Yes |
| encryptedCredential |Přihlašovací údaje zašifrované bránou |No |

>[!NOTE]
>V současné době se připojení k Cassandra pomocí protokolu TLS nepodporuje.

## <a name="dataset-properties"></a>Vlastnosti datové sady
Úplný seznam sekcí & vlastností dostupných pro definování datových sad naleznete v článku [vytvoření datových sad](data-factory-create-datasets.md) . Oddíly, jako je například struktura, dostupnost a zásada pro datovou sadu JSON, jsou podobné pro všechny typy datových sad (Azure SQL, Azure Blob, tabulka Azure atd.).

Oddíl **typeProperties** se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti dat. Oddíl typeProperties pro sadu dat typu **CassandraTable** má následující vlastnosti.

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| prostor klíčů |Název prostoru klíčů nebo schématu v databázi Cassandra. |Ano (Pokud není definován **dotaz** pro **CassandraSource** ). |
| tableName |Název tabulky v databázi Cassandra |Ano (Pokud není definován **dotaz** pro **CassandraSource** ). |

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam sekcí & vlastností dostupných pro definování aktivit najdete v článku [vytvoření kanálů](data-factory-create-pipelines.md) . Pro všechny typy aktivit jsou k dispozici vlastnosti, jako je název, popis, vstupní a výstupní tabulka a zásada.

V takovém případě se vlastnosti dostupné v části typeProperties v aktivitě liší podle typu aktivity. U aktivity kopírování se liší v závislosti na typech zdrojů a jímky.

Pokud je zdroj typu **CassandraSource**, jsou v oddílu typeProperties k dispozici následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| query |Pomocí vlastního dotazu můžete číst data. |Dotaz SQL-92 nebo dotaz CQL Viz [odkaz na CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Při použití dotazu SQL zadejte **název prostoru klíčů. název tabulky** , který bude představovat tabulku, kterou chcete dotazovat. |Ne (Pokud je definováno pole tableName a prostor pro datovou sadu). |
| consistencyLevel |Úroveň konzistence určuje, kolik replik musí odpovídat žádosti o čtení před vrácením dat do klientské aplikace. Cassandra zkontroluje zadaný počet replik dat, aby splňovaly požadavky na čtení. |JEDNA, DVĚ, TŘI, KVORA, VŠE, LOCAL_QUORUM, EACH_QUORUM LOCAL_ONE. Podrobnosti najdete v tématu [Konfigurace konzistence dat](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) . |Ne. Výchozí hodnota je jedna. |

## <a name="json-example-copy-data-from-cassandra-to-azure-blob"></a>Příklad JSON: kopírování dat z Cassandra do Azure Blob
Tento příklad poskytuje ukázkové definice JSON, které můžete použít k vytvoření kanálu pomocí sady [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ukazuje, jak kopírovat data z místní databáze Cassandra do Azure Blob Storage. Data však lze zkopírovat do kterékoli z těchto umyvadel, které jsou [zde](data-factory-data-movement-activities.md#supported-data-stores-and-formats) uvedeny, pomocí aktivity kopírování v Azure Data Factory.

> [!IMPORTANT]
> Tato ukázka poskytuje fragmenty kódu JSON. Nezahrnuje podrobné pokyny k vytvoření datové továrny. Podrobné pokyny najdete v článku [přesun dat mezi místními umístěními a v cloudu](data-factory-move-data-between-onprem-and-cloud.md) .

Ukázka má následující Entity Data Factory:

* Propojená služba typu [OnPremisesCassandra](#linked-service-properties).
* Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Vstupní [datová sada](data-factory-create-datasets.md) typu [CassandraTable](#dataset-properties).
* Výstupní [datová sada](data-factory-create-datasets.md) typu [azureblobu](data-factory-azure-blob-connector.md#dataset-properties).
* [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [CassandraSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

**Propojená služba Cassandra:**

Tento příklad používá propojenou službu **Cassandra** . Vlastnosti podporované touto propojenou službou najdete v části [propojená služba Cassandra](#linked-service-properties) .

```json
{
    "name": "CassandraLinkedService",
    "properties":
    {
        "type": "OnPremisesCassandra",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "host": "mycassandraserver",
            "port": 9042,
            "username": "user",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```

**Propojená služba Azure Storage:**

```json
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

**Vstupní datová sada Cassandra:**

```json
{
    "name": "CassandraInput",
    "properties": {
        "linkedServiceName": "CassandraLinkedService",
        "type": "CassandraTable",
        "typeProperties": {
            "tableName": "mytable",
            "keySpace": "mykeyspace"
        },
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

Nastavením možnosti **externí** na **hodnotu true** dojde k informování služby Data Factory, že datová sada je pro objekt pro vytváření dat externá a není vytvořená aktivitou v datové továrně.

**Výstupní datová sada Azure Blob:**

Data se zapisují do nového objektu BLOB každou hodinu (frekvence: hodina, interval: 1).

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/fromcassandra"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Aktivita kopírování v kanálu se zdrojem Cassandra a jímky objektů BLOB:**

Kanál obsahuje aktivitu kopírování, která je nakonfigurovaná tak, aby používala vstupní a výstupní datové sady a má naplánované spuštění každou hodinu. V definici JSON kanálu je typ **zdroje** nastavený na **CassandraSource** a typ **jímky** je nastavený na **BlobSink**.

Seznam vlastností podporovaných rozhraním RelationalSource naleznete v tématu [vlastnosti typu RelationalSource](#copy-activity-properties) .

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2016-06-01T18:00:00",
        "end":"2016-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[
        {
            "name": "CassandraToAzureBlob",
            "description": "Copy from Cassandra to an Azure blob",
            "type": "Copy",
            "inputs": [
            {
                "name": "CassandraInput"
            }
            ],
            "outputs": [
            {
                "name": "AzureBlobOutput"
            }
            ],
            "typeProperties": {
                "source": {
                    "type": "CassandraSource",
                    "query": "select id, firstname, lastname from mykeyspace.mytable"

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

### <a name="type-mapping-for-cassandra"></a>Mapování typů pro Cassandra
| Typ Cassandra | Typ založený na .NET |
| --- | --- |
| ASCII |Řetězec |
| BIGINT |Int64 |
| PŘÍZNAKY |Byte [] |
| DATOVÉHO |Logická hodnota |
| NOTACI |Decimal |
| KLEPAT |dvojité |
| Plovák |Jeden |
| INET |Řetězec |
| INT |Int32 |
| TEXT |Řetězec |
| ČASOVÉ razítko |DateTime |
| TIMEUUID |Identifikátor GUID |
| IDENTIFIKÁTOR |Identifikátor GUID |
| VARCHAR |Řetězec |
| VARINT |Decimal |

> [!NOTE]
> Pro typy kolekcí (mapování, nastavení, seznam atd.) se podívejte na téma [práce s typy kolekce Cassandra pomocí virtuální tabulky](#work-with-collections-using-virtual-table) .
>
> Uživatelsky definované typy nejsou podporovány.
>
> Délka sloupců binárního sloupce a řetězce sloupce nesmí být větší než 4000.
>
>

## <a name="work-with-collections-using-virtual-table"></a>Práce s kolekcemi pomocí virtuální tabulky
Azure Data Factory používá integrovaný ovladač ODBC pro připojení a zkopírování dat z databáze Cassandra. U typů kolekcí, včetně map, nastavení a seznamu, ovladač znovu normalizuje data v odpovídajících virtuálních tabulkách. Konkrétně, pokud tabulka obsahuje všechny sloupce kolekce, vygeneruje ovladač následující virtuální tabulky:

* **Základní tabulka**, která obsahuje stejná data jako skutečná tabulka s výjimkou sloupců kolekce. Základní tabulka používá stejný název jako skutečná tabulka, kterou představuje.
* **Virtuální tabulka** pro každý sloupec kolekce, který rozšiřuje vnořená data. Virtuální tabulky, které představují kolekce, jsou pojmenovány pomocí názvu reálné tabulky, oddělovače "*VT*" a názvu sloupce.

Virtuální tabulky odkazují na data v reálné tabulce a umožňují tak ovladači přístup k denormalizovaným datům. Podrobnosti najdete v části příklad. K obsahu kolekcí Cassandra můžete přistupovat dotazem a připojením k virtuálním tabulkám.

[Průvodce kopírováním](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) můžete použít k intuitivnímu zobrazení seznamu tabulek v databázi Cassandra, včetně virtuálních tabulek, a zobrazení náhledu dat v rámci. Dotaz můžete vytvořit také v průvodci kopírováním a ověřit tak, aby se zobrazil výsledek.

### <a name="example"></a>Příklad
Například následující "ukázková tabulka" je databázová tabulka Cassandra, která obsahuje sloupec primárního klíče s celými čísly s názvem "pk_int", textový sloupec s názvem Value, sloupec seznamu, sloupec mapy a sloupec sady (s názvem "StringSet").

| pk_int | Hodnota | Seznam | Mapa | StringSet |
| --- | --- | --- | --- | --- |
| 1 |"Ukázková hodnota 1" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"Ukázková hodnota 3" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

Ovladač by vygeneroval několik virtuálních tabulek, které reprezentují tuto jedinou tabulku. Sloupce cizího klíče ve virtuálních tabulkách odkazují na sloupce primárního klíče v reálné tabulce a označují, na který skutečný řádek tabulky odpovídá řádek virtuální tabulky.

První virtuální tabulka je základní tabulka s názvem "priklad Table" je uvedena v následující tabulce. Základní tabulka obsahuje stejná data jako původní databázová tabulka s výjimkou kolekcí, které jsou vynechány v této tabulce a rozbaleny v jiných virtuálních tabulkách.

| pk_int | Hodnota |
| --- | --- |
| 1 |"Ukázková hodnota 1" |
| 3 |"Ukázková hodnota 3" |

V následujících tabulkách jsou uvedeny virtuální tabulky, které znovu normalizují data ze sloupců seznamu, mapy a StringSet. Sloupce s názvy, které končí na "_index" nebo "_key" označují pozici dat v původním seznamu nebo mapě. Sloupce s názvy, které končí řetězcem "_value", obsahují rozšířená data z kolekce.

#### <a name="table-exampletable_vt_list"></a>Tabulka "ExampleTable_vt_List":
| pk_int | List_index | List_value |
| --- | --- | --- |
| 1 |0 |1 |
| 1 |1 |2 |
| 1 |2 |3 |
| 3 |0 |100 |
| 3 |1 |101 |
| 3 |2 |102 |
| 3 |3 |103 |

#### <a name="table-exampletable_vt_map"></a>Tabulka "ExampleTable_vt_Map":
| pk_int | Map_key | Map_value |
| --- | --- | --- |
| 1 |S1 |A |
| 1 |S2 |b |
| 3 |S1 |t |

#### <a name="table-exampletable_vt_stringset"></a>Tabulka "ExampleTable_vt_StringSet":
| pk_int | StringSet_value |
| --- | --- |
| 1 |A |
| 1 |B |
| 1 |C |
| 3 |A |
| 3 |E |

## <a name="map-source-to-sink-columns"></a>Mapovat zdroj na sloupce jímky
Další informace o mapování sloupců ve zdrojové datové sadě na sloupce v datové sadě jímky najdete v tématu [mapování sloupců datové sady v Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Opakované čtení z relačních zdrojů
Při kopírování dat z relačních úložišť dat mějte na paměti, že se vyhnete nezamýšleným výsledkům. V Azure Data Factory můžete řez znovu spustit ručně. Můžete také nakonfigurovat zásady opakování pro datovou sadu, aby se řez znovu opakoval, když dojde k selhání. Při opětovném spuštění řezu v obou případech je nutné zajistit, že stejná data budou čtena bez ohledu na to, kolikrát je řez spuštěn. Viz [opakované čtení z relačních zdrojů](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Výkon a optimalizace
Další informace o klíčových faktorech, které mají vliv na výkon přesunu dat (aktivita kopírování) v Azure Data Factory a různých způsobech jejich optimalizace, najdete v tématu [Průvodce optimalizací aktivity kopírování &](data-factory-copy-activity-performance.md) .
