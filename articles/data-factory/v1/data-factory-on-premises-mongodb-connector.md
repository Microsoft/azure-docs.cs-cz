---
title: Přesunutí dat z MongoDB
description: Přečtěte si, jak přesunout data z databáze MongoDB pomocí Azure Data Factory.
services: data-factory
author: linda33wj
ms.author: jingwang
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/13/2018
ms.openlocfilehash: edddd100bddab1d642a8169353298a2d20620274
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281337"
---
# <a name="move-data-from-mongodb-using-azure-data-factory"></a>Přesunutí dat z MongoDB pomocí Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](data-factory-on-premises-mongodb-connector.md)
> * [Verze 2 (aktuální verze)](../connector-mongodb.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si [téma Konektor MongoDB ve verzi 2](../connector-mongodb.md).


Tento článek vysvětluje, jak použít aktivitu kopírování v Azure Data Factory k přesunutí dat z místní databáze MongoDB. Vychází z článku [Aktivity přesunu dat,](data-factory-data-movement-activities.md) který představuje obecný přehled přesunu dat s aktivitou kopírování.

Data z místního úložiště dat MongoDB můžete zkopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako jímky naleznete v tabulce [Podporovaná úložiště dat.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Data Factory aktuálně podporuje pouze přesunutí dat z úložiště dat MongoDB do jiných úložišť dat, ale ne pro přesun dat z jiných úložišť dat do úložiště dat MongoDB.

## <a name="prerequisites"></a>Požadavky
Aby se služba Azure Data Factory mohla připojit k místní databázi MongoDB, musíte nainstalovat následující součásti:

- Podporované verze MongoDB jsou: 2.4, 2.6, 3.0, 3.2, 3.4 a 3.6.
- Brána pro správu dat na stejném počítači, který je hostitelem databáze nebo v samostatném počítači, aby se zabránilo konkurenci o prostředky s databází. Brána pro správu dat je software, který zabezpečeně a spravovaným způsobem propojuje místní zdroje dat s cloudovými službami. Podrobnosti o bráně pro správu dat najdete v článku [Brána pro správu](data-factory-data-management-gateway.md) dat. Podrobný návod k nastavení datového kanálu brány pro přesun dat najdete v článku [Přesun dat z místního](data-factory-move-data-between-onprem-and-cloud.md) do cloudu.

    Při instalaci brány automaticky nainstaluje ovladač Microsoft MongoDB ODBC, který slouží k připojení k MongoDB.

    > [!NOTE]
    > Musíte použít bránu pro připojení k MongoDB i v případě, že je hostované v Azure IaaS virtuálních počítačích. Pokud se pokoušíte připojit k instanci MongoDB hostované v cloudu, můžete také nainstalovat instanci brány ve virtuálním počítači IaaS.

## <a name="getting-started"></a>Začínáme
Můžete vytvořit kanál s aktivitou kopírování, která přesouvá data z místního úložiště dat MongoDB pomocí různých nástrojů nebo api.

Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Viz [Kurz: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) pro rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním dat.

K vytvoření kanálu můžete taky použít následující nástroje: **Visual Studio**, **Azure PowerShell**, **Šablona Azure Resource Manager**, Rozhraní **.NET API**a REST **API**. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v tématu [Kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aktivity.

Bez ohledu na to, zda používáte nástroje nebo api, provedete následující kroky k vytvoření kanálu, který přesune data ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvořte **propojené služby** pro propojení vstupních a výstupních úložišť dat s vaší továrně dat.
2. Vytvořte **datové sady** představující vstupní a výstupní data pro operaci kopírování.
3. Vytvořte **kanál** s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup.

Při použití průvodce jsou automaticky vytvořeny definice JSON pro tyto entity Data Factory (propojené služby, datové sady a kanál). Při použití nástrojů nebo rozhraní API (s výjimkou rozhraní .NET API) definujete tyto entity Data Factory pomocí formátu JSON.  Ukázka s definicemi JSON pro entity Data Factory, které se používají ke kopírování dat z místního úložiště dat MongoDB, najdete v [tématu JSON příklad: Kopírování dat z MongoDB do Azure Blob](#json-example-copy-data-from-mongodb-to-azure-blob) části tohoto článku.

V následujících částech jsou uvedeny podrobnosti o vlastnostech JSON, které se používají k definování entit Factory dat specifických pro zdroj MongoDB:

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb
Následující tabulka obsahuje popis prvků JSON specifických pro **propojenou službu OnPremisesMongoDB.**

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost type musí být nastavena **na: OnPremisesMongoDb.** |Ano |
| server |IP adresa nebo název hostitele serveru MongoDB. |Ano |
| port |Tcp port, který server MongoDB používá k naslouchání pro připojení klientů. |Volitelná, výchozí hodnota: 27017 |
| authenticationType |Základní, nebo Anonymní. |Ano |
| uživatelské jméno |Uživatelský účet pro přístup k MongoDB. |Ano (pokud se používá základní ověřování). |
| heslo |Heslo pro tohoto uživatele. |Ano (pokud se používá základní ověřování). |
| authSource |Název databáze MongoDB, kterou chcete použít ke kontrole přihlašovacích údajů pro ověřování. |Volitelné (pokud se používá základní ověřování). výchozí: Používá účet správce a databázi určenou pomocí vlastnosti databaseName. |
| Databasename |Název databáze MongoDB, ke které chcete získat přístup. |Ano |
| název brány |Název brány, která přistupuje k úložišti dat. |Ano |
| šifrované pověření |Pověření zašifrované bránou. |Nepovinné |

## <a name="dataset-properties"></a>Vlastnosti datové sady
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování datových sad, naleznete v článku [Vytváření datových sad.](data-factory-create-datasets.md) Oddíly, jako je struktura, dostupnost a zásady datové sady JSON, jsou podobné pro všechny typy datových sad (Azure SQL, Azure blob, Tabulka Azure atd.).

Sekce **typeProperties** se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti dat. Oddíl typeProperties pro datovou sadu typu **MongoDbCollection** má následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| název kolekce |Název kolekce v databázi MongoDB. |Ano |

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivit, naleznete v článku [Vytváření kanálů.](data-factory-create-pipelines.md) Vlastnosti, jako je název, popis, vstupní a výstupní tabulky a zásady jsou k dispozici pro všechny typy aktivit.

Vlastnosti dostupné v části **typeProperties** aktivity na druhé straně se liší podle jednotlivých typů aktivit. U aktivity kopírování se liší v závislosti na typech zdrojů a propadů.

Pokud je zdroj typu **MongoDbSource,** jsou v části typeProperties k dispozici následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query |Ke čtení dat použijte vlastní dotaz. |Řetězec dotazu SQL-92. Příklad: vyberte * z MyTable. |Ne (pokud je zadán **název kolekce** **datové sady)** |



## <a name="json-example-copy-data-from-mongodb-to-azure-blob"></a>Příklad JSON: Kopírování dat z MongoDB do objektu Blob Azure
Tento příklad obsahuje ukázkové definice JSON, které můžete použít k vytvoření kanálu pomocí [Sady Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo Azure [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ukazuje, jak zkopírovat data z místního MongoDB do úložiště objektů blob Azure. Data však můžete zkopírovat do libovolného jímky [uvedené zde](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování v Azure Data Factory.

Ukázka má následující entity datové továrny:

1. Propojená služba typu [OnPremisesMongoDb](#linked-service-properties).
2. Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní [datová sada](data-factory-create-datasets.md) typu [MongoDbCollection](#dataset-properties).
4. Výstupní [datová sada](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [MongoDbSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka zkopíruje data z výsledku dotazu v databázi MongoDB do objektu blob každou hodinu. Vlastnosti JSON použité v těchto vzorcích jsou popsány v následujících částech.

Jako první krok nastavte bránu pro správu dat podle pokynů v článku [Brána pro správu dat.](data-factory-data-management-gateway.md)

**MongoDB propojená služba:**

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

**Propojené služby Azure Storage:**

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

**Vstupní datová sada MongoDB:** Nastavení "externí": "true" informuje službu Data Factory, že tabulka je externí pro datovou továrnu a není vyráběna aktivitou v datové továrně.

```json
{
    "name": "MongoDbInputDataset",
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

**Výstupní datová sada objektu Blob Azure:**

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce pro objekt blob je dynamicky vyhodnocována na základě počátečního času zpracovávaného řezu. Cesta ke složce používá části počátečního času rok, měsíc, den a hodiny.

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

**Zkopírujte aktivitu v kanálu se zdrojem MongoDB a jímkou blob:**

Kanál obsahuje aktivitu kopírování, která je nakonfigurována pro použití výše uvedených vstupních a výstupních datových sad a je naplánována na každou hodinu. V definici kanálu JSON je **typ zdroje** nastaven na **MongoDbSource** a typ **jímky** je nastaven na **Objekt blobSink**. Dotaz SQL zadaný pro vlastnost **dotazu** vybere data za poslední hodinu ke kopírování.

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
                        "query": "$$Text.Format('select * from MyTable where LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)"
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


## <a name="schema-by-data-factory"></a>Schéma podle datové továrny
Služba Azure Data Factory odvodí schéma z kolekce MongoDB pomocí nejnovějších 100 dokumentů v kolekci. Pokud těchto 100 dokumentů neobsahuje úplné schéma, některé sloupce mohou být během operace kopírování ignorovány.

## <a name="type-mapping-for-mongodb"></a>Mapování typů pro MongoDB
Jak je uvedeno v článku [aktivity přesunu dat,](data-factory-data-movement-activities.md) copy aktivita provádí převody automatického typu z typů zdrojů na typy jímek s následujícím přístupem 2 kroky:

1. Převod z nativních typů zdrojů na typ .NET
2. Převod z typu .NET na nativní typ jímky

Při přesouvání dat do MongoDB se používají následující mapování z typů MongoDB na typy .NET.

| Typ MongoDB | Typ rozhraní .NET Framework |
| --- | --- |
| binární |Bajt[] |
| Logická hodnota |Logická hodnota |
| Datum |DateTime |
| Číslo Double |Double |
| NumberInt |Int32 |
| Početdlouhé |Int64 |
| ObjectId |Řetězec |
| Řetězec |Řetězec |
| Uuid |Identifikátor GUID |
| Objekt |Znovu normalizováno do narovnat sloupce s "_" jako vnořeným oddělovačem |

> [!NOTE]
> Další informace o podpoře polí používajících virtuální tabulky najdete [v části Podpora pro složité typy pomocí](#support-for-complex-types-using-virtual-tables) části virtuální tabulky níže.

V současné době nejsou podporovány následující datové typy MongoDB: DBPointer, JavaScript, Max/Min klíč, regulární výraz, symbol, časové razítko, nedefinované

## <a name="support-for-complex-types-using-virtual-tables"></a>Podpora složitých typů pomocí virtuálních tabulek
Azure Data Factory používá integrovaný ovladač ODBC pro připojení a kopírování dat z databáze MongoDB. U složitých typů, jako jsou pole nebo objekty s různými typy v dokumentech, ovladač znovu normalizuje data do odpovídajících virtuálních tabulek. Konkrétně pokud tabulka obsahuje takové sloupce, ovladač generuje následující virtuální tabulky:

* **Základní tabulka**, která obsahuje stejná data jako skutečná tabulka s výjimkou sloupců komplexního typu. Základní tabulka používá stejný název jako skutečná tabulka, kterou představuje.
* **Virtuální tabulka** pro každý sloupec komplexního typu, která rozbalí vnořená data. Virtuální tabulky jsou pojmenovány pomocí názvu skutečné tabulky, oddělovače "_" a názvu pole nebo objektu.

Virtuální tabulky odkazují na data v reálné tabulce, což umožňuje ovladači přístup k nenormalizovaným datům. Podrobnosti naleznete v části Příklad níže. K obsahu polí MongoDB můžete přistupovat dotazováním a připojením k virtuálním tabulkám.

Pomocí Průvodce [kopírováním](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) můžete intuitivně zobrazit seznam tabulek v databázi MongoDB včetně virtuálních tabulek a zobrazit náhled dat uvnitř. Můžete také vytvořit dotaz v Průvodci kopírováním a ověřit, chcete-li zobrazit výsledek.

### <a name="example"></a>Příklad
Například "ExampleTable" níže je tabulka MongoDB, která má jeden sloupec s polem objektů v každé buňce – Faktury a jeden sloupec s polem skalárních typů – Hodnocení.

| _id | Jméno zákazníka | Faktury | Úroveň služeb | Ratings |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id:"123", položka:"toustovač", cena:"456", sleva:"0.2"}, {invoice_id:"124", položka:"trouba", cena: "1235", sleva: "0.2"}] |Silver |[5,6] |
| 2222 |XYZ |[{invoice_id:"135", položka:"lednice", cena: "12543", sleva: "0.0"}] |Gold |[1,2] |

Ovladač by generovat více virtuálních tabulek představují tuto jednu tabulku. První virtuální tabulka je základní tabulka s názvem "ExampleTable", která je uvedena níže. Základní tabulka obsahuje všechna data původní tabulky, ale data z polí byla vynechána a jsou rozbalena ve virtuálních tabulkách.

| _id | Jméno zákazníka | Úroveň služeb |
| --- | --- | --- |
| 1111 |ABC |Silver |
| 2222 |XYZ |Gold |

V následujících tabulkách jsou zobrazeny virtuální tabulky, které představují původní pole v příkladu. Tyto tabulky obsahují následující:

* Odkaz zpět na původní sloupec primárního klíče odpovídající řádku původního pole (přes sloupec _id)
* Označení polohy dat v původním poli
* Rozšířená data pro každý prvek v rámci pole

Tabulka "ExampleTable_Invoices":

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | položka | price | Discount |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |Topinkovač |456 |0.2 |
| 1111 |1 |124 |Trouba |1235 |0.2 |
| 2222 |0 |135 |Lednice |12543 |0,0 |

Tabulka "ExampleTable_Ratings":

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

## <a name="map-source-to-sink-columns"></a>Mapovat zdroj pro jímací sloupce
Další informace o mapování sloupců ve zdrojové datové sadě na sloupce v datové sadě jímky najdete [v tématu Mapování sloupců datových sad v Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Opakovatelné čtení ze relačních zdrojů
Při kopírování dat z úložišť relačních dat mějte na paměti opakovatelnost, abyste se vyhnuli nezamýšleným výsledkům. V Azure Data Factory můžete znovu spustit řez ručně. Můžete také nakonfigurovat zásady opakování pro datovou sadu tak, aby řez je znovu spustit, když dojde k selhání. Při opětovném spuštění řezu v obou směrech je třeba se ujistit, že stejná data jsou čtena bez ohledu na to, kolikrát je řez spuštěn. Viz [Opakovatelné čtení z relačních zdrojů](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Výkon a ladění
[V tématu Průvodce sledováním výkonu & optimalizací se](data-factory-copy-activity-performance.md) dozvíte o klíčových faktorech, které ovlivňují výkon přesunu dat (aktivita kopírování) ve Službě Azure Data Factory, a o různých způsobech jeho optimalizace.

## <a name="next-steps"></a>Další kroky
Podrobný článek pro vytvoření datového kanálu, který přesouvá data z místního úložiště dat do úložiště dat Azure, najdete v článku [Přesun dat mezi místním a cloudovým](data-factory-move-data-between-onprem-and-cloud.md) článkem.
