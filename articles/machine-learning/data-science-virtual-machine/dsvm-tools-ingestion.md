---
title: Datové vědy virtuální počítač data přijímání nástroje - Azure | Microsoft Docs
description: Data nástroje pro přijímání dat vědecké účely virtuálního počítače
keywords: datové vědy nástroje, datové vědy virtuálního počítače, nástroje pro vědecké zpracování dat, vědecké zpracování dat linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: gokuma
ms.openlocfilehash: 4ba735f1a6019938c8fc270ab98d7e6f8254471b
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>Data nástroje pro přijímání dat vědecké účely virtuálního počítače

Jeden z první technické kroků v vědecké zpracování dat nebo AI projektu je identifikace datové sady, který chcete použít a aby byly v prostředí analýzy. Virtuální počítač vědecké účely dat (DSVM) poskytuje nástroje a knihovny, které mají být předány data z různých zdrojů do úložiště analytická data místně na DSVM nebo v platformě dat v cloudu nebo místně. 

Tady jsou některé nástroje přesun dat, které uvádíme v DSVM. 

## <a name="adlcopy"></a>AdlCopy

|    |           |
| ------------- | ------------- |
| Co je to?   | Nástroj pro kopírování dat z objektů BLOB služby Azure storage do Azure Data Lake Store. Je také může kopírovat data mezi dvěma účty Azure Data Lake Store.      |
| Podporované DSVM verze      | Windows      |
| Typické použití      | Import více objektů blob z úložiště Azure do Azure Data Lake Store.      |
|  Jak se použít nebo ji spustit?    |   Otevřete příkazový řádek, zadejte `adlcopy` jak získat nápovědu.    |
| Odkazy na ukázky      | [Pomocí AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)      |
| Na DSVM souvisejících nástrojích      | AzCopy, Azure příkazového řádku     |

## <a name="azure-command-line"></a>Azure příkazového řádku

|    |           |
| ------------- | ------------- |
| Co je to?   | Nástroj pro správu pro Azure. Také obsahuje příkaz příkazy pro přesun dat z platformy Azure dat jako objekty BLOB úložiště Azure, Azure Data Lake Storage     |
| Podporované DSVM verze      | Windows, Linux     |
| Typické použití      | Import, export dat do a z Azure storage, Azure Data Lake Store      |
|  Jak se použít nebo ji spustit?    |   Otevřete příkazový řádek, zadejte `az` jak získat nápovědu.    |
| Odkazy na ukázky      | [Použití Azure CLI](https://docs.microsoft.com/cli/azure)     |
| Na DSVM souvisejících nástrojích      | AzCopy, AdlCopy      |


## <a name="azcopy"></a>AzCopy

|    |           |
| ------------- | ------------- |
| Co je to?   | Nástroj pro kopírování dat do a z místních souborů, objektů BLOB služby Azure storage, soubory a tabulek.      |
| Podporované DSVM verze      | Windows      |
| Typické použití      | Kopírování souborů do úložiště objektů blob, kopírování objekty BLOB mezi účty.      |
|  Jak se použít nebo ji spustit?    |   Otevřete příkazový řádek, zadejte `azcopy` jak získat nápovědu.    |
| Odkazy na ukázky      | [AzCopy ve Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)      |
| Na DSVM souvisejících nástrojích      | AdlCopy     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Azure nástroj pro migraci dat DB Cosmos

|    |           |
| ------------- | ------------- |
| Co je to?   | Nástroj pro import dat z různých zdrojů, včetně soubory JSON, CSV soubory, SQL, MongoDB, Azure Table úložiště, Amazon DynamoDB a rozhraní API pro Azure Cosmos DB SQL kolekcí do Azure Cosmos DB.      |
| Podporované DSVM verze      | Windows      |
| Typické použití      | Import souborů z virtuálního počítače do CosmosDB, import dat z úložiště tabulek Azure do CosmosDB nebo import dat z databáze systému SQL Server do CosmosDB.     |
|  Jak se použít nebo ji spustit?    |   Chcete-li použít příkaz verze, otevřete příkazový řádek, zadejte `dt`. Chcete-li použít nástroj grafickým uživatelským rozhraním, otevřete příkazový řádek, zadejte `dtui`.    |
| Odkazy na ukázky      | [CosmosDB Import dat](https://docs.microsoft.com/azure/cosmos-db/import-data)      |
| Na DSVM souvisejících nástrojích      | AzCopy, AdlCopy      |


## <a name="bcp"></a>bcp

|    |           |
| ------------- | ------------- |
| Co je to?   | Nástroj SQL Server ke kopírování dat mezi SQL serverem a datový soubor.      |
| Podporované DSVM verze      | Windows      |
| Typické použití      | Import souboru CSV do tabulky SQL Server, export tabulky SQL serveru do souboru.      |
|  Jak se použít nebo ji spustit?    |   Otevřete příkazový řádek, zadejte `bcp` jak získat nápovědu.    |
| Odkazy na ukázky      | [Hromadné kopírování nástroj](https://docs.microsoft.com/sql/tools/bcp-utility)      |
| Na DSVM souvisejících nástrojích      | SQL Server, sqlcmd      |

## <a name="blobfuse"></a>blobfuse

|    |           |
| ------------- | ------------- |
| Co je to?   | Nástroj pro připojení ke kontejneru objektů blob v Azure v souborovém systému Linux.      |
| Podporované DSVM verze      | Linux      |
| Typické použití      | Čtení a zápis do objektů BLOB v kontejneru      |
|  Jak se použít nebo ji spustit?    |   Spustit _blobfuse_ v terminálu.    |
| Odkazy na ukázky      | [blobfuse na Githubu](https://github.com/Azure/azure-storage-fuse)      |
| Na DSVM souvisejících nástrojích      | Azure příkazového řádku      |


## <a name="microsoft-data-management-gateway"></a>Brána pro správu dat společnosti Microsoft

|    |           |
| ------------- | ------------- |
| Co je to?   | Nástroj pro připojení místní zdroje dat pro cloudové služby pro používání.      |
| Podporované DSVM verze      | Windows      |
| Typické použití      | Virtuální počítač se připojuje ke zdroji dat místní.      |
|  Jak se použít nebo ji spustit?    |   Spustíte z nabídky Start "Brána pro správu dat společnosti Microsoft".    |
| Odkazy na ukázky      | [Brána správy dat](https://msdn.microsoft.com/library/dn879362.aspx)      |
| Na DSVM souvisejících nástrojích      | AzCopy, AdlCopy, bcp    |
