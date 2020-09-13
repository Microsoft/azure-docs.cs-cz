---
title: Diagnostika spouštění Azure
description: Přehled diagnostiky spouštění Azure a spravované diagnostiky spouštění
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: 0b3e1b3bc296676c44eddf34b35a0d4e06d3b8c4
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "90007328"
---
# <a name="azure-boot-diagnostics"></a>Diagnostika spouštění Azure

Diagnostika spouštění je funkce ladění pro virtuální počítače Azure (VM), která umožňuje diagnostiku selhání při spouštění virtuálních počítačů. Diagnostika spouštění umožňuje uživateli sledovat stav svého virtuálního počítače, protože se spouští shromažďováním informací o sériovém protokolu a snímků obrazovky.

## <a name="boot-diagnostics-storage-account"></a>Účet úložiště diagnostiky spouštění
Při vytváření virtuálního počítače v Azure Portal je ve výchozím nastavení povolená Diagnostika spouštění. Doporučené prostředí pro diagnostiku spouštění je použití spravovaného účtu úložiště, protože poskytuje významné vylepšení výkonu v čase vytvoření virtuálního počítače Azure. Je to proto, že se použije účet spravovaného úložiště Azure, který odebere čas potřebný k vytvoření nového účtu úložiště uživatele pro uložení dat diagnostiky spouštění.

Alternativním prostředím pro diagnostiku spouštění je použití účtu úložiště spravovaného uživatelem. Uživatel může buď vytvořit nový účet úložiště, nebo použít nějaký existující.

> [!IMPORTANT]
> Zákazníkům Azure se neúčtují náklady na úložiště spojené s diagnostikou spouštění pomocí spravovaného účtu úložiště do října 2020.

## <a name="boot-diagnostics-view"></a>Zobrazení diagnostiky spouštění
V okně virtuální počítač je možnost Diagnostika spouštění v části *Podpora a řešení potíží* v Azure Portal. Když vyberete diagnostiku spouštění, zobrazí se informace o snímku obrazovky a sériového protokolu. Sériový protokol obsahuje zprávy jádra a snímek obrazovky je snímek aktuálního stavu virtuálních počítačů. V závislosti na tom, jestli je na virtuálním počítači spuštěný systém Windows nebo Linux, určuje, jak by měl vypadat očekávaný snímek obrazovky. V případě systému Windows uvidí uživatelé desktopové pozadí a pro Linux se zobrazí výzva k zadání přihlašovacích údajů.

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="Snímek obrazovky s diagnostikou spouštění pro Linux":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="Snímek obrazovky s diagnostikou spouštění Windows":::


## <a name="limitations"></a>Omezení
- Diagnostika spouštění je k dispozici pouze pro Azure Resource Manager virtuální počítače. 
- Diagnostika spouštění nepodporuje účty úložiště úrovně Premium. Pokud se pro uživatele diagnostiky spouštění používá účet Premium Storage, `StorageAccountTypeNotSupported` při spuštění virtuálního počítače dojde k chybě. 
- Spravované účty úložiště jsou podporované v Správce prostředků rozhraní API verze 2020-06-01 a novější.
- Služba Azure Serial Console je aktuálně nekompatibilní se spravovaným účtem úložiště pro diagnostiku spouštění. Přečtěte si další informace o [službě Azure Serial Console](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-overview).

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [konzole sériového rozhraní Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-overview) a o použití diagnostiky spouštění k [řešení potíží s virtuálními počítači v Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics).
