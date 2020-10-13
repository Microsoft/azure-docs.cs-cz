---
title: Diagnostika spouštění Azure
description: Přehled diagnostiky spouštění Azure a spravované diagnostiky spouštění
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: 52a2b5e27cd5857416343e559237d08ea9a591be
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91972386"
---
# <a name="azure-boot-diagnostics"></a>Diagnostika spouštění Azure

Diagnostika spouštění je funkce ladění pro virtuální počítače Azure (VM), která umožňuje diagnostiku selhání při spouštění virtuálních počítačů. Diagnostika spouštění umožňuje uživateli sledovat stav svého virtuálního počítače, protože se spouští shromažďováním informací o sériovém protokolu a snímků obrazovky.

## <a name="boot-diagnostics-storage-account"></a>Účet úložiště diagnostiky spouštění
Při vytváření virtuálního počítače v Azure Portal je ve výchozím nastavení povolená Diagnostika spouštění. Doporučené prostředí pro diagnostiku spouštění je použití spravovaného účtu úložiště, protože poskytuje významné vylepšení výkonu v čase vytvoření virtuálního počítače Azure. Je to proto, že se použije účet spravovaného úložiště Azure, který odebere čas potřebný k vytvoření nového účtu úložiště uživatele pro uložení dat diagnostiky spouštění.

Alternativním prostředím pro diagnostiku spouštění je použití účtu úložiště spravovaného uživatelem. Uživatel může buď vytvořit nový účet úložiště, nebo použít nějaký existující.

> [!IMPORTANT]
> Zákazníkům Azure se neúčtují náklady na úložiště spojené s diagnostikou spouštění pomocí spravovaného účtu úložiště do října 2020.
>
> Datové bloby spouštění diagnostiky dat (které tvoří protokoly a image snímků) se ukládají ve spravovaném účtu úložiště. Zákazníkům se budou účtovat jenom využívané GiBs objekty blob, a ne na zřízené velikosti disku. Měřiče snímků se použijí pro fakturaci spravovaného účtu úložiště. Vzhledem k tomu, že se spravované účty vytvářejí na úrovni Standard LRS nebo Standard ZRS, zákazníkům se budou účtovat jenom za měsíc, který bude mít za velikost svých objektů BLOB diagnostických dat. Další informace o těchto cenách najdete v tématu [ceny služby Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/). Zákazníci uvidí tento poplatek vázaný na svůj identifikátor URI prostředku virtuálního počítače. 

## <a name="boot-diagnostics-view"></a>Zobrazení diagnostiky spouštění
V okně virtuální počítač je možnost Diagnostika spouštění v části *Podpora a řešení potíží* v Azure Portal. Když vyberete diagnostiku spouštění, zobrazí se informace o snímku obrazovky a sériového protokolu. Sériový protokol obsahuje zprávy jádra a snímek obrazovky je snímek aktuálního stavu virtuálních počítačů. V závislosti na tom, jestli je na virtuálním počítači spuštěný systém Windows nebo Linux, určuje, jak by měl vypadat očekávaný snímek obrazovky. V případě systému Windows uvidí uživatelé desktopové pozadí a pro Linux se zobrazí výzva k zadání přihlašovacích údajů.

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="Snímek obrazovky s diagnostikou spouštění pro Linux":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="Snímek obrazovky s diagnostikou spouštění pro Linux":::


## <a name="limitations"></a>Omezení
- Diagnostika spouštění je k dispozici pouze pro Azure Resource Manager virtuální počítače. 
- Diagnostika spouštění nepodporuje účty úložiště úrovně Premium. Pokud se pro uživatele diagnostiky spouštění používá účet Premium Storage, `StorageAccountTypeNotSupported` při spuštění virtuálního počítače dojde k chybě. 
- Spravované účty úložiště jsou podporované v Správce prostředků rozhraní API verze 2020-06-01 a novější.
- Služba Azure Serial Console je aktuálně nekompatibilní se spravovaným účtem úložiště pro diagnostiku spouštění. Přečtěte si další informace o [službě Azure Serial Console](./troubleshooting/serial-console-overview.md).
- Diagnostika spouštění pomocí spravovaného účtu úložiště se v tuto chvíli dá použít jenom prostřednictvím Azure Portal. 

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [konzole sériového rozhraní Azure](./troubleshooting/serial-console-overview.md) a o použití diagnostiky spouštění k [řešení potíží s virtuálními počítači v Azure](./troubleshooting/boot-diagnostics.md).