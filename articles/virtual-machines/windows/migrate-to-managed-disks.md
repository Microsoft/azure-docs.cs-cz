---
title: Migrace virtuálních počítačů Azure na Managed Disks | Dokumentace Microsoftu
description: Migrate virtuální počítače Azure vytvořené využitím nespravovaných disků na spravované disky v účtech úložiště.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: f1f786ea4fbf9cea5afbbd2ff038b2b3f8bc3eaf
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2019
ms.locfileid: "56803579"
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Migrace virtuálních počítačů Azure na Managed Disks v Azure

Azure Managed Disks zjednodušuje správu úložiště tím, že si nemusíte samostatně spravovat účty úložiště.  Můžete také migrovat vaše stávající virtuální počítače Azure do služby Managed Disks, abyste využili výhod vyšší spolehlivost virtuálních počítačů ve skupině dostupnosti. Zajišťuje, že disky různých virtuálních počítačů ve skupině dostupnosti je dostatečně od sebe navzájem oddělené, aby jediný bod selhání. Automaticky umístí disky různých virtuálních počítačů ve skupině dostupnosti v různých Uložišť jednotek škálování (razítka) který omezuje dopad selhání škálovací jednotky jednoho úložiště se situace způsobena vlivem hardwaru a softwaru selhání.
Podle svých potřeb, můžete vybrat z čtyři typy úložišť. Další informace o typech disků k dispozici, najdete v našem článku [vyberte typ disku](disks-types.md)

## <a name="migrate-scenarios"></a>Scénáře migrace

Můžete migrovat do Managed Disks v následujících scénářích:

| **Migrate...**                                            | **Odkaz na dokumentaci**                                                                                                                                                                                                                                                                  |
|----------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Převést samostatné virtuální počítače a virtuální počítače ve skupině dostupnosti na spravované disky   | [Převod virtuálních počítačů použít spravované disky](convert-unmanaged-to-managed-disks.md) |
| Jeden virtuální počítač z modelu nasazení classic do Resource Manageru na spravovaných discích     | [Vytvoření virtuálního počítače z klasického virtuálního pevného disku](create-vm-specialized-portal.md)  | 
| Všechny virtuální počítače ve virtuální síti z modelu nasazení classic do Resource Manageru na spravovaných discích     | [Migrace prostředků IaaS z modelu classic na Resource Manager](migration-classic-resource-manager-ps.md) a potom [převod virtuálního počítače z nespravovaných disků na managed disks](convert-unmanaged-to-managed-disks.md) | 


## <a name="next-steps"></a>Další postup

- Další informace o [Managed Disks](managed-disks-overview.md)
- Zkontrolujte [ceny pro Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/).
