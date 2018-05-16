---
title: Možnosti clusteru Linux HPC Pack v Azure | Microsoft Docs
description: Další informace o možnostech pomocí sady Microsoft HPC Pack vytvářet a spravovat Linux vysokovýkonného výpočetního prostředí (HPC) clusteru v cloudu Azure
services: virtual-machines-linux,cloud-services
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management,hpc-pack
ms.assetid: ac60624e-aefa-40c3-8bc1-ef6d5c0ef1a2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 05/14/2018
ms.author: danlep
ms.openlocfilehash: 281867e30c78c7ed36ac739c8ae1a902463199cd
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="options-with-hpc-pack-to-create-and-manage-a-cluster-for-linux-hpc-workloads-in-azure"></a>Možnosti HPC Pack vytvářet a spravovat cluster pro úlohy HPC pro Linux v Azure
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

Tento článek se zaměřuje na možnosti Azure se použije ke spuštění úlohy Linux HPC Pack. Existují také možnosti pro spuštění [úloh Windows HPC s HPC Pack](../windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="hpc-pack-cluster-in-azure-vms-and-vm-scale-sets"></a>Cluster HPC Pack ve virtuálních počítačích Azure a škálovatelné sady virtuálních počítačů
### <a name="azure-resource-manager-templates"></a>Šablony Azure Resource Manageru
* (Githubu) [Šablony clusteru HPC Pack 2016 Update 1](https://github.com/MsHpcPack/HPCPack2016)
* (Githubu) [Šablony clusteru HPC Pack 2012 R2](https://github.com/MsHpcPack/HPCPack2012R2)
* (Rychlý start) [Vytvoření clusteru HPC Pack 2012 R2 s Linux výpočetních uzlů](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-linux-cn)

### <a name="azure-vm-images"></a>Azure Image virtuálních počítačů
Procházet [HPC Pack bitové kopie v Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?page=1&search=%22HPC%20%20Pack%22) Pokud chcete vytvořit vlastní HPC Pack cluster v Azure.

## <a name="hpc-pack-2012-r2-cluster-in-classic-deployment-model"></a>Cluster HPC Pack 2012 R2 v modelu nasazení classic
* [Vytvoření clusteru Linux HPC pomocí skriptu pro nasazení HPC Pack IaaS](../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Nastavení clusteru s podporou Linux RDMA ke spuštění aplikací MPI](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Kurz: Začínáme s Linux výpočetní uzly v clusteru služby HPC Pack v Azure](classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Kurz: Spustit NAMD pomocí sady Microsoft HPC Pack v systému Linux výpočetních uzlech v Azure](classic/hpcpack-cluster-namd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Kurz: OpenFOAM spustit pomocí sady Microsoft HPC Pack na clusteru s podporou Linux RDMA v Azure](classic/hpcpack-cluster-openfoam.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Kurz: Spustit HVĚZDIČKY – CCM + pomocí sady Microsoft HPC Pack na Linux RDMA cluster v Azure](classic/hpcpack-cluster-starccm.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

## <a name="job-submisssion"></a>Úloha submisssion
* [Odesílání úloh do clusteru HPC Pack v Azure](../windows/hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)




