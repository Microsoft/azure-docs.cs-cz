---
title: Přesun dat z MongoDB pomocí služby Data Factory | Microsoft Docs
description: Další informace o tom, jak přesunout data z databáze MongoDB pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 10ca7d9a-7715-4446-bf59-2d2876584550
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 301a1a9934f9d7e76399dfe46a65481351a61e22
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34621441"
---
# <a name="move-data-from-mongodb-using-azure-data-factory"></a>Přesun dat z MongoDB pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](data-factory-on-premises-mongodb-connector.md)
> * [Verze 2 – Preview](../connector-mongodb.md)

> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory, která je obecně dostupná (GA). Pokud používáte verze 2 služby Data Factory, který je ve verzi preview, najdete v části [MongoDB konektor v V2](../connector-mongodb.md).


Tento článek vysvětluje, jak pomocí aktivity kopírování v Azure Data Factory pro přesun dat z místní databázi MongoDB. Vychází [aktivity přesunu dat](data-factory-data-movement-activities.md) článek, který představuje obecný přehled přesun dat s aktivitou kopírování.

Do úložiště dat žádné podporované podřízený může kopírovat data z místního úložiště dat MongoDB. Seznam úložišť dat jako jímky nepodporuje aktivitě kopírování najdete v tématu [podporovanými úložišti dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabulky. Objekt pro vytváření dat aktuálně podporuje pouze přesunutí dat z jiného úložiště dat MongoDB k jiným úložištím dat, ale ne pro přesun dat z jiných úložišť dat do úložiště dat MongoDB. 

## <a name="prerequisites"></a>Požadavky
Pro službu Azure Data Factory být schopni připojit k vaší databázi MongoDB místní musíte nainstalovat následující součásti:

- Podporované verze MongoDB jsou: 2.4, 2.6, 3.0, 3.2, 3.4 a 3.6.
- Brána pro správu dat na stejném počítači, který je hostitelem databáze nebo na samostatném počítači, aby se zabránilo neslučitelných pro prostředky s databází. Brána pro správu dat je software, který se připojuje místní zdroje dat do cloudové služby v zabezpečený a spravovaný. V tématu [Brána pro správu dat](data-factory-data-management-gateway.md) podrobnosti o Brána pro správu dat najdete v článku. V tématu [přesun dat z lokálního prostředí do cloudu](data-factory-move-data-between-onprem-and-cloud.md) podrobné pokyny o nastavení brány datovém kanálu pro přesun dat najdete v článku.

    Při instalaci brány se automaticky nainstaluje ovladač Microsoft MongoDB ODBC používaný pro připojení k MongoDB.

    > [!NOTE]
    > Musíte použít bránu pro připojení k MongoDB, i když je hostován v virtuální počítače Azure IaaS. Pokud se pokoušíte připojit k instanci MongoDB hostované v cloudu, můžete také nainstalovat instanci brány ve virtuálním počítači IaaS.

## <a name="getting-started"></a>Začínáme
Vytvoření kanálu s aktivitou kopírování, který přesouvá data z úložiště místní MongoDB data pomocí různých nástrojů nebo rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál je použití **Průvodce kopírováním**. V tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) podrobný rychlé vytvoření kanálu pomocí Průvodce kopírováním data.

Tyto nástroje můžete také použít k vytvoření kanálu: **portál Azure**, **Visual Studio**, **prostředí Azure PowerShell**, **šablony Azure Resource Manageru**, **.NET API**, a **REST API**. V tématu [kurzu aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) podrobné pokyny k vytvoření kanálu s aktivitou kopírování. 

Jestli používáte nástroje nebo rozhraní API, je třeba provést následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat podřízený: 

1. Vytvoření **propojené služby** propojení vstupní a výstupní data ukládá do data factory.
2. Vytvoření **datové sady** představují vstupní a výstupní data pro kopírování. 
3. Vytvoření **kanálu** s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup. 

