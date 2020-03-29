---
title: Migrace virtuálních počítačů Azure na spravované disky
description: Migrujte virtuální počítače Azure vytvořené pomocí nespravovaných disků v účtech úložiště a používejte spravované disky.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: e8f2753ac9062803a2d6252eca1829cb0b168f02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921347"
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Migrace virtuálních počítačů Azure na spravované disky v Azure

Spravované disky Azure zjednodušují správu úložiště tím, že odebere potřebu samostatné správy účtů úložiště.  Můžete také migrovat stávající virtuální počítače Azure na spravované disky, abyste mohli těžit z lepší spolehlivosti virtuálních počítačů v sadě dostupnosti. Zajišťuje, že disky různých virtuálních počítačů v sadě dostupnosti jsou dostatečně izolované od sebe navzájem, aby se zabránilo jeden bod selhání. Automaticky umísťuje disky různých virtuálních počítačů do sady dostupnosti v různých jednotkách měřítka úložiště (razítkách), což omezuje dopad selhání jedné jednotky škálování úložiště způsobené selháním hardwaru a softwaru.
Na základě vašich potřeb si můžete vybrat ze čtyř typů možností úložiště. Další informace o dostupných typech disků naleznete v článku [Výběr typu disku](disks-types.md)

## <a name="migration-scenarios"></a> Scénáře migrace

Na spravované disky můžete migrovat v následujících scénářích:

|Scénář  |Článek  |
|---------|---------|
|Převod samostatných virtuálních počítačů a virtuálních počítačů ve skupině dostupnosti na spravované disky     |[Převod virtuálních počítačů na spravované disky](convert-unmanaged-to-managed-disks.md)         |
|Převod jednoho virtuálního počítače z klasického na Správce prostředků na spravovaných discích     |[Vytvoření virtuálního virtuálního virtuálního virtuálního virtuálního disku z klasického virtuálního pevného disku](create-vm-specialized-portal.md)         |
|Převést všechny virtuální počítače ve virtuální síti z klasického na Správce prostředků na spravovaných discích     |[Migrace prostředků IaaS z klasického správce prostředků](migration-classic-resource-manager-ps.md) a [potom převést virtuální ho z nespravovaných disků na spravované disky](convert-unmanaged-to-managed-disks.md)         |
|Upgrade virtuálních počítačů se standardními nespravovanými disky na virtuální počítače pomocí spravovaných disků premium     | Nejprve [převeďte virtuální počítač se systémem Windows z nespravovaných disků na spravované disky](convert-unmanaged-to-managed-disks.md). Potom [aktualizujte typ úložiště spravovaného disku](convert-disk-storage.md).         |

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

## <a name="next-steps"></a>Další kroky

- Další informace o [spravovaných discích](managed-disks-overview.md)
- Zkontrolujte [ceny pro spravované disky](https://azure.microsoft.com/pricing/details/managed-disks/).
