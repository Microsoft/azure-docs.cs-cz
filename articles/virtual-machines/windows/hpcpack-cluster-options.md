---
title: Možnosti clusteru Windows HPC Pack v Azure | Microsoft Docs
description: Další informace o možnostech pomocí sady Microsoft HPC Pack vytvářet a spravovat Windows vysokovýkonného výpočetního prostředí (HPC) clusteru v cloudu Azure
services: virtual-machines-windows,cloud-services,batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management,hpc-pack
ms.assetid: 02c5566d-2129-483c-9ecf-0d61030442d7
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 05/14/2018
ms.author: danlep
ms.openlocfilehash: 1232228d5e2fcd05f41a096a933072dffcca2119
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="options-with-hpc-pack-to-create-and-manage-a-cluster-for-windows-hpc-workloads-in-azure"></a>Možnosti HPC Pack vytvářet a spravovat cluster pro úlohy Windows HPC v Azure
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

Tento článek se zaměřuje na možnosti Azure k vytvoření clusterů HPC Pack spuštění úloh systému Windows. Existují také možnosti pro vytváření clusterů HPC Pack ke spuštění [úloh prostředí HPC Linux](../linux/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="hpc-pack-cluster-in-azure-vms-and-vm-scale-sets"></a>Cluster HPC Pack ve virtuálních počítačích Azure a škálovatelné sady virtuálních počítačů
### <a name="azure-resource-manager-templates"></a>Šablony Azure Resource Manageru
* (Githubu) [Šablony clusteru HPC Pack 2016 Update 1](https://github.com/MsHpcPack/HPCPack2016)
* (Githubu) [Šablony clusteru HPC Pack 2012 R2](https://github.com/MsHpcPack/HPCPack2012R2)
* (Rychlý start) [Vytvoření clusteru HPC Pack 2012 R2](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster)
* (Rychlý start) [Vytvoření clusteru HPC Pack 2012 R2 s bitovou kopií vlastní výpočetního uzlu](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-custom-image)

### <a name="azure-vm-images"></a>Azure Image virtuálních počítačů
Procházet [HPC Pack bitové kopie v Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?page=1&search=%22HPC%20%20Pack%22) Pokud chcete vytvořit vlastní HPC Pack cluster v Azure.


### <a name="tutorials"></a>Kurzy
* [Kurz: Nasazení clusteru HPC Pack 2016 v Azure](hpcpack-2016-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Spravovat cluster služby HPC Pack 2016 v Azure s Azure Active Directory](hpcpack-cluster-active-directory.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)


## <a name="hpc-pack-2012-r2-cluster-deployment-in-the-classic-deployment-model"></a>Nasazení clusteru HPC Pack 2012 R2 v modelu nasazení classic
* [Vytvoření clusteru prostředí HPC pomocí skriptu pro nasazení HPC Pack IaaS](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Kurz: Začínáme s clusteru služby HPC Pack v Azure a spuštění aplikace Excel a SOA úloh](excel-cluster-hpcpack.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Spravovat výpočetní uzly v clusteru služby HPC Pack v Azure](classic/hpcpack-cluster-node-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Zvětšovat a zmenšovat Azure výpočetních prostředků v clusteru služby HPC Pack](classic/hpcpack-cluster-node-autogrowshrink.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Nastavení clusteru Windows RDMA pomocí sady HPC Pack pro spuštění aplikací MPI](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)


## <a name="burst-to-azure-from-hpc-pack-2012-r2"></a>Rozšíření do služby Azure z HPC Pack 2012 R2
* [Rozšíření do instancí pracovního procesu systému Azure pomocí sady Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)
* [Rozšíření do Azure Batch pomocí sady HPC Pack](https://technet.microsoft.com/library/mt612877.aspx)
* [Kurz: Nastavení clusteru hybridní pomocí sady HPC Pack v Azure](../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)

## <a name="job-submission"></a>Odeslání úlohy

* [Odesílání úloh do clusteru HPC Pack v Azure](hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)






