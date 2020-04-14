---
title: Aspekty návrhu škálovacísady virtuálních strojů Azure
description: Přečtěte si o aspektech návrhu škálovacích sad virtuálních strojů Azure. Porovnejte funkce škálovacích sad s funkcemi virtuálního virtuálního měn.
keywords: linux virtuální stroj, škálovací sady virtuálních strojů
author: mimckitt
tags: azure-resource-manager
ms.assetid: c27c6a59-a0ab-4117-a01b-42b049464ca1
ms.service: virtual-machine-scale-sets
ms.tgt_pltfrm: vm-linux
ms.topic: conceptual
ms.date: 06/01/2017
ms.author: mimckitt
ms.openlocfilehash: 20f6cb08781c7c6aca7a4022e75a7be8640ef18a
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273762"
---
# <a name="design-considerations-for-scale-sets"></a>Aspekty návrhu pro škálovací sady
Tento článek popisuje aspekty návrhu pro škálovací sady virtuálních strojů. Informace o velikosti sady virtuálních strojů najdete v článku [Přehled škálovacích sad virtuálních strojů](virtual-machine-scale-sets-overview.md).

## <a name="when-to-use-scale-sets-instead-of-virtual-machines"></a>Kdy použít škálovací sady místo virtuálních počítačů?
Škálovací sady jsou obecně užitečné pro nasazení vysoce dostupné infrastruktury, kde má sada počítačů podobnou konfiguraci. Některé funkce jsou však k dispozici jenom ve škálovacích sadách, zatímco jiné funkce jsou dostupné jenom ve virtuálních virtuálních telefonech. Chcete-li učinit informované rozhodnutí o tom, kdy použít jednotlivé technologie, měli byste se nejprve podívat na některé běžně používané funkce, které jsou k dispozici ve škálovacích sadách, ale ne ve virtuálních telefonech:

### <a name="scale-set-specific-features"></a>Funkce specifické pro škálování

