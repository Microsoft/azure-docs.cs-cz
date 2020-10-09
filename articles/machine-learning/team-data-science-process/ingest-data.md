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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "76720533"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>Načtení dat do prostředí úložiště pro analýzu

Vědecké zpracování týmových dat vyžaduje, aby data byla v jednotlivých fázích ingestovaná nebo načtena do nejvhodnějšího způsobu. Cíle dat můžou zahrnovat Azure Blob Storage, databáze SQL Azure, SQL Server na virtuálních počítačích Azure, HDInsight (Hadoop), synapse Analytics a Azure Machine Learning. 

Následující články popisují, jak ingestovat data do různých cílových prostředí, kde se data ukládají a zpracovávají.

* Do/z [Azure Blob Storage](move-azure-blob.md)
* [SQL Server na virtuálním počítači Azure](move-sql-server-virtual-machine.md)
* Pro [Azure SQL Database](move-sql-azure.md)
* Do [tabulek podregistru](move-hive-tables.md)
* Do [dělených tabulek SQL](parallel-load-sql-partitioned-tables.md)
* Z [místních SQL Server](move-sql-azure-adf.md)

Technické a obchodní potřeby a také počáteční umístění, formát a velikost vašich dat budou určovat nejlepší plán příjmu dat. Nejedná se o běžný plán, který by měl mít několik kroků. Tato posloupnost úkolů může zahrnovat například zkoumání dat, předzpracování, vyčištění, snížení počtu vzorků a školení modelů.  Azure Data Factory je doporučeným prostředkem Azure pro orchestraci přesunu a transformace dat.
