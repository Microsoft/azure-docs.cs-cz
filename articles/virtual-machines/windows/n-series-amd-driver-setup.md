---
title: Nastavení ovladače GPU řady AMD pro Windows N-Series pro Windows
description: Jak nastavit ovladače AMD GPU pro virtuální počítače řady N-Series s Windows serverem nebo Windows v Azure
author: vikancha-MSFT
manager: jkabat
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/4/2019
ms.author: vikancha
ms.openlocfilehash: 6885c28d993b8ddab5fe158ad7b1480259cb8fb0
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2020
ms.locfileid: "92163781"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>Instalace ovladačů AMD GPU pro virtuální počítače řady N-Series s Windows

Aby bylo možné využít možnosti GPU pro nové virtuální počítače řady Azure NVv4, na kterých běží Windows, musí být nainstalované ovladače GPU AMD. [Rozšíření ovladače AMD GPU](../extensions/hpccompute-amd-gpu-windows.md) nainstaluje ovladače AMD GPU na virtuálním počítači s NVv4-Series. Nainstalujte nebo spravujte rozšíření pomocí Azure Portal nebo nástrojů, jako jsou šablony Azure PowerShell nebo Azure Resource Manager. Podporované operační systémy a kroky nasazení najdete v [dokumentaci k rozšíření ovladače AMD GPU](../extensions/hpccompute-amd-gpu-windows.md) .

Pokud se rozhodnete nainstalovat ovladače AMD GPU ručně, najdete v tomto článku podporované operační systémy, ovladače a postup instalace a ověření.

Na virtuálních počítačích s NVv4 jsou podporované jenom ovladače GPU publikované Microsoftem. Neinstalujte prosím ovladače GPU z žádného jiného zdroje.

Základní specifikace, kapacity úložiště a podrobnosti o discích najdete v tématu [velikosti virtuálních počítačů s Windows GPU](../sizes-gpu.md?toc=/azure/virtual-machines/windows/toc.json).



## <a name="supported-operating-systems-and-drivers"></a>Podporované operační systémy a ovladače

| Operační systém | Ovladač |
| -------- |------------- |
| Windows 10 Enterprise multi-session – Build 1903 <br/><br/>Windows 10 – Build 1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [20. Q1. Oprava hotfix](https://download.microsoft.com/download/d/e/f/def0fb44-15ab-4b83-959a-8094eb9d0dfe/AMD-Azure-NVv4-Driver-20Q1-Hotfix3.exe) (. exe) |


## <a name="driver-installation"></a>Instalace ovladače

1. Připojte se pomocí vzdálené plochy ke každému virtuálnímu počítači NVv4-Series.

2. Pokud potřebujete odinstalovat předchozí verzi ovladače, stáhněte [si ho pomocí nástroje pro](https://download.microsoft.com/download/4/f/1/4f19b714-9304-410f-9c64-826404e07857/AMDCleanupUtilityni.exe) vyčištění po instalaci. Nepoužívejte nástroj, který je součástí předchozí verze ovladače.

3. Stáhněte si a nainstalujte nejnovější ovladač.

4. Restartujte virtuální počítač.

## <a name="verify-driver-installation"></a>Ověřit instalaci ovladače

Instalaci ovladače můžete ověřit v Device Manager. Následující příklad ukazuje úspěšnou konfiguraci karty Radeon Instinct MI25 na virtuálním počítači Azure NVv4.
<br />

![Snímek obrazovky, který zobrazuje úspěšnou konfiguraci karty Instinct MI25 na virtuálním počítači Azure NVv4.](./media/n-series-amd-driver-setup/device-manager.png)

Pomocí nástroje Dxdiag můžete ověřit zobrazované vlastnosti GPU, včetně video RAM. Následující příklad ukazuje oddíl 1/2 karty Instinct MI25 na virtuálním počítači Azure NVv4.
<br />
![Snímek obrazovky, který zobrazuje oddíl 1/2 karty Instinct MI25 na virtuálním počítači Azure NVv4.](./media/n-series-amd-driver-setup/dxdiag-output-new.png)

Pokud používáte Windows 10 Build 1903 nebo vyšší, dxdiag na kartě Display (zobrazení) nezobrazí žádné informace. Použijte prosím v dolní části možnost Uložit všechny informace a ve výstupním souboru se zobrazí informace související s procesorem AMD MI25 GPU.

![Vlastnosti ovladače GPU](./media/n-series-amd-driver-setup/dxdiag-details.png)
