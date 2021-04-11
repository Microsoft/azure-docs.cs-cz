---
title: Změna velikosti virtuálních počítačů v grafickém procesoru Azure Stack Edge pro R, pro R, Mini R prostřednictvím Azure Portal
description: Naučte se, jak změnit velikost virtuálních počítačů spuštěných v grafickém procesoru Azure Stack Edge pro, Azure Stack Edge pro R, Azure Stack hraniční Mini R prostřednictvím Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/30/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to resize VMs running on an Azure Stack Edge Pro device so that I can use it to run applications using Edge compute before sending it to Azure.
ms.openlocfilehash: bf2125a6e1d0b443202a036c52fdf845f79d11fa
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080191"
---
# <a name="use-the-azure-portal-to-resize-the-vms-on-your-azure-stack-edge-pro-gpu"></a>Použijte Azure Portal pro změnu velikosti virtuálních počítačů na GPU pro procesor Azure Stack Edge pro.

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Tento článek vysvětluje, jak změnit velikost virtuálních počítačů nasazených na zařízení GPU Azure Stack Edge pro.

       
## <a name="about-vm-sizing"></a>O změně velikosti virtuálních počítačů

Velikost virtuálního počítače určuje množství výpočetních prostředků (jako je CPU, GPU a paměť), které jsou k dispozici pro virtuální počítač. Virtuální počítače byste měli vytvořit pomocí velikosti virtuálního počítače vhodné pro vaše aplikační úlohy. 

I když všechny počítače budou spuštěné na stejném hardwaru, velikosti počítačů mají různá omezení pro přístup k disku. To vám může pomáhat spravovat celkový přístup k disku v rámci virtuálních počítačů. Pokud se zatížení zvýší, můžete také změnit velikost stávajícího virtuálního počítače.

Další informace najdete v tématu [podporované velikosti virtuálních počítačů pro vaše zařízení](azure-stack-edge-gpu-virtual-machine-sizes.md).


## <a name="prerequisites"></a>Požadavky

Před změnou velikosti virtuálního počítače spuštěného v zařízení prostřednictvím Azure Portal se ujistěte, že:

1. V zařízení máte nasazený aspoň jeden virtuální počítač. Pokud chcete vytvořit tento virtuální počítač, přečtěte si pokyny v tématu [nasazení virtuálního počítače na Azure Stack Edge pro prostřednictvím Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).

1. Váš virtuální počítač by měl být v **zastaveném** stavu. Pokud chcete virtuální počítač zastavit, v části **virtuální počítače > přehled** a vyberte virtuální počítač, který chcete zastavit. Na stránce Přehled vyberte **zastavit** a po zobrazení výzvy k potvrzení vyberte **Ano** . Před změnou velikosti virtuálního počítače musíte virtuální počítač zastavit.

    ![Zastavit virtuální počítač ze stránky přehledu](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/stop-vm-2.png)


## <a name="resize-a-vm"></a>Změna velikosti virtuálního počítače

Pomocí těchto kroků můžete změnit velikost virtuálního počítače nasazeného v zařízení. 

1. Přejít na virtuální počítač, který jste zastavili, a pak přejít na stránku **Přehled** . Vyberte **Velikost virtuálního počítače (změnit)**.
    
    ![Výběr změny velikosti virtuálního počítače na stránce s přehledem](./media/azure-stack-edge-gpu-manage-virtual-machine-resize-portal/change-vm-size-1.png)

2. V okně **změnit velikost virtuálního počítače** na panelu příkazů vyberte **Velikost virtuálního počítače** a pak vyberte **změnit**.

    ![Výběr nové velikosti virtuálního počítače](./media/azure-stack-edge-gpu-manage-virtual-machine-resize-portal/change-vm-size-2.png)

3. Zobrazí se oznámení o tom, že se virtuální počítač aktualizuje. Po úspěšné aktualizaci virtuálního počítače se stránka **Přehled** aktualizuje a zobrazí se virtuální počítač se změněnou velikostí.

    ![Virtuální počítač se změněnou velikostí ](./media/azure-stack-edge-gpu-manage-virtual-machine-resize-portal/change-vm-size-3.png)


## <a name="next-steps"></a>Další kroky

Informace o tom, jak nasadit virtuální počítače na zařízení Azure Stack Edge pro, najdete v tématu [nasazení virtuálních počítačů prostřednictvím Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).
