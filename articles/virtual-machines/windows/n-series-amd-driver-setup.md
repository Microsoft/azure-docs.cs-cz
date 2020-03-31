---
title: Nastavení ovladače gpu AMD azure n-series pro Windows
description: Jak nastavit ovladače GPU AMD pro virtuální počítače řady N se systémem Windows Server nebo Windows v Azure
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
ms.openlocfilehash: 883dbc95ee77d03aee4c3231c6ab8c03f9f7f6e4
ms.sourcegitcommit: d0fd35f4f0f3ec71159e9fb43fcd8e89d653f3f2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2020
ms.locfileid: "80387831"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>Instalace ovladačů GPU AMD na virtuální chody řady N se systémem Windows

Chcete-li využít funkce GPU nových virtuálních zařízení řady Azure NVv4 se systémem Windows, musí být nainstalovány ovladače GPU AMD. Rozšíření ovladače AMD bude k dispozici v nadcházejících týdnech. Tento článek obsahuje podporované operační systémy, ovladače a postupy ruční instalace a ověření.

Základní specifikace, kapacity úložiště a podrobnosti o disku najdete [v tématu velikosti virtuálních počítačů GPU Windows](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).



## <a name="supported-operating-systems-and-drivers"></a>Podporované operační systémy a ovladače

| Operační systém | Ovladač |
| -------- |------------- |
| Windows 10 EVD - Sestavení 1903 <br/><br/>Windows 10 - Sestavení 1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [20.Q1.1](https://download.microsoft.com/download/3/8/9/3893407b-e8aa-4079-8592-735d7dd1c19a/Radeon-Pro-Software-for-Enterprise-GA.exe) (.exe) |


## <a name="driver-installation"></a>Instalace ovladače

1. Připojte se vzdálenou plochou ke každému virtuálnímu počítači řady NVv4.

2. Pokud jste zákazník s náhledem NVv4, zastavte virtuální ho a počkejte, až se přesune do stavu Zastaveno(Deallocated).

3. Spusťte virtuální hospodařící spouštění a stáhněte si nejnovější [nástroj AMD Cleanup Utility](https://download.microsoft.com/download/4/f/1/4f19b714-9304-410f-9c64-826404e07857/AMDCleanupUtilityni.exe). Odinstalujte existující ovladač spuštěním programu amdcleanuputility-x64.exe. Nepoužívejte žádný nástroj exisitng cleanup, který byl nainstalován s předchozím ovladačem.  

4. Stáhněte a nainstalujte nejnovější ovladač.

5. Restartujte virtuální počítač.

## <a name="verify-driver-installation"></a>Ověření instalace ovladače

Instalaci ovladače můžete ověřit ve Správci zařízení. Následující příklad ukazuje úspěšnou konfiguraci karty Radeon Instinct MI25 na virtuálním počítači Azure NVv4.
<br />
![Vlastnosti ovladače GPU](./media/n-series-amd-driver-setup/device-manager.png)

Pomocí dxdiagu můžete ověřit vlastnosti zobrazení GPU včetně grafické paměti RAM. Následující příklad ukazuje 1/8oddíl karty Radeon Instinct MI25 na virtuálním počítači Azure NVv4.
<br />
![Vlastnosti ovladače GPU](./media/n-series-amd-driver-setup/dxdiag.png)
