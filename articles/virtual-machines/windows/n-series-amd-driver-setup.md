---
title: Nastavení ovladače GPU řady AMD pro Windows N-Series pro Windows
description: Jak nastavit ovladače AMD GPU pro virtuální počítače řady N-Series s Windows serverem nebo Windows v Azure
author: vikancha
manager: jkabat
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/4/2019
ms.author: vikancha
ms.openlocfilehash: 1bcc13db3f503c80fda71a2104d0ff8d99e67df6
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2020
ms.locfileid: "83197998"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>Instalace ovladačů AMD GPU pro virtuální počítače řady N-Series s Windows

Aby bylo možné využít možnosti GPU pro nové virtuální počítače řady Azure NVv4, na kterých běží Windows, musí být nainstalované ovladače GPU AMD. [Rozšíření ovladače AMD GPU](../extensions/hpccompute-amd-gpu-windows.md) nainstaluje ovladače AMD GPU na virtuálním počítači s NVv4-Series. Nainstalujte nebo spravujte rozšíření pomocí Azure Portal nebo nástrojů, jako jsou šablony Azure PowerShell nebo Azure Resource Manager. Podporované operační systémy a kroky nasazení najdete v [dokumentaci k rozšíření ovladače AMD GPU](../extensions/hpccompute-amd-gpu-windows.md) .

Pokud se rozhodnete nainstalovat ovladače AMD GPU ručně, najdete v tomto článku podporované operační systémy, ovladače a postup instalace a ověření.

Na virtuálních počítačích s NVv4 jsou podporované jenom ovladače GPU publikované Microsoftem. Neinstalujte prosím ovladače GPU z žádného jiného zdroje.

Základní specifikace, kapacity úložiště a podrobnosti o discích najdete v tématu [velikosti virtuálních počítačů s Windows GPU](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).



## <a name="supported-operating-systems-and-drivers"></a>Podporované operační systémy a ovladače

| Operační systém | Ovladač |
| -------- |------------- |
| Windows 10 EVD-Build 1903 <br/><br/>Windows 10 – Build 1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [20. q 1.1](https://download.microsoft.com/download/3/8/9/3893407b-e8aa-4079-8592-735d7dd1c19a/Radeon-Pro-Software-for-Enterprise-GA.exe) (. exe) |


## <a name="driver-installation"></a>Instalace ovladače

1. Připojte se pomocí vzdálené plochy ke každému virtuálnímu počítači NVv4-Series.

2. Pokud jste zákazník ve verzi Preview NVv4, zastavte prosím virtuální počítač a počkejte, než se přesune do stavu Zastaveno (přidělení zrušeno).

3. Spusťte prosím virtuální počítač a Stáhněte si nejnovější [Nástroj AMD Cleanup](https://download.microsoft.com/download/4/f/1/4f19b714-9304-410f-9c64-826404e07857/AMDCleanupUtilityni.exe). Existující ovladač odinstalujte spuštěním příkazu "amdcleanuputility-x64. exe". Nepoužívejte prosím žádný existující Nástroj pro čištění, který byl nainstalovaný s předchozím ovladačem.  

4. Stáhněte si a nainstalujte nejnovější ovladač.

5. Restartujte virtuální počítač.

## <a name="verify-driver-installation"></a>Ověřit instalaci ovladače

Instalaci ovladače můžete ověřit v Device Manager. Následující příklad ukazuje úspěšnou konfiguraci karty Radeon Instinct MI25 na virtuálním počítači Azure NVv4.
<br />
![Vlastnosti ovladače GPU](./media/n-series-amd-driver-setup/device-manager.png)

Pomocí nástroje Dxdiag můžete ověřit zobrazované vlastnosti GPU, včetně video RAM. Následující příklad ukazuje oddíl 1/2 karty Instinct MI25 na virtuálním počítači Azure NVv4.
<br />
![Vlastnosti ovladače GPU](./media/n-series-amd-driver-setup/dxdiag-output.png)

Pokud používáte Windows 10 Build 1903 nebo vyšší, dxdiag na kartě Display (zobrazení) nezobrazí žádné informace. Použijte prosím v dolní části možnost Uložit všechny informace a ve výstupním souboru se zobrazí informace související s procesorem AMD MI25 GPU.

![Vlastnosti ovladače GPU](./media/n-series-amd-driver-setup/dxdiag-details.png)


