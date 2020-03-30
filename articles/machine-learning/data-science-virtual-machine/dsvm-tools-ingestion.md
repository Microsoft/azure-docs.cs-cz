---
title: Nástroje pro ingestování dat
titleSuffix: Azure Data Science Virtual Machine
description: Seznamte se s nástroji a nástroji pro ingestování dat, které jsou předinstalovány ve virtuálním počítači pro datové vědy.
keywords: nástroje pro datové vědy, virtuální počítač pro datové vědy, datové vědy pro linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: d86858f8d7f09628457b718ca3c481934d720081
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270053"
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>Nástroje pro pobízení dat virtuálního počítače pro datové vědy

Jako jeden z prvních technických kroků v datové vědě nebo projektu AI je nutné identifikovat datové sady, které mají být použity, a přenést je do analytického prostředí. Virtuální počítač pro datové vědy (DSVM) poskytuje nástroje a knihovny pro přenos dat z různých zdrojů do úložiště analytických dat místně v DSVM nebo do datové platformy v cloudu nebo v místním prostředí.

Zde jsou některé nástroje pro přesun dat, které jsou k dispozici v DSVM.

## <a name="adlcopy"></a>AdlCopy

|    |           |
| ------------- | ------------- |
| Co je to?   | Nástroj pro kopírování dat z úložiště objektů Blob Azure do Azure Data Lake Store. Může také kopírovat data mezi dvěma účty Azure Data Lake Store.      |
| Podporované verze DSVM      | Windows      |
| Typická použití      | Import více objektů BLOB z úložiště objektů blob Azure do Azure Data Lake Store.      |
|  Jak ji používat / spustit?    |   Otevřete příkazový `adlcopy` řádek a zadejte nápovědu.    |
| Odkazy na ukázky      | [Pomocí AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)      |
| Související nástroje na DSVM      | AzCopy, Azure CLI     |

## <a name="azure-cli"></a>Azure CLI

|    |           |
| ------------- | ------------- |
| Co je to?   | Nástroj pro správu pro Azure. Obsahuje také příkazslovesa pro přesun dat z datových platforem Azure, jako je azure blob storage a Azure Data Lake Store.     |
| Podporované verze DSVM      | Windows, Linux     |
| Typická použití      | Import a export dat do a z Azure Storage a Azure Data Lake Store.      |
|  Jak ji používat / spustit?    |   Otevřete příkazový `az` řádek a zadejte nápovědu.    |
| Odkazy na ukázky      | [Použití Azure CLI](https://docs.microsoft.com/cli/azure)     |
| Související nástroje na DSVM      | AzCopy, AdlCopy      |


## <a name="azcopy"></a>AzCopy

|    |           |
| ------------- | ------------- |
| Co je to?   | Nástroj pro kopírování dat do a z místních souborů, úložiště objektů blob Azure, souborů a tabulek.      |
| Podporované verze DSVM      | Windows      |
| Typická použití      | Kopírování souborů do úložiště objektů Blob Azure a kopírování objektů BLOB mezi účty.      |
|  Jak ji používat / spustit?    |   Otevřete příkazový `azcopy` řádek a zadejte nápovědu.    |
| Odkazy na ukázky      | [AzCopy ve Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)      |
| Související nástroje na DSVM      | AdlCopy     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Nástroj pro migraci dat Azure Cosmos DB

|    |           |
| ------------- | ------------- |
| Co je to?   | Nástroj pro import dat z různých zdrojů do Azure Cosmos DB, databáze NoSQL v cloudu. Mezi tyto zdroje patří soubory JSON, soubory CSV, SQL, MongoDB, úložiště Azure Table storage, Amazon DynamoDB a Azure Cosmos DB SQL API kolekce.      |
| Podporované verze DSVM      | Windows      |
| Typická použití      | Import souborů z virtuálního počítače do CosmosDB, import dat z úložiště tabulek Azure do CosmosDB a import dat z databáze Serveru Microsoft SQL Server do CosmosDB.     |
|  Jak ji používat / spustit?    |   Chcete-li použít verzi příkazového řádku, `dt`otevřete příkazový řádek a zadejte příkaz . Chcete-li použít nástroj GUI, otevřete příkazový řádek a zadejte příkaz `dtui`.    |
| Odkazy na ukázky      | [CosmosDB Import dat](https://docs.microsoft.com/azure/cosmos-db/import-data)      |
| Související nástroje na DSVM      | AzCopy, AdlCopy      |

## <a name="azure-storage-explorer"></a>Azure Storage Explorer

|    |           |
| ------------- | ------------- |
| Co je to?   | Grafické uživatelské rozhraní pro interakci se soubory uloženými v cloudu Azure. |
| Podporované verze DSVM      | Windows      |
| Typická použití      | Import a export dat z DSVM.    |
|  Jak ji používat / spustit?    | Vyhledejte "Průzkumník a úložiště Azure" v nabídce Start. |
| Odkazy na ukázky      | [Průzkumník úložišť Azure](vm-do-ten-things.md#access-azure-data-and-analytics-services)      |


## <a name="bcp"></a>bcp

|    |           |
| ------------- | ------------- |
| Co je to?   | SQL Server nástroj pro kopírování dat mezi SQL Server a datový soubor.      |
| Podporované verze DSVM      | Windows      |
| Typická použití      | Import souboru CSV do tabulky serveru SQL Server a export tabulky serveru SQL Server do souboru.      |
|  Jak ji používat / spustit?    |   Otevřete příkazový `bcp` řádek a zadejte nápovědu.    |
| Odkazy na ukázky      | [nástroj bCP](https://docs.microsoft.com/sql/tools/bcp-utility)      |
| Související nástroje na DSVM      | SQL Server, sqlcmd      |

## <a name="blobfuse"></a>blůza

|    |           |
| ------------- | ------------- |
| Co je to?   | Nástroj pro připojení kontejneru úložiště Objektů blob Azure v systému souborů Linux.      |
| Podporované verze DSVM      | Linux      |
| Typická použití      | Čtení a zápis do objektů BLOB v kontejneru.      |
|  Jak ji používat a provozovat?    |   Spusťte _blůzu_ na terminálu.    |
| Odkazy na ukázky      | [blůza na GitHubu](https://github.com/Azure/azure-storage-fuse)      |
| Související nástroje na DSVM      | Azure CLI      |
