---
title: Azure instalace ovladačů v instancích N-series pro Windows | Dokumentace Microsoftu
description: Jak nastavit ovladače NVIDIA GPU pro virtuální počítače řady N-series běží Windows Server nebo Windows v Azure
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
ms.date: 06/19/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 50d9ea88afc0e7d96d71b2ab26c8a8489ae41fee
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37442259"
---
# <a name="set-up-gpu-drivers-for-n-series-vms-running-windows"></a>Nastavit ovladače GPU pro virtuální počítače řady N-series s Windows 
Abyste mohli využívat výhod GPU virtuální počítače Azure řady N-series s Windows, musí být nainstalována ovladačů NVIDIA GPU. [Rozšíření ovladače GPU NVIDIA](../extensions/hpccompute-gpu-windows.md) nainstaluje vhodných ovladačů NVIDIA CUDA nebo mřížky na virtuální počítač řady N-series. Instalovat ani spravovat rozšíření pomocí webu Azure portal nebo nástrojů, jako je šablon Azure Resource Manageru nebo Azure Powershellu. Najdete v článku [dokumentaci rozšíření ovladače GPU NVIDIA](../extensions/hpccompute-gpu-windows.md) pro podporované operační systémy a kroky nasazení.

Pokud se rozhodnete pro instalaci ovladače GPU ručně, tento článek obsahuje podporované operační systémy, ovladačů a postup instalace a ověření. Informace o nastavení ruční ovladač je také k dispozici pro [virtuální počítače s Linuxem](../linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Základní specifikace, kapacity úložiště a podrobných informací o discích najdete v tématu [velikosti virtuálních počítačů s Windows GPU](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

[!INCLUDE [virtual-machines-n-series-windows-support](../../../includes/virtual-machines-n-series-windows-support.md)]

## <a name="driver-installation"></a>Instalace ovladače

1. Připojte pomocí vzdálené plochy pro jednotlivé virtuální počítače řady N-series.

2. Stažení, extrahování a instalaci podporované ovladače pro váš operační systém Windows.

Na virtuálních počítačích Azure NV je vyžadováno restartování po instalaci ovladače. Na virtuálních počítačích síťového adaptéru není restartování potřeba.

## <a name="verify-driver-installation"></a>Ověření instalace ovladače

Můžete ověřit instalaci ovladačů ve Správci zařízení. Následující příklad ukazuje úspěšná konfigurace Tesla K80 karty na síťový adaptér virtuálního počítače Azure.

![Vlastnosti ovladače GPU](./media/n-series-driver-setup/GPU_driver_properties.png)

Chcete-li zjistit stav zařízení GPU, spusťte [nvidia smi](https://developer.nvidia.com/nvidia-system-management-interface) s ovladačem nainstalovaný nástroj příkazového řádku.

1. Otevřete příkazový řádek a přejděte **C:\Program Files\NVIDIA Corporation\NVSMI** adresáře.

2. Spusťte `nvidia-smi`. Pokud je nainstalován ovladač se zobrazí výstup podobný následujícímu. Všimněte si, že **využití GPU** ukazuje **0 %** Pokud aktuálně používáte úlohu GPU na virtuálním počítači. Podrobnosti o GPU a verze ovladače se může lišit od těch, které jsou zobrazeny.

![Stav zařízení NVIDIA](./media/n-series-driver-setup/smi.png)  

## <a name="rdma-network-connectivity"></a>Připojení k síti přístup RDMA

Připojení k síti přístup RDMA dá nastavit pro virtuální počítače řady N-series s podporou RDMA, jako je NC24r nasazené ve stejné skupině dostupnosti nebo v jediné skupiny umístění ve škálovací sadě virtuálních počítačů. Rozšíření HpcVmDrivers musí přidat k instalaci ovladačů zařízení sítě Windows, které umožňují připojení RDMA. Chcete-li přidat rozšíření virtuálního počítače do virtuálních počítačů řady N-series podporou RDMA, použijte [prostředí Azure PowerShell](/powershell/azure/overview) rutiny pro Azure Resource Manageru.

Chcete-li nainstalovat nejnovější verzi 1.1 HpcVMDrivers rozšíření na existující virtuální počítač s podporou RDMA s názvem můjvp přesměrovat v oblasti USA – západ:
  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  Další informace najdete v tématu [funkcí a rozšíření virtuálních počítačů pro Windows](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Sítě RDMA podporuje rozhraní MPI (Message Passing Interface) provozu pro aplikace s [Microsoft MPI](https://msdn.microsoft.com/library/bb524831(v=vs.85).aspx) nebo Intel MPI 5.x. 


## <a name="next-steps"></a>Další postup

* Vývojáři aplikací – hardwarově akcelerovanou pro GPU NVIDIA Tesla můžete také stáhnout a nainstalovat nejnovější [CUDA Toolkit](https://developer.nvidia.com/cuda-downloads). Další informace najdete v tématu [Průvodce instalací CUDA](http://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi).


