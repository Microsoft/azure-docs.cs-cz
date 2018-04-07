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
ms.date: 10/26/2017
ms.author: danlep
ms.openlocfilehash: c5b8c16e076be3002425ceeac377043cea1a40a7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="options-with-hpc-pack-to-create-and-manage-a-cluster-for-windows-hpc-workloads-in-azure"></a>Možnosti HPC Pack vytvářet a spravovat cluster pro úlohy Windows HPC v Azure
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

Tento článek se zaměřuje na možnosti k vytvoření clusterů HPC Pack spuštění úloh systému Windows. Existují také možnosti pro vytváření clusterů HPC Pack ke spuštění [úloh prostředí HPC Linux](../linux/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="hpc-pack-cluster-in-azure-vms-and-vm-scale-sets"></a>Cluster HPC Pack ve virtuálních počítačích Azure a škálovatelné sady virtuálních počítačů
### <a name="azure-templates"></a>Šablony Azure
* (Githubu) [Šablony clusteru HPC Pack 2016](https://github.com/MsHpcPack/HPCPack2016)
* (Githubu) [Šablony clusteru HPC Pack 2012 R2](https://github.com/MsHpcPack/HPCPack2012R2)
* (Marketplace) [HPC Pack clusteru pro úlohy Windows](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/)
* (Marketplace) [Clusteru HPC Pack pro aplikaci Excel úlohy](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterexcelcn/)
* (Rychlý start) [Vytvoření clusteru prostředí HPC](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster)
* (Rychlý start) [Vytvoření clusteru prostředí HPC s bitovou kopií vlastní výpočetního uzlu](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-custom-image)

### <a name="azure-vm-images"></a>Azure Image virtuálních počítačů
* [HPC Pack 2016 hlavního uzlu na Windows Server 2016](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.HPCPack2016HeadNodeonWindowsServer2016?tab=Overview)
* [HPC Pack 2016 výpočetním uzlu na Windows Server 2016](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.HPCPack2016ComputeNodeonWindowsServer2016?tab=Overview)
* [HPC Pack 2016 hlavního uzlu v systému Windows Server 2012 R2](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.HPCPack2016HeadNodeonWindowsServer2012R2?tab=Overview)
* [HPC Pack 2016 výpočetní uzel v systému Windows Server 2012 R2](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.HPCPack2016ComputeNodeonWindowsServer2012R2?tab=Overview)
* [Hlavního uzlu HPC Pack 2012 R2 na Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)
* [Výpočetním uzlu HPC Pack 2012 R2 na Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodeonwindowsserver2012r2/)
* [HPC Pack výpočetního uzlu pomocí aplikace Excel v systému Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodewithexcelonwindowsserver2012r2/)

### <a name="powershell-deployment-script-for-hpc-pack-2012-r2"></a>Nasazovací skript Powershellu pro HPC Pack 2012 R2
* [Vytvoření clusteru prostředí HPC pomocí skriptu pro nasazení HPC Pack IaaS](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

### <a name="tutorials"></a>Kurzy
* [Kurz: Nasazení clusteru HPC Pack 2016 v Azure](hpcpack-2016-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Kurz: Začínáme s clusteru služby HPC Pack v Azure a spuštění aplikace Excel a SOA úloh](excel-cluster-hpcpack.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="manual-deployment-with-the-azure-portal"></a>Ruční nasazení pomocí portálu Azure
* [Nastavení hlavního uzlu clusteru HPC Pack virtuální počítač Azure](hpcpack-cluster-headnode.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="cluster-management"></a>Správa clusteru
* [Spravovat výpočetní uzly v clusteru služby HPC Pack v Azure](classic/hpcpack-cluster-node-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Zvětšovat a zmenšovat Azure výpočetních prostředků v clusteru služby HPC Pack](classic/hpcpack-cluster-node-autogrowshrink.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Odesílání úloh do clusteru HPC Pack v Azure](hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Správa úloh v prostředí HPC Pack](https://technet.microsoft.com/library/jj899585.aspx)
* [Spravovat cluster služby HPC Pack 2016 v Azure s Azure Active Directory](hpcpack-cluster-active-directory.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="burst-with-worker-role-nodes"></a>Burst s uzly role pracovního procesu 
* [Rozšíření do instancí pracovního procesu systému Azure pomocí sady HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)
* [Kurz: Nastavení clusteru hybridní pomocí sady HPC Pack v Azure](../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)
* [Přidání uzlů Azure "shluků" hlavnímu uzlu HPC Pack v Azure](classic/hpcpack-cluster-node-burst.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="burst-with-azure-batch"></a>Burst s Azure Batch
* [Rozšíření do Azure Batch pomocí sady HPC Pack](https://technet.microsoft.com/library/mt612877.aspx)

## <a name="rdma-clusters-for-mpi-workloads"></a>RDMA clustery pro úlohy MPI
* [Nastavení clusteru Windows RDMA pomocí sady HPC Pack pro spuštění aplikací MPI](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

