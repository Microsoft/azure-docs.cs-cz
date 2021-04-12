---
title: Nastavení prostředí pro datové vědy v Azure – Týmová věda – zpracování dat
description: Nastavte prostředí pro datové vědy v Azure pro použití v rámci vědeckého zpracování týmových dat.
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
ms.openlocfilehash: 557550627fe2c39571a848723e5d716324fee240
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "93321163"
---
# <a name="set-up-data-science-environments-for-use-in-the-team-data-science-process"></a>Nastavení prostředí vědeckého zkoumání dat pro použití v rámci vědeckého zpracování týmových dat
Vědecké zpracování týmových dat používá pro ukládání, zpracování a analýzu dat různé prostředí pro datové vědy. Zahrnují Azure Blob Storage, několik typů clusterů Azure Virtual Machine, HDInsight (Hadoop) a pracovní prostory Azure Machine Learning. Rozhodnutí o tom, které prostředí se má použít, závisí na typu a množství dat, která se mají modelovat, a na cílovém cíli pro tato data v cloudu. 

* Pokyny ohledně otázek, které je potřeba vzít v úvahu při rozhodování, najdete v tématu [plánování Azure Machine Learningho prostředí pro datové vědy](plan-your-environment.md). 
* Katalog některých scénářů, se kterými se můžete setkat při provádění pokročilých analýz, najdete v tématu [scénáře pro vědecké zpracování týmových dat](plan-sample-scenarios.md) .

Následující články popisují, jak nastavit různá prostředí pro datové vědy, která používá proces vědeckého zpracování dat týmu.

* [Účet úložiště Azure](../../storage/common/storage-account-create.md)
* [Cluster HDInsight (Hadoop)](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md)
* [Pracovní prostor Azure Machine Learning Studio (klasický)](../classic/create-workspace.md)

**Microsoft Data Science Virtual Machine (DSVM)** je také k dispozici jako image virtuálních počítačů Azure. Tento virtuální počítač je předem nainstalovaný a nakonfigurovaný pomocí několika oblíbených nástrojů, které se běžně používají pro analýzu dat a strojové učení. DSVM je k dispozici v systémech Windows i Linux. Další informace najdete v tématu [Úvod do cloudového Data Science Virtual Machine pro Linux a Windows](../data-science-virtual-machine/overview.md).

Přečtěte si, jak vytvořit:

- [DSVM s Windows](../data-science-virtual-machine/provision-vm.md)
- [DSVM s Ubuntu](../data-science-virtual-machine/dsvm-ubuntu-intro.md)
- [DSVM s CentOS](../data-science-virtual-machine/release-notes.md)