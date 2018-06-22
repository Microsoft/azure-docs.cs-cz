---
title: Úvod do prostředí na základě dat vědecké účely virtuální počítač team - Azure | Microsoft Docs
description: Vzory pro nasazení virtuálního počítače vědecké účely dat v podnikovém prostředí týmu.
keywords: přímý učení AI datové vědy nástroje, data vědecké účely virtuální počítač, geoprostorové analýzy, proces team dat vědecké účely
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 8486b0be1fb5e1385da3c7ad55f6410a1059df93
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309244"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Analýza dat týmového vědecké účely virtuální počítač a prostředí AI 
[Datové vědy virtuální počítač](overview.md) (DSVM) poskytuje bohaté prostředí v platformy Azure s předem software pro umělé intelligence (AI) a data analýzy. 

Obvyklým DSVM byla použita jako ploše jednotlivých analytics. Jednotlivých datových vědců získáte produktivitu s tento sdílený představu o jejich prostředí předem analytics. Velké analytics týmy naplánovat jejich prostředí analytics jejich datových vědců a vývojářů AI, jednu opakující motivů je sdílený analytics infrastrukturu pro vývoj a experimenty. Tato infrastruktura je spravovaných podle organizace IT zásad, které také usnadňují spolupráci a konzistenci mezi týmy vědecké účely nebo analýzy dat. 

Taky umožňuje sdílené infrastruktury IT, abyste mohli lépe využívat analytics prostředí. Některé organizace volání infrastruktury vědecké účely nebo analýzy dat týmu "analytics izolovaného prostoru." Umožňuje datových vědců pro přístup k různým datovým prostředkům rychle pochopit dat, spustit experimenty, ověření hypotézy a sestavení prediktivní modely, aniž by to ovlivňovalo produkční prostředí. 

Protože DSVM pracuje na úrovni infrastrukturu Azure, správci IT můžou snadno konfigurovat DSVM provoz souladu se zásadami IT podniku. DSVM nabízí úplnou flexibilitu při provádění různých sdílení architektury s přístupem k prostředkům podniková data řízenou. 

Tato část popisuje některé vzory a pokyny, které můžete použít k nasazení DSVM jako infrastruktura vědecké účely týmu data. Stavební bloky pro tyto vzory pocházet z infrastrukturu Azure jako služba (IaaS), takže se vztahují na všechny virtuální počítače Azure. Na použití těchto funkcí standardní infrastruktury Azure k virtuálnímu počítači vědecké účely dat je zaměřená tuto řadu články. 

Některé klíče stavební kameny prostředí rozlehlé sítě team analytics jsou:

* [Fondu došlo z datové vědy virtuálních počítačů](dsvm-pools.md)
* [Běžné identity a přístup k pracovním prostoru z jakéhokoli z DSVMs ve fondu](dsvm-common-identity.md)
* [Zabezpečený přístup ke zdrojům dat](dsvm-secure-access-keys.md)


Tato řada článků poskytuje pokyny a ukazatele pro každou z předchozí položky. Nezahrnuje všechny aspekty a požadavky při nasazení DSVM v konfiguracích velký podnik. Zde je další dokumentace k Azure, který můžete použít při implementaci DSVM instance ve vašem podniku: 

* [Zabezpečení sítě](https://docs.microsoft.com/azure/security/azure-network-security)
* [Monitorování](https://docs.microsoft.com/azure/virtual-machines/windows/monitor) a [správy](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [Protokolování a auditování](https://docs.microsoft.com/azure/security/azure-log-audit)
* [Řízení přístupu na základě rolí](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [Nastavení zásad a vynucení](https://docs.microsoft.com/azure/azure-policy/azure-policy-introduction)
* [Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware)
* [Šifrování](https://docs.microsoft.com/azure/virtual-machines/windows/encrypt-disks)
* [Data zjišťování a zásad správného řízení](https://docs.microsoft.com/azure/data-catalog/)

[Azure architektura Center](https://docs.microsoft.com/en-us/azure/architecture/) poskytuje podrobné architektura klient server a vzory pro vytváření a správě infrastruktury cloudu analytics. 
