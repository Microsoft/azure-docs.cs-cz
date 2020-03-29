---
title: Načítání dat do prostředí úložiště Azure – proces vědecké ho vědeckého oddělení týmových dat
description: Přečtěte si, jak ingestovat data do různých cílových prostředí, kde jsou data uložena a zpracována.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720533"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>Načtení dat do prostředí úložiště pro analýzu

Proces vědecké ho procesu s týmová data vyžaduje, aby data byla přijata nebo načtena nejvhodnějším způsobem v každé fázi. Mezi datové cíle patří Azure Blob Storage, databáze SQL Azure, SQL Server na virtuálním počítači Azure, HDInsight (Hadoop), Synapse Analytics a Azure Machine Learning. 

Následující články popisují, jak ingestovat data do různých cílových prostředí, kde jsou data uložena a zpracována.

* Do/z [úložiště objektů blob Azure](move-azure-blob.md)
* Na [SQL Server na virtuálním počítači Azure](move-sql-server-virtual-machine.md)
* Do [databáze Azure SQL](move-sql-azure.md)
* Do [tabulek Úlu](move-hive-tables.md)
* K [rozděleným tabulkám SQL](parallel-load-sql-partitioned-tables.md)
* Z [místního sql serveru](move-sql-azure-adf.md)

Nejlepší plán pro přijím dat určují technické a obchodní potřeby, stejně jako počáteční umístění, formát a velikost dat. Není neobvyklé, že nejlepší plán má několik kroků. Tato posloupnost úloh může zahrnovat například zkoumání dat, předběžné zpracování, čištění, vzorkování dolů a trénování modelu.  Azure Data Factory je doporučený prostředek Azure pro orchestraci přesunu a transformace dat.
