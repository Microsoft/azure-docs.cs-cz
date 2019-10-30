---
title: Načtení dat do prostředí Azure Storage – vědecký proces týmového zpracování dat
description: Přečtěte si, jak ingestovat data do různých cílových prostředí, kde se data ukládají a zpracovávají.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/09/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 515decfafe46ad0c1b5b90743688abc26a975903
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053258"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>Načtení dat do prostředí úložiště pro analýzu

Vědecké zpracování týmových dat vyžaduje, aby data byla v jednotlivých fázích procesu zpracovávána nebo načtena do různých různých úložných prostředí, aby je bylo možné zpracovat nebo analyzovat. K cílům dat běžně používaným ke zpracování patří Blob Storage Azure, databáze SQL Azure, SQL Server na virtuálním počítači Azure, HDInsight (Hadoop) a Azure Machine Learning. 

Následující články popisují, jak ingestovat data do různých cílových prostředí, kde se data ukládají a zpracovávají.

* Do/z [Azure Blob Storage](move-azure-blob.md)
* [SQL Server na virtuálním počítači Azure](move-sql-server-virtual-machine.md)
* Do [Azure SQL Database](move-sql-azure.md)
* Do [tabulek podregistru](move-hive-tables.md)
* Do [dělených tabulek SQL](parallel-load-sql-partitioned-tables.md)
* Z [místních SQL Server](move-sql-azure-adf.md)

Technické a obchodní potřeby, jakož i počáteční umístění, formát a velikost vašich dat, určují cílová prostředí, do kterých se musí data ingestovat, aby se dosáhlo cílů vaší analýzy. Scénář, který vyžaduje přesun dat mezi několika prostředími, je neobvyklý, aby bylo možné dosáhnout nejrůznějších úloh potřebných k vytvoření prediktivního modelu. Tato posloupnost úkolů může zahrnovat například zkoumání dat, předzpracování, vyčištění, snížení počtu vzorků a školení modelů.
