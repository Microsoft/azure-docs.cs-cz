---
title: Řada H - Virtuální počítače Azure
description: Specifikace pro virtuální chody řady H.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 03/10/2020
ms.author: lahugh
ms.openlocfilehash: cb05a8c42c5710182f81d4bc1c76b78abe59c882
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79470499"
---
# <a name="h-series"></a>H-series

Virtuální počítače řady H jsou optimalizované pro aplikace řízené vysokými frekvencemi procesoru nebo velkou pamětí na základní požadavky. Virtuální počítače řady H jsou vybaveny procesorovými jádry 8 nebo 16 Intel Xeon E5 2667 v3, až 14 GB paměti RAM na jádro procesoru a bez hyperthreadingu. Řada H je vybavena 56 Gb/s Mellanox FDR InfiniBand v neblokující konfiguraci tuku pro konzistentní výkon RDMA. Virtuální servery řady H podporují technologie Intel MPI 5.x a MS-MPI.

ACU: 290–300

Úložiště Premium: Není podporováno

Ukládání úložiště Premium: Není podporováno

Migrace za provozu: Není podporována

Aktualizace pro zachování paměti: Není podporováno

| Velikost | Virtuální procesory | Procesor | Paměť (GB) | Šířka pásma paměti GB/s | Základní frekvence procesoru (GHz) | Frekvence všech jader (GHz, špička) | Jednojádrová frekvence (GHz, špička) | Výkon RDMA (Gb/s) | Podpora MPI | Dočasné úložiště (GB) | Max. datových disků | Maximální síťové karty Ethernet |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8   | 8  | Intel Xeon E5 2667 v3 | 56 | 40 | 3,2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1000 | 32 | 2 |
| Standard_H16  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3,2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H8m  | 8  | Intel Xeon E5 2667 v3 | 112 | 40 | 3,2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1000 | 32 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3,2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H16r <sup>1</sup>  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3,2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| <sup>Standard_H16mr</sup> | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3,2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |

<sup>1</sup> Pro aplikace MPI je vyhrazená síť back-endRD povolena sítí FDR InfiniBand.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]


## <a name="supported-os-images-linux"></a>Podporované obrázky operačního systému (Linux)
 
Azure Marketplace má mnoho linuxových distribucí, které podporují připojení RDMA:
  
* **HPC založená na CentOS** - Pro virtuální počítače bez SR-IOV, verze 6.5 HPC založená na CentOS nebo novější verze jsou vhodné až 7.5. Pro virtuální aplikace řady H se doporučují verze 7.1 až 7.5. Ovladače RDMA a Intel MPI 5.1 jsou nainstalované na virtuálním počítači.
  Pro virtuální počítače SR-IOV je CentOS-HPC 7.6 optimalizován a předinstalován s nainstalovanými ovladači RDMA a různými balíčky MPI.
  Pro další image virtuálního počítače RHEL/CentOS přidejte rozšíření InfiniBandLinux, které povolí infiniBand. Toto rozšíření virtuálního počítače s Linuxem nainstaluje ovladače Mellanox OFED (na virtuálních počítačích SR-IOV) pro připojení RDMA. Následující rutina prostředí PowerShell nainstaluje nejnovější verzi (verze 1.0) rozšíření InfiniBandDriverLinux na existující virtuální počítač podporující RDMA. Virtuální ms podporující RDMA se jmenuje *myVM* a nasadí se ve skupině prostředků s názvem *myResourceGroup* v oblasti *ZÁPADNÍ USA* takto:

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  ```
  Rozšíření virtuálních počítačů lze také zahrnout do šablon Azure Resource Manager pro snadné nasazení s následujícím prvkem JSON:
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverLinux",
  "typeHandlerVersion": "1.0",
  } 
  ```
  
  Následující příkaz nainstaluje nejnovější verzi 1.0 InfiniBandDriverLinux rozšíření na všechny virtuální počítače podporující RDMA v existující škálovací sadě virtuálních strojů s názvem *myVMSS* nasazené ve skupině prostředků s názvem *myResourceGroup*:
  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```
  
  > [!NOTE]
  > Na obrázcích HPC založených na CentOS jsou aktualizace jádra zakázány v konfiguračním **souboru yum.** Důvodem je, že linuxové ovladače RDMA jsou distribuovány jako balíček RPM a aktualizace ovladačů nemusí fungovat, pokud je aktualizováno jádro.
  >
  

* **SUSE Linux Enterprise Server** - SLES 12 SP3 pro HPC, SLES 12 SP3 pro HPC (Premium), SLES 12 SP1 pro HPC, SLES 12 SP1 pro HPC (Premium), SLES 12 SP4 a SLES 15. Ovladače RDMA jsou nainstalovány a balíčky MpI Intel jsou distribuovány na virtuálním počítači. Nainstalujte MPI spuštěním následujícího příkazu:

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
  
* **Ubuntu** - Ubuntu Server 16.04 LTS, 18.04 LTS. Konfigurace ovladačů RDMA na virtuálním počítači a registrace u společnosti Intel ke stažení technologie Intel MPI:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../includes/virtual-machines-common-ubuntu-rdma.md)]  

  Další podrobnosti o povolení infinibandu, nastavení MPI, naleznete [v tématu Povolit InfiniBand](./workloads/hpc/enable-infiniband.md).

## <a name="other-sizes"></a>Jiné velikosti

- [Obecné účely](sizes-general.md)
- [Optimalizované z hlediska paměti](sizes-memory.md)
- [Optimalizované z hlediska úložiště](sizes-storage.md)
- [Optimalizované z hlediska GPU](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám můžou pomoct porovnat výpočetní výkon napříč virtuálními jednotkami Azure.
