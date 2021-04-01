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
ms.openlocfilehash: 46e911ad6bdf5a478d46e425f8700740ece56c6e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "96452666"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>Načtení dat do prostředí úložiště pro analýzu

Vědecké zpracování týmových dat vyžaduje, aby data byla v jednotlivých fázích ingestovaná nebo načtena do nejvhodnějšího způsobu. Cíle dat můžou zahrnovat Azure Blob Storage, databáze SQL Azure, SQL Server na virtuálním počítači Azure, HDInsight (Hadoop), Azure synapse Analytics a Azure Machine Learning. 

Následující články popisují, jak ingestovat data do různých cílových prostředí, kde se data ukládají a zpracovávají.

* Do/z [Azure Blob Storage](move-azure-blob.md)
* [SQL Server na virtuálním počítači Azure](move-sql-server-virtual-machine.md)
* Pro [Azure SQL Database](move-sql-azure.md)
* Do [tabulek podregistru](move-hive-tables.md)
* Do [dělených tabulek SQL](parallel-load-sql-partitioned-tables.md)
* Z [místních SQL Server](move-sql-azure-adf.md)

Technické a obchodní potřeby a také počáteční umístění, formát a velikost vašich dat budou určovat nejlepší plán příjmu dat. Nejedná se o běžný plán, který by měl mít několik kroků. Tato posloupnost úkolů může zahrnovat například zkoumání dat, předzpracování, vyčištění, snížení počtu vzorků a školení modelů.  Azure Data Factory je doporučeným prostředkem Azure pro orchestraci přesunu a transformace dat.
