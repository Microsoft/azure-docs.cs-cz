---
title: Ukázky Prostředí Azure pro Azure Data Factory
description: Ukázky Prostředí Azure PowerShell – skripty, které vám pomůžou vytvářet a spravovat datové továrny.
services: data-factory
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 01/16/2018
ms.openlocfilehash: 705b2464285b747ea7999b112733b7e99f761f22
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419081"
---
# <a name="azure-powershell-samples-for-azure-data-factory"></a>Ukázky Azure PowerShellu pro Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Následující tabulka obsahuje odkazy na ukázkové skripty Azure PowerShellu pro Azure Data Factory.

| |  |
|---|---|
|**Kopírování dat**||
|[Kopírování objektů BLOB ze složky do jiné složky v úložišti objektů Blob Azure](scripts/copy-azure-blob-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tento skript Prostředí PowerShell kopíruje objekty BLOB ze složky ve službě Azure Blob Storage do jiné složky ve stejném úložišti objektů blob. |
|[Kopírování dat z místního SQL Serveru do úložiště objektů blob Azure](scripts/hybrid-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tento skript Prostředí PowerShell zkopíruje data z místní databáze SERVERU SQL Server do úložiště objektů blob Azure. |
|[Hromadná kopie](scripts/bulk-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tento ukázkový skript PowerShellu zkopíruje data z více tabulek v databázi Azure SQL do datového skladu Azure SQL. |
|[Přírůstková kopie](scripts/incremental-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tento ukázkový skript prostředí PowerShell načte pouze nové nebo aktualizované záznamy ze zdrojového úložiště dat do úložiště dat jímky po počáteční úplné kopii dat ze zdroje do jímky. |
|**Transformace dat**||
|[Transformace dat pomocí clusteru Spark](scripts/transform-data-spark-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tento skript prostředí PowerShell transformuje data spuštěním programu v clusteru Spark. |
|**Výtah a přesun balíčků SSIS do Azure**||
|[Vytvoření prostředí Azure-SSIS Integration Runtime](scripts/deploy-azure-ssis-integration-runtime-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tento skript Prostředí PowerShell zřizoval modul runtime integrace Azure-SSIS, který v Azure spouští balíčky služby SQL Server Integration Services (SSIS). |



