---
title: Přímá kvóta virtuálních počítačů
description: Zvyšte maximální kvótu pro virtuální počítače, které poskytují model využití Azure, který vám umožní v Exchangi snížit náklady na to, aby se virtuální počítače v Azure odebraly podle potřeby.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: c8b9a2251d7923fe1919b7b934f6c97877cd5b37
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745481"
---
# <a name="spot-quota-increase-limits-for-all-vm-series"></a>Kvóta na místě: Zvyšte limity pro všechny řady virtuálních počítačů.

Virtuální počítače na místě poskytují jiný model využití Azure. Umožňují vám snížit náklady na Exchangi, aby mohli Azure odebírat virtuální počítače podle potřeby pro nasazení instancí virtuálních počítačů s průběžnými platbami nebo pro rezervované instance. Další informace o virtuálních počítačích s přímým odkazem najdete v tématu [virtuální počítače Azure na místě pro službu Virtual Machine Scale Sets](../../virtual-machine-scale-sets/use-spot.md).

Azure Resource Manager podporuje dva typy kvót vCPU pro virtuální počítače:

* *Virtuální počítače* s průběžnými platbami a *rezervované instance virtuálních počítačů* podléhají *standardní vCPU kvótě*.
* *Virtuální počítače na místě* podléhají *vCPU kvótě*.

Pro typ kvóty vCPU typu quote se vynutily Správce prostředků vCPU kvóty napříč všemi dostupnými řadami virtuálních počítačů jako s jediným oblastním limitem.

Když nasadíte nový virtuální počítač s přímým odkazem, nemusíte mít všechny nové a stávající vCPU využití pro všechny instance virtuálních počítačů na místě překročit schválenou kvótu vCPU limitu. Pokud dojde k překročení kvóty na místě, nasazení na místě není povoleno.

Tento článek pojednává o tom, jak požádat o zvýšení limitu kvóty bodu vCPU pomocí Azure Portal.

Další informace o standardních kvótách vCPU najdete v tématu [kvóty pro virtuální počítače vCPU](../../virtual-machines/windows/quotas.md) a [omezení, kvóty a omezení předplatného a služeb Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md).

Další informace o zvýšení limitu vCPU podle oblasti najdete v tématu [standardní kvóta: zvýšení limitů podle oblasti](regional-quota-requests.md).

## <a name="request-a-quota-limit-increase-from-help--support"></a>Požádat o zvýšení kvóty na pomoc a podporu

Vyžádání zvýšení kvóty omezení pro všechny řady virtuálních počítačů pomocí **pomoci a podpory**:

> [!NOTE]
> Můžete také požádat o zvýšení kvóty na více oblastí prostřednictvím jediného případu podpory. Podrobnosti najdete v kroku 8.

1. V nabídce [Azure Portal](https://portal.azure.com) vyberte možnost **help + podpora**.

   ![Odkaz Help + support](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. V **okně pomoc a podpora** vyberte **Nová žádost o podporu**.

    ![Vytvořit novou žádost o podporu](./media/resource-manager-core-quotas-request/new-support-request.png)

1. Jako **typ problému** vyberte **omezení služby a předplatné (kvóty)**.

   ![Vyberte typ problému.](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. V poli **předplatné** vyberte předplatné, jehož kvótu chcete zvýšit.

   ![Vyberte předplatné pro zvýšenou kvótu.](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. V části **typ kvóty** vyberte **COMPUTE-VM (cores-vCPU) zvýšení limitu předplatného**.

   ![Vyberte typ kvóty.](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Vyberte **Další: řešení** pro otevření **podrobností problému**. Pokud chcete zadat další informace, vyberte **zadat podrobnosti** .

   ![Odkaz "poskytnout podrobnosti"](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. V části **Podrobnosti kvóty** proveďte následující kroky:

   1. V části **model nasazení** vyberte příslušný model a pro **umístění** vyberte umístění.

      ![Poskytnout podrobnosti o dodatečné kvótě](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. Pro vybrané umístění v části **typy** v **možnosti vybrat typ** zvolte možnost **bod**.

      ![Vybrat typ bodu](./media/resource-manager-core-quotas-request/select-spot-type.png)

       V části **typy** můžete požadovat typy kvót Standard a quote z jediného případu podpory prostřednictvím podpory vícenásobného výběru.

       Další informace najdete v tématu [standardní kvóta: zvýšení limitů podle řady virtuálních počítačů](per-vm-quota-requests.md).

   1. Zadejte nové omezení kvóty, které chcete pro toto předplatné.

      ![Vybrat novou kvótu pro virtuální počítač na místě](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. Pokud chcete požádat o zvýšení kvóty pro více než jedno umístění, vyberte další umístění v **umístění** a potom vyberte vhodný typ virtuálního počítače. Pak můžete zadat limit, který platí pro další umístění.

   ![Zadat další umístění v podrobnostech o kvótě](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Pokud chcete pokračovat v vytváření žádosti o podporu, vyberte **Uložit a pokračovat** .

## <a name="request-a-quota-limit-increase-from-the-subscriptions-pane"></a>Vyžádat zvýšení kvóty v podokně předplatná

Požadavek na zvýšení kvóty omezení pro všechny řady virtuálních počítačů z podokna **předplatná** :

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

1. V části **typ kvóty** vyberte **COMPUTE-VM (cores-vCPU) zvýšení limitu předplatného**.

   ![Vyberte typ kvóty.](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Vyberte **Další: řešení** pro otevření **podrobností problému**. Pokud chcete zadat další informace, vyberte **zadat podrobnosti** . V části **Podrobnosti o kvótě** zadejte následující informace:

   1. V části **model nasazení** vyberte příslušný model a pro **umístění** vyberte umístění.

      ![Poskytnutí podrobností o kvótě](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. Pro vybrané umístění v části **typy** v **možnosti vybrat typ** zvolte možnost **bod**.

      ![Vybrat typ bodu](./media/resource-manager-core-quotas-request/select-spot-type.png)

      Další informace najdete v tématu [standardní kvóta: zvýšení limitů podle řady virtuálních počítačů](per-vm-quota-requests.md).

   1. Zadejte nové omezení kvóty, které chcete pro toto předplatné.

      ![Zadejte novou hodnotu pro vCPU limit.](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. Pokud chcete požádat o zvýšení kvóty pro více než jedno umístění, vyberte další umístění v **umístění** a potom vyberte vhodný typ virtuálního počítače. Pak můžete zadat limit, který platí pro další umístění.

   ![Vybrat další umístění v podrobnostech o kvótě](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Pokud chcete pokračovat v vytváření žádosti o podporu, vyberte **Uložit a pokračovat** .
