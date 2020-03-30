---
title: Nastavení prostředí datových věd v Azure – proces týmové datové vědy
description: Nastavte prostředí pro datové vědy v Azure pro použití v procesu vědecké ho týmového datového procesu.
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
ms.openlocfilehash: 6d1185e13397b3e466bd1e8d609a46f2f68bf390
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063935"
---
# <a name="set-up-data-science-environments-for-use-in-the-team-data-science-process"></a>Nastavení prostředí vědeckého zkoumání dat pro použití v rámci vědeckého zpracování týmových dat
Proces vědecké analýzy týmových dat používá různá prostředí datových věd pro ukládání, zpracování a analýzu dat. Patří mezi ně Azure Blob Storage, několik typů virtuálních počítačů Azure, HDInsight (Hadoop) clustery a pracovní prostory Azure Machine Learning. Rozhodnutí o prostředí, které se má použít, závisí na typu a množství dat, která mají být modelována, a cílovém cíli pro tato data v cloudu. 

* Pokyny k otázkám, které je třeba zvážit při provádění tohoto rozhodnutí, najdete v [tématu Plánování prostředí Azure Machine Learning Data Science .](plan-your-environment.md) 
* Katalog některých scénářů, se kterými se můžete setkat při provádění pokročilých analýz, najdete [v tématu Scénáře pro proces vědecké analýzy týmových dat](plan-sample-scenarios.md)

Následující články popisují, jak nastavit různá prostředí datové vědy používaná procesem vědecké vědy o týmových datech.

* [Účet úložiště Azure](../../storage/common/storage-account-create.md)
* [Cluster HDInsight (Hadoop)](customize-hadoop-cluster.md)
* [Pracovní prostor Azure Machine Learning Studio (klasický)](../studio/create-workspace.md)

**Microsoft Data Science Virtual Machine (DSVM)** je taky k dispozici jako image virtuálního počítače Azure (VM). Tento virtuální počítač je předinstalovaný a nakonfigurovaný s několika oblíbenými nástroji, které se běžně používají pro analýzu dat a strojové učení. DSVM je k dispozici na Windows i Linux. Další informace naleznete [v tématu Úvod do cloudového virtuálního počítače pro datové vědy pro Linux a Windows](../data-science-virtual-machine/overview.md).

Přečtěte si, jak vytvořit:

- [DSVM s Windows](../data-science-virtual-machine/provision-vm.md)
- [DSVM s Ubuntu](../data-science-virtual-machine/dsvm-ubuntu-intro.md)
- [DSVM s CentOS](../data-science-virtual-machine/linux-dsvm-intro.md)
