---
title: Přesunout data z MongoDB
description: Přečtěte si informace o tom, jak přesouvat data z databáze MongoDB pomocí Azure Data Factory.
services: data-factory
author: linda33wj
ms.author: jingwang
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/13/2018
ms.openlocfilehash: edddd100bddab1d642a8169353298a2d20620274
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "79281337"
---
# <a name="move-data-from-mongodb-using-azure-data-factory"></a>Přesun dat z MongoDB pomocí Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](data-factory-on-premises-mongodb-connector.md)
> * [Verze 2 (aktuální verze)](../connector-mongodb.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [konektor MongoDB v v2](../connector-mongodb.md).


Tento článek vysvětluje, jak pomocí aktivity kopírování v Azure Data Factory přesouvat data z místní databáze MongoDB. Sestavuje se podle článku [aktivity přesunu dat](data-factory-data-movement-activities.md) , který prezentuje obecný přehled přesunu dat s aktivitou kopírování.

Data z místního úložiště dat MongoDB můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, která aktivita kopírování podporuje jako jímky, najdete v tabulce [podporovaná úložiště dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Data Factory aktuálně podporuje jenom přesun dat z úložiště MongoDB data do jiných úložišť dat, ale ne pro přesun dat z jiných úložišť dat do úložiště MongoDB.

## <a name="prerequisites"></a>Požadavky
Aby se služba Azure Data Factory mohla připojit k místní databázi MongoDB, musíte nainstalovat tyto komponenty:

- Podporované verze MongoDB jsou: 2,4, 2,6, 3,0, 3,2, 3,4 a 3,6.
- Správa dat bránu na stejném počítači, který je hostitelem databáze nebo na samostatném počítači, abyste se vyhnuli konkurenčním prostředkům s databází. Správa dat Gateway je software, který se připojuje k místním zdrojům dat ke cloudovým službám zabezpečeným a spravovaným způsobem. Podrobnosti o Správa dat bráně najdete v článku o [Správa dat brány](data-factory-data-management-gateway.md) . Podrobné pokyny týkající se nastavení brány a datového kanálu pro přesun dat najdete v článku [o přesunu dat z místního prostředí do cloudu](data-factory-move-data-between-onprem-and-cloud.md) .

    Při instalaci brány se automaticky nainstaluje ovladač Microsoft MongoDB ODBC, který se používá pro připojení k MongoDB.

    > [!NOTE]
    > Bránu musíte použít pro připojení k MongoDB i v případě, že je hostovaná ve virtuálních počítačích Azure s IaaS. Pokud se pokoušíte připojit k instanci MongoDB hostované v cloudu, můžete také nainstalovat instanci brány do virtuálního počítače IaaS.

## <a name="getting-started"></a>Začínáme
Můžete vytvořit kanál s aktivitou kopírování, která přesouvá data z místního úložiště dat MongoDB pomocí různých nástrojů nebo rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním dat najdete v tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) .

K vytvoření kanálu můžete také použít následující nástroje: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager template**, **.NET API**a **REST API**. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v [kurzu kopírování aktivit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Bez ohledu na to, jestli používáte nástroje nebo rozhraní API, provedete následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvořte **propojené služby** , které propojí vstupní a výstupní úložiště dat s datovou továrnou.
2. Vytvořte datové **sady** , které reprezentují vstupní a výstupní data pro operaci kopírování.
3. Vytvořte **kanál** s aktivitou kopírování, která převezme datovou sadu jako vstup a datovou sadu jako výstup.

Při použití Průvodce se automaticky vytvoří definice JSON pro tyto Entity Data Factory (propojené služby, datové sady a kanál). Pokud používáte nástroje/rozhraní API (s výjimkou rozhraní .NET API), definujete tyto Data Factory entit pomocí formátu JSON.  Ukázku s definicemi JSON pro Entity Data Factory, které se používají ke kopírování dat z místního úložiště dat MongoDB, najdete v části [JSON example: kopírování dat z MongoDB do objektu blob Azure](#json-example-copy-data-from-mongodb-to-azure-blob) v tomto článku.

Následující části obsahují podrobné informace o vlastnostech JSON, které se používají k definování Data Factory entit specifických pro zdroj MongoDB:

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Následující tabulka uvádí popis pro prvky JSON specifické pro propojenou službu **OnPremisesMongoDB** .

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| typ |Vlastnost Type musí být nastavená na: **OnPremisesMongoDb** . |Ano |
| server |IP adresa nebo název hostitele serveru MongoDB |Ano |
| port |Port TCP, který server MongoDB používá k naslouchání klientským připojením. |Volitelná výchozí hodnota: 27017 |
| authenticationType |Basic nebo Anonymous. |Ano |
| username |Uživatelský účet pro přístup k MongoDB. |Ano (Pokud se používá základní ověřování). |
| heslo |Heslo pro tohoto uživatele. |Ano (Pokud se používá základní ověřování). |
| authSource |Název databáze MongoDB, kterou chcete použít ke kontrole vašich přihlašovacích údajů pro ověřování. |Volitelné (Pokud se používá základní ověřování). výchozí: používá účet správce a databázi určenou pomocí vlastnosti databaseName. |
| Databáze |Název databáze MongoDB, ke které chcete získat přístup. |Ano |
| gatewayName |Název brány, která přistupuje k úložišti dat. |Ano |
| encryptedCredential |Přihlašovací údaje zašifrované bránou |Volitelné |

## <a name="dataset-properties"></a>Vlastnosti datové sady
Úplný seznam sekcí & vlastností dostupných pro definování datových sad naleznete v článku [vytvoření datových sad](data-factory-create-datasets.md) . Oddíly, jako je například struktura, dostupnost a zásada pro datovou sadu JSON, jsou podobné pro všechny typy datových sad (Azure SQL, Azure Blob, tabulka Azure atd.).

Oddíl **typeProperties** se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti dat. Oddíl typeProperties pro sadu dat typu **MongoDbCollection** má následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| collectionName |Název kolekce v databázi MongoDB |Ano |

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam sekcí & vlastností dostupných pro definování aktivit najdete v článku [vytvoření kanálů](data-factory-create-pipelines.md) . Pro všechny typy aktivit jsou k dispozici vlastnosti, jako je název, popis, vstupní a výstupní tabulka a zásada.

Vlastnosti, které jsou k dispozici v části **typeProperties** aktivity, se liší v závislosti na jednotlivých typech aktivit. U aktivity kopírování se liší v závislosti na typech zdrojů a jímky.

Pokud je zdrojem typ **MongoDbSource** , jsou v oddílu typeProperties k dispozici následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query |Pomocí vlastního dotazu můžete číst data. |Řetězec dotazu SQL-92 Příklad: SELECT * FROM MyTable. |Ne (Pokud je zadán parametr **CollectionName** pro **sadu dat** ) |



## <a name="json-example-copy-data-from-mongodb-to-azure-blob"></a>Příklad JSON: kopírování dat z MongoDB do Azure Blob
Tento příklad poskytuje ukázkové definice JSON, které můžete použít k vytvoření kanálu pomocí sady [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ukazuje, jak kopírovat data z místního MongoDB do Azure Blob Storage. Data však lze zkopírovat do kterékoli z těchto umyvadel, které jsou [zde](data-factory-data-movement-activities.md#supported-data-stores-and-formats) uvedeny, pomocí aktivity kopírování v Azure Data Factory.

Ukázka má následující Entity Data Factory:

1. Propojená služba typu [OnPremisesMongoDb](#linked-service-properties).
2. Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní [datová sada](data-factory-create-datasets.md) typu [MongoDbCollection](#dataset-properties).
4. Výstupní [datová sada](data-factory-create-datasets.md) typu [azureblobu](data-factory-azure-blob-connector.md#dataset-properties).
5. [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [MongoDbSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka kopíruje data z výsledků dotazu v MongoDB databázi do objektu BLOB každou hodinu. Vlastnosti JSON použité v těchto ukázkách jsou popsány v oddílech následujících po ukázkách.

Jako první krok nastavte bránu pro správu dat podle pokynů v článku [Správa dat brány](data-factory-data-management-gateway.md) .

**Propojená služba MongoDB:**

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

**Vstupní datová sada MongoDB:** Nastavení "externí": "true" informuje službu Data Factory o tom, že je tabulka externí pro objekt pro vytváření dat, a není vytvořena aktivitou v datové továrně.

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

**Výstupní datová sada Azure Blob:**

Data se zapisují do nového objektu BLOB každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce pro objekt BLOB je dynamicky vyhodnocována na základě počátečního času zpracovávaného řezu. Cesta ke složce používá části rok, měsíc, den a hodiny v počátečním čase.

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

**Aktivita kopírování v kanálu se zdrojem MongoDB a jímky objektů BLOB:**

Kanál obsahuje aktivitu kopírování, která je nakonfigurovaná tak, aby používala výše uvedenou vstupní a výstupní datovou sadu a je naplánované spuštění každou hodinu. V definici JSON kanálu je typ **zdroje** nastavený na **MongoDbSource** a typ **jímky** je nastavený na **BlobSink**. Dotaz SQL zadaný pro vlastnost **dotazu** vybere data během uplynulé hodiny ke zkopírování.

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


## <a name="schema-by-data-factory"></a>Schéma podle Data Factory
Služba Azure Data Factory odvodí schéma z kolekce MongoDB pomocí nejnovějších 100 dokumentů v kolekci. Pokud tyto dokumenty 100 neobsahují úplné schéma, mohou být některé sloupce během operace kopírování ignorovány.

## <a name="type-mapping-for-mongodb"></a>Mapování typů pro MongoDB
Jak je uvedeno v článku [aktivity přesunu dat](data-factory-data-movement-activities.md) , aktivita kopírování provádí automatické převody typů ze zdrojových typů do typů jímky s následujícím přístupem ke dvěma krokům:

1. Převod z nativních zdrojových typů na typ .NET
2. Převést z typu .NET na nativní typ jímky

Při přesunu dat na MongoDB se z typů MongoDB na typy .NET používají následující mapování.

| Typ MongoDB | Typ rozhraní .NET Framework |
| --- | --- |
| Binární |Byte [] |
| Logická hodnota |Logická hodnota |
| Datum |DateTime |
| NumberDouble |dvojité |
| NumberInt |Int32 |
| NumberLong |Int64 |
| ObjectId |Řetězec |
| Řetězec |Řetězec |
| IDENTIFIKÁTOR |Identifikátor GUID |
| Objekt |Znovu normalizovat do sloučených sloupců pomocí _ jako vnořeného oddělovače |

> [!NOTE]
> Další informace o podpoře pro pole pomocí virtuálních tabulek najdete níže v části [Podpora komplexních typů pomocí virtuálních tabulek](#support-for-complex-types-using-virtual-tables) .

V současné době nejsou podporovány následující datové typy MongoDB: DBPointer, JavaScript, Max/min Key, regulární výraz, symbol, časové razítko, Nedefinováno.

## <a name="support-for-complex-types-using-virtual-tables"></a>Podpora komplexních typů pomocí virtuálních tabulek
Azure Data Factory používá integrovaný ovladač ODBC pro připojení a zkopírování dat z databáze MongoDB. U komplexních typů, jako jsou pole nebo objekty s různými typy v rámci dokumentů, ovladač znovu normalizuje data do odpovídajících virtuálních tabulek. Konkrétně, pokud tabulka obsahuje takové sloupce, vygeneruje ovladač následující virtuální tabulky:

* **Základní tabulka**, která obsahuje stejná data jako skutečná tabulka s výjimkou sloupců se složitým typem. Základní tabulka používá stejný název jako skutečná tabulka, kterou představuje.
* **Virtuální tabulka** pro každý sloupec komplexního typu, který rozšiřuje vnořená data. Virtuální tabulky jsou pojmenovány pomocí názvu reálné tabulky, oddělovače "_" a názvu pole nebo objektu.

Virtuální tabulky odkazují na data v reálné tabulce a umožňují tak ovladači přístup k denormalizovaným datům. Viz část příklad podrobnosti. K obsahu polí MongoDB můžete přistupovat dotazem a připojením k virtuálním tabulkám.

[Průvodce kopírováním](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) můžete použít k intuitivnímu zobrazení seznamu tabulek v databázi MongoDB, včetně virtuálních tabulek, a zobrazení náhledu dat v rámci. Dotaz můžete vytvořit také v průvodci kopírováním a ověřit tak, aby se zobrazil výsledek.

### <a name="example"></a>Příklad
Například "priklad Table" níže je tabulka MongoDB, která má jeden sloupec s polem objektů v každé buňce – faktury a jeden sloupec s polem skalárních typů – hodnocení.

| _id | Název zákazníka | Faktury | Úroveň služeb | Ratings |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id: "123", Item: "informační zpráva", Cena: "456", sleva: "0,2"}, {invoice_id: "124", položka: "sušárna", Cena: "1235", sleva: "0,2"}] |Silver |[5, 6] |
| 2222 |XYZ |[{invoice_id: "135"; Item: "nákupem ledničky"; Price: "12543"; Discount: "0,0"}] |Gold |[1, 2] |

Ovladač by vygeneroval několik virtuálních tabulek, které reprezentují tuto jedinou tabulku. První virtuální tabulka je základní tabulka s názvem "priklad Table", která je uvedená níže. Základní tabulka obsahuje všechna data původní tabulky, ale data z těchto polí byla vynechána a jsou rozbalena ve virtuálních tabulkách.

| _id | Název zákazníka | Úroveň služeb |
| --- | --- | --- |
| 1111 |ABC |Silver |
| 2222 |XYZ |Gold |

V následujících tabulkách jsou uvedeny virtuální tabulky, které představují původní pole v příkladu. Tyto tabulky obsahují následující:

* Odkaz zpátky na původní sloupec primárního klíče, který odpovídá řádku původního pole (prostřednictvím _id sloupce)
* Označení pozice dat v původním poli
* Rozšířená data pro každý prvek v poli

Tabulka "ExampleTable_Invoices":

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | položka | price | Discount |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |informační zpráva |456 |0,2 |
| 1111 |1 |124 |termostat |1235 |0,2 |
| 2222 |0 |135 |nákupem ledničky |12543 |0,0 |

Tabulka "ExampleTable_Ratings":

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

## <a name="map-source-to-sink-columns"></a>Mapovat zdroj na sloupce jímky
Další informace o mapování sloupců ve zdrojové datové sadě na sloupce v datové sadě jímky najdete v tématu [mapování sloupců datové sady v Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Opakované čtení z relačních zdrojů
Při kopírování dat z relačních úložišť dat mějte na paměti, že se vyhnete nezamýšleným výsledkům. V Azure Data Factory můžete řez znovu spustit ručně. Můžete také nakonfigurovat zásady opakování pro datovou sadu, aby se řez znovu opakoval, když dojde k selhání. Při opětovném spuštění řezu v obou případech je nutné zajistit, že stejná data budou čtena bez ohledu na to, kolikrát je řez spuštěn. Viz [opakované čtení z relačních zdrojů](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Výkon a optimalizace
Další informace o klíčových faktorech, které mají vliv na výkon přesunu dat (aktivita kopírování) v Azure Data Factory a různých způsobech jejich optimalizace, najdete v tématu [Průvodce optimalizací aktivity kopírování &](data-factory-copy-activity-performance.md) .

## <a name="next-steps"></a>Další kroky
Podrobné pokyny k vytvoření datového kanálu, který přesouvá data z místního úložiště dat do úložiště dat Azure, najdete v článku [přesunutí dat mezi místním a cloudovým](data-factory-move-data-between-onprem-and-cloud.md) článkem.
