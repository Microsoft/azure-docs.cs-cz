---
title: Velikosti virtuálních počítačů Azure Linux – HPC
description: Obsahuje seznam různých velikostí dostupných pro virtuální počítače s vysokým výkonem pro Linux v Azure. Uvádí informace o počtu vCPU, datových discích a síťových rozhraních a propustnosti úložiště a šířce pásma sítě pro velikosti v této sérii.
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: jonbeck
ms.openlocfilehash: 214ab48b6b0dca37eff3b3f155aaa92afc7fee16
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034898"
---
# <a name="high-performance-compute-virtual-machine-sizes"></a>Vysoce výkonné výpočetní velikosti virtuálních počítačů

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


### <a name="mpi"></a>MPI 

Velikosti virtuálních počítačů s podporou SR-IOV v Azure umožňují téměř jakýkoli charakter MPI.
Na virtuálních počítačích, které nejsou povolené SR-IOV, se podporují jenom verze Intel MPI 5. x. Novější verze (2017, 2018) běhové knihovny Intel MPI mohou nebo nemusí být kompatibilní s ovladači RDMA pro Azure Linux.


### <a name="supported-os-images"></a>Podporované image operačních systémů
 
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
  
  Následující příkaz nainstaluje nejnovější InfiniBandDriverLinux rozšíření verze 1,0 na všechny virtuální počítače podporující RDMA v existující sadě škálování virtuálního počítače s názvem *myVMSS* nasazenou ve skupině prostředků s názvem *myResourceGroup*:
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

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]  

  Další informace o povolení InfiniBand, nastavení MPI najdete v tématu [Povolení InfiniBand](../workloads/hpc/enable-infiniband.md).


### <a name="cluster-configuration-options"></a>Možnosti konfigurace clusteru

Azure poskytuje několik možností pro vytváření clusterů virtuálních počítačů se systémem Linux HPC, které mohou komunikovat pomocí sítě RDMA, včetně těchto: 

* **Virtuální počítače** – nasazení virtuálních počítačů HPC s podporou RDMA ve stejné skupině dostupnosti (při použití modelu nasazení Azure Resource Manager). Pokud používáte model nasazení Classic, nasaďte virtuální počítače do stejné cloudové služby. 

* **Virtual Machine Scale Sets** – v sadě škálování virtuálního počítače Nezapomeňte toto nasazení omezit na jednu skupinu umístění. Například v šabloně Správce prostředků nastavte vlastnost `singlePlacementGroup` na `true`. 

* **MPI mezi virtuálními počítači** – Pokud se vyžaduje komunikace MPI mezi virtuálními počítači, ujistěte se, že jsou virtuální počítače ve stejné skupině dostupnosti nebo ve stejné sadě škálování virtuálního počítače.

* **Azure CycleCloud** – vytvoření clusteru HPC ve [službě Azure CycleCloud](/azure/cyclecloud/) pro spouštění úloh MPI v uzlech systému Linux.

* **Azure Batch** – vytvoření fondu [Azure Batch](/azure/batch/) pro spouštění úloh MPI na výpočetních uzlech se systémem Linux. Další informace najdete v tématu [použití instancí podporujících technologii RDMA nebo GPU ve fondech služby Batch](../../batch/batch-pool-compute-intensive-sizes.md). Pro spouštění úloh založených na kontejnerech ve službě Batch se také zobrazí projekt [Batch loděnice](https://github.com/Azure/batch-shipyard) .

* Sada **Microsoft HPC pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) podporuje ve výpočetních uzlech nasazených na virtuálních počítačích Azure, které jsou spravovány hlavním uzlem Windows serveru, několik distribucí Linux. Ukázkové nasazení najdete [v tématu Vytvoření clusteru HPC Pack Linux RDMA v Azure](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam).


### <a name="network-considerations"></a>Síťové požadavky
* U virtuálních počítačů se systémem Linux bez SR-IOV v Azure je eth1 vyhrazena pro síťový provoz RDMA. Neměňte žádné nastavení eth1 ani žádné informace v konfiguračním souboru, které odkazují na tuto síť.
* V případě virtuálních počítačů s podporou SR-IOV (s a HC-Series) je ib0 vyhrazený pro síťový provoz RDMA.
* Síť RDMA v Azure rezervuje adresní prostor 172.16.0.0/16. Pokud chcete spouštět aplikace MPI na instancích nasazených ve službě Azure Virtual Network, ujistěte se, že adresní prostor virtuální sítě nepřekrývá síť RDMA.
* V závislosti na zvoleném nástroji pro správu clusteru může být pro spuštění úloh MPI nutná další konfigurace systému. Například na clusteru virtuálních počítačů možná budete muset vytvořit vztah důvěryhodnosti mezi uzly clusteru generováním klíčů SSH nebo vytvořením přihlašovacích údajů SSH bez hesla.


## <a name="other-sizes"></a>Jiné velikosti
- [Obecné účely](sizes-general.md)
- [Optimalizované z hlediska výpočetních služeb](sizes-compute.md)
- [Optimalizované z hlediska paměti](sizes-memory.md)
- [Optimalizované z hlediska úložiště](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o tom, jak nastavit, optimalizovat a škálovat [úlohy prostředí HPC](../workloads/hpc/configure.md) v Azure.
- Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám pomůžou porovnat výpočetní výkon napříč SKU Azure.
