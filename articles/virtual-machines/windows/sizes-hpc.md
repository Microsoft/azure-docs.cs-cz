---
title: Azure velikosti virtuálních počítačů s Windows – HPC | Dokumentace Microsoftu
description: Obsahuje seznam různých velikostí, které jsou k dispozici pro Windows vysoce výkonných výpočetních virtuálních počítačů v Azure. Obsahuje informace o počtu virtuálních procesorů, datové disky a síťové adaptéry, jakož i úložiště propustnost a šířku pásma sítě pro velikosti této série.
services: virtual-machines-windows
documentationcenter: ''
author: jonbeck7
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/15/2018
ms.author: jonbeck
ms.openlocfilehash: d8ab1531c4e1fa33fbdba12a4ecbeb8908dd6a94
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/04/2018
ms.locfileid: "34654347"
---
# <a name="high-performance-compute-vm-sizes"></a>Velikosti virtuálních počítačů vysokovýkonné výpočty

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


* **Operační systém** – Windows Server 2016, Windows Server 2012 R2, Windows Server 2012

* **MPI** – Microsoft MPI (MS-MPI) 2012 R2 nebo novější, Intel MPI knihovny 5.x

  Podporovaná implementace MPI pomocí rozhraní Microsoft Network Direct ke komunikaci mezi instancemi. 

* **Adresní prostor sítě RDMA** – síťové RDMA v Azure si vyhrazuje 172.16.0.0/16 prostor adres. Ke spouštění aplikací MPI v nasazené instance ve službě Azure virtual network, ujistěte se, že se adresní prostor virtuální sítě nepřekrývá síť RDMA.

* **Rozšíření virtuálního počítače HpcVmDrivers** – na virtuálních počítačích s podporou RDMA, přidejte HpcVmDrivers rozšíření k instalaci ovladačů zařízení sítě Windows pro připojení RDMA. (V některých nasazeních instancí A8 a A9 HpcVmDrivers rozšíření se přidá automaticky.) Chcete-li přidat rozšíření virtuálního počítače k virtuálnímu počítači, můžete použít [prostředí Azure PowerShell](/powershell/azure/overview) rutiny. 

  
  Následující příkaz nainstaluje nejnovější rozšíření HpcVMDrivers verze 1.1 na existující RDMA podporovat virtuální počítač s názvem *myVM* nasazených ve skupině prostředků s názvem *myResourceGroup* v  *USA – západ* oblasti:

  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  
  Další informace najdete v tématu [funkce a rozšíření virtuálních počítačů](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Můžete se s rozšířeními pro virtuální počítače nasazené v také pracovat [modelu nasazení classic](classic/manage-extensions.md).


## <a name="using-hpc-pack"></a>Pomocí sady HPC Pack

[Sady Microsoft HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), od Microsoftu zdarma HPC clusteru a úlohy řešení správy, je jedna možnost vytvořit výpočetní cluster v Azure ke spouštění aplikací MPI založené na Windows a jiné úlohy HPC. HPC Pack 2012 R2 a novější verze obsahují běhové prostředí pro MS-MPI, které používá síť Azure RDMA při nasazení na virtuální počítače s podporou RDMA.



## <a name="other-sizes"></a>Další velikosti
- [Obecné účely](sizes-general.md)
- [Optimalizované z hlediska výpočetních služeb](sizes-compute.md)
- [Optimalizované z hlediska paměti](../virtual-machines-windows-sizes-memory.md)
- [Optimalizované z hlediska úložiště](../virtual-machines-windows-sizes-storage.md)
- [Optimalizované z hlediska GPU](sizes-gpu.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další postup

- Kontrolní seznamy, které výpočetně náročných instancí pomocí sady HPC Pack ve Windows serveru, naleznete v tématu [nastavení clusteru Windows RDMA pomocí sady HPC Pack pro spouštění aplikací MPI](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

- K použití výpočetně náročných instancí při spuštění aplikací MPI pomocí služby Azure Batch, najdete v článku [použití úkolů s více instancemi ke spouštění aplikací rozhraní MPI (Message Passing Interface) ve službě Azure Batch](../../batch/batch-mpi.md).

- Další informace o tom [Azure výpočetních jednotek (ACU)](acu.md) můžete porovnat výpočetní výkon jednotlivých SKU v Azure.




