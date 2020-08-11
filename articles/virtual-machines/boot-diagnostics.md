---
title: Diagnostika spouštění Azure
description: Přehled diagnostiky spouštění Azure a spravované diagnostiky spouštění
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: e2ba5d909a3aa43921f52295d2f7216aac76bc32
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/11/2020
ms.locfileid: "88067082"
---
# <a name="azure-boot-diagnostics"></a>Diagnostika spouštění Azure

Diagnostika spouštění je funkce ladění pro virtuální počítače Azure (VM), která umožňuje diagnostiku selhání při spouštění virtuálních počítačů. Diagnostika spouštění umožňuje uživateli sledovat stav svého virtuálního počítače, protože se spouští shromažďováním informací o sériovém protokolu a snímků obrazovky.

## <a name="boot-diagnostics-view"></a>Zobrazení diagnostiky spouštění
V okně virtuální počítač je možnost Diagnostika spouštění v části *Podpora a řešení potíží* v Azure Portal. Když vyberete diagnostiku spouštění, zobrazí se informace o snímku obrazovky a sériového protokolu. Sériový protokol obsahuje zprávy jádra a snímek obrazovky je snímek aktuálního stavu virtuálních počítačů. V závislosti na tom, jestli je na virtuálním počítači spuštěný systém Windows nebo Linux, určuje, jak by měl vypadat očekávaný snímek obrazovky. V případě systému Windows uvidí uživatelé desktopové pozadí a pro Linux se zobrazí výzva k zadání přihlašovacích údajů.

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="Snímek obrazovky s diagnostikou spouštění pro Linux":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="Snímek obrazovky s diagnostikou spouštění Windows":::


## <a name="limitations"></a>Omezení
- Diagnostika spouštění je k dispozici pouze pro Azure Resource Manager virtuální počítače. 
- Diagnostika spouštění nepodporuje účty úložiště úrovně Premium. Pokud se pro uživatele diagnostiky spouštění používá účet Premium Storage, `StorageAccountTypeNotSupported` při spuštění virtuálního počítače dojde k chybě. 

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [konzole sériového rozhraní Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-overview) a o použití diagnostiky spouštění k [řešení potíží s virtuálními počítači v Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics).
