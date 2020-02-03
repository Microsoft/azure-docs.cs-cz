---
title: Načtení dat do Azure Storage prostředí – vědecký proces týmového zpracování dat
description: Přečtěte si, jak ingestovat data do různých cílových prostředí, kde se data ukládají a zpracovávají.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: f8eab59d810fb825dbebf80d01d8efd2dd0a9841
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720533"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>Načtení dat do prostředí úložiště pro analýzu

Vědecké zpracování týmových dat vyžaduje, aby data byla v jednotlivých fázích ingestovaná nebo načtena do nejvhodnějšího způsobu. Cíle dat můžou zahrnovat Azure Blob Storage, databáze SQL Azure, SQL Server na virtuálních počítačích Azure, HDInsight (Hadoop), synapse Analytics a Azure Machine Learning. 

Následující články popisují, jak ingestovat data do různých cílových prostředích, kde je data uložena a zpracována.

* Do/z [Azure Blob Storage](move-azure-blob.md)
* [SQL Server na virtuálním počítači Azure](move-sql-server-virtual-machine.md)
* Pro [Azure SQL Database](move-sql-azure.md)
* Do [tabulek podregistru](move-hive-tables.md)
* Do [dělených tabulek SQL](parallel-load-sql-partitioned-tables.md)
* Z [místních SQL Server](move-sql-azure-adf.md)

Technické a obchodní potřeby a také počáteční umístění, formát a velikost vašich dat budou určovat nejlepší plán příjmu dat. Nejedná se o běžný plán, který by měl mít několik kroků. Toto pořadí úloh může obsahovat například zkoumání dat, předběžné zpracování, čištění, vzorkování dolů a cvičení modelu.  Azure Data Factory je doporučeným prostředkem Azure pro orchestraci přesunu a transformace dat.
