---
title: Azure velikosti virtuálních počítačů s Windows – HPC | Dokumentace Microsoftu
description: Obsahuje seznam různých velikostí, které jsou k dispozici pro Windows vysoce výkonných výpočetních virtuálních počítačů v Azure. Obsahuje informace o počtu virtuálních procesorů, datové disky a síťové adaptéry, jakož i úložiště propustnost a šířku pásma sítě pro velikosti této série.
services: virtual-machines-windows
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: jonbeck;amverma
ms.openlocfilehash: 5fc5b5a287a421f93d3184ded3e429c5cff8fa3c
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/04/2019
ms.locfileid: "67566296"
---
# <a name="high-performance-compute-vm-sizes"></a>Velikosti virtuálních počítačů vysokovýkonné výpočty

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


* **Operační systém** – Windows Server 2016 na všechny výše uvedené HPC řady virtuálních počítačů. Windows Server 2012 R2, Windows Server 2012 jsou také podporovány na virtuálních počítačích bez rozhraní SR-IOV povoleno (tedy bez HB a hybridní připojení).

* **MPI** -SR-IOV povolit téměř jakékoli flavor MPI pro použití s Mellanox OFED povolené velikosti virtuálních počítačů v Azure (HB, hybridní připojení).
Podporovaná implementace MPI na jiné rozhraní SR-IOV povoleno virtuálních počítačů, pomocí rozhraní Microsoft sítě s přímým přístupem (ND) ke komunikaci mezi instancemi. Proto pouze Microsoft MPI (MS-MPI) 2012 R2 nebo novější a jsou podporovány verze 5.x Intel MPI. Novější verze (2017, 2018) Intel MPI Runtime knihovny může nebo nemusí být kompatibilní s ovladači Azure RDMA.

* **Rozšíření virtuálního počítače InfiniBandDriverWindows** – na virtuálních počítačích s podporou RDMA, přidejte InfiniBandDriverWindows rozšíření pro umožnění InfiniBand. Toto rozšíření virtuálního počítače Windows nainstaluje Windows Network Direct ovladače (na virtuálních počítačích bez rozhraní SR-IOV) nebo ovladače Mellanox OFED (na virtuálních počítačích rozhraní SR-IOV) pro připojení RDMA.
V některých nasazeních instancí A8 a a9 pro aplikace je automaticky přidán HpcVmDrivers rozšíření. Všimněte si, že je rozšíření virtuálního počítače HpcVmDrivers zastaralé; nebude aktualizován. Chcete-li přidat rozšíření virtuálního počítače k virtuálnímu počítači, můžete použít [prostředí Azure PowerShell](/powershell/azure/overview) rutiny. 

  Následující příkaz nainstaluje nejnovější verze 1.0 InfiniBandDriverWindows rozšíření na existující RDMA podporovat virtuální počítač s názvem *myVM* nasazených ve skupině prostředků s názvem *myResourceGroup* v  *USA – západ* oblasti:

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  ```
  Rozšíření virtuálních počítačů mohou být také součástí šablony Azure Resource Manageru pro snadné nasazení pomocí elementu JSON:
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverWindows",
  "typeHandlerVersion": "1.0",
  } 
  ```

  Následující příkaz nainstaluje nejnovější verze 1.0 InfiniBandDriverWindows rozšíření pro všechny virtuální počítače podporující RDMA v existující škálovací sady s názvem *myVMSS* nasazených ve skupině prostředků s názvem *myResourceGroup*:

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```

  Další informace najdete v tématu [funkce a rozšíření virtuálních počítačů](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Můžete se s rozšířeními pro virtuální počítače nasazené v také pracovat [modelu nasazení classic](classic/manage-extensions.md).

* **Adresní prostor sítě RDMA** – síťové RDMA v Azure si vyhrazuje 172.16.0.0/16 prostor adres. Ke spouštění aplikací MPI v nasazené instance ve službě Azure virtual network, ujistěte se, že se adresní prostor virtuální sítě nepřekrývá síť RDMA.


### <a name="cluster-configuration-options"></a>Možnosti konfigurace clusteru

Azure poskytuje několik možností, jak vytvářet clustery virtuálních počítačů Windows HPC, který může komunikovat pomocí sítě RDMA, včetně: 

* **Virtuální počítače** -nasadit virtuální počítače s podporou RDMA HPC ve stejné skupině dostupnosti (Pokud používáte model nasazení Azure Resource Manageru). Pokud používáte model nasazení classic, nasaďte virtuální počítače ve stejné cloudové službě. 

* **Škálovací sady virtuálních počítačů** - v virtuálního počítače škálovací sady, ujistěte se, že omezíte nasazení do jediné skupiny umístění. Například v šabloně Resource Manageru, nastavte `singlePlacementGroup` vlastnost `true`. 

* **MPI mezi virtuálními počítači** – Pokud MPI komunikace v případě potřeby mezi virtuální počítače (VM), ujistěte se, že virtuální počítače jsou ve stejné skupině dostupnosti nastavena nebo virtuální počítač stejné škálovací sady.

* **Azure CycleCloud** – vytvoření clusteru prostředí HPC v [Azure CycleCloud](/azure/cyclecloud/) ke spouštění úloh MPI na uzlech Windows.

* **Služba Azure Batch** – vytvoření [Azure Batch](/azure/batch/) výpočetní uzly fondu pro spouštění úloh MPI ve Windows serveru. Další informace najdete v tématu [použití podporující RDMA nebo s podporou grafického procesoru instancí ve fondech Batch](../../batch/batch-pool-compute-intensive-sizes.md). Viz také [Batch loděnice](https://github.com/Azure/batch-shipyard) projekt, pro spouštění úloh kontejneru v Batch.

* **Sady Microsoft HPC Pack** - [sady HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) zahrnuje běhové prostředí pro MS-MPI, které používá síť Azure RDMA při nasazení na virtuálních počítačích Windows s podporou RDMA. Například nasazení, najdete v článku [nastavení clusteru Windows RDMA pomocí sady HPC Pack pro spouštění aplikací MPI](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

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
