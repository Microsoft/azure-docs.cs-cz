---
title: Analýza týmu a prostředí ai
titleSuffix: Azure Data Science Virtual Machine
description: Vzory pro nasazení virtuálního virtuálního ms datové vědy v podnikovém týmovém prostředí.
keywords: hluboké učení, AI, nástroje pro datovou vědu, virtuální stroj pro datové vědy, geoprostorová analytika, proces vědecké analýzy týmových dat
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 05/08/2018
ms.openlocfilehash: 06d05d6d410af13bfbe85f3cb66523c1d48cb77c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "70195661"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Týmová analýza založená na virtuálních počítačích pro datové vědy a prostředí ai 
[Virtuální počítač pro datové vědy](overview.md) (DSVM) poskytuje bohaté prostředí na platformě Azure s předem vytvořeným softwarem pro umělou inteligenci (AI) a analýzu dat.

DSVM se tradičně používá jako samostatná analytická plocha. Jednotliví datoví vědci získávají produktivitu díky tomuto sdílenému, předem vytvořenému analytickému prostředí. Vzhledem k tomu, že velké analytické týmy plánují prostředí pro své datové vědce a vývojáře ai, jedním z opakujících se motivů je sdílená analytická infrastruktura pro vývoj a experimentování. Tato infrastruktura je spravována v souladu s podnikovými it zásadami, které také usnadňují spolupráci a konzistenci napříč týmy pro datovou vědu a analýzu.

Sdílená infrastruktura umožňuje lepší využití IT v analytickém prostředí. Některé organizace nazývají týmovou infrastrukturu pro datovou vědu a analýzu *analytickou infrastrukturou pro analýzu .* Umožňuje datovým vědcům přístup k různým datovým prostředkům, aby rychle porozuměli datům. Toto prostředí izolovaného prostoru také pomáhá datovým vědcům spouštět experimenty, ověřovat hypotézy a vytvářet prediktivní modely bez ovlivnění produkčního prostředí.

Vzhledem k tomu, že Služba DSVM pracuje na úrovni infrastruktury Azure, mohou správci IT snadno nakonfigurovat zařízení DSVM tak, aby fungovalo v souladu se zásadami IT podniku. DSVM nabízí plnou flexibilitu při implementaci různých architektur sdílení a zároveň nabízí přístup k podnikovým datovým aktivům kontrolovaným způsobem.

Tato část popisuje některé vzory a pokyny, které můžete použít k nasazení DSVM jako infrastruktury založené na týmové datové vědy. Vzhledem k tomu, že stavební bloky pro tyto vzory pocházejí z infrastruktury Azure jako služby (IaaS), platí pro všechny virtuální počítače Azure. Tato řada článků se zaměřuje na použití těchto standardních funkcí infrastruktury Azure pro DSVM.

Mezi klíčové stavební kameny prostředí analýzy podnikových týmů patří:

* [Fond automatických škálování dsvms](dsvm-pools.md)
* [Společná identita a přístup k pracovnímu prostoru z některého z dsvms ve fondu](dsvm-common-identity.md)
* [Zabezpečený přístup ke zdrojům dat](dsvm-secure-access-keys.md)


Tato řada poskytuje pokyny a ukazatele pro každé z předchozích témat. Nezahrnuje všechny důležité a spekty a požadavky na nasazení dsvms ve velkých podnikových konfiguracích. Tady jsou některé další prostředky Azure, které můžete použít při implementaci instancí DSVM ve vašem podniku:

* [Zabezpečení sítě](https://docs.microsoft.com/azure/security/fundamentals/network-security)
* [Monitorování](https://docs.microsoft.com/azure/virtual-machines/windows/monitor) a [řízení](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [Protokolování a auditování](https://docs.microsoft.com/azure/security/fundamentals/log-audit)
* [Řízení přístupu založené na rolích](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [Nastavení a vynucení zásad](../../governance/policy/overview.md)
* [Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
* [Šifrování](https://docs.microsoft.com/azure/virtual-machines/windows/encrypt-disks)
* [Zjišťování a zásady správného řízení dat](https://docs.microsoft.com/azure/data-catalog/)

A konečně, [Centrum architektury Azure](https://docs.microsoft.com/azure/architecture/) poskytuje podrobnou komplexní architekturu a modely pro vytváření a správu cloudové analytické infrastruktury.