---
title: Rezidence dat
description: Data zasídlí a informace o serverech s podporou ARC Azure (Preview).
ms.topic: reference
ms.date: 08/25/2020
ms.custom: references_regions
ms.openlocfilehash: 028398c27dde9760192d366b9e8a91dca8e429cf
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/25/2020
ms.locfileid: "88860840"
---
# <a name="azure-arc-enabled-servers-preview-data-residency"></a>Servery s podporou ARC Azure (Preview): zasídlí dat

Tento článek vysvětluje koncept zasídlí dat a způsob, jakým se vztahuje na servery s podporou ARC Azure (Preview).

Servery s podporou ARC Azure (Preview) jsou **[dostupné ve verzi preview](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)** **USA, Evropa nebo Asie a Tichomoří**.

## <a name="data-residency"></a>Rezidence dat

Servery s podporou ARC Azure (Preview) uložení nastavení konfigurace [rozšíření virtuálních počítačů Azure](manage-vm-extensions.md) (tj. hodnot vlastností) rozšíření vyžaduje zadání před tím, než se na připojeném počítači pokusí povolit. Když například povolíte rozšíření Log Analytics VM, požádá o Log Analytics **ID pracovního prostoru** a **primární klíč**.

Shromažďují se taky informace o metadatech připojeného počítače. Konkrétně se jedná o tyto:

* Název a verze operačního systému
* Název počítače
* Plně kvalifikovaný název domény počítače (FQDN)
* Verze agenta připojeného počítače

Servery s podporou ARC (ve verzi Preview) umožňují zadat oblast, kde se budou data ukládat. Microsoft se může replikovat do jiných oblastí s cílem zajistit odolnost dat, ale Microsoft nereplikuje ani nepřesouvá data mimo zeměpisnou oblast. Tato data jsou uložená v oblasti, kde je nakonfigurovaný prostředek počítače Azure ARC. Například pokud je počítač zaregistrován pomocí ARC v oblasti Východní USA, tato data jsou uložena v oblasti USA.

Další informace o naší místní podpoře odolnosti a dodržování předpisů najdete v tématu [Azure geografie](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o návrhu pro [odolnost Azure](/architecture/reliability/architect).