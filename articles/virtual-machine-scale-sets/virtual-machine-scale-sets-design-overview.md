---
title: Aspekty návrhu pro sady škálování virtuálního počítače Azure | Microsoft Docs
description: Další informace o aspektech návrhu pro vaše sady škálování virtuálního počítače Azure
keywords: virtuální počítač Linux, sadách škálování virtuálních počítačů
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: c27c6a59-a0ab-4117-a01b-42b049464ca1
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: negat
ms.openlocfilehash: 8c9253caad8b85b25e3142429c1e23be6f92dd64
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34652395"
---
# <a name="design-considerations-for-scale-sets"></a>Aspekty návrhu pro sady škálování
Tento článek popisuje aspekty návrhu pro sady škálování virtuálního počítače. Informace o tom, co jsou sady škálování virtuálního počítače, najdete v části [Přehled sady škálování virtuálních počítačů](virtual-machine-scale-sets-overview.md).

## <a name="when-to-use-scale-sets-instead-of-virtual-machines"></a>Kdy použít měřítko nastaví místo virtuální počítače?
Obecně platí škálovatelné sady jsou užitečné pro nasazení infrastruktury s vysokou dostupností kde sada počítačů má podobnou konfigurací. Ale některé funkce jsou k dispozici pouze v sadách škálování při další funkce jsou dostupné jen ve virtuálních počítačích. Chcete-li provést informované rozhodnutí o tom, kdy používat jednotlivé technologie, byste měli vzít první podívejte se na některé z běžně používané funkcí, které jsou k dispozici v sady škálování, ale není virtuální počítače:

### <a name="scale-set-specific-features"></a>Funkce specifické pro sadu škálování

