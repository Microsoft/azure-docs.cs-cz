---
title: Rezidence dat
description: Data zasídlí a informace o serverech s podporou ARC Azure.
ms.topic: reference
ms.date: 09/22/2020
ms.custom: references_regions
ms.openlocfilehash: 73acb07b8caa3c1368c936463f04969c29d37985
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91327761"
---
# <a name="azure-arc-enabled-servers-data-residency"></a>Servery s podporou ARC Azure: zasídlí dat

Tento článek vysvětluje koncept zasídlí dat a způsob, jakým se vztahuje na servery s podporou ARC Azure.

Servery s podporou ARC Azure jsou **[k dispozici](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)** v **USA, Evropě, spojeném království nebo Asie a Tichomoří**.

## <a name="data-residency"></a>Rezidence dat

Servery s podporou ARC Azure ukládají nastavení konfigurace [rozšíření virtuálních počítačů Azure](manage-vm-extensions.md) (to znamená hodnoty vlastností), které rozšíření vyžaduje, aby se zadávalo na připojeném počítači. Když například povolíte rozšíření Log Analytics VM, požádá o Log Analytics **ID pracovního prostoru** a **primární klíč**.

Shromažďují se taky informace o metadatech připojeného počítače. Konkrétně se jedná o tyto:

* Název a verze operačního systému
* Název počítače
* Plně kvalifikovaný název domény počítače (FQDN)
* Verze agenta připojeného počítače

Servery s podporou ARC umožňují zadat oblast, ve které budou vaše data uložená. Microsoft se může replikovat do jiných oblastí s cílem zajistit odolnost dat, ale Microsoft nereplikuje ani nepřesouvá data mimo zeměpisnou oblast. Tato data jsou uložená v oblasti, kde je nakonfigurovaný prostředek počítače Azure ARC. Například pokud je počítač zaregistrován pomocí ARC v oblasti Východní USA, tato data jsou uložena v oblasti USA.

Další informace o naší místní podpoře odolnosti a dodržování předpisů najdete v tématu [Azure geografie](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o návrhu pro [odolnost Azure](/azure/architecture/reliability/architect).