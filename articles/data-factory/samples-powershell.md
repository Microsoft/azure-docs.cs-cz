---
title: Ukázky Azure PowerShell pro Azure Data Factory
description: Ukázky Azure PowerShell – skripty, které vám pomůžou vytvářet a spravovat datové továrny.
ms.service: data-factory
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 03/16/2021
ms.openlocfilehash: 21ab661dd19d76526cd97b75660b9749e1342e09
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104783230"
---
# <a name="azure-powershell-samples-for-azure-data-factory"></a>Ukázky Azure PowerShell pro Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Následující tabulka obsahuje odkazy na ukázkové Azure PowerShell skripty pro Azure Data Factory.

| Skript | Description  |
|---|---|
|**Kopírování dat**||
|[Kopírování objektů BLOB ze složky do jiné složky v Azure Blob Storage](scripts/copy-azure-blob-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tento skript PowerShellu kopíruje objekty blob ze složky v Azure Blob Storage do jiné složky ve stejném Blob Storage. |
|[Kopírování dat z SQL Server do Azure Blob Storage](scripts/hybrid-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tento skript PowerShellu kopíruje data z databáze SQL Server do úložiště objektů BLOB v Azure. |
|[Hromadná kopie](scripts/bulk-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tento ukázkový skript PowerShellu kopíruje data z několika tabulek v databázi v Azure SQL Database do služby Azure synapse Analytics. |
|[Přírůstková kopie](scripts/incremental-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tento ukázkový skript PowerShellu načte jenom nové nebo aktualizované záznamy ze zdrojového úložiště dat do úložiště dat jímky po počáteční úplné kopii dat ze zdroje do jímky. |
|**Transformace dat**||
|[Transformace dat pomocí clusteru Spark](scripts/transform-data-spark-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tento skript PowerShellu transformuje data spuštěním programu v clusteru Spark. |
|**Přezvednutí a posunutí balíčků SSIS do Azure**||
|[Vytvoření prostředí Azure-SSIS Integration Runtime](scripts/deploy-azure-ssis-integration-runtime-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tento skript PowerShellu zřídí prostředí Azure-SSIS Integration runtime, které spouští balíčky služba SSIS (SQL Server Integration Services) (SSIS) v Azure. |



