---
title: Azure doporučení služby Advisor vysokou dostupnost | Microsoft Docs
description: Pomocí Azure Advisor lze zvýšit vysokou dostupnost vašich Azure nasazení.
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: 23764b476f01c30b1755c507a0cfa5ead27be91e
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34736547"
---
# <a name="advisor-high-availability-recommendations"></a>Doporučení pro vysokou dostupnost služby Advisor

Azure Advisor pomáhá zajistit, a zlepšování kontinuity důležitými obchodními aplikacemi. Můžete získat vysokou dostupnost doporučení Advisor z **vysokou dostupnost** Advisor řídicího panelu.

## <a name="ensure-virtual-machine-fault-tolerance"></a>Zajištění odolnosti proti chybám virtuálního počítače

Pokud chcete zajistit redundanci pro vaši aplikaci, doporučujeme seskupit dva nebo více virtuálních počítačů do skupiny dostupnosti. Advisor identifikuje virtuálních počítačů, které nejsou součástí skupiny dostupnosti a doporučuje jejich přesunutí do skupiny dostupnosti. Tato konfigurace zajistí, že během buď plánované i neplánované údržby alespoň jeden virtuální počítač je k dispozici a splňuje virtuální počítač Azure SLA. Můžete vytvořit sadu dostupnosti pro virtuální počítač nebo virtuální počítač přidat do stávající sadu dostupnosti.

> [!NOTE]
> Pokud se rozhodnete vytvořit skupinu dostupnosti, je nutné přidat alespoň jeden další virtuální počítač do ní. Doporučujeme, které jsou seskupeny dvě nebo více virtuálních počítačů ve skupině dostupnosti nastavena zajistit, aby aspoň jeden počítač je k dispozici během výpadku.

## <a name="ensure-availability-set-fault-tolerance"></a>Ujistěte se, že odolnost proti chybám sadu dostupnosti 

Pokud chcete zajistit redundanci pro vaši aplikaci, doporučujeme seskupit dva nebo více virtuálních počítačů do skupiny dostupnosti. Advisor určí skupiny dostupnosti, které obsahují jeden virtuální počítač a doporučuje přidání do jednoho nebo více virtuálních počítačů. Tato konfigurace zajistí, že během buď plánované i neplánované údržby alespoň jeden virtuální počítač je k dispozici a splňuje virtuální počítač Azure SLA. Můžete k vytvoření virtuálního počítače nebo při přidání existujícího virtuálního počítače do skupiny dostupnosti.  

## <a name="ensure-application-gateway-fault-tolerance"></a>Ujistěte se, aplikace brány odolnost proti chybám
Zajištění kontinuity obchodních procesů důležitých aplikací, které jsou zapnuté nástrojem application Gateway, Advisor identifikuje instance brány aplikace, kteří nejsou nakonfigurovaní pro odolnost proti chybám, a navrhování nápravné akce, které můžete provést. Advisor určí střední a velké aplikace s jedinou instancí brány a doporučí přidat nejméně jedna další instance. Také určí jeden nebo více instance malé application Gateway a doporučuje migrace na střední a velké jednotky SKU. Advisor doporučuje tyto akce se ujistíte, že instance brány vaší aplikace nakonfigurovaná vyhovět požadavkům SLA pro tyto prostředky.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks"></a>Zvýšení výkonu a spolehlivosti disky virtuálního počítače

Advisor určí virtuálních počítačů s standardní disky a doporučuje upgrade na prémiové disky.
 
Azure Premium Storage nabízí podporu vysoce výkonné, nízkou latencí disků pro virtuální počítače spustit I náročnými úlohy. Disky virtuálního počítače, které používají účty úložiště premium ukládat data do disků SSD (Solid-State Drive). Nejlepšího výkonu pro vaši aplikaci doporučujeme migraci všechny disky virtuálního počítače, které vyžadují vysokou IOPS pro storage úrovně premium. 

Pokud vaše disky nevyžadují vysokou IOPS, můžete omezit náklady na údržbu je v standardní úložiště. Standardní úložiště ukládá data disku virtuálního počítače na jednotkách pevných disků (HDD) namísto SSD. Můžete migrovat disky virtuálního počítače do prémiové disky. Pro prémiové disky jsou podporovány na virtuálním počítači většina SKU. Ale v některých případech, pokud chcete použít prémiové disky, může musíte upgradovat virtuální počítač SKU také.

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>Virtuální počítač data chránit před nechtěným odstraněním
Nastavení zálohování virtuálních počítačů zajistíte dostupnost důležitých podnikových dat a poskytuje ochranu před náhodným odstraněním nebo poškození.  Advisor identifikuje virtuální počítače, kde není povolené zálohování, a doporučí povolení zálohování. 

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Ujistěte se, že máte přístup do cloudu Azure Odborníci v případě potřeby
Při spuštění důležitých zatížení, je důležité mít přístup k technické podpory v případě potřeby. Advisor určí potenciální důležitých podnikových odběry, které nemají součástí jejich plán podpory se na technickou podporu a doporučuje upgrade na možnost, která zahrnuje se na technickou podporu.

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Informace o přístupu k zajištění vysoké dostupnosti doporučení v Advisor

1. Přihlaste se k [portál Azure](https://portal.azure.com)a pak otevřete [Advisor](https://aka.ms/azureadvisordashboard).

2.  Na řídicím panelu služby Advisor, klikněte na **vysokou dostupnost** kartě.

## <a name="next-steps"></a>Další postup

Další informace o doporučení služby Advisor najdete v tématu:
* [Úvod do Azure Advisor](advisor-overview.md)
* [Začínáme se službou Advisor](advisor-get-started.md)
* [Náklady na doporučení služby Advisor](advisor-performance-recommendations.md)
* [Poradce při hodnocení výkonu doporučení](advisor-performance-recommendations.md)
* [Doporučení zabezpečení Advisor](advisor-security-recommendations.md)

