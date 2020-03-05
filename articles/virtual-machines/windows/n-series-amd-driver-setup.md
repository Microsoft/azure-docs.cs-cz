---
title: Nastavení ovladače GPU řady AMD pro Windows N-Series pro Windows
description: Jak nastavit ovladače AMD GPU pro virtuální počítače řady N-Series s Windows serverem nebo Windows v Azure
services: virtual-machines-windows
author: vikancha
manager: jkabat
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/4/2019
ms.author: vikancha
ms.openlocfilehash: 164f07f6545c1c225814958bba5722536b11a9b4
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2020
ms.locfileid: "78269435"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>Instalace ovladačů AMD GPU pro virtuální počítače řady N-Series s Windows

Aby bylo možné využít možnosti GPU pro nové virtuální počítače řady Azure NVv4, na kterých běží Windows, musí být nainstalované ovladače GPU AMD. Rozšíření ovladače AMD bude k dispozici v nadcházejících týdnech. Tento článek poskytuje podporované operační systémy, ovladače a Postup ruční instalace a ověření.

Základní specifikace, kapacity úložiště a podrobnosti o discích najdete v tématu [velikosti virtuálních počítačů s Windows GPU](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).



## <a name="supported-operating-systems-and-drivers"></a>Podporované operační systémy a ovladače

| Operační systém | Ovladač |
| -------- |------------- |
| Windows 10 EVD-Build 1903 <br/><br/>Windows 10 – Build 1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [20. q 1.1](https://download.microsoft.com/download/3/8/9/3893407b-e8aa-4079-8592-735d7dd1c19a/Radeon-Pro-Software-for-Enterprise-GA.exe) (. exe) |


## <a name="driver-installation"></a>Instalace ovladače

1. Připojte se pomocí vzdálené plochy ke každému virtuálnímu počítači NVv4-Series.

2. Pokud jste zákazník ve verzi Preview NVv4, zastavte prosím virtuální počítač a počkejte, než se přesune do stavu Zastaveno (přidělení zrušeno).

3. Spusťte virtuální počítač a potom odinstalujte ovladač verze Preview spuštěním souboru "amdcleanuputility-x64. exe" umístěného ve složce ". ..\AMDCleanUninstallUtility". Přesná cesta se bude lišit v závislosti na tom, kde jsou předchozí instalační soubory ovladačů.  

4. Stáhněte si a nainstalujte nejnovější ovladač.

5. Restartujte virtuální počítač.

## <a name="verify-driver-installation"></a>Ověřit instalaci ovladače

Instalaci ovladače můžete ověřit v Device Manager. Následující příklad ukazuje úspěšnou konfiguraci karty Radeon Instinct MI25 na virtuálním počítači Azure NVv4.
<br />
vlastnosti ovladače ![GPU](./media/n-series-amd-driver-setup/device-manager.png)

Pomocí nástroje Dxdiag můžete ověřit zobrazované vlastnosti GPU, včetně video RAM. Následující příklad ukazuje 1/8. oddíl karty Radeon Instinct MI25 na virtuálním počítači Azure NVv4.
<br />
vlastnosti ovladače ![GPU](./media/n-series-amd-driver-setup/dxdiag.png)
