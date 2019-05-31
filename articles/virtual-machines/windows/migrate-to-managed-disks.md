---
title: Migrace virtuálních počítačů Azure na Managed Disks | Dokumentace Microsoftu
description: Migrate virtuální počítače Azure vytvořené využitím nespravovaných disků na spravované disky v účtech úložiště.
services: virtual-machines-windows
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 7a390c5231f715ce778c0cc267211b8bfb5934d2
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66418400"
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Migrace virtuálních počítačů Azure na Managed Disks v Azure

Azure Managed Disks zjednodušuje správu úložiště tím, že si nemusíte samostatně spravovat účty úložiště.  Můžete také migrovat vaše stávající virtuální počítače Azure do služby Managed Disks, abyste využili výhod vyšší spolehlivost virtuálních počítačů ve skupině dostupnosti. Zajišťuje, že disky různých virtuálních počítačů ve skupině dostupnosti jsou dostatečně izolované od sebe, aby jediný bod selhání. Automaticky umístí disky různých virtuálních počítačů ve skupině dostupnosti v různých Uložišť jednotek škálování (razítka) který omezuje dopad selhání škálovací jednotky jednoho úložiště se situace způsobena vlivem hardwaru a softwaru selhání.
Podle svých potřeb, můžete vybrat z čtyři typy úložišť. Další informace o typech disků k dispozici, najdete v našem článku [vyberte typ disku](disks-types.md)

## <a name="migration-scenarios"></a>Scénáře migrace

Můžete migrovat do Managed Disks v následujících scénářích:

|Scénář  |Článek  |
|---------|---------|
|Převod samostatných virtuálních počítačů a virtuálních počítačů ve skupině dostupnosti na spravované disky     |[Převod virtuálních počítačů použít spravované disky](convert-unmanaged-to-managed-disks.md)         |
|Převod jednoho virtuálního počítače z modelu nasazení classic do Resource Manageru na spravovaných discích     |[Vytvoření virtuálního počítače z klasického virtuálního pevného disku](create-vm-specialized-portal.md)         |
|Převést všechny virtuální počítače ve virtuální síti classic do Resource Manageru na spravovaných discích     |[Migrace prostředků IaaS z modelu classic na Resource Manager](migration-classic-resource-manager-ps.md) a potom [převod virtuálního počítače z nespravovaných disků na managed disks](convert-unmanaged-to-managed-disks.md)         |
|Upgrade s unmanaged disks úrovně standard pro virtuální počítače s disky premium spravovaných virtuálních počítačů     | Nejprve je potřeba [převod virtuálního počítače s Windows z nespravovaných disků na managed disks](convert-unmanaged-to-managed-disks.md). Potom [aktualizovat typ úložiště spravovaného disku](convert-disk-storage.md).         |

## <a name="next-steps"></a>Další postup

- Další informace o [Managed Disks](managed-disks-overview.md)
- Zkontrolujte [ceny pro Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/).
