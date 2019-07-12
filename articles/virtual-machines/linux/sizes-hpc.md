---
title: Velikosti virtuálních počítačů Azure s Linuxem – HPC | Dokumentace Microsoftu
description: Obsahuje seznam různých velikostí, které jsou k dispozici pro Linux vysoce výkonných výpočetních virtuálních počítačů v Azure. Obsahuje informace o počtu virtuálních procesorů, datové disky a síťové adaptéry, jakož i úložiště propustnost a šířku pásma sítě pro velikosti této série.
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: jonbeck
ms.openlocfilehash: 847f25d9be1a8654bbc0435d7874acb0ff793304
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67695604"
---
# <a name="high-performance-compute-virtual-machine-sizes"></a>Vysokovýkonné výpočetní velikosti virtuálních počítačů

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


### <a name="mpi"></a>MPI 

SR-IOV povoleno velikosti virtuálních počítačů v Azure umožňují téměř jakékoli flavor MPI, který se má použít.
Na jiných rozhraní SR-IOV povoleno virtuálních počítačích podporovány jsou pouze verze 5.x Intel MPI. Novější verze (2017, 2018) Intel MPI Runtime knihovny může nebo nemusí být kompatibilní s ovladači Azure Linux RDMA.


### <a name="supported-os-images"></a>Podporované Image operačního systému
 
Tržiště Azure Marketplace nabízí řadu distribucí systému Linux, které podporují RDMA připojení:
  
