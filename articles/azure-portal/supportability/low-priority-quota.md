---
title: Kvóta virtuálního počítače na místě – Azure
description: Zvyšte limity kvót pro virtuální počítače na místě, které poskytují model využití Azure, který vám umožní předpokládat nižší náklady výměnou za to, že azure umožní odebrat virtuální počítače podle potřeby.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 3e6dddfc458f390f5648e23a8a2af8d714de4bf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842760"
---
# <a name="spot-quota-increase-limits-for-all-vm-series"></a>Bodová kvóta: Zvýšení limitů pro všechny řady virtuálních mit

Spot virtuální počítače (VM) poskytují jiný model využití Azure. Umožňují vám převzít nižší náklady výměnou za to, že umožníte Azure odebrat virtuální počítače podle potřeby pro nasazení platebních dat s průběžnými platbami nebo rezervovaných instancí virtuálních počítačů. Další informace o virtuálních počítačích na místě najdete v [tématu Azure spot virtuálních počítače pro škálovací sady virtuálních strojů](../../virtual-machine-scale-sets/use-spot.md).

Azure Resource Manager podporuje dva typy kvót virtuálních procesorů pro virtuální počítače:

* Virtuální počítače s *průběžnými platbami* a *rezervované instance virtuálních počítače* podléhají *standardní kvótě virtuálního procesoru*.
* *Virtuální počítače spot* podléhají *kvótě virtuálního procesoru na místě*.

Pro typ kvóty virtuálního procesoru na místě jsou kvóty virtuálních procesorů správce prostředků vynuceny ve všech dostupných řadách virtuálních počítačů jako jeden místní limit.

Při každém nasazení nového virtuálního počítače na místě nesmí celkové nové a stávající využití virtuálního procesoru pro všechny instance virtuálních virtuálních počítače v místě překročit limit kvóty schváleného virtuálního procesoru. Pokud je překročena kvóta na místě, nasazení virtuálního počítače není povoleno.

Tento článek popisuje, jak požádat o zvýšení limitu kvóty virtuálních procesorů na místě pomocí portálu Azure.

Další informace o standardních kvótách virtuálních procesorů najdete v [tématu Kvóty virtuálních procesorů virtuálních procesorů virtuálních procesorů](../../virtual-machines/windows/quotas.md) virtuálních zařízení a [omezení předplatného a služeb Azure, kvóty a omezení](../../azure-resource-manager/management/azure-subscription-service-limits.md).

Další informace o zvýšení limitu virtuálních procesorů podle oblasti naleznete v [tématu Standardní kvóta: Zvýšení limitů podle oblasti](regional-quota-requests.md).

## <a name="request-a-quota-limit-increase-from-help--support"></a>Požádat o zvýšení limitu kvóty z nápovědy + podpory

Chcete-li požádat o zvýšení limitu na místě kvóty pro všechny řady virtuálních strojů pomocí **nápovědy + podpory**:

> [!NOTE]
> Můžete také požádat o zvýšení limitu kvóty pro více oblastí prostřednictvím jednoho případu podpory. Podrobnosti naleznete v kroku 8.

