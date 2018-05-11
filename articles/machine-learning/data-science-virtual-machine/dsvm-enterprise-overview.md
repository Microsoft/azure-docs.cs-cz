---
title: Úvod do virtuálního počítače vědecké účely dat na základě Team prostředí - Azure | Microsoft Docs
description: Vzory pro nasazení virtuálních počítačů vědecké účely Data jako podnikovém prostředí týmy.
keywords: přímý učení AI datové vědy nástroje, data vědecké účely virtuální počítač, geoprostorové analýzy, proces team dat vědecké účely
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 911d6484421cc9fddad0530bf8d9ab4f01d48bf8
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Datové vědy virtuálního počítače na základě analýzy týmu a prostředí AI 
[Datové vědy virtuální počítač](overview.md) (DSVM) poskytuje bohaté prostředí v cloudu Azure s předem připravené software pro analýzu AI a data. Tradičně DSVM byla použita jako jednotlivé analytics plochy a jednotlivých datových vědců získat produktivitu s tento sdílený představu o jejich prostředí předdefinovaných analytics. Jak velké analytics týmy naplánovat jejich prostředí analytics jejich datových vědců a vývojářů AI, jednu opakující motivů je pro sdílené analytics vývoj a experimentování infrastrukturu, která je spravovaných podle organizace IT zásad který také usnadňuje spolupráce a konzistence napříč celou datové vědy / analytics týmy. Taky umožňuje sdílené infrastruktury IT, abyste mohli lépe využívat analytics prostředí. Vědecké zpracování týmu dat / infrastruktury analýzy se také označuje některé organizace jako "Izolovaného Analytics" datových vědců a rychle pochopit dat, spusťte experimenty, ověřte předpoklad, sestavení prediktivní modely bezpečným způsobem, který umožňuje bez dopadu na produkční prostředí přitom má přístup k různým datovým prostředkům. 

Vzhledem k tomu, že DSVM pracuje na úrovni infrastrukturu Azure, správci IT můžou snadno konfigurovat DSVM provoz souladu se zásadami IT enterprise a nabízí úplnou flexibilitu při provádění různých sdílení architektury s přístupem k podniková data prostředky řízené způsobem. 

Tato část popisuje některé vzory a pokyny, které lze použít k nasazení DSVM jako infrastruktura vědecké účely týmu data.  Stavební bloky pro tyto vzory jsou využity přímo z Azure IaaS (infrastruktura jako služba) a jako takový platí pro všechny virtuální počítače Azure. Na použití těchto funkcí standardní infrastruktury Azure k virtuálnímu počítači vědecké účely dat je konkrétní zaměřená tuto řadu články. 

Některé klíče stavební kameny prostředí rozlehlé sítě team analytics jsou:

* [Automaticky škálovat fondu z datové vědy virtuálních počítačů](dsvm-pools.md)
* [Běžné identit a přístupu do pracovního prostoru z jakéhokoli z DSVMs ve fondu](dsvm-common-identity.md)
* [Zabezpečený přístup ke zdrojům dat](dsvm-secure-access-keys.md)
* Zásady správného řízení a zjišťování podnikové a otevřete datových sad

Z této série články pokyny a ukazatele jsou uvedeny v každé z těchto aspektů. [Azure architektura center](https://docs.microsoft.com/en-us/azure/architecture/) poskytuje podrobnější začátku do konce architektura pro vaši infrastrukturu analytics.  
