---
title: Úvod do prostředí týmového virtuální počítač pro datové vědy – Azure | Dokumentace Microsoftu
description: Vzory pro virtuální počítač pro datové vědy v podnikovém prostředí týmu nasazení.
keywords: obsáhlý learning, AI, nástrojů pro datové vědy, virtuální počítač pro datové vědy, geoprostorové analýzy, vědecké zpracování týmových dat
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: vijetaj
ms.openlocfilehash: 5f34498fbdacf7fc6e62788913c795ab70ceef23
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2019
ms.locfileid: "69991617"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Datové vědy virtuálního počítače na základě týmu analýzy a AI prostředí 
[Data Science Virtual Machine](overview.md) (DSVM) poskytuje bohatý prostředí na platformě Azure s předem připraveným softwarem pro umělou Intelligence (AI) a analýzou dat.

Tradičně datové VĚDY slouží jako jednotlivé analytický desktop. Tato sdílená a předem sestavená analytická prostředí získávají jednotlivá specialista na data. Jelikož týmy pro velké analýzy plánují prostředí pro odborníky na data a pro vývojáře AI, je jedním z opakovaných motivů sdílená analytická infrastruktura pro vývoj a experimentování. Tato infrastruktura se spravuje v souladu s podnikovými zásadami IT, která také usnadňuje spolupráci a konzistenci napříč týmy pro datové vědy a analýzy.

Sdílená infrastruktura umožňuje lepší využití IT prostředí pro analýzu. Některé organizace volají v rámci *analytického izolovaného prostoru*pro datové vědy a analytické infrastruktury. Umožňuje pracovníkům dat přistupovat k různým datovým assetům a rychle pochopit data. Toto prostředí izolovaného prostoru také pomáhá pracovníkům dat provádět experimenty, ověřovat hypotézy a sestavovat prediktivní modely, aniž by to ovlivnilo produkční prostředí.

Protože datové VĚDY funguje na úrovni infrastruktury Azure, správci IT můžou snadno konfigurovat DSVM provozovat souladu se zásadami IT rozlehlé sítě. DSVM nabízí plnou flexibilitu při implementaci různých architektur sdílení a zároveň nabízí přístup k podnikovým datovým prostředkům řízeným způsobem.

Tato část popisuje některé modely a pokyny, které můžete použít k nasazení datové VĚDY jako infrastruktura týmu data science. Vzhledem k tomu, že stavební bloky pro tyto vzory pocházejí z infrastruktury Azure jako služby (IaaS), vztahují se na všechny virtuální počítače Azure. Tato série článků se zaměřuje na použití těchto standardních funkcí infrastruktury Azure na DSVM.

Mezi klíčové stavební bloky prostředí Enterprise Team Analytics patří:

* [Fond DSVMs s autoškálou](dsvm-pools.md)
* [Společné identity a přístup k pracovnímu prostoru žádné datové ve fondu](dsvm-common-identity.md)
* [Zabezpečený přístup ke zdrojům dat](dsvm-secure-access-keys.md)


Tato série poskytuje pokyny a ukazatele pro každé z předchozích témat. Nezabývá se všemi důležitými informacemi a požadavky pro nasazení DSVMs ve velkých podnikových konfiguracích. Tady jsou některé další prostředky Azure, které můžete použít při implementaci DSVM instancí v podniku:

* [Zabezpečení sítě](https://docs.microsoft.com/azure/security/fundamentals/network-security)
* [Monitorování](https://docs.microsoft.com/azure/virtual-machines/windows/monitor) a [správy](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [Protokolování a auditování](https://docs.microsoft.com/azure/security/fundamentals/log-audit)
* [Řízení přístupu na základě rolí](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [Nastavení zásad a vynucení](../../governance/policy/overview.md)
* [Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
* [Šifrování](https://docs.microsoft.com/azure/virtual-machines/windows/encrypt-disks)
* [Data zjišťování a zásad správného řízení](https://docs.microsoft.com/azure/data-catalog/)

A konečně [cetrum architektury Azure](https://docs.microsoft.com/azure/architecture/) poskytuje detailní ucelenou architekturu a modely pro sestavování a správu cloudové infrastruktury pro analýzu.
