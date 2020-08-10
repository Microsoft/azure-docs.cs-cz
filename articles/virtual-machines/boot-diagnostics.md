---
title: Diagnostika spouštění Azure
description: Přehled diagnostiky spouštění Azure a spravované diagnostiky spouštění
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: 9ffd5a6397fa01efcf9aece93333dcb5e5b418cc
ms.sourcegitcommit: 1a0dfa54116aa036af86bd95dcf322307cfb3f83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2020
ms.locfileid: "88042958"
---
# <a name="azure-boot-diagnostics"></a>Diagnostika spouštění Azure

Diagnostika spouštění je funkce ladění pro virtuální počítače Azure (VM), která umožňuje diagnostiku selhání při spouštění virtuálních počítačů. Diagnostika spouštění umožňuje uživateli sledovat stav svého virtuálního počítače, protože se spouští shromažďováním informací o sériovém protokolu a snímků obrazovky.

## <a name="boot-diagnostics-storage-account"></a>Účet úložiště diagnostiky spouštění
Ve výchozím nastavení je diagnostika spouštění povolená pro všechny virtuální počítače vytvořené pomocí Azure Portal a využívá spravovaný účet úložiště. Když uživatelé spravovaného účtu úložiště získají výrazné zlepšení doby nasazení virtuálních počítačů. Z tohoto důvodu doporučujeme zákazníkům používat diagnostiku spouštění se spravovaným účtem úložiště pro všechny virtuální počítače.

> [!NOTE]
> Zákazníkům Azure se nebude účtovat za úložiště, pokud se rozhodne používat diagnostiku spouštění se spravovaným účtem úložiště do 1. října 2020.

Alternativním prostředím pro diagnostiku spouštění je použití vlastního účtu úložiště. Uživatel může buď vytvořit nový účet úložiště, nebo použít nějaký existující. Další informace o vlastních účtech úložiště najdete v tématu [Přehled účtu úložiště](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-portal.png" alt-text="Snímek obrazovky, který ukazuje, jak povolit diagnostiku spouštění":::

## <a name="boot-diagnostics-view"></a>Zobrazení diagnostiky spouštění
V okně virtuální počítač je možnost Diagnostika spouštění v části *Podpora a řešení potíží* v Azure Portal. Když vyberete diagnostiku spouštění, zobrazí se informace o snímku obrazovky a sériového protokolu. Sériový protokol obsahuje zprávy jádra a snímek obrazovky je snímek aktuálního stavu virtuálních počítačů. V závislosti na tom, jestli je na virtuálním počítači spuštěný systém Windows nebo Linux, určuje, jak by měl vypadat očekávaný snímek obrazovky. V případě systému Windows uvidí uživatelé desktopové pozadí a pro Linux se zobrazí výzva k zadání přihlašovacích údajů.

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="Snímek obrazovky s diagnostikou spouštění pro Linux":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="Snímek obrazovky s diagnostikou spouštění Windows":::


## <a name="limitations"></a>Omezení
- Diagnostika spouštění je dostupná jenom pro virtuální počítače Azure Resource Manager (ARM). 
- Diagnostika spouštění nepodporuje účty úložiště úrovně Premium. Pokud se pro uživatele diagnostiky spouštění používá účet Premium Storage, `StorageAccountTypeNotSupported` při spuštění virtuálního počítače dojde k chybě. 
- Služba Azure Serial Console v současné době nepodporuje spravovaný účet úložiště pro diagnostiku spouštění.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [konzole sériového rozhraní Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-overview) a o použití diagnostiky spouštění k [řešení potíží s virtuálními počítači v Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics).
