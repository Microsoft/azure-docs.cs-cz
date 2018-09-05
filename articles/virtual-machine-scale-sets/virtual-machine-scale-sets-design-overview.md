---
title: Aspekty návrhu pro Škálovací sady virtuálních počítačů Azure | Dokumentace Microsoftu
description: Další informace o aspektech návrhu pro Škálovací sady virtuálních počítačů Azure
keywords: virtuální počítač s linuxem, škálovacích sad virtuálních počítačů
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
ms.openlocfilehash: 1716ebf1d3490511d7102c8c756c78c0f0c55291
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2018
ms.locfileid: "43669852"
---
# <a name="design-considerations-for-scale-sets"></a>Aspekty návrhu pro Škálovací sady
Tento článek popisuje aspekty návrhu pro Škálovací sady virtuálních počítačů. Informace o tom, co jsou Škálovací sady virtuálních počítačů, [přehled škálovacích sad virtuálních počítačů](virtual-machine-scale-sets-overview.md).

## <a name="when-to-use-scale-sets-instead-of-virtual-machines"></a>Kdy použít škálovací sady místo virtuálních počítačů?
Obecně platí, škálovací sady jsou užitečné při nasazování infrastrukturu s vysokou dostupností kde sadu počítačů má podobnou konfiguraci. Ale některé funkce jsou k dispozici pouze ve škálovacích sadách při další funkce jsou k dispozici ve virtuálních počítačích. Aby bylo možné provést informované rozhodnutí o tom, kdy používat jednotlivé technologie, byste měli provést nejdřív podívat na některé běžně používané funkce, které jsou k dispozici ve škálovacích sadách, ale ne virtuální počítače:

### <a name="scale-set-specific-features"></a>Funkce specifické pro sadu škálování