- Jakmile zadáte konfiguraci škálovací sady, můžete aktualizovat vlastnost *kapacity* a nasadit paralelně více virtuálních počítačů. Tento proces je lepší než psaní skriptu orchestrat nasazení mnoha jednotlivých virtuálních her paralelně.
- Automatické [škálování Azure můžete použít k automatickému škálování škálovací sady,](./virtual-machine-scale-sets-autoscale-overview.md) ale ne jednotlivých virtuálních počítačích.
- Můžete [reimage škálovací sady virtuálních disekonů,](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage) ale [ne jednotlivé virtuální chod .](https://docs.microsoft.com/rest/api/compute/virtualmachines)
- Můžete [overprovision](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview#overprovisioning) škálovací sady virtuálních počítače pro zvýšení spolehlivosti a rychlejší nasazení. Nelze přezrezervovat jednotlivé virtuální hody, pokud nenapíšete vlastní kód k provedení této akce.
- Můžete zadat [zásady upgradu,](./virtual-machine-scale-sets-upgrade-scale-set.md) které usnadní zavádění upgradů mezi virtuálními počítači ve vaší škálovací sadě. S jednotlivými virtuálními ms, musíte organizovat aktualizace sami.

### <a name="vm-specific-features"></a>Funkce specifické pro virtuální montovana

Některé funkce jsou momentálně dostupné jenom ve virtuálních discích:

- Můžete zachytit obraz z jednotlivých virtuálních ms, ale ne z virtuálního virtuálního virtuálního virtuálního<
- Jednotlivé virtuální počítače můžete migrovat z nativních disků na spravované disky, ale nemůžete migrovat instance virtuálních počítačů ve škálovací sadě.
- Veřejné IP adresy IPv6 můžete přiřadit jednotlivým kartám virtuálního síťového rozhraní virtuálního počítače(NIC), ale nemůžete to udělat pro instance virtuálních počítačů ve škálovací sadě. Veřejné IP adresy IPv6 můžete přiřadit k vykladačům zatížení před jednotlivými virtuálními počítačůnebo virtuálními počítačůmi škálovací sady.

## <a name="storage"></a>Storage

### <a name="scale-sets-with-azure-managed-disks"></a>Škálovací sady se spravovanými disky Azure
Škálovací sady se můžou vytvářet pomocí [spravovaných disků Azure](../virtual-machines/windows/managed-disks-overview.md) namísto tradičních účtů úložiště Azure. Spravované disky poskytují následující výhody:
- Není potřeba předem vytvořit sadu účtů úložiště Azure pro virtuální počítače škálovací sady.
- Můžete definovat [připojené datové disky](virtual-machine-scale-sets-attached-disks.md) pro virtuální počítače ve vaší škálovací sadě.
- Škálovací sady lze nakonfigurovat tak, aby [podporovaly až 1 000 virtuálních počítače v sadě](virtual-machine-scale-sets-placement-groups.md). 

Pokud máte existující šablonu, můžete ji také [aktualizovat tak, aby používala spravované disky](virtual-machine-scale-sets-convert-template-to-md.md).

### <a name="user-managed-storage"></a>Úložiště spravované uživatelem
Škálovací sada, která není definována pomocí spravovaných disků Azure, závisí na účtech úložiště vytvořených uživateli k uložení disků operačního systému virtuálních počítačů v sadě. Poměr 20 virtuálních zařízení na účet úložiště nebo méně se doporučuje k dosažení maximální vi a také využít _nadměrného využívání_ (viz níže). Doporučujeme také rozložit počáteční znaky názvů účtů úložiště napříč abecedou. To pomáhá rozložit zatížení napříč různými interními systémy. 


## <a name="overprovisioning"></a>Nadměrné poskytování služeb
Škálovací sady aktuálně výchozí "overprovisioning" virtuálních disponecí. Se zapnutým přezřízením škálovací sady ve skutečnosti otáčí více virtuálních počítačích, než jste požadovali, a po úspěšném zřízení požadovaného počtu virtuálních počítačů odstraní další virtuální počítač. Overprovisioning zlepšuje zřizování úspěšnost a snižuje dobu nasazení. Neúčtují se vám další virtuální počítače a nezapočítávají se do limitů kvót.

Zatímco overprovisioning zlepšit zřizování úspěšnost, může způsobit matoucí chování pro aplikaci, která není určena ke zpracování dalších virtuálních počítačů, které se zobrazují a pak zmizí. Chcete-li vypnout overprovisioning, ujistěte se, `"overprovision": "false"`že máte následující řetězec v šabloně: . Další podrobnosti naleznete v [dokumentaci k rozhraní REST API škálovací sady](/rest/api/virtualmachinescalesets/create-or-update-a-set).

Pokud vaše škálovací sada používá úložiště spravované uživatelem a vypnete nadměrné zřizování, můžete mít více než 20 virtuálních počítačů na účet úložiště, ale z důvodů výkonu vstupně-vím se nedoporučuje přecházet nad 40. 

## <a name="limits"></a>Omezení
Škálovací sada postavená na image Marketplace (označovaná také jako image platformy) a nakonfigurovaná pro použití spravovaných disků Azure podporuje kapacitu až 1 000 virtuálních počítačů. Pokud nakonfigurujete škálovací sadu tak, aby podporovala více než 100 virtuálních počítačích, ne všechny scénáře fungují stejně (například vyrovnávání zatížení). Další informace najdete [v tématu Práce s velkými škálovacími sadami virtuálních strojů](virtual-machine-scale-sets-placement-groups.md). 

Škálovací sada nakonfigurovaná pomocí účtů úložiště spravovaného uživateli je momentálně omezena na 100 virtuálních počítačů (pro toto škálování se doporučuje 5 účtů úložiště).

Škálovací sada postavená na vlastní image (kterou jste vytvořili) může mít kapacitu až 600 virtuálních počítačů při konfiguraci pomocí disků Azure Managed. Pokud je škálovací sada nakonfigurována s účty úložiště spravované uživateli, musí vytvořit všechny virtuální pevné disky operačního systému v rámci jednoho účtu úložiště. V důsledku toho je maximální doporučený počet virtuálních počítačů ve škálovací sadě postavené na vlastní image a úložiště spravované uživatelem 20. Pokud vypnete overprovisioning, můžete přejít až na 40.

Pro více virtuálních počítačů, než tato omezení umožňují, je třeba nasadit více škálovacísady, jak je znázorněno v [této šabloně](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale).

