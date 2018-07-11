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
ms.date: 07/06/2018
ms.author: jonbeck
ms.openlocfilehash: 748cb4612b2b5aed26ba8197cfad0782f2645e1e
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37902125"
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
 
### <a name="cluster-configuration"></a>Konfigurace clusteru 
    
Konfigurace dalších systému je potřeba ke spouštění úloh MPI na Clusterované virtuální počítače. Například v clusteru virtuálních počítačů, musíte vytvořit vztah důvěryhodnosti mezi výpočetní uzly. Typické nastavení, najdete v části [nastavení clusteru Linux RDMA pro spouštění aplikací MPI](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

### <a name="network-topology-considerations"></a>Aspekty topologie sítě
* Na podporou RDMA virtuálních počítačů s Linuxem v Azure je Eth1 vyhrazený pro RDMA síťový provoz. Neměňte nastavení Eth1 nebo jakékoli informace v konfiguračním souboru odkazující na tuto síť. Eth0 je vyhrazený pro pravidelné Azure síťový provoz.

* Sítě RDMA v Azure si vyhrazuje 172.16.0.0/16 prostor adres. 


## <a name="using-hpc-pack"></a>Pomocí sady HPC Pack
[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), od Microsoftu zdarma HPC clusteru a úlohy řešení správy, je jednou z možností pro použití výpočetně náročných instancí s Linuxem. Nejnovější verze sady HPC Pack podporu několika Linuxových distribucí pro spuštění na nasazených výpočetních uzlů ve virtuálních počítačích Azure, spravuje hlavního uzlu Windows serveru. S podporou RDMA Linuxovými výpočetními uzly s technologií Intel MPI sady HPC Pack můžete naplánovat a spustit Linux MPI aplikace s přístupem k síti přístup RDMA. Zobrazit [začít pracovat s Linuxovými výpočetními uzly v clusteru HPC Pack v Azure](classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="other-sizes"></a>Další velikosti
- [Obecné účely](sizes-general.md)
- [Optimalizované z hlediska výpočetních služeb](sizes-compute.md)
- [Optimalizované z hlediska paměti](sizes-memory.md)
- [Optimalizované z hlediska úložiště](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další postup

- Chcete-li začít nasazovat a RDMA v Linuxu pomocí velikosti náročné na výpočetní prostředky, přečtěte si téma [nastavení clusteru Linux RDMA pro spouštění aplikací MPI](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

- Další informace o tom [Azure výpočetních jednotek (ACU)](acu.md) můžete porovnat výpočetní výkon jednotlivých SKU v Azure.