1. V nabídce [Portál Azure](https://portal.azure.com) vyberte **Nápověda + podpora**.

   ![Odkaz Nápověda + podpora](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. V **nápovědě + podpoře**vyberte Nový **požadavek na podporu**.

    ![Vytvoření nové žádosti o podporu](./media/resource-manager-core-quotas-request/new-support-request.png)

1. V **případě typu Problém**vyberte možnost Omezení služeb a předplatného **(kvóty).**

   ![Výběr typu problému](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. V **části Předplatné**vyberte předplatné, jehož kvótu chcete zvýšit.

   ![Výběr předplatného pro zvýšenou kvótu](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. U **typu Kvóta**vyberte **zvýšení limitu předplatného Compute-VM (cores-vCPU)**.

   ![Výběr typu kvóty](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Vyberte **další: Řešení** pro otevření **podrobností o problému**. Chcete-li zadat další informace, vyberte **možnost Zadat podrobnosti.**

   ![Odkaz "Poskytnout podrobnosti"](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. V **podrobnostech kvóty**proveďte následující kroky:

   1. V **části Model nasazení**vyberte příslušný model a umístění vyberte umístění. **Locations**

      ![Poskytnutí dalších podrobností o kvótě](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. Pro vybrané umístění v části **Typy**v **části Vyberte typ**zvolte **Rozpoznat**.

      ![Vybrat typ přímé skvrny](./media/resource-manager-core-quotas-request/select-spot-type.png)

       V části **Typy**můžete požadovat standardní i namátkové typy kvót z jednoho případu podpory prostřednictvím podpory s více násobným výběrem.

       Další informace najdete [v tématu Standardní kvóta: Zvýšení limitů podle řady virtuálních vod](per-vm-quota-requests.md).

   1. Zadejte nový limit kvóty, který chcete pro toto předplatné.

      ![Výběr nové kvóty pro přímý virtuální virtuální ms](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. Chcete-li požádat o zvýšení kvóty pro více než jedno umístění, vyberte další umístění v **umístění**a vyberte příslušný typ virtuálního aplikace. Poté můžete zadat limit, který se vztahuje na další umístění.

   ![Určení dalších umístění v podrobnostech o kvótě](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Vyberte **Uložit a pokračujte** ve vytváření žádosti o podporu.

## <a name="request-a-quota-limit-increase-from-the-subscriptions-pane"></a>Požádat o zvýšení limitu kvóty z podokna Odběry

Chcete-li požádat o zvýšení limitu na místě kvóty pro všechny řady virtuálních aplikací z podokna **Odběry:**

> [!NOTE]
> Můžete také požádat o zvýšení limitu kvóty pro více oblastí prostřednictvím jednoho případu podpory. Podrobnosti naleznete v kroku 7.

1. Na [webu Azure Portal](https://portal.azure.com)vyhledejte a vyberte **předplatná**.

   ![Předplatná ve vyhledávání na portálu Azure](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Vyberte předplatné, jehož kvótu chcete zvýšit.

   ![Odběry pro výběr změn](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. V levém podokně vyberte **Možnost Využití + kvóty**.

   ![Odkaz Využití + kvóty](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. Vpravo nahoře vyberte **Požádat o zvýšení**.

   ![Výběrem zvýšíte kvótu.](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. U **typu Kvóta**vyberte **zvýšení limitu předplatného Compute-VM (cores-vCPU)**.

   ![Výběr typu kvóty](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Vyberte **další: Řešení** pro otevření **podrobností o problému**. Chcete-li zadat další informace, vyberte **možnost Zadat podrobnosti.** Do **položek Podrobnosti o kvótě**zadejte tyto údaje:

   1. V **části Model nasazení**vyberte příslušný model a umístění vyberte umístění. **Locations**

      ![Uveďte podrobnosti o kvótě](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. Pro vybrané umístění v části **Typy**v **části Vyberte typ**zvolte **Rozpoznat**.

      ![Vybrat typ přímého bodu](./media/resource-manager-core-quotas-request/select-spot-type.png)

      Další informace najdete [v tématu Standardní kvóta: Zvýšení limitů podle řady virtuálních vod](per-vm-quota-requests.md).

   1. Zadejte nový limit kvóty, který chcete pro toto předplatné.

      ![Zadejte novou hodnotu pro limit virtuálního procesoru.](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. Chcete-li požádat o zvýšení kvóty pro více než jedno umístění, vyberte další umístění v **umístění**a vyberte příslušný typ virtuálního aplikace. Poté můžete zadat limit, který se vztahuje na další umístění.

   ![Výběr dalších umístění v podrobnostech o kvótě](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Vyberte **Uložit a pokračujte** ve vytváření žádosti o podporu.
