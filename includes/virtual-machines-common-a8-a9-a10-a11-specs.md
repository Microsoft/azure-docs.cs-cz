---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.topic: include
ms.date: 05/29/2018
ms.author: azcspmt;jonbeck;cynthn;amverma
ms.custom: include file
ms.openlocfilehash: 8de1163ab68b394b6eeaaad6412995156dbe7212
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75901753"
---
## <a name="deployment-considerations"></a>Aspekty nasazování
* **Azure subscription** – To deploy more than a few compute-intensive instances, consider a pay-as-you-go subscription or other purchase options. Pokud používáte [bezplatný účet Azure](https://azure.microsoft.com/free/), můžete použít pouze omezený počet výpočetních jader Azure.

* **Pricing and availability** - These VM sizes are offered only in the Standard pricing tier. Check [Products available by region](https://azure.microsoft.com/global-infrastructure/services/) for availability in Azure regions. 
* **Cores quota** – You might need to increase the cores quota in your Azure subscription from the default value. Your subscription might also limit the number of cores you can deploy in certain VM size families, including the H-series. To request a quota increase, [open an online customer support request](../articles/azure-portal/supportability/how-to-create-azure-support-request.md) at no charge. (Default limits may vary depending on your subscription category.)
  
  > [!NOTE]
  > Contact Azure Support if you have large-scale capacity needs. Azure quotas are credit limits, not capacity guarantees. Regardless of your quota, you are only charged for cores that you use.
  > 
  > 
* **Virtual network** – An Azure [virtual network](https://azure.microsoft.com/documentation/services/virtual-network/) is not required to use the compute-intensive instances. However, for many deployments you need at least a cloud-based Azure virtual network, or a site-to-site connection if you need to access on-premises resources. When needed, create a new virtual network to deploy the instances. Adding compute-intensive VMs to a virtual network in an affinity group is not supported.
* **Resizing** – Because of their specialized hardware, you can only resize compute-intensive instances within the same size family (H-series or compute-intensive A-series). For example, you can only resize an H-series VM from one H-series size to another. In addition, resizing from a non-compute-intensive size to a compute-intensive size is not supported.  

## <a name="rdma-capable-instances"></a>Instance s podporou RDMA
A subset of the compute-intensive instances (A8, A9, H16r, H16mr, HB and HC) feature a network interface for remote direct memory access (RDMA) connectivity. Selected N-series sizes designated with 'r' such as the NC24rs configurations (NC24rs_v2 and NC24rs_v3) are also RDMA-capable. This interface is in addition to the standard Azure network interface available to other VM sizes. 
  
This interface allows the RDMA-capable instances to communicate over an InfiniBand (IB) network, operating at EDR rates for HB, HC, FDR rates for H16r, H16mr, and RDMA-capable N-series virtual machines, and QDR rates for A8 and A9 virtual machines. These RDMA capabilities can boost the scalability and performance of certain Message Passing Interface (MPI) applications. For more information on speed, see the details in the tables on this page.

> [!NOTE]
> In Azure, IP over IB is only supported on the SR-IOV enabled VMs (SR-IOV for InfiniBand, currently HB and HC). RDMA over IB is supported for all RDMA-capable instances.
>

