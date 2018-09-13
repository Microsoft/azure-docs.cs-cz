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
ms.openlocfilehash: 07b9ac8389a2bf06e0356f65d816d75d49cc0cf5
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35644515"
---
# <a name="supported-matrix-for-this-release"></a>Podporované matice pro tuto verzi 
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