- Jakmile zadáte měřítka nastavit konfiguraci, můžete aktualizovat *kapacity* vlastnost nasadit více virtuálních počítačů současně. Tento proces je lepší, než psaní skriptu k orchestraci nasazení hodně jednotlivých virtuálních počítačů současně.
- Můžete [používat automatické škálování Azure automaticky škálovat škálovací sadu](./virtual-machine-scale-sets-autoscale-overview.md) , ale ne jednotlivé virtuální počítače.
- Můžete [obnovení z Image škálovací sady virtuálních počítačů](https://docs.microsoft.com/rest/api/virtualmachinescalesets/manage-a-vm) ale [není jednotlivé virtuální počítače](https://docs.microsoft.com/rest/api/compute/virtualmachines).
- Můžete [overprovision](./virtual-machine-scale-sets-design-overview.md) sady škálování virtuálních počítačů pro větší spolehlivost a rychlejší nasazení časy. Jednotlivé virtuální počítače nelze overprovision, pokud psát vlastní kód k provedení této akce.
- Můžete zadat [zásad upgradu](./virtual-machine-scale-sets-upgrade-scale-set.md) snadno zavádět upgrady mezi virtuální počítače ve škálovací sadě. S jednotlivé virtuální počítače které musí orchestraci aktualizací sami.

### <a name="vm-specific-features"></a>Funkce specifické pro virtuální počítač

Některé funkce jsou aktuálně k dispozici pouze ve virtuálních počítačích:

- Můžete zaznamenat bitovou kopii z konkrétního virtuálního počítače, ale ne z virtuálního počítače ve škálovací sadě.
- Konkrétního virtuálního počítače můžete migrovat z nativních discích spravovaných disky, ale nemůžete migrovat instance virtuálního počítače ve škálovací sadě.
- Veřejné IP adresy IPv6 přiřadit jednotlivých virtuálních počítačů virtuální síťové rozhraní karty (síťové adaptéry), ale nelze učinit pro instance virtuálních počítačů v sadě škálování. Je možné přiřadit IPv6 veřejné IP adresy pro před buď jednotlivé virtuální počítače Vyrovnávání zatížení nebo škálovací sady virtuálních počítačů.

## <a name="storage"></a>Úložiště

### <a name="scale-sets-with-azure-managed-disks"></a>Sady škálování s Azure spravované disky
Sady škálování může být vytvořen pomocí [Azure spravované disky](../virtual-machines/windows/managed-disks-overview.md) místo účty tradiční úložiště Azure. Spravované disky poskytují následující výhody:
- Nemusíte předem vytvořit sadu účtů úložiště Azure pro škálovací sady virtuálních počítačů.
- Můžete definovat [připojené datových disků](virtual-machine-scale-sets-attached-disks.md) pro virtuální počítače ve vaší škálování nastavit.
- Sady škálování lze nakonfigurovat, aby [podporovat až 1 000 virtuálních počítačů v sadě](virtual-machine-scale-sets-placement-groups.md). 

Pokud máte existující šablony, můžete také [aktualizovat šablonu, kterou chcete používat spravované disky](virtual-machine-scale-sets-convert-template-to-md.md).

### <a name="user-managed-storage"></a>Spravovaná uživatelem úložiště
Sada škálování, které není definovaná s Azure spravované disky spoléhá na účty úložiště vytvořené uživatelem k uložení disku operačního systému virtuálních počítačů v sadě. Poměr 20 virtuálních počítačů na účet úložiště nebo méně se doporučuje k dosažení maximální vstupně-výstupní operace a také využít výhod _předimenzování_ (viz níže). Dále je doporučeno, rozkládá znaky začátku názvy účtů úložiště mezi abecedy. V tom pomáhá šíří zatížení napříč různými systémy interní. 


## <a name="overprovisioning"></a>Předimenzování
Škálování nastaví aktuálně výchozí nastavení "předimenzování" virtuální počítače. S předimenzování zapnutý, měřítka nastavit monitoru je ve skutečnosti nestabilní až více virtuálních počítačů, než jste žádali, a pak odstraní další virtuální počítače po úspěšně zřízený požadovaný počet virtuálních počítačů. Předimenzování zvyšuje úspěšnost zřizování a snižuje čas nasazení. Fakturuje nejsou pro další virtuální počítače a jejich nepočítá vaší kvóty.

Předimenzování zlepšit úspěšnost zřizování, může to způsobit matoucí chování pro aplikaci, která není určená pro zpracování další virtuální počítače, zobrazování a pak jindy mizí. Chcete-li předimenzování vypnout, ujistěte se, máte následující řetězec v šabloně: `"overprovision": "false"`. Další podrobnosti najdete v [dokumentace k REST API nastavit škálování](/rest/api/virtualmachinescalesets/create-or-update-a-set).

Pokud škálovací sadu používá uživatele spravovat úložiště a vypnete předimenzování, může mít více než 20 virtuálních počítačů na účet úložiště, ale není doporučeno přejděte výše 40 z důvodů výkonu vstupně-výstupní operace. 

## <a name="limits"></a>Omezení
Sada škálování založená na image pořízenou prostřednictvím Marketplace (také označované jako image platformy) a nakonfigurované na používání Azure spravované disky podporuje kapacitou až 1 000 virtuálních počítačů. Pokud nakonfigurujete vaší sad pro podporu více než 100 virtuálních počítačů škálování, ne všechny scénáře fungovat stejně (pro příklad Vyrovnávání zatížení). Další informace najdete v tématu [práce s škálovací sady virtuálních počítačů velké](virtual-machine-scale-sets-placement-groups.md). 

Nastavit nakonfigurovaný s účty úložiště uživatele, spravovat škálování je aktuálně omezená na 100 virtuálních počítačů (a 5 účty úložiště se doporučují pro tohoto měřítka).

Sada škálování založený na vlastní image (jedna je sestavena) může mít kapacitou až 300 virtuálních počítačů. při konfiguraci s Azure spravované disky. Pokud byly sadou škálování je nakonfigurovaný s účty úložiště spravované uživatele, musíte vytvořit všechny VHD disku operačního systému v rámci jednoho účtu úložiště. V důsledku toho maximální počet virtuálních počítačů ve škálovací sadě založený na vlastní image a doporučené spravovaná uživatelem úložiště je 20. Pokud vypnete předimenzování, můžete přejít až 40.

Pro další virtuální počítače než tyto limity povolit, je třeba nasadit více sad škálování, jak je znázorněno v [této šablony](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale).

