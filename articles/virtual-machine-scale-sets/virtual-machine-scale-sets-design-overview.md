---
title: Faktory návrhu pro Azure Virtual Machine Scale Sets
description: Přečtěte si o požadavcích na návrh pro Azure Virtual Machine Scale Sets. Porovnejte funkce sady škálování s funkcemi virtuálních počítačů.
keywords: virtuální počítač Linux, sada škálování virtuálních počítačů
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 06/25/2020
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 04446243ed827cca4972a4b606c4930e74a2c704
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104774962"
---
# <a name="design-considerations-for-scale-sets"></a>Požadavky na návrh pro sady škálování
Tento článek popisuje požadavky návrhu pro Virtual Machine Scale Sets. Informace o tom, co Virtual Machine Scale Sets, najdete v tématu [Virtual Machine Scale Sets Overview](./overview.md).

## <a name="when-to-use-scale-sets-instead-of-virtual-machines"></a>Kdy použít sady škálování místo virtuálních počítačů?
Obecně platí, že sady škálování jsou užitečné pro nasazení infrastruktury s vysokou dostupností, kde sada počítačů má podobnou konfiguraci. Některé funkce jsou ale dostupné jenom v sadách škálování, ale jiné funkce jsou dostupné jenom na virtuálních počítačích. Aby bylo možné se rozhodnout, kdy používat jednotlivé technologie, měli byste se nejdřív podívat na některé běžně používané funkce, které jsou dostupné v sadě škálování, ale ne na virtuální počítače:

### <a name="scale-set-specific-features"></a>Funkce škály specifické pro sadu