* **Založené na centOS HPC** – bez rozhraní SR-IOV povolená virtuálních počítačů založených na CentOS verze 6.5 HPC nebo novější verze, až 7.5 jsou vhodné. Pro virtuální počítače řady H-series se doporučuje verze 7.1 k 7.5. Na virtuálním počítači se nainstalují ovladače RDMA a Intel MPI 5.1.
  Pro virtuální počítače s SR-IOV CentOS HPC 7.6 pochází optimalizované a předem načtené RDMA ovladačů a balíčky různých MPI, které jsou nainstalovány.
  Vyhledáme další Image virtuálního počítače RHEL nebo CentOS přidejte InfiniBandLinux rozšíření pro umožnění InfiniBand. Toto rozšíření virtuálního počítače s Linuxem instaluje ovladače Mellanox OFED (na virtuálních počítačích rozhraní SR-IOV) pro připojení RDMA. Následující rutiny Powershellu nainstaluje nejnovější verzi (verze 1.0) InfiniBandDriverLinux rozšíření na existující virtuální počítač s podporou RDMA. RDMA podporovat virtuální počítač má název *myVM* a je nasazený ve skupině prostředků s názvem *myResourceGroup* v *USA – západ* oblasti následujícím způsobem:

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  ```
  Rozšíření virtuálních počítačů mohou být také součástí šablony Azure Resource Manageru pro snadné nasazení pomocí elementu JSON:
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverLinux",
  "typeHandlerVersion": "1.0",
  } 
  ```
  
  Následující příkaz nainstaluje nejnovější verze 1.0 InfiniBandDriverLinux rozšíření pro všechny virtuální počítače podporující RDMA v existující škálovací sady s názvem *myVMSS* nasazených ve skupině prostředků s názvem *myResourceGroup*:
  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```
  
  > [!NOTE]
  > Pro Image založené na CentOS HPC aktualizace jádra jsou zakázány ve **yumu** konfigurační soubor. Je to proto, že ovladače RDMA Linuxu se distribuují jako balíček RPM a aktualizace ovladačů nemusí fungovat, pokud se aktualizuje jádra.
  >
  

* **SUSE Linux Enterprise Server** – SLES 12 SP3 pro prostředí HPC, SLES 12 SP3 pro prostředí HPC (Premium), SLES 12 SP1 pro prostředí HPC, SLES 12 SP1 pro prostředí HPC (Premium), SLES 12 SP4 a SLES 15. Instalace ovladačů RDMA a Intel MPI balíčky nejsou distribuovány na virtuálním počítači. Nainstalujte MPI spuštěním následujícího příkazu:

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
  
* **Ubuntu** – Ubuntu Server 16.04 LTS, 18.04 LTS. Konfigurace ovladače RDMA na virtuálním počítači a s technologií Intel stáhnout Intel MPI registrace:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]  

  Další podrobnosti o povolení InfiniBand, nastavení MPI, naleznete v tématu [povolit InfiniBand](../workloads/hpc/enable-infiniband.md).


### <a name="cluster-configuration-options"></a>Možnosti konfigurace clusteru

Azure poskytuje celou řadu možností pro vytváření clusterů HPC virtuálních počítačů s Linuxem, který může komunikovat pomocí sítě RDMA, včetně: 

* **Virtuální počítače** -nasadit virtuální počítače s podporou RDMA HPC ve stejné skupině dostupnosti (Pokud používáte model nasazení Azure Resource Manageru). Pokud používáte model nasazení classic, nasaďte virtuální počítače ve stejné cloudové službě. 

* **Škálovací sady virtuálních počítačů** - v virtuálního počítače škálovací sady, ujistěte se, že omezíte nasazení do jediné skupiny umístění. Například v šabloně Resource Manageru, nastavte `singlePlacementGroup` vlastnost `true`. 

* **MPI mezi virtuálními počítači** – Pokud MPI komunikace v případě potřeby mezi virtuální počítače (VM), ujistěte se, že virtuální počítače jsou ve stejné skupině dostupnosti nastavena nebo virtuální počítač stejné škálovací sady.

* **Azure CycleCloud** – vytvoření clusteru prostředí HPC v [Azure CycleCloud](/azure/cyclecloud/) ke spouštění úloh MPI na uzly s Linuxem.

* **Služba Azure Batch** – vytvoření [Azure Batch](/azure/batch/) výpočetní uzly fondu pro spouštění úloh MPI v Linuxu. Další informace najdete v tématu [použití podporující RDMA nebo s podporou grafického procesoru instancí ve fondech Batch](../../batch/batch-pool-compute-intensive-sizes.md). Viz také [Batch loděnice](https://github.com/Azure/batch-shipyard) projekt, pro spouštění úloh kontejneru v Batch.

* **Sady Microsoft HPC Pack** - [sady HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) podporuje několik distribucí systému Linux ke spuštění na nasazených výpočetních uzlů ve virtuálních počítačích Azure podporující RDMA spravuje hlavního uzlu Windows serveru. Ukázkové nasazení, najdete v části [vytvořit prostředí HPC Pack RDMA clusteru s Linuxem v Azure](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam).


### <a name="network-considerations"></a>Důležité informace o síti
* Na jiných SR-IOV podporou RDMA virtuální počítače s Linuxem v Azure, eth1 vyhrazené pro RDMA síťový provoz. Neměňte nastavení eth1 nebo jakékoli informace v konfiguračním souboru odkazující na tuto síť.
* Na virtuálních počítačů (HB a řady HC) s povoleným rozhraní SR-IOV, ib0 je rezervovaná pro síťový provoz RDMA.
* Sítě RDMA v Azure si vyhrazuje 172.16.0.0/16 prostor adres. Ke spouštění aplikací MPI v nasazené instance ve službě Azure virtual network, ujistěte se, že se adresní prostor virtuální sítě nepřekrývá síť RDMA.
* V závislosti na vašem výběru nástroj pro správu clusteru může být potřeba další systém konfigurace ke spouštění úloh MPI. Například v clusteru virtuálních počítačů, možná bude nutné k navázání vztahu důvěryhodnosti mezi uzly clusteru generování klíčů SSH nebo tím, že passwordless přihlášení SSH.


## <a name="other-sizes"></a>Další velikosti
- [Obecné účely](sizes-general.md)
- [Optimalizované z hlediska výpočetních služeb](sizes-compute.md)
- [Optimalizované z hlediska paměti](sizes-memory.md)
- [Optimalizované z hlediska úložiště](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další postup

- Další informace o tom, jak nastavit, optimalizací a Škálováním [úlohy HPC](../workloads/hpc/configure.md) v Azure.
- Další informace o tom [Azure výpočetních jednotek (ACU)](acu.md) můžete porovnat výpočetní výkon jednotlivých SKU v Azure.
