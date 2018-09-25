---
title: Podporované kombinace prostředí dat a spuštění pro Příprava dat aplikace Azure Machine Learning | Dokumentace Microsoftu
description: Tento dokument obsahuje úplný seznam podporovaných kombinací různé běhové moduly a zdroje dat pro Azure Machine Learning Data přípravy
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 9168ac1d26432ca3eee5a59b63aa0cec3ae72856
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46989052"
---
# <a name="supported-matrix-for-this-release"></a>Podporované matice pro tuto verzi 

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


Pokud váš kód načítá data s použitím zdroje dat Machine Learning Azure nebo Azure Machine Learning Příprava dat, získávání buď Pandas nebo Spark dataframe následující kombinace experiment výpočetní prostředí a umístění se podporují:

|     |Místní soubory  |Azure Blob Storage  |Databáze systému SQL Server ***  |
|---------|---------|---------|---------|---------|
|Místní Pythonu    |     Podporováno    |Nepodporuje se         | Nepodporuje se        |         |
|Docker (virtuální počítač s Linuxem) Python     |Podporované v souborech projektu pouze *         | Nepodporuje se        |        Nepodporuje se |         |
|PySpark docker (virtuální počítač s Linuxem)     |Podporované v souborech projektu pouze *     |Podporováno         | Podporuje se**        |         |
|Azure Data Science virtuálního počítače Pythonu     |Podporované v souborech projektu pouze *         |Nepodporuje se         |Nepodporuje se         |         |
|Azure Data Science virtuálního počítače PySPark     | Podporované v souborech projektu pouze *        |Nepodporuje se         |Nepodporuje se         |         |
|Azure HDInsight PySpark     | Nepodporuje se        |Podporováno         |Podporuje se**         |         |
|Azure HDInsight Pythonu     | Nepodporuje se        | Nepodporuje se        | Nepodporuje se        |         |

Azure Data Lake Store se aktuálně nepodporuje pro všechny cílové výpočetní prostředí.

* Pokud se používá místní cesty souborů, jsou soubory v rámci projektu zkopírovány do výpočetní prostředí a pak si můžete přečíst zde. Souborů mimo projekt nejsou zkopírovány a ve výpočetním prostředí, nebude možné interpretovat cesty. Zvažte možnost použít nahrazení zdroje dat tak, aby váš kód může použít místní soubor při místním spuštění. Přepněte se do objektu blob služby Azure Storage pro různé konfigurace spuštění. Také můžete vzorkování podpory u zdrojů dat pro správu se spustí na velkých objemů dat pouze při některých konfiguracích spuštění.

** Používá Maven JDBC SQL Server ovladač 6.2.1. Ujistěte se, že tento balíček (nebo na kompatibilní) je součástí souboru spark_dependencies.yml pro výpočetní prostředí.

Podporuje Azure SQL Database nebo SQL Server za předpokladu, že databáze můžete přejít z výpočetní prostředí. 
