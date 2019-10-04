---
title: Nástroje pro přijímání dat
titleSuffix: Azure Data Science Virtual Machine
description: Přečtěte si informace o nástrojích pro přijímání dat a nástrojích, které jsou předinstalované na Data Science Virtual Machine.
keywords: nástroje pro datové vědy, virtuální počítač pro datové vědy, nástroje pro datové vědy, datové vědy pro Linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: conceptual
ms.date: 10/3/2019
ms.openlocfilehash: 47a701cce348e86359947376b3d6a9915dfb2264
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71950158"
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>Data Science Virtual Machine nástroje pro přijímání dat

Jako jeden z prvních technických kroků v projektu pro datové vědy nebo AI musíte identifikovat datové sady, které se mají použít, a přenést je do vašeho prostředí pro analýzu. Data Science Virtual Machine (DSVM) poskytuje nástroje a knihovny pro přenos dat z různých zdrojů do úložiště analytických dat místně na DSVM nebo do datové platformy, ať už v cloudu, nebo v místním prostředí.

Zde jsou některé nástroje pro přesun dat, které jsou k dispozici v DSVM.

## <a name="adlcopy"></a>AdlCopy

|    |           |
| ------------- | ------------- |
| Co to je?   | Nástroj pro kopírování dat z úložiště objektů BLOB v Azure do Azure Data Lake Store. Může také kopírovat data mezi dvěma Azure Data Lake Store účty.      |
| Podporované verze DSVM      | Windows      |
| Typická použití      | Import několika objektů BLOB ze služby Azure Blob Storage do Azure Data Lake Store.      |
|  Jak ji použít nebo spustit?    |   Otevřete příkazový řádek a zadejte `adlcopy`, abyste získali nápovědu.    |
| Odkazy na ukázky      | [Použití AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)      |
| Související nástroje na DSVM      | AzCopy, Azure CLI     |

## <a name="azure-cli"></a>Rozhraní příkazového řádku Azure

|    |           |
| ------------- | ------------- |
| Co to je?   | Nástroj pro správu pro Azure. Obsahuje taky příkazy k přesunu dat z datových platforem Azure, jako je Azure Blob Storage a Azure Data Lake Store.     |
| Podporované verze DSVM      | Windows, Linux     |
| Typická použití      | Import a export dat z Azure Storage a do Azure Data Lake Store      |
|  Jak ji použít nebo spustit?    |   Otevřete příkazový řádek a zadejte `az`, abyste získali nápovědu.    |
| Odkazy na ukázky      | [Použití rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure)     |
| Související nástroje na DSVM      | AzCopy, AdlCopy      |


## <a name="azcopy"></a>AzCopy

|    |           |
| ------------- | ------------- |
| Co to je?   | Nástroj pro kopírování dat do a z místních souborů, úložiště objektů BLOB v Azure, souborů a tabulek.      |
| Podporované verze DSVM      | Windows      |
| Typická použití      | Kopírování souborů do Azure Blob Storage a kopírování objektů BLOB mezi účty.      |
|  Jak ji použít nebo spustit?    |   Otevřete příkazový řádek a zadejte `azcopy`, abyste získali nápovědu.    |
| Odkazy na ukázky      | [AzCopy ve Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)      |
| Související nástroje na DSVM      | AdlCopy     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Nástroj pro migraci dat Azure Cosmos DB

|    |           |
| ------------- | ------------- |
| Co to je?   | Nástroj pro import dat z různých zdrojů do Azure Cosmos DB. Tyto zdroje zahrnují soubory JSON, soubory CSV, SQL, MongoDB, Azure Table Storage, Amazon DynamoDB a Azure Cosmos DB kolekce rozhraní SQL API.      |
| Podporované verze DSVM      | Windows      |
| Typická použití      | Import souborů z virtuálního počítače do CosmosDB, import dat z Azure Table Storage do CosmosDB a import dat z databáze Microsoft SQL Server do CosmosDB.     |
|  Jak ji použít nebo spustit?    |   Chcete-li použít verzi příkazového řádku, otevřete příkazový řádek a zadejte `dt`. Chcete-li použít nástroj grafického uživatelského rozhraní, otevřete příkazový řádek a zadejte `dtui`.    |
| Odkazy na ukázky      | [Importovat data CosmosDB](https://docs.microsoft.com/azure/cosmos-db/import-data)      |
| Související nástroje na DSVM      | AzCopy, AdlCopy      |


## <a name="bcp"></a>znak

|    |           |
| ------------- | ------------- |
| Co to je?   | SQL Server nástroj pro kopírování dat mezi SQL Server a datovým souborem.      |
| Podporované verze DSVM      | Windows      |
| Typická použití      | Import souboru CSV do SQL Server tabulky a export tabulky SQL Server do souboru.      |
|  Jak ji použít nebo spustit?    |   Otevřete příkazový řádek a zadejte `bcp`, abyste získali nápovědu.    |
| Odkazy na ukázky      | [nástroj BCP](https://docs.microsoft.com/sql/tools/bcp-utility)      |
| Související nástroje na DSVM      | SQL Server, Sqlcmd      |

## <a name="blobfuse"></a>blobfuse

|    |           |
| ------------- | ------------- |
| Co to je?   | Nástroj pro připojení kontejneru úložiště objektů BLOB v Azure v systému souborů Linux      |
| Podporované verze DSVM      | Linux      |
| Typická použití      | Čtení a zápis do objektů BLOB v kontejneru.      |
|  Jak ji použít a spustit?    |   Spusťte _blobfuse_ na terminálu.    |
| Odkazy na ukázky      | [blobfuse na GitHubu](https://github.com/Azure/azure-storage-fuse)      |
| Související nástroje na DSVM      | Rozhraní příkazového řádku Azure      |
