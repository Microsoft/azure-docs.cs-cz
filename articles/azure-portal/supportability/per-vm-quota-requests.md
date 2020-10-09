---
title: Požádat o zvýšení limitů kvót vCPU na každou řadu virtuálních počítačů Azure
description: Jak požádat o zvýšení limitu kvóty vCPU pro řady virtuálních počítačů ve Azure Portal, což zvyšuje celkový počet regionálních vCPU omezení na stejnou částku.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: e00b1cf11b906390335f257b20787d394b3a8ed4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85117161"
---
# <a name="standard-quota-increase-limits-by-vm-series"></a>Standardní kvóta: Zvýšení limitů podle řady virtuálních počítačů

Azure Resource Manager podporuje dva typy kvót vCPU pro virtuální počítače:

* *Virtuální počítače* s průběžnými platbami a *rezervované instance virtuálních počítačů* podléhají *standardní vCPU kvótě*.
* *Virtuální počítače na místě* podléhají *vCPU kvótě*.

Standardní kvóta vCPU pro průběžné platby a rezervované instance virtuálních počítačů se vynutily na dvou úrovních pro každé předplatné v každé oblasti:

* První úroveň je *celkový regionální limit vCPU*ve všech řadách virtuálních počítačů.
* Druhá úroveň je *vCPU limit řady per-VM*, například vCPU Dv3-Series.

Když nasadíte nový virtuální počítač s přímým odkazem, nemusíte mít všechny nové a stávající vCPU využití pro všechny instance virtuálních počítačů na místě překročit schválenou kvótu vCPU limitu. Pokud dojde k překročení kvóty na místě, nasazení na místě není povoleno.

Můžete požádat o zvýšení limitu kvóty vCPU pro řady virtuálních počítačů pomocí Azure Portal. Zvýšení kvóty řady virtuálních počítačů automaticky zvýší celkový rozsah regionálního vCPUu o stejné množství.

