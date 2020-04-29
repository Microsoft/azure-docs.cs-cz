---
title: Migrace virtuálních počítačů Azure do Managed Disks
description: Migrujte virtuální počítače Azure vytvořené pomocí nespravovaných disků v účtech úložiště, abyste mohli Managed Disks použít.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: e8f2753ac9062803a2d6252eca1829cb0b168f02
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77921347"
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Migrace virtuálních počítačů Azure do Managed Disks v Azure

Azure Managed Disks zjednodušuje správu úložiště tím, že odstraňuje nutnost samostatné správy účtů úložiště.  Můžete také migrovat stávající virtuální počítače Azure na Managed Disks, abyste mohli využívat lepší spolehlivost virtuálních počítačů ve skupině dostupnosti. Zajišťuje, aby byly disky různých virtuálních počítačů ve skupině dostupnosti dostatečně izolované od sebe navzájem, aby nedocházelo k jednomu bodu selhání. Automaticky umístí disky různých virtuálních počítačů do skupiny dostupnosti v různých jednotkách škálování úložiště (razítka), které omezují dopad selhání jedné jednotky úložiště kvůli selháním hardwaru a softwaru.
Podle svých potřeb můžete vybírat ze čtyř typů úložišť. Další informace o dostupných typech disků najdete v našem článku [Výběr typu disku](disks-types.md) .

## <a name="migration-scenarios"></a> Scénáře migrace

Migraci na Managed Disks můžete provést v následujících scénářích:

|Scénář  |Článek  |
|---------|---------|
|Převod samostatných virtuálních počítačů a virtuálních počítačů ve skupině dostupnosti na spravované disky     |[Převod virtuálních počítačů na používání spravovaných disků](convert-unmanaged-to-managed-disks.md)         |
|Převedení jednoho virtuálního počítače z klasického na Správce prostředků na spravovaných discích     |[Vytvoření virtuálního počítače z klasického virtuálního pevného disku](create-vm-specialized-portal.md)         |
|Převést všechny virtuální počítače ve virtuální síti z klasického na Správce prostředků na spravovaných discích     |[Migrace prostředků IaaS z modelu Classic na správce prostředků](migration-classic-resource-manager-ps.md) a [převeďte virtuální počítač z nespravovaných disků na Managed disks](convert-unmanaged-to-managed-disks.md)         |
|Upgrade virtuálních počítačů se standardními nespravovanými disky na virtuální počítače se spravovanými disky Premium     | Nejprve [převeďte virtuální počítač s Windows z nespravovaných disků na Managed disks](convert-unmanaged-to-managed-disks.md). Pak [aktualizujte typ úložiště spravovaného disku](convert-disk-storage.md).         |

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

## <a name="next-steps"></a>Další kroky

- Další informace o [Managed disks](managed-disks-overview.md)
- Projděte si [ceny Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/).
