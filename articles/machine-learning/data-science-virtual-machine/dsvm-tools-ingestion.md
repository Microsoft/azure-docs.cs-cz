---
title: Virtuální počítač vědy data nástroje pro ingestování dat – Azure | Dokumentace Microsoftu
description: Přečtěte si informace o nástrojích pro přijímání dat a nástrojích, které jsou předinstalované na Data Science Virtual Machine.
keywords: nástroje pro datové vědy, virtuální počítač pro datové vědy, datové vědy pro linux
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: vijetaj
ms.openlocfilehash: 58bfab43d39f050e19687c30a61e05892fffc3f2
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2019
ms.locfileid: "70060606"
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>Nástroje pro ingestování dat data Science virtuálního počítače

Jako jeden z prvních technických kroků v projektu pro datové vědy nebo AI musíte identifikovat datové sady, které se mají použít, a přenést je do vašeho prostředí pro analýzu. Data Science Virtual Machine (DSVM) poskytuje nástroje a knihovny pro přenos dat z různých zdrojů do úložiště analytických dat místně na DSVM nebo do datové platformy, ať už v cloudu, nebo v místním prostředí.

Zde jsou některé nástroje pro přesun dat, které jsou k dispozici v DSVM.

## <a name="adlcopy"></a>AdlCopy

|    |           |
| ------------- | ------------- |
| Co je to?   | Nástroj pro kopírování dat z úložiště objektů BLOB v Azure do Azure Data Lake Store. Můžete také kopírovat data mezi dva účty Azure Data Lake Store.      |
| Podporované verze DSVM      | Windows      |
| Typické použití      | Import několika objektů BLOB ze služby Azure Blob Storage do Azure Data Lake Store.      |
|  Jak používat nebo ji spustit?    |   Otevřete příkazový řádek a zadejte `adlcopy` , abyste získali nápovědu.    |
| Odkazy na ukázky      | [Pomocí AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)      |
| Související nástroje na DSVM      | AzCopy, Azure CLI     |

## <a name="azure-cli"></a>Azure CLI

|    |           |
| ------------- | ------------- |
| Co je to?   | Nástroj pro správu Azure. Obsahuje taky příkazy k přesunu dat z datových platforem Azure, jako je Azure Blob Storage a Azure Data Lake Store.     |
| Podporované verze DSVM      | Windows, Linux     |
| Typické použití      | Import a export dat z Azure Storage a do Azure Data Lake Store      |
|  Jak používat nebo ji spustit?    |   Otevřete příkazový řádek a zadejte `az` , abyste získali nápovědu.    |
| Odkazy na ukázky      | [Použití Azure CLI](https://docs.microsoft.com/cli/azure)     |
| Související nástroje na DSVM      | AzCopy AdlCopy      |


## <a name="azcopy"></a>AzCopy

|    |           |
| ------------- | ------------- |
| Co je to?   | Nástroj pro kopírování dat do a z místních souborů, úložiště objektů BLOB v Azure, souborů a tabulek.      |
| Podporované verze DSVM      | Windows      |
| Typické použití      | Kopírování souborů do Azure Blob Storage a kopírování objektů BLOB mezi účty.      |
|  Jak používat nebo ji spustit?    |   Otevřete příkazový řádek a zadejte `azcopy` , abyste získali nápovědu.    |
| Odkazy na ukázky      | [AzCopy ve Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)      |
| Související nástroje na DSVM      | AdlCopy     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Nástroj Azure Cosmos DB dat migrace

|    |           |
| ------------- | ------------- |
| Co je to?   | Nástroj pro import dat z různých zdrojů do Azure Cosmos DB. Tyto zdroje zahrnují soubory JSON, soubory CSV, SQL, MongoDB, Azure Table Storage, Amazon DynamoDB a Azure Cosmos DB kolekce rozhraní SQL API.      |
| Podporované verze DSVM      | Windows      |
| Typické použití      | Import souborů z virtuálního počítače do CosmosDB, import dat z Azure Table Storage do CosmosDB a import dat z databáze Microsoft SQL Server do CosmosDB.     |
|  Jak používat nebo ji spustit?    |   Chcete-li použít verzi příkazového řádku, otevřete příkazový řádek a zadejte `dt`. Chcete-li použít nástroj grafického uživatelského rozhraní, otevřete příkazový `dtui`řádek a zadejte.    |
| Odkazy na ukázky      | [Importovat data CosmosDB](https://docs.microsoft.com/azure/cosmos-db/import-data)      |
| Související nástroje na DSVM      | AzCopy AdlCopy      |


## <a name="bcp"></a>bcp

|    |           |
| ------------- | ------------- |
| Co je to?   | SQL Server nástroj pro kopírování dat mezi SQL serverem a datový soubor.      |
| Podporované verze DSVM      | Windows      |
| Typické použití      | Import souboru CSV do SQL Server tabulky a export tabulky SQL Server do souboru.      |
|  Jak používat nebo ji spustit?    |   Otevřete příkazový řádek a zadejte `bcp` , abyste získali nápovědu.    |
| Odkazy na ukázky      | [nástroj BCP](https://docs.microsoft.com/sql/tools/bcp-utility)      |
| Související nástroje na DSVM      | SQL Server, sqlcmd      |

## <a name="blobfuse"></a>Blobfuse

|    |           |
| ------------- | ------------- |
| Co je to?   | Nástroj pro připojení kontejneru úložiště objektů BLOB v Azure v systému souborů Linux      |
| Podporované verze DSVM      | Linux      |
| Typické použití      | Čtení a zápis do objektů BLOB v kontejneru.      |
|  Jak ji použít a spustit?    |   Spustit _blobfuse_ v terminálu.    |
| Odkazy na ukázky      | [blobfuse na Githubu](https://github.com/Azure/azure-storage-fuse)      |
| Související nástroje na DSVM      | Azure CLI      |


## <a name="microsoft-data-management-gateway"></a>Brána pro správu dat společnosti Microsoft

|    |           |
| ------------- | ------------- |
| Co je to?   | Nástroj pro připojení místních zdrojů dat ke cloudovým službám pro spotřebu.      |
| Podporované verze DSVM      | Windows      |
| Typické použití      | Virtuální počítač se připojuje ke zdroji dat místní.      |
|  Jak používat nebo ji spustit?    |   V nabídce Start otevřete bránu Microsoft Správa dat.    |
| Odkazy na ukázky      | [Brána správy dat](https://msdn.microsoft.com/library/dn879362.aspx)      |
| Související nástroje na DSVM      | AzCopy AdlCopy, bcp    |