Další informace o standardních kvótách vCPU najdete v tématu věnovaném [kvótám virtuálních počítačů vCPU](../../virtual-machines/windows/quotas.md) a [omezením předplatného a služeb Azure](https://docs.microsoft.com/azure/azure-supportability/classic-deployment-model-quota-increase-requests).

Další informace o zvýšení limitu vCPU podle oblasti pro standardní kvótu najdete v tématu [standardní kvóta: zvýšení limitů podle oblasti](regional-quota-requests.md).

Další informace o zvýšení limitů vCPU virtuálních počítačů najdete v tématu věnovaném [kvótám na místě: zvýšení limitů pro všechny řady virtuálních počítačů](low-priority-quota.md).

Můžete požádat o zvýšení standardních limitů vCPU kvót na řadu virtuálních počítačů jedním ze dvou způsobů, jak je popsáno v následujících částech.

## <a name="request-a-standard-quota-increase-from-help--support"></a>Požádat o zvýšení standardní kvóty z helpu a podpory

Požadavek na standardní zvýšení kvóty vCPU na řadu virtuálních počítačů v **nápovědě a podpoře**:

> [!NOTE]
> Můžete také požádat o zvýšení kvóty na více oblastí prostřednictvím jediného případu podpory. Podrobnosti najdete v kroku 8.

1. V nabídce [Azure Portal](https://portal.azure.com) vyberte možnost **help + podpora**.

   ![Odkaz Help + support](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. V **okně pomoc a podpora**vyberte **Nová žádost o podporu**.

    ![Vytvořit novou žádost o podporu](./media/resource-manager-core-quotas-request/new-support-request.png)

1. Jako **typ problému**vyberte **omezení služby a předplatné (kvóty)**.

   ![Vyberte typ problému.](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. V poli **předplatné**vyberte předplatné, jehož kvótu chcete zvýšit.

   ![Vyberte předplatné pro zvýšenou kvótu.](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. V části **typ kvóty**vyberte **COMPUTE-VM (cores-vCPU) zvýšení limitu předplatného**.

   ![Vyberte typ kvóty.](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Vyberte **Další: řešení** pro otevření **podrobností problému**. Pokud chcete zadat další informace, vyberte **zadat podrobnosti** .

   ![Odkaz "poskytnout podrobnosti"](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. V **podrobnostech o kvótě**proveďte následující kroky:

   ![Podrobnosti o další kvótě TProvide](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. V části **model nasazení**vyberte příslušný model.

   1. V oblasti **umístění**vyberte umístění. Pro vybrané umístění v části **typy**v **možnosti vybrat typ**zvolte možnost **standardní**.

      ![Podrobnosti kvóty – typy kvót](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      V části **typy**můžete požadovat typy kvót Standard a quote z jediného případu podpory prostřednictvím podpory vícenásobného výběru.

      Další informace o zvýšení limitů kvót na místě najdete v tématu [virtuální počítače Azure na místě pro službu Virtual Machine Scale Sets](../../virtual-machine-scale-sets/use-spot.md).

   1. V části **Standard**vyberte pro vyšší kvóty řadu SKU.

      ![Podrobnosti kvóty – série SKU](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. Zadejte nové limity kvót, které chcete pro toto předplatné. Chcete-li ze seznamu odebrat SKU, zrušte zaškrtnutí políčka u položky SKU nebo vyberte ikonu zahodit "X".

      ![Vybrat nový limit vCPU](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. Pokud chcete požádat o zvýšení kvóty pro více než jedno umístění, vyberte další umístění v **umístění**a potom vyberte vhodný typ virtuálního počítače. Pak můžete zadat limit, který platí pro další umístění.

   ![Zadat další umístění v podrobnostech o kvótě](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Pokud chcete pokračovat v vytváření žádosti o podporu, vyberte **Uložit a pokračovat** .

## <a name="request-a-standard-quota-increase-from-subscriptions"></a>Požadavek na zvýšení standardní kvóty z předplatných

Požadavek na standardní zvýšení kvóty vCPU na řadu virtuálních počítačů z **předplatných**:

> [!NOTE]
> Můžete také požádat o zvýšení kvóty na více oblastí prostřednictvím jediného případu podpory. Podrobnosti najdete v kroku 7.

1. V [Azure Portal](https://portal.azure.com)vyhledejte a vyberte **předplatná**.

   ![Předplatná ve službě Azure Portal Search](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Vyberte předplatné, jehož kvóta se má zvýšit.

   ![Předplatná, která se mají vybrat pro změny](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. V levém podokně vyberte **využití + kvóty**.

   ![Odkaz využití a kvóty](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. V pravém horním rohu vyberte **požádat o zvýšení**.

   ![Vyberte, chcete-li zvýšit kvótu.](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. V části **typ kvóty**vyberte **COMPUTE-VM (cores-vCPU) zvýšení limitu předplatného**.

   ![Vyberte typ kvóty.](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. V **podrobnostech o kvótě**proveďte následující kroky:

   1. V části **model nasazení**vyberte příslušný model a pro **umístění**vyberte umístění.

      ![Poskytnutí podrobností o kvótě](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. Pro vybrané umístění vyberte v části **typy**možnost **Vybrat typ**a pak vyberte **standardní**.

      ![Vybrat standardní typ](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      V části **typy**můžete požadovat typy kvót Standard a quote z jediného případu podpory prostřednictvím podpory vícenásobného výběru.

      Další informace o zvýšení limitů kvót na místě najdete v tématu [virtuální počítače Azure na místě pro službu Virtual Machine Scale Sets](../../virtual-machine-scale-sets/use-spot.md).

   1. V části **Standard**vyberte řady SKU, jejichž kvóty chcete zvýšit.

      ![Podrobnosti kvóty – série SKU](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. Zadejte nové limity kvót, které chcete pro toto předplatné. Chcete-li ze seznamu odstranit SKLADOVOU položku, zrušte zaškrtnutí políčka vedle skladové položky (SKU) nebo vyberte ikonu zahodit "X".

      ![Vybrat nový limit vCPU](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. Pokud chcete požádat o zvýšení kvóty pro více než jedno umístění, vyberte další umístění v **umístění**a potom vyberte vhodný typ virtuálního počítače.

   Tento krok předem načte řadu SKU, kterou jste vybrali pro starší umístění. Zadejte omezení kvóty, která chcete použít pro další řady.

   ![Vybrat další umístění v podrobnostech o kvótě](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Pokud chcete pokračovat v vytváření žádosti o podporu, vyberte **Uložit a pokračovat** .
