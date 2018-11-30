---
title: Načtení dat do prostředí úložiště Azure pro účely analýzy | Dokumentace Microsoftu
description: Přesun dat z a do služby Azure Blob Storage
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/09/2017
ms.author: tdsp
ms.custom: (previous author=deguhath, ms.author=deguhath)
ms.openlocfilehash: f73488947b85c97d00257faf8907055227570cb6
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52441712"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>Načtení dat do prostředí úložiště pro analýzu

Vědecké zpracování týmových dat vyžaduje, aby se data ingestují nebo načtena do celé řady různých úložiště prostředí ji zpracovat nebo analyzovat nejvhodnějším způsobem v každé fázi procesu. Cíle dat, běžně používá pro zpracování patří Azure Blob Storage, databáze SQL Azure, SQL Server na virtuálním počítači Azure, HDInsight (Hadoop) a Azure Machine Learning. 

Následující články popisují, jak ingestovat data do různých cílových prostředích, kde je data uložena a zpracována.

* Z/[úložiště objektů Blob v Azure](move-azure-blob.md)
* K [systému SQL Server na virtuálním počítači Azure](move-sql-server-virtual-machine.md)
* K [Azure SQL database](move-sql-azure.md)
* K [tabulky Hive](move-hive-tables.md)
* K [dělené tabulky SQL](parallel-load-sql-partitioned-tables.md)
* Z [místního SQL serveru](move-sql-azure-adf.md)

Naformátujte technické a obchodní potřeby, stejně jako počáteční umístění a velikost dat určuje cílových prostředí, ve které je potřeba data ingestování k dosažení cílů analýzy. Není, scénář tak, aby vyžadovala data přesunout mezi několika prostředích k dosažení různých úloh potřebných k vytvoření prediktivního modelu. Toto pořadí úloh může obsahovat například zkoumání dat, předběžné zpracování, čištění, vzorkování dolů a cvičení modelu.