- Až zadáte škálovací sady konfigurace, můžete aktualizovat *kapacity* vlastnost nasazení více virtuálních počítačů současně. Tento proces je lepší než psaní skriptu pro orchestraci nasazování mnoho jednotlivých virtuálních počítačů současně.
- Je možné [automatické škálování škálovací sady pomocí Azure Autoscale](./virtual-machine-scale-sets-autoscale-overview.md) , ale nikoli jednotlivým virtuálním počítačům.
- Je možné [obnovení z Image škálovací sady virtuálních počítačů](https://docs.microsoft.com/en-us/rest/api/compute/virtualmachinescalesets/reimage) ale [není jednotlivým virtuálním počítačům](https://docs.microsoft.com/rest/api/compute/virtualmachines).
- Je možné [nadměrné](https://docs.microsoft.com/en-us/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview#overprovisioning) pro zvýšení spolehlivosti a rychlejší doba nasazení škálovací sady virtuálních počítačů. Jednotlivé virtuální počítače nelze značných, není-li napsat vlastní kód k provedení této akce.
- Můžete zadat [zásad upgradu](./virtual-machine-scale-sets-upgrade-scale-set.md) usnadňuje zavedení upgrady napříč virtuálními počítači ve škálovací sadě. S jednotlivými virtuálními počítači je nutné orchestraci aktualizací sami.

### <a name="vm-specific-features"></a>Funkce specifické pro virtuální počítač

Některé funkce jsou aktuálně dostupné pouze ve virtuálních počítačích:

- Můžete zaznamenat bitovou kopii z konkrétního virtuálního počítače, ale ne z virtuálních počítačů ve škálovací sadě.
- Konkrétního virtuálního počítače můžete migrovat z nativních disků na managed disks, ale nemůžete migrovat instance virtuálních počítačů ve škálovací sadě.
- Můžete přiřadit veřejné IP adresy protokolu IPv6 pro jednotlivé virtuální počítač virtuální síťové karty (síťové adaptéry), ale nejde udělat pro instance virtuálních počítačů ve škálovací sadě. Můžete přiřadit veřejnou IP adresu IPv6 pro před buď jednotlivým virtuálním počítačům Vyrovnávání zatížení nebo škálovací sady virtuálních počítačů.

## <a name="storage"></a>Úložiště

### <a name="scale-sets-with-azure-managed-disks"></a>Škálovací sady pomocí Azure Managed Disks
Škálovací sady se dají vytvářet pomocí [Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md) místo účtů tradiční Azure storage. Spravované disky poskytují následující výhody:
- Nemusíte předem vytvořit sadu účtů úložiště Azure pro škálovací sady virtuálních počítačů.
- Můžete definovat [připojené datové disky](virtual-machine-scale-sets-attached-disks.md) pro virtuální počítače ve škálovací nastavit.
- Škálovací sady lze nakonfigurovat pro [podporují až 1 000 virtuálních počítačů v sadě](virtual-machine-scale-sets-placement-groups.md). 

Pokud máte existující šablonu, můžete také [aktualizovat šablonu, kterou chcete používat službu Managed Disks](virtual-machine-scale-sets-convert-template-to-md.md).

### <a name="user-managed-storage"></a>Cloudově spravovaného úložiště
Škálovací sadu, která není definovaná s Azure Managed Disks závisí na účty úložiště vytvořené uživatelem pro ukládání disků operačního systému virtuálních počítačů v sadě. Doporučuje se poměr 20 virtuálními počítači na jeden účet úložiště nebo i rychleji dosáhnout maximální vstupně-výstupní operace a taky využít výhod _předimenzování_ (viz níže). Doporučuje se také, rozložit počáteční znaky v názvech účtů úložiště mezi abecedy. Provádí se tak pomáhá rozložit zatížení mezi různých interních systémů. 


## <a name="overprovisioning"></a>Předimenzování
Škálovací sady aktuálně výchozí "předimenzování" virtuální počítače. S vypnutým předimenzováním zapnuté, škálování nastavte ve skutečnosti přede zprovoznit další virtuální počítače, než se zobrazí výzva pro a pak odstraní nadbytečné virtuální počítače po požadovaný počet virtuálních počítačů jsou úspěšně zřízený. Předimenzování úspěšnosti zřizování zvyšuje a snižuje čas nasazení. Se vám nic neúčtuje za další virtuální počítače a že se nepočítají do vaší kvóty.

Zatímco předimenzování zvýšit úspěšnost zřizování, může to způsobit matoucí chování pro aplikace, která není určená pro zpracování nadbytečné virtuální počítače, povolí a potom zmizení. Chcete-li předimenzování vypnout, ujistěte se, máte následující řetězec v šabloně: `"overprovision": "false"`. Další podrobnosti najdete v [dokumentace k rozhraní API REST pro nastavení Škálovací](/rest/api/virtualmachinescalesets/create-or-update-a-set).

Pokud vaše škálovací sada používá cloudově spravovaného úložiště a vypnete předimenzování, může mít více než 20 virtuálními počítači na jeden účet úložiště, ale nedoporučuje ambice 40 z důvodů výkonu vstupně-výstupních operací. 

## <a name="limits"></a>Omezení
Škálovací sadu založenou na image Marketplace (také označovaný jako image platformy) a nakonfigurovat tak, aby používat službu Azure Managed Disks podporuje kapacitu až 1 000 virtuálních počítačů. Pokud nakonfigurujete svou škálovací sadu pro podporu více než 100 virtuálních počítačů, ne všechny scénáře fungovat stejně (pro příklad Vyrovnávání zatížení). Další informace najdete v tématu [práce se škálovacími sadami virtuálních počítačů velké](virtual-machine-scale-sets-placement-groups.md). 

Škálovací sady nakonfigurovaný s účty úložiště spravovaného uživatele je aktuálně omezena na 100 virtuálních počítačů (a 5 účty úložiště se doporučují pro tuto škálovací).

Škálovací sady využívající vlastní image (které jste sestavili sami je jeden) může mít kapacitu až na 300 virtuálních počítačů, když je nakonfigurována s Azure Managed disks. Pokud škálovací sada je nakonfigurovaný s účty úložiště spravovaného uživatele, musíte vytvořit všechny VHD disku operačního systému v rámci jednoho účtu úložiště. V důsledku toho doporučuje maximální počet virtuálních počítačů ve škálovací sadě vytvořené na vlastní imagi a cloudově spravovaného úložiště je 20. Pokud vypnete předimenzování, můžete přejít až 40.

Pro virtuální počítače s více než tato omezení povolit, je třeba nasadit více škálovacích sadách, jak je znázorněno v [Tato šablona](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale).

