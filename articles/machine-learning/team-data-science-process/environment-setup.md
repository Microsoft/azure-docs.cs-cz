---
title: Nastavení prostředí vědeckého zkoumání dat v Azure | Dokumentace Microsoftu
description: Nastavení data science prostředí v Azure pro použití v vědecké zpracování týmových dat.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: 481cfa6a-7ea3-46ac-b0f9-2e3982c37153
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: deguhath
ms.openlocfilehash: 6c28a64830afeb19c6a9264888b296c3b99990d1
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2018
ms.locfileid: "39262561"
---
# <a name="set-up-data-science-environments-for-use-in-the-team-data-science-process"></a>Nastavení prostředí vědeckého zkoumání dat pro použití v rámci vědeckého zpracování týmových dat
Proces vědecké účely Team dat používá různé vědě prostředí datových pro úložiště, zpracování a analýzu dat. Patří mezi ně Azure Blob Storage, několik typů virtuálních počítačů Azure, clustery HDInsight (Hadoop) a pracovní prostory Azure Machine Learning. Rozhodnutí o prostředí, ve kterém chcete použít závisí na typu a množství dat, modelovat a cílového místa pro tato data v cloudu. 

* Pokyny na otázky, které je při tomto rozhodnutí zvažte najdete v tématu [plánování si Azure Machine Learning prostředí pro datové vědy](plan-your-environment.md). 
* Katalog některé scénáře, se mohou vyskytnout při provádění pokročilých analýz, naleznete v tématu [scénáře pro vědecké zpracování týmových dat](plan-sample-scenarios.md)

Tato nabídka odkazy na témata, která popisují, jak vytvořit různé prostředí pro datové vědy používat vědecké zpracování týmových dat.

[!INCLUDE [data-science-environment-setup](../../../includes/cap-setup-environments.md)]

**Microsoft Data virtuálního počítače VĚDY** jsou také dostupné jako image virtuálních počítačů (VM) Azure. Tento virtuální počítač je předem nainstalovaný a nakonfigurovaný s několika oblíbenými nástroji, které se běžně používají pro analýzu dat a strojové učení. Datové VĚDY je dostupná ve Windows a Linuxu. Další informace najdete v tématu [Úvod do cloudové virtuální počítač pro datové vědy pro Linux a Windows](../data-science-virtual-machine/overview.md).

Zjistěte, jak vytvořit:

- [Windows DSVM](../data-science-virtual-machine/provision-vm.md)
- [Ubuntu DSVM](../data-science-virtual-machine/dsvm-ubuntu-intro.md)
- [CentOS DSVM](../data-science-virtual-machine/linux-dsvm-intro.md)
- [Hloubkové učení virtuálního počítače](../data-science-virtual-machine/provision-deep-learning-dsvm.md)
