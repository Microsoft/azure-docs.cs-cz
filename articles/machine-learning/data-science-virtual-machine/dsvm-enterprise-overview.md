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
ms.openlocfilehash: 6a755ef4d933046377a6a25be76655b44f4bf508
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Datové vědy virtuálního počítače na základě analýzy týmu a prostředí AI 
[Datové vědy virtuální počítač](overview.md) (DSVM) poskytuje bohaté prostředí v cloudu Azure s předem připravené software pro analýzu AI a data. Tradičně DSVM byla použita jako jednotlivé analytics plochy a jednotlivých datových vědců získat produktivitu s tento sdílený představu o jejich prostředí předdefinovaných analytics. Jak velké analytics týmy naplánovat jejich prostředí analytics jejich datových vědců a vývojářů AI, jednu opakující motivů je pro sdílené analytics vývoj a experimentování infrastrukturu, která je spravovaných podle organizace IT zásad který také usnadňuje spolupráce a konzistence napříč celou datové vědy / analytics týmy. Taky umožňuje sdílené infrastruktury IT, abyste mohli lépe využívat analytics prostředí. Vědecké zpracování týmu dat / infrastruktury analýzy se také označuje některé organizace jako "Izolovaného Analytics" datových vědců a rychle pochopit dat, spusťte experimenty, ověřte předpoklad, sestavení prediktivní modely bezpečným způsobem, který umožňuje bez dopadu na produkční prostředí přitom má přístup k různým datovým prostředkům. 

Vzhledem k tomu, že DSVM pracuje na úrovni infrastrukturu Azure, správci IT můžou snadno konfigurovat DSVM provoz souladu se zásadami IT enterprise a nabízí úplnou flexibilitu při provádění různých sdílení architektury s přístupem k podniková data prostředky řízené způsobem. 

Tato část popisuje některé vzory a pokyny, které lze použít k nasazení DSVM jako infrastruktura vědecké účely týmu data.  Stavební bloky pro tyto vzory jsou využity přímo z Azure IaaS (infrastruktura jako služba) a jako takový platí pro všechny virtuální počítače Azure. Na použití těchto funkcí standardní infrastruktury Azure k virtuálnímu počítači vědecké účely dat je konkrétní zaměřená tuto řadu články. 

Některé klíče stavební kameny prostředí rozlehlé sítě team analytics jsou:

* [Automaticky škálovat fondu z datové vědy virtuálních počítačů](dsvm-pools.md)
* [Běžné identit a přístupu do pracovního prostoru z jakéhokoli z DSVMs ve fondu](dsvm-common-identity.md)
* [Zabezpečený přístup ke zdrojům dat](dsvm-secure-access-keys.md)


Z této série články pokyny a ukazatele jsou uvedeny v každé z těchto výše aspekty. Samozřejmě je několik další aspekty a požadavky při nasazení v rozlehlých sítí konfigurace, které je přímo, ale nezabývá tuto řadu články DSVM. Zde jsou některé další aspekty a ukazatele odkazující na dokumentaci obecné Azure, který lze snadno použít při implementaci v rámci instancí DSVM ve vašem podniku. 

* [Zabezpečení sítě](https://docs.microsoft.com/azure/security/azure-network-security)
* [Monitorování](https://docs.microsoft.com/azure/virtual-machines/windows/monitor) a [správy](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [Protokolování a auditování](https://docs.microsoft.com/azure/security/azure-log-audit)
* [Řízení přístupu na základě rolí](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [Nastavení zásad a vynucení](https://docs.microsoft.com/azure/azure-policy/azure-policy-introduction)
* [Proti malwaru](https://docs.microsoft.com/azure/security/azure-security-antimalware)
* [Šifrování](https://docs.microsoft.com/azure/virtual-machines/windows/encrypt-disks)
* [Data zjišťování a zásad správného řízení](https://docs.microsoft.com/azure/data-catalog/)

[Center Azure architektura](https://docs.microsoft.com/en-us/azure/architecture/) je také skvělým zdrojem, který poskytuje podrobné začátku do konce architektura a vzory pro vytváření a správě infrastruktury cloudu na základě analýzy. 