- Jakmile zadáte konfiguraci sady škálování, můžete aktualizovat vlastnost *Capacity* , aby se nasadilo více virtuálních počítačů paralelně. Tento proces je vhodnější než psaní skriptu pro orchestraci nasazení mnoha jednotlivých virtuálních počítačů paralelně.
- Automatické škálování [Azure můžete použít k automatickému škálování sady škálování](./virtual-machine-scale-sets-autoscale-overview.md) , ale ne jednotlivých virtuálních počítačů.
- [Virtuální počítače](/rest/api/compute/virtualmachinescalesets/reimage) můžete obnovit přes image, ale [ne jednotlivé virtuální počítače](/rest/api/compute/virtualmachines).
- Pro zvýšení spolehlivosti a rychlejšího nasazení můžete [zajistit nadměrné zřízení](#overprovisioning) virtuálních počítačů sady škálování. Pokud nevytvoříte vlastní kód k provedení této akce, nemůžete přezřizovat jednotlivé virtuální počítače.
- Můžete určit [zásady upgradu](./virtual-machine-scale-sets-upgrade-scale-set.md) , které usnadňují zavedení upgradů mezi virtuálními počítači ve vaší sadě škálování. U jednotlivých virtuálních počítačů je nutné aktualizace orchestrovat sami.

### <a name="vm-specific-features"></a>Funkce specifické pro virtuální počítače

Některé funkce jsou v tuto chvíli dostupné jenom na virtuálních počítačích:

- Image můžete zachytit z jednotlivého virtuálního počítače, ale ne z virtuálního počítače v sadě škálování.
- Jednotlivé virtuální počítače můžete migrovat z nativních disků na Managed disks, ale nemůžete migrovat instance virtuálních počítačů do sady škálování.
- Můžete přiřadit veřejné IP adresy IPv6 jednotlivým virtuálním síťovým kartám virtuálních počítačů (nic), ale nemůžou to udělat pro instance virtuálních počítačů v sadě škálování. Veřejné IP adresy IPv6 můžete přiřadit nástrojům pro vyrovnávání zatížení před jednotlivými virtuálními počítači nebo virtuálními počítači sady škálování.

## <a name="storage"></a>Storage

### <a name="scale-sets-with-azure-managed-disks"></a>Škálování sad pomocí Azure Managed Disks
Sady škálování je možné vytvořit s využitím [azure Managed disks](../virtual-machines/managed-disks-overview.md) místo tradičních účtů úložiště Azure. Managed Disks poskytují následující výhody:
- Nemusíte předem vytvářet sadu účtů úložiště Azure pro virtuální počítače sady škálování.
- Můžete definovat [připojené datové disky](virtual-machine-scale-sets-attached-disks.md) pro virtuální počítače ve vaší sadě škálování.
- Sady škálování je možné nakonfigurovat tak, aby [podporovaly až 1 000 virtuálních počítačů v sadě](virtual-machine-scale-sets-placement-groups.md). 

Pokud máte existující šablonu, můžete [šablonu také aktualizovat tak, aby používala Managed disks](virtual-machine-scale-sets-convert-template-to-md.md).

### <a name="user-managed-storage"></a>Úložiště spravované uživatelem
Sada škálování, která není definovaná s Azure Managed Disks, spoléhá na uživatelem vytvořené účty úložiště pro ukládání disků s operačním systémem virtuálních počítačů v sadě. Poměr 20 virtuálních počítačů na účet úložiště nebo méně se doporučuje k dosažení maximální vstupně-výstupní operace a také k _využití výhod převzetí (viz_ níže). Doporučuje se také rozšířit počáteční znaky názvů účtů úložiště napříč abecedou. Díky tomu je možné rozprostření zatížení napříč různými interními systémy. 


## <a name="overprovisioning"></a>Předimenzování
Služba Scale Sets je aktuálně ve výchozím nastavení nastavena na "nadměrné zřízení" virtuálních počítačů. Když je přebytečná zřizování zapnuté, sada škálování ve skutečnosti připíná více virtuálních počítačů, než jste požadovali, a potom po úspěšném zřízení požadovaného počtu virtuálních počítačů odstraní nadbytečné virtuální počítače. Přezřizování zlepšuje míry úspěšnosti zřizování a zkracuje dobu nasazení. Za další virtuální počítače se vám neúčtují žádné poplatky, které se nepočítají na vaše limity kvót.

I když přebytečná zřizování vylepší míry úspěšnosti zřizování, může způsobit matoucí chování aplikace, která není navržená tak, aby se zobrazovaly další virtuální počítače a pak zmizí. Pokud chcete zapnout převýšení, ujistěte se, že máte v šabloně následující řetězec: `"overprovision": "false"` . Další podrobnosti najdete v [dokumentaci k sadě škálování REST API](/rest/api/virtualmachinescalesets/create-or-update-a-set).

Pokud vaše sada škálování používá uživatelsky spravované úložiště a vypnete přebírání, můžete mít více než 20 virtuálních počítačů na jeden účet úložiště, ale nedoporučujeme jít nad rámec 40 z hlediska výkonu v/v. 

## <a name="limits"></a>Omezení
Sada škálování vytvořená na obrázku Marketplace (označovaná také jako image platformy) a nakonfigurovaná na použití Azure Managed Disks podporuje kapacitu až 1 000 virtuálních počítačů. Pokud nakonfigurujete sadu škálování tak, aby podporovala více než 100 virtuálních počítačů, nebudou všechny scénáře fungovat stejně (například vyrovnávání zatížení). Další informace najdete v tématu [práce s velkými sadami škálování virtuálních počítačů](virtual-machine-scale-sets-placement-groups.md). 

Sada škálování nakonfigurovaná s uživatelsky spravovanými účty úložiště je aktuálně omezená na 100 virtuálních počítačů (a pro toto škálování se doporučuje 5 účtů úložiště).

Sada škálování vytvořená na vlastní imagi (vámi vytvořená vámi) může mít kapacitu až 600 virtuálních počítačů, pokud je nakonfigurovaná pomocí služby Azure Managed disks. Pokud je sada škálování nakonfigurovaná s uživatelskými účty úložiště, musí se v rámci jednoho účtu úložiště vytvořit všechny virtuální pevné disky s operačním systémem. Výsledkem je, že maximální doporučený počet virtuálních počítačů v sadě škálování postavené na vlastní imagi a v uživatelsky spravovaném úložišti je 20. Pokud vypnete přezřizování, můžete přejít až na 40.

Pro více virtuálních počítačů, než jsou tato omezení povolena, je nutné nasadit více sad škálování, jak je znázorněno v [této šabloně](https://azure.microsoft.com/resources/templates/301-custom-images-at-scale/).
