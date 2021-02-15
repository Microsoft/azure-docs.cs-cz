---
title: Nástroje pro ingestování dat
titleSuffix: Azure Data Science Virtual Machine
description: Přečtěte si informace o nástrojích pro přijímání dat a nástrojích, které jsou předinstalované na Data Science Virtual Machine.
keywords: nástroje pro datové vědy, virtuální počítač pro datové vědy, datové vědy pro linux
services: machine-learning
ms.service: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 21a6efa8108bfd0a317eb955e8b3ffcfba0862a2
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519367"
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>Data Science Virtual Machine nástroje pro přijímání dat

Jako jeden z prvních technických kroků v projektu pro datové vědy nebo AI musíte identifikovat datové sady, které se mají použít, a přenést je do vašeho prostředí pro analýzu. Data Science Virtual Machine (DSVM) poskytuje nástroje a knihovny pro přenos dat z různých zdrojů do úložiště analytických dat místně na DSVM nebo do datové platformy, ať už v cloudu, nebo v místním prostředí.

Zde jsou některé nástroje pro přesun dat, které jsou k dispozici v DSVM.

## <a name="adlcopy"></a>AdlCopy

| Kategorie | Hodnota |
| ------------- | ------------- |
| Co to je?   | Nástroj pro kopírování dat z úložiště objektů BLOB v Azure do Azure Data Lake Store. Může také kopírovat data mezi dvěma Azure Data Lake Store účty.      |
| Podporované verze DSVM      | Windows      |
| Typická použití      | Import několika objektů BLOB ze služby Azure Blob Storage do Azure Data Lake Store.      |
|  Jak ji použít nebo spustit?    |   Otevřete příkazový řádek a zadejte `adlcopy` , abyste získali nápovědu.    |
| Odkazy na ukázky      | [Pomocí AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md)      |
| Související nástroje na DSVM      | AzCopy, Azure CLI     |

## <a name="azure-cli"></a>Azure CLI

| Kategorie | Hodnota |
| ------------- | ------------- |
| Co to je?   | Nástroj pro správu pro Azure. Obsahuje taky příkazy k přesunu dat z datových platforem Azure, jako je Azure Blob Storage a Azure Data Lake Store.     |
| Podporované verze DSVM      | Windows, Linux     |
| Typická použití      | Import a export dat z Azure Storage a do Azure Data Lake Store      |
|  Jak ji použít nebo spustit?    |   Otevřete příkazový řádek a zadejte `az` , abyste získali nápovědu.    |
| Odkazy na ukázky      | [Použití Azure CLI](/cli/azure)     |
| Související nástroje na DSVM      | AzCopy, AdlCopy      |


## <a name="azcopy"></a>AzCopy

| Kategorie | Hodnota |
| ------------- | ------------- |
| Co to je?   | Nástroj pro kopírování dat do a z místních souborů, úložiště objektů BLOB v Azure, souborů a tabulek.      |
| Podporované verze DSVM      | Windows      |
| Typická použití      | Kopírování souborů do Azure Blob Storage a kopírování objektů BLOB mezi účty.      |
|  Jak ji použít nebo spustit?    |   Otevřete příkazový řádek a zadejte `azcopy` , abyste získali nápovědu.    |
| Odkazy na ukázky      | [AzCopy ve Windows](../../storage/common/storage-use-azcopy-v10.md)      |
| Související nástroje na DSVM      | AdlCopy     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Nástroj pro migraci dat Azure Cosmos DB

| Kategorie | Hodnota |
| ------------- | ------------- |
| Co to je?   | Nástroj pro import dat z různých zdrojů do Azure Cosmos DB databáze NoSQL v cloudu. Tyto zdroje zahrnují soubory JSON, soubory CSV, SQL, MongoDB, Azure Table Storage, Amazon DynamoDB a Azure Cosmos DB kolekce rozhraní SQL API.      |
| Podporované verze DSVM      | Windows      |
| Typická použití      | Import souborů z virtuálního počítače do CosmosDB, import dat z Azure Table Storage do CosmosDB a import dat z databáze Microsoft SQL Server do CosmosDB.     |
|  Jak ji použít nebo spustit?    |   Chcete-li použít verzi příkazového řádku, otevřete příkazový řádek a zadejte `dt` . Chcete-li použít nástroj grafického uživatelského rozhraní, otevřete příkazový řádek a zadejte `dtui` .    |
| Odkazy na ukázky      | [Importovat data CosmosDB](../../cosmos-db/import-data.md)      |
| Související nástroje na DSVM      | AzCopy, AdlCopy      |

## <a name="azure-storage-explorer"></a>Průzkumník služby Azure Storage

| Kategorie | Hodnota |
| ------------- | ------------- |
| Co to je?   | Grafické uživatelské rozhraní pro interakci se soubory uloženými v cloudu Azure. |
| Podporované verze DSVM      | Windows      |
| Typická použití      | Import a export dat z DSVM.    |
|  Jak ji použít nebo spustit?    | V nabídce Start vyhledejte "Průzkumník služby Azure Storage". |
| Odkazy na ukázky      | [Azure Storage Explorer](vm-do-ten-things.md#access-azure-data-and-analytics-services)      |


## <a name="bcp"></a>bcp

| Kategorie | Hodnota |
| ------------- | ------------- |
| Co to je?   | SQL Server nástroj pro kopírování dat mezi SQL Server a datovým souborem.      |
| Podporované verze DSVM      | Windows      |
| Typická použití      | Import souboru CSV do SQL Server tabulky a export tabulky SQL Server do souboru.      |
|  Jak ji použít nebo spustit?    |   Otevřete příkazový řádek a zadejte `bcp` , abyste získali nápovědu.    |
| Odkazy na ukázky      | [nástroj BCP](/sql/tools/bcp-utility)      |
| Související nástroje na DSVM      | SQL Server, Sqlcmd      |

## <a name="blobfuse"></a>blobfuse

| Kategorie | Hodnota |
| ------------- | ------------- |
| Co to je?   | Nástroj pro připojení kontejneru úložiště objektů BLOB v Azure v systému souborů Linux      |
| Podporované verze DSVM      | Linux      |
| Typická použití      | Čtení a zápis do objektů BLOB v kontejneru.      |
|  Jak ji použít a spustit?    |   Spusťte _blobfuse_ na terminálu.    |
| Odkazy na ukázky      | [blobfuse na GitHubu](https://github.com/Azure/azure-storage-fuse)      |
| Související nástroje na DSVM      | Azure CLI      |