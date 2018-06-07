---
title: Přesun dat z databáze Sybase pomocí Azure Data Factory | Microsoft Docs
description: Další informace o tom, jak přesunout data z databáze Sybase pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: b379ee10-0ff5-4974-8c87-c95f82f1c5c6
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/02/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 1c39443a187c5ba5e92b22d48914289bc410ec79
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34622342"
---
# <a name="move-data-from-sybase-using-azure-data-factory"></a>Přesun dat z databáze Sybase pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](data-factory-onprem-sybase-connector.md)
> * [Verze 2 – Preview](../connector-sybase.md)

> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory, která je obecně dostupná (GA). Pokud používáte verze 2 služby Data Factory, který je ve verzi preview, najdete v části [Sybase konektoru V2](../connector-sybase.md).

Tento článek vysvětluje, jak pomocí aktivity kopírování v Azure Data Factory pro přesun dat z databáze Sybase na místě. Vychází [aktivity přesunu dat](data-factory-data-movement-activities.md) článek, který představuje obecný přehled přesun dat s aktivitou kopírování.

Do úložiště dat žádné podporované podřízený může kopírovat data z úložiště dat místní databázi Sybase. Seznam úložišť dat jako jímky nepodporuje aktivitě kopírování najdete v tématu [podporovanými úložišti dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabulky. Objekt pro vytváření dat aktuálně podporuje pouze přesunutí dat z jiného úložiště dat Sybase jiným úložištím dat, ale ne pro přesun dat z jiných úložišť dat do úložiště dat Sybase. 

## <a name="prerequisites"></a>Požadavky
Služba data Factory podporuje připojení k místní databázi Sybase zdroje pomocí Brána pro správu dat. V tématu [přesouvání dat mezi místní umístění a cloudem](data-factory-move-data-between-onprem-and-cloud.md) článku se dozvíte o Brána pro správu dat a podrobné pokyny o nastavení brány.

Vyžaduje se brána, i když je databáze Sybase hostované ve virtuálním počítači Azure IaaS. Bránu můžete nainstalovat na stejný virtuální počítač IaaS jako úložiště dat nebo na jiný virtuální počítač, dokud brána se může připojit k databázi.

> [!NOTE]
> V tématu [potíží brány](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) tipy k řešení potíží s připojení nebo brány související s problémy.

## <a name="supported-versions-and-installation"></a>Podporované verze a instalaci
Pro bránu pro správu dat pro připojení k databázi Sybase, je potřeba nainstalovat [zprostředkovatele dat pro databázi Sybase iAnywhere.Data.SQLAnywhere](http://go.microsoft.com/fwlink/?linkid=324846) 16 nebo vyšší ve stejném systému jako brána pro správu dat. 

SAP Sybase SQL odkudkoli (ASA) verze 16 a vyšší je podporováno; IQ a App Service Environment nejsou podporovány.

## <a name="getting-started"></a>Začínáme
Vytvoření kanálu s aktivitou kopírování, který přesouvá data z úložiště místní Cassandra data pomocí různých nástrojů nebo rozhraní API. 

- Nejjednodušší způsob, jak vytvořit kanál je použití **Průvodce kopírováním**. V tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) podrobný rychlé vytvoření kanálu pomocí Průvodce kopírováním data. 
- Tyto nástroje můžete také použít k vytvoření kanálu: **portál Azure**, **Visual Studio**, **prostředí Azure PowerShell**, **šablony Azure Resource Manageru**, **.NET API**, a **REST API**. V tématu [kurzu aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) podrobné pokyny k vytvoření kanálu s aktivitou kopírování. 

Jestli používáte nástroje nebo rozhraní API, je třeba provést následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat podřízený:

1. Vytvoření **propojené služby** propojení vstupní a výstupní data ukládá do data factory.
2. Vytvoření **datové sady** představují vstupní a výstupní data pro kopírování. 
3. Vytvoření **kanálu** s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup. 

Když použijete průvodce, jsou automaticky vytvoří definice JSON pro tyto entity služby Data Factory (propojené služby, datové sady a kanál). Při použití nástroje nebo rozhraní API (s výjimkou .NET API), definujete tyto entity služby Data Factory pomocí formátu JSON.  Příklad s definicemi JSON entit služby Data Factory, které se používají ke zkopírování dat z úložiště dat Sybase na místě, naleznete v tématu [JSON příklad: kopírování dat z databáze Sybase do objektu Blob Azure](#json-example-copy-data-from-sybase-to-azure-blob) tohoto článku. 

Následující části obsahují podrobnosti o vlastnostech formátu JSON, které slouží k určení konkrétní entity služby Data Factory k úložišti dat Sybase:

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Následující tabulka obsahuje popis JSON elementy, které jsou specifické pro Sybase propojené služby.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost typu musí být nastavena na: **OnPremisesSybase** |Ano |
| server |Název serveru databáze Sybase. |Ano |
| databáze |Název databáze Sybase. |Ano |
| Schéma |Název schématu v databázi. |Ne |
| authenticationType. |Typ ověřování používaný pro připojení k databázi Sybase. Možné hodnoty jsou: anonymní, základní a systému Windows. |Ano |
| uživatelské jméno |Pokud používáte ověřování Basic nebo Windows, zadejte uživatelské jméno. |Ne |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. |Ne |
| gatewayName |Název brány, kterou služba Data Factory měla použít pro připojení k místní databázi Sybase. |Ano |

## <a name="dataset-properties"></a>Vlastnosti datové sady
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [vytváření datových sad](data-factory-create-datasets.md) článku. Oddíly, jako je například struktura, dostupnost a zásad JSON datové sady jsou podobné pro všechny typy datovou sadu (Azure SQL Azure blob, tabulky Azure, atd.).

V rámci typeProperties části se liší pro jednotlivé typy datovou sadu a poskytuje informace o umístění dat v úložišti. **Rámci typeProperties** části pro datovou sadu typu **RelationalTable** (což zahrnuje datová sada Sybase) má následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky instance databáze Sybase na kterou odkazuje propojená služba. |Ne (Pokud **dotazu** z **RelationalSource** je zadána) |

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivit najdete v tématu [vytváření kanálů](data-factory-create-pipelines.md) článku. Vlastnosti, například název, popis, vstupní a výstupní tabulky a zásad jsou dostupné pro všechny typy aktivit.

Vzhledem k tomu, vlastnosti dostupné v rámci typeProperties části aktivity se liší podle každý typ aktivity. Pro aktivitu kopírování budou lišit v závislosti na typech zdrojů a jímky.

Pokud je zdroj typu **RelationalSource** (která zahrnuje Sybase), následující vlastnosti jsou k dispozici v **rámci typeProperties** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query |Čtení dat pomocí vlastního dotazu. |Řetězec dotazu SQL. Příklad: vybrat * z MyTable. |Ne (Pokud **tableName** z **datovou sadu** je zadána) |


## <a name="json-example-copy-data-from-sybase-to-azure-blob"></a>Příklad JSON: kopírování dat z databáze Sybase do objektu Blob Azure
Následující příklad uvádí ukázka JSON definice, které můžete použít k vytvoření kanálu pomocí [portál Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) nebo [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [prostředí Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Se ukazují, jak zkopírovat data z databáze Sybase do Azure Blob Storage. Však lze zkopírovat data do jakéhokoli z jímky uvádí [sem](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování v Azure Data Factory.   

Ukázka má následující entity objektu pro vytváření dat:

1. Propojené služby typu [OnPremisesSybase](data-factory-onprem-sybase-connector.md#linked-service-properties).
2. Liked službu typu [azurestorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstup [datovou sadu](data-factory-create-datasets.md) typu [RelationalTable](data-factory-onprem-sybase-connector.md#dataset-properties).
4. Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [RelationalSource](data-factory-onprem-sybase-connector.md#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka zkopíruje data z výsledku dotazu v databázi Sybase do objektu blob každou hodinu. Vlastnostech JSON použitých ve tyto ukázky jsou popsané v části následující ukázky.

Jako první krok nastavte Brána pro správu dat. Tyto pokyny jsou v [přesouvání dat mezi místní umístění a cloudem](data-factory-move-data-between-onprem-and-cloud.md) článku.

**Sybase propojené služby:**

```JSON
{
    "name": "OnPremSybaseLinkedService",
    "properties": {
        "type": "OnPremisesSybase",
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

**Propojená služba Azure Blob storage:**

```JSON
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

**Sybase vstupní datové sady:**

Příkladu se předpokládá, jste vytvořili tabulku "MyTable" v Sybase a obsahuje sloupec s názvem "časové razítko" pro data časové řady.

Nastavení "externí": true informuje služba Data Factory, že tato datová sada je externí k objektu pro vytváření dat a není vyprodukované aktivitu v datové továrně. Všimněte si, že **typ** propojené služby je nastavena na: **RelationalTable**.

```JSON
{
    "name": "SybaseDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremSybaseLinkedService",
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

**Azure Blob výstupní datovou sadu:**

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodiny, interval: 1). Cesta ke složce pro tento objekt blob je vyhodnocován dynamicky podle času zahájení řezu, které jsou zpracovávány. Cesta ke složce používá rok, měsíc, den a čas částí čas spuštění.

```JSON
{
    "name": "AzureBlobSybaseDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/sybase/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

Kanál obsahuje aktivitu kopírování, který je nakonfigurovaný na použití vstupní a výstupní datové sady a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **RelationalSource** a **podřízený** je typ nastaven na **BlobSink**. Zadané pro dotaz SQL **dotazu** vlastnost vybere data ze správce databáze. Objednávky tabulky v databázi.

```JSON
{
    "name": "CopySybaseToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "select * from DBA.Orders"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "inputs": [
                    {
                        "name": "SybaseDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobSybaseDataSet"
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
                "name": "SybaseToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="type-mapping-for-sybase"></a>Mapování typu pro databázi Sybase
Jak je uvedeno v [aktivity přesunu dat](data-factory-data-movement-activities.md) článku aktivita kopírování provádí automatické typ převody z typů zdroje do jímky typů s následující postup krok 2:

1. Převést na typ .NET typy nativní zdrojů
2. Převést na typ jímky nativní typ formátu .NET

Sybase podporuje T-SQL a typy T-SQL. Tabulku mapování z typů sql pro typ formátu .NET, naleznete v části [konektor služby Azure SQL](data-factory-azure-sql-connector.md) článku.

## <a name="map-source-to-sink-columns"></a>Mapování zdroje jímky sloupců
Další informace o mapování sloupců v datové sadě zdrojového sloupce v datové sadě podřízený najdete v tématu [mapování sloupců datovou sadu v Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Opakovatelných číst z relační zdrojů
Při kopírování dat z relačních dat ukládá, uvědomte si, aby se zabránilo neúmyslnému výstupy opakovatelnosti. V Azure Data Factory může řez znovu ručně. Zásady opakovaných pokusů pro datovou sadu můžete také nakonfigurovat tak, aby řez se znovu spustí, když dojde k chybě. Řez se znovu spustí, buď způsobem, musíte zajistit, že stejná data je pro čtení bez ohledu na to kolikrát řez je spustit. V tématu [Repeatable číst z relačními zdroji](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Výkon a ladění
V tématu [výkonu kopie aktivity & ladění průvodce](data-factory-copy-activity-performance.md) Další informace o klíčových faktorů, že dopad výkon přesun dat (aktivita kopírování) v Azure Data Factory a různé způsoby, jak optimalizovat ho.
