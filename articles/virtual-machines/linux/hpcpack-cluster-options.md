---
title: Možnosti clusteru HPC Pack systému Linux v Azure | Dokumentace Microsoftu
description: Další informace o možnostech pomocí sady Microsoft HPC Pack můžete vytvářet a spravovat Linuxu vysokovýkonného výpočetního prostředí (HPC) clusteru v cloudu Azure
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
ms.openlocfilehash: 79600909387b1876b112219b5b9b1e45ba4054b7
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2018
ms.locfileid: "49340071"
---
# <a name="options-with-hpc-pack-to-create-and-manage-a-cluster-for-linux-hpc-workloads-in-azure"></a>Možnosti sadou HPC Pack pro vytváření a správě clusteru pro úlohy HPC pro Linux v Azure
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

Tento článek se zaměřuje na možnosti Azure pomocí sady HPC Pack Linux spouštěli. Existují také možnosti pro spuštění [úlohy Windows HPC pomocí sady HPC Pack](../windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="hpc-pack-cluster-in-azure-vms-and-vm-scale-sets"></a>Cluster HPC Pack ve virtuálních počítačích Azure a škálovací sady virtuálních počítačů
### <a name="azure-resource-manager-templates"></a>Šablony Azure Resource Manageru
* (GitHub) [Šablony clusteru HPC Pack 2016 Update 1](https://github.com/MsHpcPack/HPCPack2016)
* (GitHub) [Šablony clusteru prostředí HPC Pack 2012 R2](https://github.com/MsHpcPack/HPCPack2012R2)
* (Rychlý start) [Vytvoření clusteru prostředí HPC Pack 2012 R2 s Linuxovými výpočetními uzly](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-linux-cn)

### <a name="azure-vm-images"></a>Azure imagí virtuálních počítačů
Procházet [sady HPC Pack imagí v Tržišti Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?page=1&search=%22HPC%20%20Pack%22) Pokud chcete sestavit vlastní cluster HPC Pack v Azure.

## <a name="hpc-pack-2012-r2-cluster-in-classic-deployment-model"></a>Cluster HPC Pack 2012 R2 v modelu nasazení classic
* [Vytvoření clusteru HPC pro Linux se skriptem nasazení IaaS sady HPC Pack](../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Kurz: Začínáme s výpočetními uzly Linuxu v clusteru HPC Pack v Azure](classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Kurz: Spuštění NAMD pomocí sady Microsoft HPC Pack v Linuxu výpočetních uzlech v Azure](classic/hpcpack-cluster-namd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Kurz: Spuštění OpenFOAM se sadou Microsoft HPC Pack v clusteru Linux RDMA v Azure](classic/hpcpack-cluster-openfoam.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Kurz: Spuštění STAR-CCM + pomocí sady Microsoft HPC Pack na Linux RDMA clusteru v Azure](classic/hpcpack-cluster-starccm.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

## <a name="job-submisssion"></a>Submisssion úlohy
* [Odesílání úloh do clusteru HPC Pack v Azure](../windows/hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)




