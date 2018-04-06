---
title: Instalace nástroje Azure ovladač N-series pro Windows | Microsoft Docs
description: Jak nastavit NVIDIA GPU ovladače pro N-series virtuální počítače se systémem Windows Server nebo Windows v Azure
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: f3950c34-9406-48ae-bcd9-c0418607b37d
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/04/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: efa8c2603d6ff4493656cda41306a5dad46bc5f3
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="set-up-gpu-drivers-for-n-series-vms-running-windows"></a>Nastavení GPU ovladače pro N-series virtuální počítače se systémem Windows 
Abyste mohli využívat možnosti GPU virtuální počítače Azure N-series spuštěna podporovaná verze systému Windows Server nebo Windows, musí být nainstalován NVIDIA grafické ovladače. Tento článek obsahuje kroky instalace ovladačů po nasadit virtuální počítač s N-series. Informace o instalaci ovladačů je také k dispozici pro [virtuální počítače s Linuxem](../linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Základní specifikace, kapacity úložiště a disku podrobnosti najdete v tématu [velikosti virtuálních počítačů Windows GPU](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

[!INCLUDE [virtual-machines-n-series-windows-support](../../../includes/virtual-machines-n-series-windows-support.md)]

## <a name="driver-installation"></a>Instalace ovladače

1. Připojte pomocí vzdálené plochy pro každý virtuální počítač N-series.

2. Stáhněte, extrahovat a nainstalujte ovladač podporované pro operační systém Windows.

Na virtuálních počítačích Azure vs je nutné provést restart po instalaci ovladačů. Na virtuálních počítačích NC není nutné restartovat počítač.

## <a name="verify-driver-installation"></a>Ověření instalace ovladačů

Můžete ověřit, instalace ovladačů ve Správci zařízení. Následující příklad ukazuje úspěšná konfigurace karty tesla – měrná K80 ve virtuálním počítači Azure názvového kontextu.

![Vlastnosti ovladač GPU](./media/n-series-driver-setup/GPU_driver_properties.png)

K dotazování na stav zařízení GPU, spusťte [nvidia smi](https://developer.nvidia.com/nvidia-system-management-interface) pomocí ovladače nainstalovaný nástroj příkazového řádku.

1. Otevřete příkazový řádek a změňte **C:\Program Files\NVIDIA Corporation\NVSMI** adresáře.

2. Spusťte `nvidia-smi`. Pokud je nainstalován ovladač zobrazí se výstup podobný následujícímu. Všimněte si, že **GPU Util** ukazuje **0 %** Pokud aktuálně používáte zatížení grafického procesoru na virtuálním počítači. Verze ovladače a GPU podrobnosti se může lišit od těch vidět.

![Stav zařízení NVIDIA](./media/n-series-driver-setup/smi.png)  

## <a name="rdma-network-connectivity"></a>Připojení k síti RDMA

Síťové připojení RDMA se dá nastavit na virtuálních počítačích podporující RDMA N-series, jako je NC24r nasazené ve stejné skupině dostupnosti nebo sadu škálování virtuálního počítače. Rozšíření HpcVmDrivers musí být přidaný do nainstalovat ovladače zařízení sítě systému Windows, které umožňují připojení RDMA. Chcete-li přidat rozšíření virtuálního počítače na virtuální počítač N-series podporou RDMA, použijte [prostředí Azure PowerShell](/powershell/azure/overview) rutiny pro Azure Resource Manager.

Chcete-li nainstalovat nejnovější verze 1.1 HpcVMDrivers rozšíření na existující virtuální počítač podporuje RDMA s názvem Můjvp v oblasti západní USA:
  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  Další informace najdete v tématu [rozšíření virtuálního počítače a funkce ve Windows](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Síť RDMA podporuje rozhraní MPI (Message Passing) provozu pro aplikace spuštěné s [Microsoft MPI](https://msdn.microsoft.com/library/bb524831(v=vs.85).aspx) nebo Intel MPI 5.x. 


## <a name="next-steps"></a>Další postup

* Vývojářům tvorbu GPU accelerated aplikací pro grafickými procesory tesla – měrná NVIDIA můžete také stáhnout a nainstalovat [CUDA Toolkit 9.1](https://developer.nvidia.com/cuda-downloads). Další informace najdete v tématu [Průvodce instalací CUDA](http://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi).


