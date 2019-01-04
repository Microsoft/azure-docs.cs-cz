---
title: Přesun dat z MongoDB pomocí služby Data Factory | Dokumentace Microsoftu
description: Další informace o tom, jak přesunout data z databáze MongoDB pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 10ca7d9a-7715-4446-bf59-2d2876584550
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 4059d8d2f6020a23e3593bb906c2e3fc64a4779e
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54025585"
---
# <a name="move-data-from-mongodb-using-azure-data-factory"></a>Přesun dat z MongoDB pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](data-factory-on-premises-mongodb-connector.md)
> * [Verze 2 (aktuální verze)](../connector-mongodb.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [konektoru MongoDB ve V2](../connector-mongodb.md).


Tento článek vysvětluje, jak pomocí aktivity kopírování ve službě Azure Data Factory k přesunu dat z místní databáze MongoDB. Je nástavbou [aktivity přesunu dat](data-factory-data-movement-activities.md) článek, který nabízí obecný přehled o přesun dat pomocí aktivity kopírování.

Kopírování dat z do místního úložiště dat MongoDB do jakékoli podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako jímky, najdete v článku [podporovanými úložišti dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabulky. Data factory aktuálně podporuje pouze přesouvá data z úložiště dat MongoDB do jiných úložišť dat, ale ne pro přesun dat z jiných úložišť dat do úložiště dat MongoDB. 

## <a name="prerequisites"></a>Požadavky
Pro službu Azure Data Factory bude moct připojit k vaší místní databáze MongoDB je třeba nainstalovat následující komponenty:

- Jsou podporované verze MongoDB:  2.4, 2.6, 3.0, 3.2, 3.4 a 3.6.
- Brána správy dat ve stejném počítači, který je hostitelem databáze nebo na samostatném počítači, aby soutěží o prostředky s databází. Brána správy dat je software, který se připojuje ke cloudovým službám místních zdrojů dat zabezpečení a správě způsobem. Zobrazit [brána správy dat](data-factory-data-management-gateway.md) , kde najdete podrobnosti o brána správy dat. Zobrazit [přesun dat z místních do cloudu](data-factory-move-data-between-onprem-and-cloud.md) najdete podrobné pokyny o nastavení brány datového kanálu pro přesun dat.

    Když bránu instalujete, automaticky nainstaluje ovladač Microsoft MongoDB ODBC použité pro připojení k MongoDB.

    > [!NOTE]
    > Musíte použít bránu pro připojení k MongoDB, i když je hostované ve virtuálních počítačích Azure IaaS. Pokud se pokoušíte připojit k instanci MongoDB hostované v cloudu, můžete také nainstalovat instanci brány ve virtuálním počítači IaaS.

## <a name="getting-started"></a>Začínáme
Vytvoření kanálu s aktivitou kopírování, který přesouvá data z úložiště dat místní MongoDB pomocí různých nástrojů a rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Zobrazit [kurzu: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním data.

Tyto nástroje můžete také použít k vytvoření kanálu: **Azure portal**, **sady Visual Studio**, **prostředí Azure PowerShell**, **šablony Azure Resource Manageru**, **rozhraní .NET API**a  **Rozhraní REST API**. Zobrazit [kurz aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) podrobné pokyny k vytvoření kanálu s aktivitou kopírování. 

Ať už používáte, nástrojů nebo rozhraní API, proveďte následující kroky k vytvoření kanálu pro přesouvání dat ze zdrojového úložiště dat do úložiště dat jímky: 

1. Vytvoření **propojené služby** propojení vstupní a výstupní data ukládá do služby data factory.
2. Vytvoření **datových sad** k představují vstupní a výstupní data pro operaci kopírování. 
3. Vytvoření **kanálu** s aktivitou kopírování, která přijímá jako vstupní datovou sadu a datovou sadu jako výstup. 

Při použití Průvodce definice JSON pro tyto entity služby Data Factory (propojené služby, datové sady a kanál) se automaticky vytvoří za vás. Při použití nástroje a rozhraní API (s výjimkou rozhraní .NET API), můžete definovat tyto entity služby Data Factory ve formátu JSON.  Tady je příklad s definice JSON entit služby Data Factory, které se používají ke kopírování dat z úložiště dat místní MongoDB, naleznete v tématu [příklad JSON: Kopírování dat z MongoDB do objektů Blob v Azure](#json-example-copy-data-from-mongodb-to-azure-blob) části tohoto článku. 

Následující části obsahují podrobnosti o vlastnostech JSON, které se používají k definování entit služby Data Factory konkrétní ke zdroji MongoDB:

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Následující tabulka obsahuje popis JSON elementy, které jsou specifické pro **OnPremisesMongoDB** propojenou službu.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost type musí být nastavená na: **OnPremisesMongoDb** |Ano |
| server |IP adresa nebo název hostitele serveru MongoDB. |Ano |
| port |Port TCP, který MongoDB server používá k naslouchání pro připojení klientů. |Volitelné, výchozí hodnota: 27017 |
| authenticationType. |Basic nebo Anonymous. |Ano |
| uživatelské jméno |Uživatelský účet pro přístup k MongoDB. |Ano (Pokud se používá základní ověřování). |
| heslo |Heslo pro tohoto uživatele. |Ano (Pokud se používá základní ověřování). |
| authSource |Název databáze MongoDB, kterou chcete použít ke kontrole přihlašovacích údajů pro ověřování. |Nepovinné (Pokud se používá základní ověřování). Výchozí: používá účet správce a databáze určená vlastnost databaseName. |
| databaseName |Název databáze MongoDB, které chcete získat přístup. |Ano |
| Název brány |Název brány, který přistupuje k úložišti. |Ano |
| encryptedCredential |Přihlašovací údaje zašifrované pomocí brány. |Nepovinné |

## <a name="dataset-properties"></a>Vlastnosti datové sady
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [vytváření datových sad](data-factory-create-datasets.md) článku. Oddíly, jako je například struktura, dostupnost a zásad JSON datové sady jsou podobné pro všechny datové sady typy (Azure SQL, Azure blob, tabulky Azure, atd.).

**TypeProperties** oddílu se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti. TypeProperties části datové sady typu **MongoDbCollection** má následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| collectionName |Název kolekce v databázi MongoDB. |Ano |

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [vytváření kanálů](data-factory-create-pipelines.md) článku. Vlastnosti, jako je název, popis, vstupní a výstupní tabulky a zásady jsou k dispozici pro všechny typy aktivit.

K dispozici ve vlastnosti **typeProperties** části aktivity se liší na druhé straně s jednotlivými typu aktivity. Pro aktivitu kopírování se liší v závislosti na typy zdroje a jímky.

Pokud je zdroj typu **MongoDbSource** v části typeProperties jsou k dispozici následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query |Použijte vlastní dotaz číst data. |Řetězec dotazu SQL 92. Příklad: vybrat * z MyTable. |Ne (Pokud **collectionName** z **datovou sadu** určena) |



## <a name="json-example-copy-data-from-mongodb-to-azure-blob"></a>Příklad JSON: Kopírování dat z MongoDB do objektů Blob v Azure
V tomto příkladu obsahuje ukázky JSON definice, které můžete použít k vytvoření kanálu pomocí [webu Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) nebo [sady Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [prostředí Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ukazuje, jak kopírovat data z MongoDB s místními ke službě Azure Blob Storage. Ale data je možné zkopírovat do libovolné jímky uvedeno [tady](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivit kopírování ve službě Azure Data Factory.

Ukázka obsahuje následující entit datové továrny:

1. Propojené služby typu [OnPremisesMongoDb](#linked-service-properties).
2. Propojené služby typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní hodnota [datovou sadu](data-factory-create-datasets.md) typu [MongoDbCollection](#dataset-properties).
4. Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [MongoDbSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázce kopíruje data z výsledků dotazu v databázi MongoDB do objektu blob každou hodinu. Vlastnostech JSON použitých v tyto ukázky jsou popsány v části podle ukázky.

Jako první krok instalace brány správy dat podle pokynů [brána správy dat](data-factory-data-management-gateway.md) článku.

**MongoDB propojené služby:**

```json
{
    "name": "OnPremisesMongoDbLinkedService",
    "properties":
    {
        "type": "OnPremisesMongoDb",
        "typeProperties":
        {
            "authenticationType": "<Basic or Anonymous>",
            "server": "< The IP address or host name of the MongoDB server >",  
            "port": "<The number of the TCP port that the MongoDB server uses to listen for client connections.>",
            "username": "<username>",
            "password": "<password>",
           "authSource": "< The database that you want to use to check your credentials for authentication. >",
            "databaseName": "<database name>",
            "gatewayName": "<mygateway>"
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

**MongoDB vstupní datové sady:** Nastavení "externí": "PRAVDA" informuje služby Data Factory, že v tabulce je externí do služby data factory a není vytvořen aktivitou ve službě data factory.

```json
{
     "name":  "MongoDbInputDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": "OnPremisesMongoDbLinkedService",
        "typeProperties": {
            "collectionName": "<Collection name>"    
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

**Výstupní datová sada Azure Blob:**

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce pro objekt blob se dynamicky vyhodnocuje na základě doby spuštění řez, který se právě zpracovává. Cesta ke složce používá rok, měsíc, den a části hodin čas spuštění.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/frommongodb/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Aktivita kopírování v kanálu s MongoDB zdroje a jímky objektu Blob:**

Kanálu obsahujícího aktivitu kopírování, který je nakonfigurovaný pomocí výše uvedených vstupní a výstupní datové sady a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **MongoDbSource** a **jímky** je typ nastaven na **BlobSink**. Zadaná pro dotaz SQL **dotazu** vlastnost vybere data za poslední hodinu pro kopírování.

```json
{
    "name": "CopyMongoDBToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "MongoDbSource",
                        "query": "$$Text.Format('select * from  MyTable where LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "MongoDbInputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutputDataSet"
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
                "name": "MongoDBToAzureBlob"
            }
        ],
        "start": "2016-06-01T18:00:00Z",
        "end": "2016-06-01T19:00:00Z"
    }
}
```


## <a name="schema-by-data-factory"></a>Schéma službou Data Factory
Služba Azure Data Factory odvodí schéma z kolekce MongoDB pomocí nejnovějších 100 dokumentů v kolekci. Pokud tyto dokumenty 100 neobsahují úplného schématu, mohou být některé sloupce ignorovány během operace kopírování.

## <a name="type-mapping-for-mongodb"></a>Mapování typu pro MongoDB
Jak je uvedeno v [aktivity přesunu dat](data-factory-data-movement-activities.md) článku, aktivita kopírování provádí automatické typ převody z typů zdroje do jímky typy s přístupem následující krok 2:

1. Převést na typ formátu .NET typy nativních zdrojů
2. Převést z typu .NET native jímky typu

Při přesouvání dat až po MongoDB se používají následující mapování z typů MongoDB na typy .NET.

| Typ MongoDB | Typ rozhraní .NET framework |
| --- | --- |
| Binární hodnota |Byte] |
| Logická hodnota |Logická hodnota |
| Datum |DateTime |
| NumberDouble |Double |
| NumberInt |Datový typ Int32 |
| NumberLong |Int64 |
| ObjectID |Řetězec |
| Řetězec |Řetězec |
| IDENTIFIKÁTOR UUID |Guid |
| Objekt |Renormalized do sloučit sloupce s "_" jako vnořené oddělovač |

> [!NOTE]
> Další informace o podpoře pro pole pomocí virtuální tabulky, najdete v tématu [podporu pro komplexní typy s použitím virtuální tabulky](#support-for-complex-types-using-virtual-tables) níže v části.

V současné době nejsou podporovány následující typy dat MongoDB: Klíč DBPointer, JavaScript, Max nebo Min regulárního výrazu, symbolů, časové razítko, Nedefinováno

## <a name="support-for-complex-types-using-virtual-tables"></a>Podpora pro komplexní typy pomocí virtuální tabulky
Azure Data Factory využívá integrované ovladače ODBC pro připojení k a kopírování dat z databáze MongoDB. U komplexních typů jako je například pole nebo objekty s různými typy mezi dokumenty ovladač znovu sjednotí data na odpovídající virtuální tabulky. Konkrétně Pokud tabulka obsahuje tyto sloupce, ovladač generuje následující virtuální tabulky:

* A **základní tabulka**, která obsahuje stejná data jako skutečné tabulky s výjimkou sloupců komplexního typu. Základní tabulka používá stejný název jako skutečné tabulky, který představuje.
* A **virtuální tabulky** pro každý sloupec komplexní typ, který rozbalí vnořené data. Virtuální tabulky jsou pojmenovány pomocí názvu tabulky skutečných, oddělovač "_" a název pole nebo objekt.

Virtuální tabulky odkazují na data v tabulce skutečné, povolení ovladače pro přístup k Nenormalizovaná data. Příklad v části Podrobnosti uvedené níže. Obsah pole MongoDB můžou používat tak dotazování a připojení k virtuální tabulky.

Můžete použít [Průvodce kopírováním](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) intuitivně zobrazit seznam tabulek v databázi MongoDB, včetně virtuální tabulky a zobrazte náhled dat uvnitř. Můžete také vytvořit dotaz v Průvodci kopírováním a ověření k zobrazení výsledku.

### <a name="example"></a>Příklad:
Například "ExampleTable" níže je tabulka MongoDB, která má jeden sloupec s polem objektů v každé buňce – faktury a jeden sloupec s polem skalárních typů – hodnocení.

| ID _ovládacího | Jméno zákazníka | Faktury | Úroveň služby | Hodnocení |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id: "123", položka: "toaster"; price: "456", slevy: "0.2"}, {invoice_id: "124", položka: "sušárně"; price: slevy "1235": "0.2"}] |Stříbrný |[5,6] |
| 2222 |XYZ |[{invoice_id: položky "135": "ledničky"; price: "12543", slevy: "0,0"}] |Zlatá |[1,2] |

Ovladač vygeneruje více virtuální tabulky k reprezentaci této jediné tabulce. První virtuální tabulky je základní tabulku s názvem "ExampleTable" je uvedeno níže. Základní tabulka obsahuje všechna data v původní tabulce, ale data z pole byl vynechán a rozbalen virtuální tabulky.

| ID _ovládacího | Jméno zákazníka | Úroveň služby |
| --- | --- | --- |
| 1111 |ABC |Stříbrný |
| 2222 |XYZ |Zlatá |

Následující tabulky popisují virtuální tabulky, které představují původní pole v příkladu. Tyto tabulky obsahují následující:

* Představuje odkaz zpět na původní sloupec primárního klíče odpovídající řádek původní pole (přes sloupec ID _ovládacího)
* Údaj o umístění dat v rámci původní pole
* Rozšířená data pro každý prvek v poli

Tabulka "ExampleTable_Invoices":

| ID _ovládacího | ExampleTable_Invoices_dim1_idx | invoice_id | Položka | price | Sleva |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |Toaster |456 |0.2 |
| 1111 |1 |124 |sušárně |1235 |0.2 |
| 2222 |0 |135 |ledničky |12543 |0.0 |

Tabulka "ExampleTable_Ratings":

| ID _ovládacího | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

## <a name="map-source-to-sink-columns"></a>Mapování zdroje do jímky sloupce
Další informace o mapování sloupců v datové sadě zdroje do sloupců v datové sadě jímky, najdete v článku [mapování sloupců v datové sadě ve službě Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Opakovatelné čtení z relačních zdrojů
Při kopírování dat z relačních dat ukládá, mějte opakovatelnosti aby se zabránilo neúmyslnému výsledků. Ve službě Azure Data Factory můžete znovu spustit řezu ručně. Zásady opakování pro datovou sadu můžete také nakonfigurovat tak, aby určitý řez se znovu spustí, když dojde k chybě. V obou případech se znovu spustí určitý řez, musíte zajistit, že stejná data je pro čtení bez ohledu na to kolikrát spustit určitý řez. Zobrazit [Repeatable z relačních zdrojů](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Výkon a optimalizace
Zobrazit [výkonem aktivity kopírování & Průvodci optimalizací](data-factory-copy-activity-performance.md) Další informace o klíčových faktorů této ovlivnit výkon přesouvání dat (aktivita kopírování) ve službě Azure Data Factory a různé způsoby, jak optimalizovat.

## <a name="next-steps"></a>Další kroky
Zobrazit [přesun dat mezi místním prostředím a cloudem](data-factory-move-data-between-onprem-and-cloud.md) najdete podrobné pokyny k vytvoření datového kanálu, který přesouvá data z úložiště dat v místním úložišti dat Azure.
