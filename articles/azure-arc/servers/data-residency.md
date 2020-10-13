---
title: Rezidence dat
description: Data zasídlí a informace o serverech s podporou ARC Azure.
ms.topic: reference
ms.date: 10/08/2020
ms.custom: references_regions
ms.openlocfilehash: c5ece96acc3ee07ba2896279888363c7d52d737e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91856445"
---
# <a name="azure-arc-enabled-servers-data-residency"></a>Servery s podporou ARC Azure: zasídlí dat

Tento článek vysvětluje koncept zasídlí dat a způsob, jakým se vztahuje na servery s podporou ARC Azure.

Servery s podporou ARC Azure jsou **[k dispozici](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)** v **USA, Evropě, Spojeném království, Austrálii a Asie a Tichomoří**.

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