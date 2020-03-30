---
title: Žádost o zvýšení limitů kvót virtuálních procesorů na řadu virtuálních počítačích Azure
description: Jak požádat o zvýšení limitu kvóty virtuálních procesorů pro řadu virtuálních počítače na webu Azure Portal, což zvyšuje celkový limit místního virtuálního procesoru o stejnou částku.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: de06375dad5999a29691435317e62585a2ea7f64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843709"
---
# <a name="standard-quota-increase-limits-by-vm-series"></a>Standardní kvóta: Zvýšení limitů podle řady virtuálních montovek

Azure Resource Manager podporuje dva typy kvót virtuálních procesorů pro virtuální počítače:

* Virtuální počítače s *průběžnými platbami* a *rezervované instance virtuálních počítače* podléhají *standardní kvótě virtuálního procesoru*.
* *Virtuální počítače spot* podléhají *kvótě virtuálního procesoru na místě*.

Standardní kvóta virtuálního procesoru pro instance platebních a rezervovaných virtuálních strojů je vynucena na dvou úrovních pro každé předplatné v každé oblasti:

* První vrstva je *celkový limit místních virtuálních procesorů*napříč všemi řadami virtuálních montovny.
* Druhá vrstva je *limit virtuálních procesorů řady virtuálních procesorů řady*v v. například virtuální procesory řady Dv3.

Při každém nasazení nového virtuálního počítače na místě, celkové nové a stávající využití virtuálních procesorů pro tuto řadu virtuálních počítače nesmí překročit schválenou kvótu virtuálních procesorů pro tuto konkrétní řadu virtuálních počítače. Kromě toho celkový počet nových a stávajících virtuálních procesorů, které jsou nasazeny ve všech řadách virtuálních počítače by neměla překročit celkovou schválenou kvótu místní virtuální ch výkonu pro předplatné. Pokud je překročena některá z těchto kvót, nasazení virtuálního počítače není povoleno.

Můžete požádat o zvýšení limitu kvóty virtuálních procesorů pro řadu virtuálních počítače pomocí portálu Azure. Zvýšení kvóty řady virtuálních počítače automaticky zvýší celkový limit místní virtuální ho výkonu o stejnou částku.

Další informace o standardních kvótách virtuálních procesorů najdete v [tématu Kvóty virtuálních procesorů virtuálních procesorů virtuálních procesorů virtuálních](../../virtual-machines/windows/quotas.md) zařízení a [omezení předplatného a služeb Azure](https://docs.microsoft.com/azure/azure-supportability/classic-deployment-model-quota-increase-requests).

Další informace o zvýšení limitu virtuálních procesorů podle oblasti pro standardní kvótu naleznete v [tématu Standardní kvóta: Zvýšení limitů podle oblasti](regional-quota-requests.md).

Další informace o zvýšení limitů virtuálních procesorů virtuálních procesorů virtuálních počítače na místě najdete v [tématu Kvóta bodů: Zvýšení limitů pro všechny řady virtuálních počítače](low-priority-quota.md).

Můžete požádat o zvýšení standardníkvóty virtuálního procesoru na řadu virtuálních počítače v obou směrech, jak je popsáno v následujících částech.

## <a name="request-a-standard-quota-increase-from-help--support"></a>Požádat o standardní zvýšení kvóty z nápovědy + podpory

Chcete-li požádat o zvýšení kvóty standardního virtuálního procesoru na řadu virtuálních počítače z **nápovědy + podpory:**

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

1. V **podrobnostech o kvótě**postupujte takto:

   ![TPoskytnout další podrobnosti o kvótě](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. Pro **model nasazení**vyberte příslušný model.

   1. V **poučná místa**vyberte umístění. Pro vybrané umístění v části **Typy**v **části Vyberte typ**zvolte **Standardní**.

      ![Podrobnosti o kvótě – typy kvót](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      V části **Typy**můžete požadovat standardní i namátkové typy kvót z jednoho případu podpory prostřednictvím podpory s více násobným výběrem.

      Další informace o zvýšení limitů bodových kvót najdete v [tématu Virtuální počítače na místě Azure pro škálovací sady virtuálních strojů](../../virtual-machine-scale-sets/use-spot.md).

   1. Ve **standardním**vyberte řadu skladových položk pro zvýšení kvót.

      ![Podrobnosti o kvótách – řada Skladových míst](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. Zadejte nové limity kvót, které chcete pro toto předplatné. Chcete-li odebrat skladovou položku ze seznamu, zrušte zaškrtnutí políčka vedle skladové položky nebo zaškrtněte ikonu zahodit "X".

      ![Výběr nového limitu virtuálního procesoru](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. Chcete-li požádat o zvýšení kvóty pro více než jedno umístění, vyberte další umístění v **umístění**a vyberte příslušný typ virtuálního aplikace. Poté můžete zadat limit, který se vztahuje na další umístění.

   ![Určení dalších umístění v podrobnostech o kvótě](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Vyberte **Uložit a pokračujte** ve vytváření žádosti o podporu.

## <a name="request-a-standard-quota-increase-from-subscriptions"></a>Požádat o standardní zvýšení kvóty z předplatného

Chcete-li požádat o zvýšení kvóty standardního virtuálního procesoru na řadu virtuálních počítače z **odběrů:**

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

1. V **podrobnostech o kvótě**postupujte takto:

   1. V **části Model nasazení**vyberte příslušný model a umístění vyberte umístění. **Locations**

      ![Uveďte podrobnosti o kvótě](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. Pro vybrané umístění vyberte v části **Typy** **možnost Vybrat typ**a pak vyberte **Standardní**.

      ![Vybrat standardní typ](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      V části **Typy**můžete požadovat standardní i namátkové typy kvót z jednoho případu podpory prostřednictvím podpory s více násobným výběrem.

      Další informace o zvýšení limitů bodových kvót najdete v [tématu Virtuální počítače na místě Azure pro škálovací sady virtuálních strojů](../../virtual-machine-scale-sets/use-spot.md).

   1. V **části Standardní**vyberte řadu Skladové položky, jejíž kvóty chcete zvýšit.

      ![Podrobnosti o kvótách – řada Skladových míst](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. Zadejte nové limity kvót, které chcete pro toto předplatné. Chcete-li odebrat skladovou položku ze seznamu, zrušte zaškrtnutí políčka vedle skladové položky nebo zaškrtněte ikonu zahodit "X".

      ![Výběr nového limitu virtuálního procesoru](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. Chcete-li požádat o zvýšení kvóty pro více než jedno umístění, vyberte další umístění v **umístění**a vyberte příslušný typ virtuálního aplikace.

   Tento krok předem načte řadu Skladové položky, kterou jste vybrali pro dřívější umístění. Zadejte limity kvót, které chcete použít pro další řady.

   ![Výběr dalších umístění v podrobnostech o kvótě](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Vyberte **Uložit a pokračujte** ve vytváření žádosti o podporu.
