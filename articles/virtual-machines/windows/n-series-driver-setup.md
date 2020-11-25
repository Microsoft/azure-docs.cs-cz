---
title: Nastavení ovladače GPU NVIDIA řady N-Series pro Windows
description: Postup nastavení ovladačů NVIDIA GPU pro virtuální počítače řady N-Series s Windows serverem nebo Windows v Azure
author: vikancha-MSFT
manager: jkabat
ms.service: virtual-machines-windows
ms.topic: how-to
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: vikancha
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2dccfebed26c8064db697413e7417ae08d69a3ac
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022029"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-windows"></a>Instalace ovladačů NVIDIA GPU pro virtuální počítače řady N-Series s Windows 

Pokud chcete využívat možnosti GPU pro virtuální počítače řady Azure N-Series, které využívají grafické procesory NVIDIA, musíte nainstalovat ovladače GPU NVIDIA. [Rozšíření ovladače NVIDIA GPU](../extensions/hpccompute-gpu-windows.md) nainstaluje vhodné ovladače NVIDIA CUDA nebo Grid na virtuální počítač řady N-Series. Nainstalujte nebo spravujte rozšíření pomocí Azure Portal nebo nástrojů, jako jsou šablony Azure PowerShell nebo Azure Resource Manager. Podporované operační systémy a kroky nasazení najdete v [dokumentaci k rozšíření ovladače GPU NVIDIA](../extensions/hpccompute-gpu-windows.md) .

Pokud se rozhodnete nainstalovat ovladače NVIDIA GPU ručně, najdete v tomto článku podporované operační systémy, ovladače a postup instalace a ověření. Pro [virtuální počítače se systémem Linux](../linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)jsou k dispozici také informace o ruční instalaci ovladače.

Základní specifikace, kapacity úložiště a podrobnosti o discích najdete v tématu [velikosti virtuálních počítačů s Windows GPU](../sizes-gpu.md?toc=/azure/virtual-machines/windows/toc.json). 

[!INCLUDE [virtual-machines-n-series-windows-support](../../../includes/virtual-machines-n-series-windows-support.md)]

## <a name="driver-installation"></a>Instalace ovladače

1. Připojte se pomocí vzdálené plochy ke každému virtuálnímu počítači řady N-Series.

2. Stáhněte, extrahujte a nainstalujte podporovaný ovladač pro operační systém Windows.

Po instalaci ovladače mřížky na virtuálním počítači je potřeba restartovat počítač. Po instalaci ovladače CUDA není nutné restartovat počítač.

## <a name="verify-driver-installation"></a>Ověřit instalaci ovladače

Mějte na paměti, že ovládací panel NVIDIA je dostupný jenom s instalací ovladače mřížky. Pokud jste nainstalovali ovladače CUDA, ovládací panel NVIDIA nebude viditelný.

Instalaci ovladače můžete ověřit v Device Manager. Následující příklad ukazuje úspěšnou konfiguraci karty Tesla K80 na virtuálním počítači Azure NC.

![Vlastnosti ovladače GPU](./media/n-series-driver-setup/GPU_driver_properties.png)

Pokud chcete zadat dotaz na stav zařízení GPU, spusťte nástroj příkazového řádku [NVIDIA-SMI](https://developer.nvidia.com/nvidia-system-management-interface) nainstalovaný s ovladačem.

1. Otevřete příkazový řádek a přejděte do adresáře **C:\Program Files\NVIDIA Corporation\NVSMI** .

2. Spusťte `nvidia-smi`. Pokud je ovladač nainstalovaný, zobrazí se výstup podobný následujícímu. **GPU-util** zobrazuje **0%** , pokud na virtuálním počítači aktuálně neběží úloha GPU. Podrobnosti o verzi ovladače a GPU se mohou lišit od zobrazených.

![Stav zařízení NVIDIA](./media/n-series-driver-setup/smi.png)  

## <a name="rdma-network-connectivity"></a>Připojení k síti RDMA

Připojení k síti RDMA je možné povolit u virtuálních počítačů řady N-Series s podporou RDMA, jako je NC24r nasazených ve stejné skupině dostupnosti nebo do jedné skupiny umístění v sadě škálování virtuálního počítače. Aby bylo možné instalovat ovladače síťových zařízení systému Windows, které umožňují připojení RDMA, je nutné přidat rozšíření HpcVmDrivers. Pokud chcete přidat rozšíření virtuálního počítače do virtuálního počítače N-Series s povoleným RDMA, použijte pro Azure Resource Manager rutiny [Azure PowerShell](/powershell/azure/) .

K instalaci nejnovějšího rozšíření verze 1,1 HpcVMDrivers na existující virtuální počítač s podporou RDMA s názvem myVM v oblasti Západní USA:
  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  Další informace najdete v tématu [rozšíření virtuálních počítačů a funkce pro Windows](../extensions/features-windows.md).

Síť RDMA podporuje provoz rozhraní MPI (Message Passing Interface) pro aplikace běžící s [Microsoft MPI](/message-passing-interface/microsoft-mpi) nebo Intel MPI 5. x. 


## <a name="next-steps"></a>Další kroky

* Vývojáři, kteří sestavují aplikace náročné na grafické PROCESORy NVIDIA Tesla, mohou také stáhnout a nainstalovat nejnovější [sadu CUDA Toolkit](https://developer.nvidia.com/cuda-downloads). Další informace najdete v příručce pro [instalaci CUDA](https://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi).
