---
title: Přesun dat z databáze DB2 pomocí služby Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak přesunout data z databáze DB2 lokálně pomocí aktivity kopírování objekt pro vytváření dat Azure
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: c1644e17-4560-46bb-bf3c-b923126671f1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0e190faca778f4a65a3bd4a29d05c01a89ee7e11
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55816726"
---
# <a name="move-data-from-db2-by-using-azure-data-factory-copy-activity"></a>Přesun dat z databáze DB2 pomocí aktivity kopírování objekt pro vytváření dat Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](data-factory-onprem-db2-connector.md)
> * [Verze 2 (aktuální verze)](../connector-db2.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [konektor DB2 ve verzi V2](../connector-db2.md).


Tento článek popisuje, jak můžete použít aktivitu kopírování, která ve službě Azure Data Factory ke zkopírování dat do úložiště dat z databáze DB2 lokálně. Může kopírovat data do jakékoli úložiště, který je uveden jako podporovaný jímka v [aktivity přesunu dat služby Data Factory](data-factory-data-movement-activities.md#supported-data-stores-and-formats) článku. Toto téma staví na článek služby Data Factory, který nabízí přehled o přesun dat pomocí aktivity kopírování a jsou uvedeny kombinace podporované datové úložiště. 

Data Factory v současné době podporuje pouze přesouvá data z databáze DB2 [podporovaného úložiště dat jímky](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Přesun dat z jiných dat ukládá do DB2 databáze není podporována.

## <a name="prerequisites"></a>Požadavky
Data Factory podporuje připojení k databázi DB2 lokálně pomocí [brána správy dat](data-factory-data-management-gateway.md). Podrobné pokyny k nastavení brány datového kanálu pro přesun dat, najdete v článku [přesun dat z místních do cloudu](data-factory-move-data-between-onprem-and-cloud.md) článku.

Vyžaduje se brána, i v případě, že DB2 je hostovaný na virtuálním počítači Azure IaaS. Bránu můžete nainstalovat na stejný virtuální počítač IaaS jako úložiště dat. Pokud brána lze připojit k databázi, můžete bránu nainstalovat na jiný virtuální počítač.

Brána správy dat poskytuje integrované ovladač DB2, takže není nutné ručně nainstalovat ovladač pro kopírování dat z databáze DB2.

> [!NOTE]
> Tipy pro řešení potíží s připojení a brány problémy, najdete v článku [potíží brány](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) článku.


## <a name="supported-versions"></a>Podporované verze
Konektor Data Factory DB2 podporuje následující platformy IBM DB2 a verze s využitím správce přístupu k SQL Distributed relační databáze architektury (DRDA) verze 9, 10 a 11:

* IBM DB2 verze 11.1 z/OS
* IBM DB2 verze 10.1 z/OS
* IBM DB2 (AS400) i verze 7.2
* IBM DB2 (AS400) i verze 7.1
* IBM DB2 pro Linux, UNIX a Windows (LUW) verze 11
* IBM DB2 pro LUW verze 10.5
* IBM DB2 pro LUW verze 10.1

> [!TIP]
> Pokud se zobrazí chybová zpráva "balíček, který odpovídá na žádost o spuštění příkazu SQL nebyl nalezen. SQLSTATE = 51002 SQLCODE =-805, "důvod je potřebný balíček není vytvořena pro běžného uživatele v operačním systému. Chcete-li vyřešit tento problém, postupujte podle těchto pokynů pro DB2 typu serveru:
> - DB2 for i (AS400): Umožnit power users vytvoří kolekci pro běžného uživatele před spuštěním aktivity kopírování. Chcete-li vytvořit kolekci, použijte příkaz: `create collection <username>`
> - DB2 z/OS nebo LUW: Používejte účet vysoká oprávnění--power users nebo správce, který má autority balíčku a VAZBU, BINDADD, udělení provést na veřejné oprávnění – jednou spustit kopii. Potřebný balíček se automaticky vytvoří během kopírování. Následně můžete přepnout zpět na normální uživatele pro spuštění další kopie.

## <a name="getting-started"></a>Začínáme
Vytvoření kanálu s aktivitou kopírování pro přesun dat z úložiště dat DB2 lokálně pomocí různých nástrojů a rozhraní API: 

- Nejjednodušší způsob, jak vytvořit kanál, je použití Průvodce kopírováním služby Azure Data Factory. Rychlý postup k vytvoření kanálu pomocí Průvodce kopírováním, najdete v části [kurzu: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md). 
- Nástroje můžete také použít k vytvoření kanálu, včetně webu Azure portal, sady Visual Studio, Azure PowerShell, šablony Azure Resource Manageru, rozhraní API pro .NET a rozhraní REST API. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v tématu [kurz aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

Ať už používáte, nástrojů nebo rozhraní API, proveďte následující kroky k vytvoření kanálu pro přesouvání dat ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvoření propojené služby, které spojí vstupní a výstupní úložiště dat do služby data factory.
2. Vytvoření datové sady, které představují vstupní a výstupní data pro operaci kopírování. 
3. Vytvoření kanálu s aktivitou kopírování, která přijímá jako vstupní datovou sadu a datovou sadu jako výstup. 

Při použití Průvodce kopírováním, definice JSON pro Data Factory propojené služby, datové sady a kanál entity se automaticky vytvoří za vás. Při použití rozhraní API (s výjimkou rozhraní .NET API) nebo nástroje pro definování entit služby Data Factory ve formátu JSON. Příklad JSON: Kopírování dat z databáze DB2 do úložiště objektů Blob v Azure ukazuje definice JSON entit služby Data Factory, které se používají ke kopírování dat z úložiště dat DB2 lokálně.

Následující části obsahují podrobnosti o vlastnostech JSON, které se používají k definování entit služby Data Factory, které jsou specifické pro úložiště dat DB2.

## <a name="db2-linked-service-properties"></a>Vlastnosti DB2 propojené služby
V následující tabulce jsou uvedeny vlastnosti JSON, které jsou specifické pro DB2 propojené služby.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| **type** |Tato vlastnost musí být nastavená na **OnPremisesDb2**. |Ano |
| **server** |Název serveru DB2. |Ano |
| **database** |Název databáze DB2. |Ano |
| **schema** |Název schématu v databázi DB2. Tato vlastnost je velká a malá písmena. |Ne |
| **authenticationType** |Typ ověřování, který se používá pro připojení k databázi DB2. Možné hodnoty jsou: Anonymní, základní a Windows. |Ano |
| **uživatelské jméno** |Název pro uživatelský účet, pokud používáte ověřování Basic nebo Windows. |Ne |
| **Heslo** |Heslo pro uživatelský účet. |Ne |
| **gatewayName** |Název brány, který služba Data Factory měla použít pro připojení k místní databázi DB2. |Ano |

## <a name="dataset-properties"></a>Vlastnosti datové sady
Seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [vytváření datových sad](data-factory-create-datasets.md) článku. Části, jako například **struktura**, **dostupnosti**a **zásady** pro datové sady JSON, jsou podobné pro všechny datové sady typy (Azure SQL, Azure Blob storage, Azure Table storage a tak dále).

**TypeProperties** oddílu se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti. **TypeProperties** části datové sady typu **RelationalTable**, která obsahuje datové sady DB2, má následující vlastnost:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| **tableName** |Název tabulky instance databáze DB2, propojená služba odkazuje na. Tato vlastnost je velká a malá písmena. |Ne (Pokud **dotazu** vlastnost aktivity kopírování typu **RelationalSource** určena) |

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivity kopírování najdete v tématu [vytváření kanálů](data-factory-create-pipelines.md) článku. Vlastnosti aktivity, například kopírování **název**, **popis**, **vstupy** tabulky, **výstupy** tabulky, a **zásady**, jsou k dispozici pro všechny typy aktivit. Vlastnosti, které jsou k dispozici v **typeProperties** části aktivity se liší pro každý typ aktivity. Pro aktivitu kopírování vlastností lišit v závislosti na typy dat zdroje a jímky.

Pro aktivitu kopírování, pokud je zdroj typu **RelationalSource** (která zahrnuje DB2), jsou k dispozici v následujících vlastností **typeProperties** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| **Dotaz** |Použijte vlastní dotaz číst data. |Řetězec dotazu SQL. Příklad: `"query": "select * from "MySchema"."MyTable""` |Ne (Pokud **tableName** je zadána vlastnost datové sady) |

> [!NOTE]
> Schéma a tabulku názvy jsou malá a velká písmena. V příkazu dotazu, uzavřete názvy vlastností s použitím "" (dvojité uvozovky).

## <a name="json-example-copy-data-from-db2-to-azure-blob-storage"></a>Příklad JSON: Kopírování dat z databáze DB2 do úložiště objektů Blob v Azure
V tomto příkladu obsahuje ukázky JSON definice, které můžete použít k vytvoření kanálu pomocí [webu Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md), [sady Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), nebo [prostředí Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). V příkladu se dozvíte, jak kopírovat data z databáze DB2 do úložiště objektů Blob. Nicméně je možné zkopírovat data do [všechna podporovaná data ukládat typ jímky](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování objekt pro vytváření dat Azure.

Ukázka obsahuje následující entity služby Data Factory:

- DB2 propojené služby typu [OnPremisesDb2](data-factory-onprem-db2-connector.md#linked-service-properties)
- Služby Azure Blob storage, propojené služby typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
- Vstupní hodnota [datovou sadu](data-factory-create-datasets.md) typu [RelationalTable](data-factory-onprem-db2-connector.md#dataset-properties)
- Výstup [datovou sadu](data-factory-create-datasets.md) typu [Azureblobu](data-factory-azure-blob-connector.md#dataset-properties)
- A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která se používá [RelationalSource](data-factory-onprem-db2-connector.md#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) vlastnosti

Ukázce kopíruje data z výsledku dotazu do DB2 databáze do objektu blob Azure každou hodinu. Vlastnostech JSON použitých v příkladu jsou popsány v následující části definice entity.

Jako první krok nainstalujte a nakonfigurujte bránu data gateway. Pokyny naleznete v [přesun dat mezi místními umístěními a cloudem](data-factory-move-data-between-onprem-and-cloud.md) článku.

**DB2 propojené služby**

```json
{
    "name": "OnPremDb2LinkedService",
    "properties": {
        "type": "OnPremisesDb2",
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

**Objekt Blob propojená služba Azure storage**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorageLinkedService",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
        }
    }
}
```

**Vstupní datové sady DB2**

Příkladu se předpokládá, že jste vytvořili tabulku do DB2 s názvem "MyTable", která má sloupec s názvem "časové razítko" data časových řad.

**Externí** je nastavena na hodnotu "true". Toto nastavení poskytuje služba Data Factory, že tato datová sada je externí do služby data factory a není vytvořený ve službě data factory aktivitou. Všimněte si, **typ** je nastavena na **RelationalTable**.


```json
{
    "name": "Db2DataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremDb2LinkedService",
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

**Výstupní datová sada Azure Blob**

Data se zapisují do nového objektu blob každou hodinu tak, že nastavíte **frekvence** vlastnost "Hodina" a **interval** vlastnost na hodnotu 1. **FolderPath** pro objekt blob se dynamicky vyhodnocuje na základě vlastností počáteční čas řezu, který se právě zpracovává. Cesta ke složce používá rok, měsíc, den a hodina části čas spuštění.

```json
{
    "name": "AzureBlobDb2DataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/db2/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Kanál pro aktivitu kopírování**

Kanálu obsahujícího aktivitu kopírování, který je nakonfigurovaný na použití zadaný vstupní a výstupní datové sady a které je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **RelationalSource** a **jímky** je typ nastaven na **BlobSink**. Zadaná pro dotaz SQL **dotazu** vlastnost vybere data z tabulky "Orders".

```json
{
    "name": "CopyDb2ToBlob",
    "properties": {
        "description": "pipeline for the copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "select * from \"Orders\""
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "inputs": [
                    {
                        "name": "Db2DataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDb2DataSet"
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
                "name": "Db2ToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="type-mapping-for-db2"></a>Mapování typu pro DB2
Jak je uvedeno v [aktivity přesunu dat](data-factory-data-movement-activities.md) článku, aktivita kopírování provádí převody automatické typ z typ zdroje do jímky typu s použitím následujících dvoukrokový přístup:

1. Nativní zdrojový typ převést na typ .NET
2. Převést z typu .NET native jímky typu

Aktivitu kopírování, která převádí data z databáze DB2 typu na typ .NET jsou použity následující mapování:

| Typ databáze DB2. | Typ rozhraní .NET framework |
| --- | --- |
| SmallInt |Int16 |
| Integer |Int32 |
| BigInt |Int64 |
| Real |Single |
| Double |Double |
| Float |Double |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Čísla |Decimal |
| Datum |DateTime |
| Čas |TimeSpan |
| Časové razítko |DateTime |
| XML |Byte[] |
| Char |String |
| VarChar |String |
| LongVarChar |String |
| DB2DynArray |String |
| Binární hodnota |Byte[] |
| VarBinary |Byte[] |
| LongVarBinary |Byte[] |
| Obrázek |String |
| VarGraphic |String |
| LongVarGraphic |String |
| Clob |String |
| Objekt blob |Byte[] |
| DbClob |String |
| SmallInt |Int16 |
| Integer |Int32 |
| BigInt |Int64 |
| Real |Single |
| Double |Double |
| Float |Double |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Čísla |Decimal |
| Datum |DateTime |
| Čas |TimeSpan |
| Časové razítko |DateTime |
| XML |Byte[] |
| Char |String |

## <a name="map-source-to-sink-columns"></a>Mapování zdroje do jímky sloupce
Další postup mapování sloupců v datové sadě zdroje do sloupců v datové sadě jímky najdete v tématu [mapování sloupců v datové sadě ve službě Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-reads-from-relational-sources"></a>Opakovatelné operace čtení z relačních zdrojů
Při kopírování dat z relační datové úložiště, mějte opakovatelnosti aby se zabránilo neúmyslnému výsledků. Ve službě Azure Data Factory můžete znovu spustit řezu ručně. Můžete také nakonfigurovat opakování **zásady** vlastnost pro datovou sadu znovu spustit určitý řez, pokud dojde k chybě. Ujistěte se, že je stejná data čtení bez ohledu na to, kolikrát se znovu spustí řezu a bez ohledu na to, jak spustit řez znovu. Další informace najdete v tématu [Repeatable čte z relačních zdrojů](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Výkon a ladění
Další informace o klíčových faktorů, které ovlivňují výkon aktivitu kopírování a způsoby, jak optimalizovat výkon [průvodci laděním a výkonem aktivity kopírování](data-factory-copy-activity-performance.md).
