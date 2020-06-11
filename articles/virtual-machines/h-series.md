---
title: H-Series – Azure Virtual Machines
description: Specifikace pro virtuální počítače řady H-Series.
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: article
ms.date: 03/10/2020
ms.author: jushiman
ms.openlocfilehash: f79dcb8886985d60a1ed82e1a77d231cf7d3ad24
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/10/2020
ms.locfileid: "84678677"
---
# <a name="h-series"></a>H-series

Virtuální počítače H-Series jsou optimalizované pro aplikace řízené vysokými kmitočty procesoru nebo velkým množstvím paměti podle základních požadavků. Virtuální počítače řady H-Series funkce 8 nebo 16 Intel Xeon E5 2667 V3 procesory, až 14 GB paměti RAM na jádro procesoru a žádné podprocesy. Funkce H-Series 56 GB/s Mellanox FDR InfiniBand v neblokované konfiguraci stromu FAT pro zajištění konzistentního výkonu RDMA. Virtuální počítače H-series podporují Intel MPI 5. x a MS-MPI.

ACU: 290–300

Premium Storage: nepodporováno

Ukládání Premium Storage do mezipaměti: nepodporováno

Migrace za provozu: nepodporováno

Aktualizace pro zachování paměti: nepodporováno

| Velikost | Virtuální procesory | Procesor | Paměť (GB) | Propustnost paměti GB/s | Základní frekvence procesoru (GHz) | Frekvence všech jader (GHz, špička) | Frekvence s jedním jádrem (GHz, špička) | Výkon RDMA (GB/s) | Podpora MPI | Dočasné úložiště (GB) | Max. datových disků | Maximální propustnost disku: IOPS | Maximální počet síťových karet sítě Ethernet |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8   | 8  | Intel Xeon E5 2667 V3 | 56 | 40 | 3.2 | 3.3 | 3,6 | - | Intel 5. x, MS-MPI | 1000 | 32 | 32 × 500 | 2 |
| Standard_H16  | 16 | Intel Xeon E5 2667 V3 | 112 | 80 | 3.2 | 3.3 | 3,6 | - | Intel 5. x, MS-MPI | 2000 | 64 | 64 × 500 | 4 |
| Standard_H8m  | 8  | Intel Xeon E5 2667 V3 | 112 | 40 | 3.2 | 3.3 | 3,6 | - | Intel 5. x, MS-MPI | 1000 | 32 | 32 × 500 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 2667 V3 | 224 | 80 | 3.2 | 3.3 | 3,6 | - | Intel 5. x, MS-MPI | 2000 | 64 | 64 × 500 | 4 |
| Standard_H16r <sup>1</sup>  | 16 | Intel Xeon E5 2667 V3 | 112 | 80 | 3.2 | 3.3 | 3,6 | 56 | Intel 5. x, MS-MPI | 2000 | 64 | 64 × 500 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon E5 2667 V3 | 224 | 80 | 3.2 | 3.3 | 3,6 | 56 | Intel 5. x, MS-MPI | 2000 | 64 | 64 × 500 | 4 |

<sup>1</sup> pro MPI aplikace je v síti FDR InfiniBand povolena vyhrazená síť s back-end službou RDMA.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]


## <a name="supported-os-images-linux"></a>Podporované image operačního systému (Linux)
 
Azure Marketplace má mnoho distribucí systému Linux, které podporují připojení RDMA:
  
* **HPC založená na CentOS** – pro virtuální počítače, které nejsou povolené SR-IOV, CentOS verze 6,5 HPC nebo novější, jsou vhodné až 7,5. Pro virtuální počítače řady H-Series doporučujeme verze 7,1 až 7,5. Na virtuálním počítači jsou nainstalované ovladače RDMA a Intel MPI 5,1.
  Pro virtuální počítače SR-IOV přináší CentOS-HPC 7,6 optimalizované a předem načtené s ovladači RDMA a různými nainstalovanými balíčky MPI.
  Pro jiné image virtuálních počítačů s RHEL/CentOS přidejte rozšíření InfiniBandLinux, aby bylo možné InfiniBand povolit. Tato přípona virtuálního počítače se systémem Linux nainstaluje ovladače Mellanox OFED (na virtuálních počítačích SR-IOV) pro připojení RDMA. Následující rutina prostředí PowerShell nainstaluje nejnovější verzi (verze 1,0) rozšíření InfiniBandDriverLinux na existující virtuální počítač s podporou RDMA. Virtuální počítač s podporou RDMA má název *myVM* a do skupiny prostředků s názvem *myResourceGroup* ve *západní USA* oblasti se nasadí takto:

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  ```
  Alternativně lze rozšíření virtuálních počítačů zahrnout do šablon Azure Resource Manager pro snadné nasazení pomocí následujícího elementu JSON:
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverLinux",
  "typeHandlerVersion": "1.0",
  } 
  ```
  
  Následující příkaz nainstaluje nejnovější InfiniBandDriverLinux rozšíření verze 1,0 na všechny virtuální počítače podporující RDMA ve stávající sadě virtuálních počítačů s názvem *myVMSS* nasazenou ve skupině prostředků s názvem *myResourceGroup*:
  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```
  
  > [!NOTE]
  > U imagí HPC založených na CentOS jsou aktualizace jádra v konfiguračním souboru **Yumu** zakázané. Důvodem je to, že ovladače pro Linux RDMA jsou distribuované jako balíček ot./min. a aktualizace ovladačů nemusí fungovat, pokud je jádro aktualizované.
  >
  

* **SUSE Linux Enterprise Server** – SLES 12 SP3 pro HPC, SLES 12 SP3 pro HPC (Premium), SLES 12 SP1 pro HPC, SLES 12 SP1 pro HPC (Premium), SLES 12 SP4 a SLES 15. Jsou nainstalované ovladače RDMA a na virtuálním počítači jsou distribuované balíčky Intel MPI. Nainstalujte MPI spuštěním následujícího příkazu:

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
  
* **Ubuntu** -Ubuntu Server 16,04 LTS, 18,04 LTS. Nakonfigurujte na virtuálním počítači ovladače RDMA a zaregistrujte se pomocí Intel pro stažení Intel MPI:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../includes/virtual-machines-common-ubuntu-rdma.md)]  

  Další informace o povolení InfiniBand, nastavení MPI najdete v tématu [Povolení InfiniBand](./workloads/hpc/enable-infiniband.md).

## <a name="other-sizes"></a>Jiné velikosti

- [Obecné účely](sizes-general.md)
- [Optimalizované z hlediska paměti](sizes-memory.md)
- [Optimalizované z hlediska úložiště](sizes-storage.md)
- [Optimalizované z hlediska GPU](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám pomůžou porovnat výpočetní výkon napříč SKU Azure.
