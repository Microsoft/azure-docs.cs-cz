---
title: Virtuální počítač vědy data nástroje pro ingestování dat – Azure | Dokumentace Microsoftu
description: Další informace o nástroje pro ingestování dat a nástroje, které jsou předinstalované v virtuální počítač pro datové vědy.
keywords: nástroje pro datové vědy, virtuální počítač pro datové vědy, datové vědy pro linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: gokuma
ms.openlocfilehash: 75f068a857be33337348958fbc472f37f6d84e12
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53075843"
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>Nástroje pro ingestování dat data Science virtuálního počítače

Jedním z prvních technické kroků pro datové vědy a AI projektu je určit datové sady, které použije a přiřaďte je do vašeho prostředí analytics. Na Data virtuálního počítače VĚDY poskytuje nástroje a knihovny, které chcete přenést data z různých zdrojů do úložiště analytických dat místně na datové VĚDY nebo datové platformy v cloudu nebo místně. 

Tady jsou některé nástroje přesunu dat, které poskytujeme na datové VĚDY. 

## <a name="adlcopy"></a>AdlCopy

|    |           |
| ------------- | ------------- |
| Co je to?   | Nástroj pro kopírování dat z Azure storage BLOB do Azure Data Lake Store. Můžete také kopírovat data mezi dva účty Azure Data Lake Store.      |
| Podporované DSVM verze      | Windows      |
| Typické použití      | Import více objekty BLOB ze služby Azure storage do Azure Data Lake Store.      |
|  Jak používat nebo ji spustit?    |   Otevřete příkazový řádek, zadejte `adlcopy` zobrazíte nápovědu.    |
| Odkazy na ukázky      | [Pomocí AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)      |
| Související nástroje na datové VĚDY      | AzCopy, rozhraní příkazového řádku Azure     |

## <a name="azure-command-line"></a>Rozhraní příkazového řádku Azure

|    |           |
| ------------- | ------------- |
| Co je to?   | Nástroj pro správu Azure. Obsahuje také příkaz příkazy pro přesun dat z Azure datové platformy, jako jsou objekty BLOB služby Azure storage, Azure Data Lake Storage     |
| Podporované DSVM verze      | Windows, Linux     |
| Typické použití      | Importování, exportování dat do a z Azure storage, Azure Data Lake Store      |
|  Jak používat nebo ji spustit?    |   Otevřete příkazový řádek, zadejte `az` zobrazíte nápovědu.    |
| Odkazy na ukázky      | [Použití Azure CLI](https://docs.microsoft.com/cli/azure)     |
| Související nástroje na datové VĚDY      | AzCopy AdlCopy      |


## <a name="azcopy"></a>AzCopy

|    |           |
| ------------- | ------------- |
| Co je to?   | Nástroj pro kopírování dat do a z místních souborů, objektů BLOB úložiště Azure, soubory a tabulky.      |
| Podporované DSVM verze      | Windows      |
| Typické použití      | Kopírování souborů do úložiště objektů blob, kopírování objektů BLOB mezi účty.      |
|  Jak používat nebo ji spustit?    |   Otevřete příkazový řádek, zadejte `azcopy` zobrazíte nápovědu.    |
| Odkazy na ukázky      | [AzCopy ve Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)      |
| Související nástroje na datové VĚDY      | AdlCopy     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Nástroj Azure Cosmos DB dat migrace

|    |           |
| ------------- | ------------- |
| Co je to?   | Nástroj pro import dat z různých zdrojů, včetně soubory JSON, soubory CSV, SQL, MongoDB, Azure Table storage, Amazon DynamoDB a rozhraní SQL API služby Azure Cosmos DB kolekcí do služby Azure Cosmos DB.      |
| Podporované DSVM verze      | Windows      |
| Typické použití      | Import souborů z virtuálního počítače do služby cosmos DB, import dat z Azure table storage do služby cosmos DB nebo import dat z databáze SQL serveru do služby cosmos DB.     |
|  Jak používat nebo ji spustit?    |   Použití příkazového řádku verze, otevřete příkazový řádek, zadejte `dt`. Chcete-li použít grafického uživatelského rozhraní, otevřete příkazový řádek, zadejte `dtui`.    |
| Odkazy na ukázky      | [Cosmos DB importovat Data](https://docs.microsoft.com/azure/cosmos-db/import-data)      |
| Související nástroje na datové VĚDY      | AzCopy AdlCopy      |


## <a name="bcp"></a>bcp

|    |           |
| ------------- | ------------- |
| Co je to?   | SQL Server nástroj pro kopírování dat mezi SQL serverem a datový soubor.      |
| Podporované DSVM verze      | Windows      |
| Typické použití      | Import souboru CSV do tabulky SQL Server, Export tabulek systému SQL Server do souboru.      |
|  Jak používat nebo ji spustit?    |   Otevřete příkazový řádek, zadejte `bcp` zobrazíte nápovědu.    |
| Odkazy na ukázky      | [Nástroj pro hromadné kopírování](https://docs.microsoft.com/sql/tools/bcp-utility)      |
| Související nástroje na datové VĚDY      | SQL Server, sqlcmd      |

## <a name="blobfuse"></a>Blobfuse

|    |           |
| ------------- | ------------- |
| Co je to?   | Nástroj pro připojení ke kontejneru objektů blob v Azure v Linuxu.      |
| Podporované DSVM verze      | Linux      |
| Typické použití      | Čtení a zápis do objektů BLOB v kontejneru      |
|  Jak používat nebo ji spustit?    |   Spustit _blobfuse_ v terminálu.    |
| Odkazy na ukázky      | [blobfuse na Githubu](https://github.com/Azure/azure-storage-fuse)      |
| Související nástroje na datové VĚDY      | Rozhraní příkazového řádku Azure      |


## <a name="microsoft-data-management-gateway"></a>Brána pro správu dat společnosti Microsoft

|    |           |
| ------------- | ------------- |
| Co je to?   | Nástroj pro připojení místních zdrojů dat ke cloudovým službám pro spotřebu.      |
| Podporované DSVM verze      | Windows      |
| Typické použití      | Virtuální počítač se připojuje ke zdroji dat místní.      |
|  Jak používat nebo ji spustit?    |   Z nabídky Start spusťte "Brána pro správu dat společnosti Microsoft".    |
| Odkazy na ukázky      | [Brána správy dat](https://msdn.microsoft.com/library/dn879362.aspx)      |
| Související nástroje na datové VĚDY      | AzCopy AdlCopy, bcp    |
