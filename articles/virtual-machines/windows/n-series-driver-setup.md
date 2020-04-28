---
title: Nastavení ovladače GPU řady Azure N pro Windows
description: Jak nastavit ovladače NVIDIA GPU pro virtuální počítače řady N se systémem Windows Server nebo Windows v Azure
author: vikancha
manager: jkabat
ms.service: virtual-machines-windows
ms.topic: how-to
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: vikancha
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bc11937410bf0307a00895e0ebd1f01a58bd1b1b
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81865779"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-windows"></a>Instalace ovladačů NVIDIA GPU na virtuální chodů řady N se systémem Windows 

Chcete-li využít funkce GPU virtuálních zařízení řady Azure N se systémem Windows, musí být nainstalovány ovladače GPU NVIDIA. [Rozšíření ovladače GPU NVIDIA](../extensions/hpccompute-gpu-windows.md) nainstaluje příslušné ovladače NVIDIA CUDA nebo GRID na virtuální počítač řady N. Nainstalujte nebo spravujte rozšíření pomocí portálu Azure nebo nástrojů, jako jsou šablony Azure PowerShellnebo Azure Resource Manager. Informace o podporovaných operačních systémech a krocích nasazení naleznete v [dokumentaci k rozšíření ovladače GPU NVIDIA.](../extensions/hpccompute-gpu-windows.md)

Pokud se rozhodnete nainstalovat ovladače GPU ručně, tento článek obsahuje podporované operační systémy, ovladače a kroky instalace a ověření. Informace o ručním nastavení ovladače jsou k dispozici také pro [virtuální počítače s Linuxem](../linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Základní specifikace, kapacity úložiště a podrobnosti o disku najdete [v tématu velikosti virtuálních počítačů GPU Windows](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

[!INCLUDE [virtual-machines-n-series-windows-support](../../../includes/virtual-machines-n-series-windows-support.md)]

## <a name="driver-installation"></a>Instalace ovladače

1. Připojte se vzdálenou plochou ke každému virtuálnímu počítači řady N.

2. Stáhněte, extrahujte a nainstalujte podporovaný ovladač pro operační systém Windows.

Po instalaci ovladače GRID na virtuální mkér, restartování je vyžadováno. Po instalaci ovladače CUDA není restartování vyžadováno.

## <a name="verify-driver-installation"></a>Ověření instalace ovladače

Vezměte prosím na vědomí, že ovládací panel Nvidia je přístupný pouze s instalací ovladače GRID. Pokud jste nainstalovali ovladače CUDA, nebude ovládací panel Nvidia viditelný.

Instalaci ovladače můžete ověřit ve Správci zařízení. Následující příklad ukazuje úspěšnou konfiguraci karty Tesla K80 na virtuálním počítači Azure NC.

![Vlastnosti ovladače GPU](./media/n-series-driver-setup/GPU_driver_properties.png)

Chcete-li zadat dotaz na stav zařízení GPU, spusťte nástroj příkazového řádku [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) nainstalovaný s ovladačem.

1. Otevřete příkazový řádek a změňte na adresář **C:\Program Files\NVIDIA Corporation\NVSMI.**

2. Spusťte `nvidia-smi`. Pokud je nainstalován ovladač, zobrazí se výstup podobný následujícímu. **GPU-Util** zobrazuje **0 %,** pokud aktuálně spouštějíte úlohu GPU na virtuálním počítači. Vaše verze ovladače a podrobnosti gpu se mohou lišit od zobrazených verzí.

![Stav zařízení NVIDIA](./media/n-series-driver-setup/smi.png)  

## <a name="rdma-network-connectivity"></a>Připojení k síti RDMA

Připojení k síti RDMA lze povolit na virtuálních počítačích řady N podporujících RDMA, jako je NC24r nasazených ve stejné skupině dostupnosti nebo ve skupině s jedním umístěním ve škálovací sadě virtuálních strojů. K instalaci ovladačů síťových zařízení se systémem Windows, které umožňují připojení rdma, je nutné přidat rozšíření HpcVmDrivers. Pokud chcete přidat rozšíření virtuálního počítače do virtuálního počítače řady N s podporou RDMA, použijte rutiny [Azure PowerShell](/powershell/azure/overview) pro Azure Resource Manager.

Instalace nejnovější verze 1.1 HpcVMDrivers rozšíření na existující virtuální počítač podporující RDMA s názvem myVM v oblasti Západní USA:
  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  Další informace naleznete v [tématu Rozšíření a funkce virtuálních strojů pro Windows](extensions-features.md).

Síť RDMA podporuje přenosmpi (Message Passing Interface) pro aplikace spuštěné s [rozhraním Microsoft MPI](https://docs.microsoft.com/message-passing-interface/microsoft-mpi) nebo Intel MPI 5.x. 


## <a name="next-steps"></a>Další kroky

* Vývojáři, kteří budují aplikace akcelerované GPU pro GPU NVIDIA Tesla, mohou také stáhnout a nainstalovat nejnovější [CUDA Toolkit](https://developer.nvidia.com/cuda-downloads). Další informace naleznete v [instalační příručce CUDA](https://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi).


