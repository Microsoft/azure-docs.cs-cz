---
title: Podporované kombinace prostředí provádění a dat pro Azure Machine Learning Data přípravy | Microsoft Docs
description: Tento dokument obsahuje úplný seznam podporovaných kombinací jiné moduly runtime a zdroje dat pro Azure Machine Learning Data přípravy
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: ee1379995dffd8aebbd71757c06e06ea43561794
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34830441"
---
# <a name="supported-matrix-for-this-release"></a>Podporované matice pro tuto verzi 
Pokud váš kód načte data pomocí Azure Machine Learning Data zdroje nebo Azure Machine Learning Data přípravy, získávání buď Pandas nebo dataframe Spark, následující kombinace experimentu výpočetní prostředí a data umístění jsou podporovány:

|     |Místní soubory  |Azure Blob Storage  |Databáze systému SQL Server ***  |
|---------|---------|---------|---------|---------|
|Místní Python    |     Podporováno    |Nepodporuje se         | Nepodporuje se        |         |
|Python docker (virtuální počítač s Linuxem)     |Podporované v souborech projektu pouze *         | Nepodporuje se        |        Nepodporuje se |         |
|PySpark docker (virtuální počítač s Linuxem)     |Podporované v souborech projektu pouze *     |Podporováno         | Podporuje se**        |         |
|Python virtuálního počítače Azure dat vědecké účely     |Podporované v souborech projektu pouze *         |Nepodporuje se         |Nepodporuje se         |         |
|PySPark virtuální počítač Azure dat vědecké účely     | Podporované v souborech projektu pouze *        |Nepodporuje se         |Nepodporuje se         |         |
|Azure HDInsight PySpark     | Nepodporuje se        |Podporováno         |Podporuje se**         |         |
|Azure HDInsight Python     | Nepodporuje se        | Nepodporuje se        | Nepodporuje se        |         |

Azure Data Lake Store se aktuálně nepodporuje pro všechny výpočetní cíl.

* Pokud místní cesty souborů se používá, jsou soubory v projektu zkopírován do výpočetní prostředí a přečtěte si existuje. Nejsou zkopíruje soubory mimo projekt a cesty už vyřešit ve výpočetním prostředí. Zvažte použití nahrazení zdroje dat tak, aby váš kód můžete použít místní soubor při místním spuštění. Potom přepněte na objekt blob úložiště Azure pro jinou konfiguraci spuštění. Také můžete vzorkování podporu pro zdroje dat pro správu běží na velkých objemů dat pouze při některých konfiguracích spuštění.

** Používá Maven JDBC SQL Server ovladač 6.2.1. Je nutné zajistit, že tento balíček (nebo kompatibilní jeden) je součástí souboru spark_dependencies.yml pro výpočetní prostředí.

Podporuje Azure SQL Database nebo SQL Server, pokud že databázi lze přejít z výpočetním prostředí. 
