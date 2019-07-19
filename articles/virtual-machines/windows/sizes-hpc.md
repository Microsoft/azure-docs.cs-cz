---
title: Velikosti virtuálních počítačů Azure s Windows – HPC | Microsoft Docs
description: Obsahuje seznam různých velikostí dostupných pro virtuální počítače Windows s vysokým výkonem v Azure. Uvádí informace o počtu vCPU, datových discích a síťových rozhraních a propustnosti úložiště a šířce pásma sítě pro velikosti v této sérii.
services: virtual-machines-windows
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: amverma
ms.reviewer: jonbeck
ms.openlocfilehash: 6fd08ca912c14a50064f4b6da18334d8bf9df0ca
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871593"
---
# <a name="high-performance-compute-vm-sizes"></a>Vysoce výkonné výpočetní velikosti virtuálních počítačů

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


* **Operační systém** – Windows Server 2016 ve všech výše uvedených virtuálních počítačích řady HPC. Windows Server 2012 R2, Windows Server 2012 se podporuje taky na virtuálních počítačích s povolenou výjimkou SR a HC.

* **MPI** – velikosti virtuálních počítačů s povoleným rozhraním SR-IOV v Azure (bez použití rozhraní HC) umožňují téměř libovolné typy MPI, které se mají používat s Mellanox OFED.
V případě virtuálních počítačů s podporou SR-IOV podporované implementace MPI používají ke komunikaci mezi instancemi rozhraní Microsoft Network Direct (ND). Proto jsou podporovány pouze verze Microsoft MPI (MS-MPI) 2012 R2 nebo novější a Intel MPI 5. x. Novější verze (2017, 2018) běhové knihovny Intel MPI mohou nebo nemusí být kompatibilní s ovladači Azure RDMA.

* **Rozšíření virtuálního počítače InfiniBandDriverWindows** – na virtuálních počítačích s podporou RDMA přidejte rozšíření InfiniBandDriverWindows a povolte InfiniBand. Toto rozšíření Windows VM nainstaluje ovladače síťové sítě pro Windows (na virtuálních počítačích bez SR-IOV) nebo Mellanox ovladače OFED (na virtuálních počítačích SR-IOV) pro připojení RDMA.
V některých nasazeních instancí A8 a A8 se rozšíření HpcVmDrivers přidá automaticky. Všimněte si, že rozšíření virtuálního počítače HpcVmDrivers je zastaralé; nebude aktualizován. Pokud chcete přidat rozšíření virtuálního počítače do virtuálního počítače, můžete použít rutiny [Azure PowerShell](/powershell/azure/overview) . 

  Následující příkaz nainstaluje nejnovější rozšíření verze 1,0 InfiniBandDriverWindows na existující virtuální počítač s podporou RDMA s názvem *myVM* nasazený ve skupině prostředků s názvem *myResourceGroup* v oblasti *západní USA* :

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  ```
  Alternativně lze rozšíření virtuálních počítačů zahrnout do šablon Azure Resource Manager pro snadné nasazení s následujícím elementem JSON:
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverWindows",
  "typeHandlerVersion": "1.0",
  } 
  ```

  Následující příkaz nainstaluje nejnovější InfiniBandDriverWindows rozšíření verze 1,0 na všechny virtuální počítače podporující RDMA v existující sadě škálování virtuálního počítače s názvem *myVMSS* nasazenou ve skupině prostředků s názvem *myResourceGroup*:

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```

  Další informace najdete v tématu [rozšíření a funkce virtuálních počítačů](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Můžete také pracovat s rozšířeními pro virtuální počítače nasazené v [modelu nasazení Classic](classic/manage-extensions.md).

* **Adresní prostor síťového adres RDMA** – síť RDMA v Azure rezervuje adresní prostor 172.16.0.0/16. Pokud chcete spouštět aplikace MPI na instancích nasazených ve službě Azure Virtual Network, ujistěte se, že adresní prostor virtuální sítě nepřekrývá síť RDMA.


### <a name="cluster-configuration-options"></a>Možnosti konfigurace clusteru

Azure poskytuje několik možností pro vytváření clusterů virtuálních počítačů Windows HPC, které mohou komunikovat pomocí sítě RDMA, včetně těchto: 

* **Virtuální počítače** – nasazení virtuálních počítačů HPC s podporou RDMA ve stejné skupině dostupnosti (při použití modelu nasazení Azure Resource Manager). Pokud používáte model nasazení Classic, nasaďte virtuální počítače do stejné cloudové služby. 

* **Virtual Machine Scale Sets** – v sadě škálování virtuálního počítače Nezapomeňte toto nasazení omezit na jednu skupinu umístění. Například v šabloně správce prostředků nastavte `singlePlacementGroup` vlastnost na. `true` 

* **MPI mezi virtuálními počítači** – Pokud se vyžaduje komunikace MPI mezi virtuálními počítači, ujistěte se, že jsou virtuální počítače ve stejné skupině dostupnosti nebo ve stejné sadě škálování virtuálního počítače.

* **Azure CycleCloud** – vytvoření clusteru HPC v [Azure CycleCloud](/azure/cyclecloud/) pro spouštění úloh MPI v uzlech Windows

* **Azure Batch** – vytvořte fond [Azure Batch](/azure/batch/) pro spouštění úloh MPI na výpočetních uzlech Windows serveru. Další informace najdete v tématu [použití instancí podporujících technologii RDMA nebo GPU ve fondech služby Batch](../../batch/batch-pool-compute-intensive-sizes.md). Pro spouštění úloh založených na kontejnerech ve službě Batch se také zobrazí projekt [Batch loděnice](https://github.com/Azure/batch-shipyard) .

* **Sada Microsoft HPC Pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) obsahuje běhové prostředí pro MS-MPI, které používá síť Azure RDMA při nasazení na virtuální počítače s Windows s podporou RDMA. Například nasazení najdete v tématu [Nastavení clusteru Windows RDMA se sadou HPC Pack pro spouštění aplikací MPI](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="other-sizes"></a>Jiné velikosti
- [Obecné účely](sizes-general.md)
- [Optimalizované z hlediska výpočetních služeb](sizes-compute.md)
- [Optimalizované z hlediska paměti](../virtual-machines-windows-sizes-memory.md)
- [Optimalizované z hlediska úložiště](../virtual-machines-windows-sizes-storage.md)
- [Optimalizované z hlediska GPU](sizes-gpu.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další postup

- Kontrolní seznamy pro použití výpočetně náročných instancí se sadou HPC Pack na Windows serveru najdete v tématu [Nastavení clusteru Windows RDMA se sadou HPC Pack pro spouštění aplikací MPI](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

- Pokud chcete používat instance náročné na výpočetní výkon při spouštění aplikací MPI s Azure Batch, přečtěte si téma [použití úloh s více instancemi ke spouštění aplikací MPI (Message Passing Interface) v Azure Batch](../../batch/batch-mpi.md).

- Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám pomůžou porovnat výpočetní výkon napříč SKU Azure.