Když použijete průvodce, jsou automaticky vytvoří definice JSON pro tyto entity služby Data Factory (propojené služby, datové sady a kanál). Při použití nástroje nebo rozhraní API (s výjimkou .NET API), definujete tyto entity služby Data Factory pomocí formátu JSON.  Příklad s definicemi JSON entit služby Data Factory, které se používají ke zkopírování dat z úložiště dat místní MongoDB, naleznete v tématu [JSON příklad: kopírování dat z MongoDB do objektu Blob Azure](#json-example-copy-data-from-mongodb-to-azure-blob) tohoto článku. 

Následující části obsahují podrobnosti o vlastnostech formátu JSON, které slouží k určení entit služby Data Factory konkrétní zdroj MongoDB:

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Následující tabulka obsahuje popis JSON elementy, které jsou specifické pro **OnPremisesMongoDB** propojené služby.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost typu musí být nastavena na: **OnPremisesMongoDb** |Ano |
| server |IP adresa nebo název hostitele serveru MongoDB. |Ano |
| port |Port TCP, který používá MongoDB server naslouchat pro připojení klientů. |Volitelné, výchozí hodnota: 27017 |
| authenticationType. |Základní, nebo anonymní. |Ano |
| uživatelské jméno |Uživatelský účet pro přístup k MongoDB. |Ano (Pokud se používá základní ověřování). |
| heslo |Heslo pro uživatele. |Ano (Pokud se používá základní ověřování). |
| authSource |Název databáze MongoDB, kterou chcete použít ke kontrole přihlašovacích údajů pro ověřování. |Volitelný parametr (Pokud se používá základní ověřování). Výchozí: používá účet správce a do databáze určené pomocí vlastnost databaseName. |
| databaseName |Název databáze MongoDB, kterou chcete získat přístup. |Ano |
| gatewayName |Název brány, který přistupuje k úložišti. |Ano |
| encryptedCredential |Přihlašovací údaje zašifrovaná pomocí brány. |Nepovinné |

## <a name="dataset-properties"></a>Vlastnosti datové sady
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [vytváření datových sad](data-factory-create-datasets.md) článku. Oddíly, jako je například struktura, dostupnost a zásad JSON datové sady jsou podobné pro všechny typy datovou sadu (Azure SQL Azure blob, tabulky Azure, atd.).

**Rámci typeProperties** oddílu se liší pro jednotlivé typy datovou sadu a informace o umístění dat v úložišti dat. Rámci typeProperties část datové sady typ **MongoDbCollection** má následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| Název_kolekce |Název kolekce v databázi MongoDB. |Ano |

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [vytváření kanálů](data-factory-create-pipelines.md) článku. Vlastnosti, například název, popis, vstupní a výstupní tabulky a zásad jsou dostupné pro všechny typy aktivit.

Vlastnosti, které jsou k dispozici v **rámci typeProperties** části aktivity na druhé straně lišit každý typ aktivity. Pro aktivitu kopírování budou lišit v závislosti na typech zdrojů a jímky.

Pokud je zdroj typu **MongoDbSource** následující vlastnosti jsou k dispozici v rámci typeProperties části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query |Čtení dat pomocí vlastního dotazu. |Řetězec dotazu SQL 92. Příklad: vybrat * z MyTable. |Ne (Pokud **Název_kolekce** z **datovou sadu** je zadána) |



## <a name="json-example-copy-data-from-mongodb-to-azure-blob"></a>Příklad JSON: kopírování dat z MongoDB do objektu Blob Azure
Tento příklad obsahuje ukázkové JSON definice, které můžete použít k vytvoření kanálu pomocí [portál Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) nebo [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [prostředí Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ukazuje, jak zkopírovat data z místní MongoDB do Azure Blob Storage. Však lze zkopírovat data do jakéhokoli z jímky uvádí [sem](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování v Azure Data Factory.

Ukázka má následující entity objektu pro vytváření dat:

1. Propojené služby typu [OnPremisesMongoDb](#linked-service-properties).
2. Propojené služby typu [azurestorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstup [datovou sadu](data-factory-create-datasets.md) typu [MongoDbCollection](#dataset-properties).
4. Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [MongoDbSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka zkopíruje data z výsledku dotazu v databázi MongoDB do objektu blob každou hodinu. Vlastnostech JSON použitých ve tyto ukázky jsou popsané v části následující ukázky.

Jako první krok, nastavit Brána pro správu dat podle pokynů v [Brána pro správu dat](data-factory-data-management-gateway.md) článku.

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

**Vstupní datové sady MongoDB:** nastavení "externí": "PRAVDA" informuje služba Data Factory, v tabulce je externí k objektu pro vytváření dat a není vyprodukované aktivitu v datové továrně.

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

**Azure Blob výstupní datovou sadu:**

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodiny, interval: 1). Cesta ke složce pro tento objekt blob je vyhodnocován dynamicky podle času zahájení řezu, které jsou zpracovávány. Cesta ke složce používá rok, měsíc, den a čas částí čas spuštění.

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

**Aktivita kopírování v kanálu s MongoDB zdrojový a podřízený objekt Blob:**

Kanál obsahuje aktivitu kopírování, která je nakonfigurována pro používání výše vstupní a výstupní datové sady a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **MongoDbSource** a **podřízený** je typ nastaven na **BlobSink**. Zadané pro dotaz SQL **dotazu** vlastnost vybere data za poslední hodinu pro kopírování.

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
Služba Azure Data Factory odvodí schématu z kolekce MongoDB pomocí nejnovější 100 dokumenty v kolekci. Pokud tyto dokumenty 100 neobsahují úplné schéma, může být některé sloupce ignorován při kopírování.

## <a name="type-mapping-for-mongodb"></a>Mapování typu pro MongoDB
Jak je uvedeno v [aktivity přesunu dat](data-factory-data-movement-activities.md) článku aktivita kopírování provádí automatické typ převody z typů zdroje do jímky typů s následující postup krok 2:

1. Převést na typ .NET typy nativní zdrojů
2. Převést na typ jímky nativní typ formátu .NET

Při přesunu dat na MongoDB se používají následující mapování z typů MongoDB na typy .NET.

| Typ MongoDB | Typ rozhraní .NET framework |
| --- | --- |
| Binární hodnota |Byte] |
| Logická hodnota |Logická hodnota |
| Datum |DateTime |
| NumberDouble |Double |
| NumberInt |Int32 |
| NumberLong |Int64 |
| ObjectID |Řetězec |
| Řetězec |Řetězec |
| UUID |Guid |
| Objekt |Renormalized do vyrovnání sloupce s "_" jako vnořené oddělovače |

> [!NOTE]
> Další informace o podpoře pro polí pomocí virtuální tabulky, najdete v tématu [podporu pro komplexní typy pomocí virtuální tabulky](#support-for-complex-types-using-virtual-tables) části níže.

V současné době nejsou podporovány následující typy dat MongoDB: DBPointer, JavaScript, Max za minutu klíče, regulární výraz, Symbol, časové razítko, Undefined

## <a name="support-for-complex-types-using-virtual-tables"></a>Podpora pro komplexní typy pomocí virtuální tabulky
Azure Data Factory používá integrované ovladače ODBC pro připojení k a kopírování dat z databáze MongoDB. Pro komplexní typy jako pole nebo objekty s různé typy mezi dokumenty ovladač znovu sjednotí data na odpovídající virtuální tabulky. Konkrétně Pokud tabulka obsahuje tyto sloupce, ovladač generuje následující virtuální tabulky:

* A **základní tabulka**, která obsahuje stejná data jako skutečné tabulky s výjimkou komplexní typ sloupce. Základní tabulka používá stejný název jako skutečné tabulky, která reprezentuje.
* A **virtuální tabulku** pro každý sloupec komplexní typ, který rozbalí vnořená data. Virtuální tabulky jsou pojmenované pomocí názvu skutečné tabulky, oddělovač "_" a název pole nebo objekt.

Virtuální tabulky odkazovat na data v tabulce skutečné povolení ovladače pro přístup k datům nenormalizované. Viz část o příklad níže podrobnosti. Dotazování a připojení virtuální tabulky, můžete přístup k obsahu polí MongoDB.

Můžete použít [Průvodce kopírováním](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) intuitivně zobrazit seznam tabulek v databázi MongoDB, včetně virtuální tabulky a zobrazte náhled dat, uvnitř. Můžete také vytvořit dotaz v Průvodci kopírováním a ověření zobrazíte výsledek.

### <a name="example"></a>Příklad:
Například "ExampleTable" pod je MongoDB tabulku, která obsahuje jeden sloupec s pole objektů v každé buňce – faktury a jeden sloupec s pole Skalární typy – hodnocení.

| _id | Jméno zákazníka | Faktury | Úroveň služby | Hodnocení |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id: "123", položka: "Toaster byl", cena: "456", slevu: "0,2"}, {invoice_id: "124", položka: "sušárny", ceny: slevách "1235": "0,2"}] |Stříbrný |[5,6] |
| 2222 |XYZ |[{invoice_id: "135", položka: "ledničky", cena: "12543", slevu: "0,0"}] |Zlatá |[1,2] |

Ovladač by vygeneroval více virtuální tabulky k reprezentaci této jednu tabulku. První virtuální tabulky je základní tabulka s názvem "ExampleTable", viz následující obrázek. Základní tabulka obsahuje všechna data z původní tabulky, ale data z pole byla vynechána a je v tabulkách virtuální rozbalena.

| _id | Jméno zákazníka | Úroveň služby |
| --- | --- | --- |
| 1111 |ABC |Stříbrný |
| 2222 |XYZ |Zlatá |

Virtuální tabulky, které představují původní pole v příkladu v následujících tabulkách. Tyto tabulky obsahují následující:

* Odkaz zpět na původní sloupec primárního klíče odpovídající řádek původní pole (přes sloupec _id)
* Označení pozice dat v rámci původní pole
* Rozšířená data pro každý prvek v rámci pole

Tabulka "ExampleTable_Invoices":

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | Položka | price | Sleva |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |Toaster byl |456 |0.2 |
| 1111 |1 |124 |sušárny |1235 |0.2 |
| 2222 |0 |135 |ledničky |12543 |0.0 |

Tabulka "ExampleTable_Ratings":

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

## <a name="map-source-to-sink-columns"></a>Mapování zdroje jímky sloupců
Další informace o mapování sloupců v datové sadě zdrojového sloupce v datové sadě podřízený najdete v tématu [mapování sloupců datovou sadu v Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Opakovatelných číst z relační zdrojů
Při kopírování dat z relačních dat ukládá, uvědomte si, aby se zabránilo neúmyslnému výstupy opakovatelnosti. V Azure Data Factory může řez znovu ručně. Zásady opakovaných pokusů pro datovou sadu můžete také nakonfigurovat tak, aby řez se znovu spustí, když dojde k chybě. Řez se znovu spustí, buď způsobem, musíte zajistit, že stejná data je pro čtení bez ohledu na to kolikrát řez je spustit. V tématu [Repeatable číst z relačními zdroji](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Výkon a ladění
V tématu [výkonu kopie aktivity & ladění průvodce](data-factory-copy-activity-performance.md) Další informace o klíčových faktorů, že dopad výkon přesun dat (aktivita kopírování) v Azure Data Factory a různé způsoby, jak optimalizovat ho.

## <a name="next-steps"></a>Další kroky
V tématu [přesun dat mezi místními a cloudovými](data-factory-move-data-between-onprem-and-cloud.md) článek podrobné pokyny pro vytváření dat kanál, který přesouvá data z místního úložiště dat k úložišti dat Azure.
