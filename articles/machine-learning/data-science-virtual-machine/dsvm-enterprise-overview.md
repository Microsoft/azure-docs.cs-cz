---
title: Team Analytics a prostředí AI
titleSuffix: Azure Data Science Virtual Machine
description: Vzory pro nasazení Data Science VM v podnikovém týmovém prostředí.
keywords: obsáhlý Learning, AI, nástroje pro datové vědy, virtuální počítač pro datové vědy, geoprostorové analýzy, vědecké zpracování týmových dat
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 05/08/2018
ms.openlocfilehash: 538802cc2129a8e8f379c8b569bd1f8696097dab
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93314176"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Team Analytics a prostředí AI založené na Data Science Virtual Machine 
[Data Science Virtual Machine](overview.md) (DSVM) poskytuje bohatý prostředí na platformě Azure s předem připraveným softwarem pro umělou Intelligence (AI) a analýzou dat.

Tradičně se DSVM použil jako individuální analytická plocha. Tato sdílená a předem sestavená analytická prostředí získávají jednotlivá specialista na data. Jelikož týmy pro velké analýzy plánují prostředí pro odborníky na data a pro vývojáře AI, je jedním z opakovaných motivů sdílená analytická infrastruktura pro vývoj a experimentování. Tato infrastruktura se spravuje v souladu s podnikovými zásadami IT, která také usnadňuje spolupráci a konzistenci napříč týmy pro datové vědy a analýzy.

Sdílená infrastruktura umožňuje lepší využití IT prostředí pro analýzu. Některé organizace volají v rámci *analytického izolovaného prostoru* pro datové vědy a analytické infrastruktury. Umožňuje pracovníkům dat přistupovat k různým datovým assetům a rychle pochopit data. Toto prostředí izolovaného prostoru také pomáhá pracovníkům dat provádět experimenty, ověřovat hypotézy a sestavovat prediktivní modely, aniž by to ovlivnilo produkční prostředí.

Vzhledem k tomu, že DSVM funguje na úrovni infrastruktury Azure, správci IT můžou snadno nakonfigurovat DSVM tak, aby fungoval v souladu se zásadami IT v podniku. DSVM nabízí plnou flexibilitu při implementaci různých architektur sdílení a zároveň nabízí přístup k podnikovým datovým prostředkům řízeným způsobem.

Tato část popisuje některé vzory a pokyny, které můžete použít k nasazení DSVM jako týmové infrastruktury pro datové vědy. Vzhledem k tomu, že stavební bloky pro tyto vzory pocházejí z infrastruktury Azure jako služby (IaaS), vztahují se na všechny virtuální počítače Azure. Tato série článků se zaměřuje na použití těchto standardních funkcí infrastruktury Azure na DSVM.

Mezi klíčové stavební bloky prostředí Enterprise Team Analytics patří:

* [Fond DSVMs s autoškálou](dsvm-pools.md)
* [Společná identita a přístup k pracovnímu prostoru z libovolného DSVMs ve fondu](dsvm-common-identity.md)
* [Zabezpečený přístup ke zdrojům dat](dsvm-secure-access-keys.md)


Tato série poskytuje pokyny a ukazatele pro každé z předchozích témat. Nezabývá se všemi důležitými informacemi a požadavky pro nasazení DSVMs ve velkých podnikových konfiguracích. Tady jsou některé další prostředky Azure, které můžete použít při implementaci DSVM instancí v podniku:

* [Zabezpečení sítě](../../security/fundamentals/network-overview.md)
* [Monitorování](../../azure-monitor/insights/monitor-vm-azure.md) a [Správa](../../virtual-machines/maintenance-and-updates.md?bc=%252fazure%252fvirtual-machines%252fwindows%252fbreadcrumb%252ftoc.json%252c%252fazure%252fvirtual-machines%252fwindows%252fbreadcrumb%252ftoc.json&toc=%252fazure%252fvirtual-machines%252fwindows%252ftoc.json%253ftoc%253d%252fazure%252fvirtual-machines%252fwindows%252ftoc.json)
* [Protokolování a auditování](../../security/fundamentals/log-audit.md)
* [Řízení přístupu na základě role Azure (Azure RBAC)](../../role-based-access-control/overview.md)
* [Nastavení zásad a vynucení](../../governance/policy/overview.md)
* [Antimalwarové](../../security/fundamentals/antimalware.md)
* [Šifrování](../../virtual-machines/windows/disk-encryption-overview.md)
* [Zjišťování a řízení dat](../../data-catalog/index.yml)

A konečně [cetrum architektury Azure](/azure/architecture/) poskytuje detailní ucelenou architekturu a modely pro sestavování a správu cloudové infrastruktury pro analýzu.