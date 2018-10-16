---
title: Velikosti virtuálních počítačů Azure s Linuxem – HPC | Dokumentace Microsoftu
description: Obsahuje seznam různých velikostí, které jsou k dispozici pro Linux vysoce výkonných výpočetních virtuálních počítačů v Azure. Obsahuje informace o počtu virtuálních procesorů, datové disky a síťové adaptéry, jakož i úložiště propustnost a šířku pásma sítě pro velikosti této série.
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: jeconnoc
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
ms.openlocfilehash: 70dca655d5300fcd34b4198093e136f6a971963b
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2018
ms.locfileid: "49344485"
---
# <a name="high-performance-compute-virtual-machine-sizes"></a>Vysokovýkonné výpočetní velikosti virtuálních počítačů

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


### <a name="mpi"></a>MPI 

Podporovány jsou pouze verze 5.x Intel MPI. Novější verze (2017, 2018) Intel MPI knihovny modulu runtime nejsou kompatibilní s ovladači Azure Linux RDMA.


### <a name="distributions"></a>Distribuce
 
Nasazení virtuálního počítače náročné na výpočetní z některou k imagí v Tržišti Azure Marketplace, který podporuje připojení RDMA:
  
* **Ubuntu** – Ubuntu Server 16.04 LTS. Konfigurace ovladače RDMA na virtuálním počítači a s technologií Intel stáhnout Intel MPI registrace:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **SUSE Linux Enterprise Server** – SLES 12 SP3 pro prostředí HPC, SLES 12 SP3 pro prostředí HPC (Premium), SLES 12 SP1 pro prostředí HPC, SLES 12 SP1 pro prostředí HPC (Premium). Instalace ovladačů RDMA a Intel MPI balíčky nejsou distribuovány na virtuálním počítači. Nainstalujte MPI spuštěním následujícího příkazu:

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
    
* **Založené na centOS HPC** -založené na CentOS 6.5 HPC nebo novější verze (pro H-series, se doporučuje verze 7.1 nebo novější). Na virtuálním počítači se nainstalují ovladače RDMA a Intel MPI 5.1.  
 
  > [!NOTE]
  > Pro Image založené na CentOS HPC aktualizace jádra jsou zakázány ve **yumu** konfigurační soubor. Je to proto, že ovladače RDMA Linuxu se distribuují jako balíček RPM a aktualizace ovladačů nemusí fungovat, pokud se aktualizuje jádra.
  > 
 
### <a name="cluster-configuration-options"></a>Možnosti konfigurace clusteru

Azure poskytuje celou řadu možností pro vytváření clusterů HPC virtuálních počítačů s Linuxem, který může komunikovat pomocí sítě RDMA, včetně: 

* **Virtuální počítače** -nasadit virtuální počítače s podporou RDMA HPC ve stejné skupině dostupnosti (Pokud používáte model nasazení Azure Resource Manageru). Pokud používáte model nasazení classic, nasaďte virtuální počítače ve stejné cloudové službě. 

* **Škálovací sady virtuálních počítačů** - v virtuálního počítače škálovací sady, ujistěte se, že omezíte nasazení do jediné skupiny umístění. Například v šabloně Resource Manageru, nastavte `singlePlacementGroup` vlastnost `true`. 

* **Azure CycleCloud** – vytvoření clusteru prostředí HPC v [Azure CycleCloud](/azure/cyclecloud/) ke spouštění úloh MPI na uzly s Linuxem.

* **Služba Azure Batch** – vytvoření [Azure Batch](/azure/batch/) výpočetní uzly fondu pro spouštění úloh MPI v Linuxu. Další informace najdete v tématu [použití podporující RDMA nebo s podporou grafického procesoru instancí ve fondech Batch](../../batch/batch-pool-compute-intensive-sizes.md). Viz také [Batch loděnice](https://github.com/Azure/batch-shipyard) projekt, pro spouštění úloh kontejneru v Batch.

* **Sady Microsoft HPC Pack** - [sady HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) podporuje několik distribucí systému Linux ke spuštění na nasazených výpočetních uzlů ve virtuálních počítačích Azure podporující RDMA spravuje hlavního uzlu Windows serveru. Ukázkové nasazení, najdete v části [vytvořit prostředí HPC Pack RDMA clusteru s Linuxem v Azure](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam).

V závislosti na vašem výběru nástroj pro správu clusteru může být potřeba další systém konfigurace ke spouštění úloh MPI. Například v clusteru virtuálních počítačů, možná bude nutné k navázání vztahu důvěryhodnosti mezi uzly clusteru generování klíčů SSH nebo vytvoření vztahu důvěryhodnosti passwordless SSH.

### <a name="network-topology-considerations"></a>Aspekty topologie sítě
* Na podporou RDMA virtuálních počítačů s Linuxem v Azure je Eth1 vyhrazený pro RDMA síťový provoz. Neměňte nastavení Eth1 nebo jakékoli informace v konfiguračním souboru odkazující na tuto síť. Eth0 je vyhrazený pro pravidelné Azure síťový provoz.

* Sítě RDMA v Azure si vyhrazuje 172.16.0.0/16 prostor adres. 




## <a name="other-sizes"></a>Další velikosti
- [Obecné účely](sizes-general.md)
- [Optimalizované z hlediska výpočetních služeb](sizes-compute.md)
- [Optimalizované z hlediska paměti](sizes-memory.md)
- [Optimalizované z hlediska úložiště](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další postup

- Další informace o tom [Azure výpočetních jednotek (ACU)](acu.md) můžete porovnat výpočetní výkon jednotlivých SKU v Azure.




