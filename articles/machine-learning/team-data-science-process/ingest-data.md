---
title: Načtení dat do prostředí Azure storage – vědecké zpracování týmových dat
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
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: b53ece6cd2e61fa6f6340b7bda3021ee7d9d767c
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53139703"
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
