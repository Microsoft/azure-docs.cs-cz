---
title: Úvod do prostředí týmového virtuální počítač pro datové vědy – Azure | Dokumentace Microsoftu
description: Vzory pro virtuální počítač pro datové vědy v podnikovém prostředí týmu nasazení.
keywords: obsáhlý learning, AI, nástrojů pro datové vědy, virtuální počítač pro datové vědy, geoprostorové analýzy, vědecké zpracování týmových dat
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
ms.openlocfilehash: ea8d53ee71e9272167a045e2ea1780828f974d30
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45573587"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Datové vědy virtuálního počítače na základě týmu analýzy a AI prostředí 
[Virtuální počítač pro datové vědy](overview.md) (DSVM) poskytuje bohaté prostředí na platformě Azure s využitím předem připravených softwarem umělé inteligence (AI) a analýzy dat. 

Tradičně datové VĚDY slouží jako jednotlivé analytický desktop. Odborníci přes data jednotlivých získají produktivity pomocí sdílených vyskytují na předem připravených analýzy prostředí. Analýzy velkých týmů plánování prostředí analytics pro odborníky přes data a vývojářům AI, jedním z opakované motivy je sdílený analytické infrastruktury pro vývoj a experimentování. Tato infrastruktura spravuje v podnikovém IT zásady, které také usnadňují spolupráci a konzistence napříč týmy datové vědy a analýzy. 

Sdílená infrastruktura taky umožňuje IT mohli lépe využívat analýzy prostředí. Některé organizace volání infrastruktury vědy a analýzy dat na základě týmu "analytics izolovaném prostoru." Umožňuje datovým vědcům pro přístup k různým datové assety rychle pochopit data, spouštět experimenty, ověřit hypotézy a vytvářet prediktivní modely, aniž by to mělo dopad na produkční prostředí. 

Protože datové VĚDY funguje na úrovni infrastruktury Azure, správci IT můžou snadno konfigurovat DSVM provozovat souladu se zásadami IT rozlehlé sítě. Datové VĚDY poskytuje úplnou flexibilitu při provádění různých sdílení architektury s přístupem k podnikovým datovým assetům řízené způsobem. 

Tato část popisuje některé modely a pokyny, které můžete použít k nasazení datové VĚDY jako infrastruktura týmu data science. Stavební bloky pro tyto vzory pocházejí z infrastruktury Azure jako služba (IaaS), tak se vztahují na všechny virtuální počítače Azure. Sada Tato série článků je zaměřena na použití těchto funkcí standardní infrastruktury Azure pro virtuální počítač pro datové vědy. 

Zde jsou některé z nejdůležitějších stavebních bloků analýzy týmu podnikovém prostředí:

* [Došlo ke ztrátě schopnosti fondu z virtuálního počítače pro datové vědy](dsvm-pools.md)
* [Společné identity a přístup k pracovnímu prostoru žádné datové ve fondu](dsvm-common-identity.md)
* [Zabezpečený přístup ke zdrojům dat](dsvm-secure-access-keys.md)


Tato série článků obsahuje pokyny a odkazy pro každý z předchozí položky. Nezahrnuje všechny aspekty a požadavky při nasazení v rozlehlých konfigurace DSVM. Tady je další dokumentace ke službě Azure, který vám pomůže při implementaci DSVM instancí ve vašem podniku: 

* [Zabezpečení sítě](https://docs.microsoft.com/azure/security/azure-network-security)
* [Monitorování](https://docs.microsoft.com/azure/virtual-machines/windows/monitor) a [správy](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [Protokolování a auditování](https://docs.microsoft.com/azure/security/azure-log-audit)
* [Řízení přístupu na základě rolí](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [Nastavení zásad a vynucení](https://docs.microsoft.com/azure/azure-policy/azure-policy-introduction)
* [Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware)
* [Šifrování](https://docs.microsoft.com/azure/virtual-machines/windows/encrypt-disks)
* [Data zjišťování a zásad správného řízení](https://docs.microsoft.com/azure/data-catalog/)

[Azure Architecture Center](https://docs.microsoft.com/azure/architecture/) poskytuje podrobné architekturu začátku do konce a vzory pro vytváření a správu infrastruktury cloudové analýzy. 
