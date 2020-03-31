---
title: Klasický model nasazení Azure
description: Model klasického nasazení, který je teď nahrazen modelem Správce prostředků, vynucuje globální limit kvóty virtuálních procesorů pro virtuální počítače a škálovací sady virtuálních strojů.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: a3d5106cafc1d3bfe77f3e42e85cedb668fc4fa0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76835558"
---
# <a name="classic-deployment-model"></a>Model nasazení Classic

Model klasického nasazení je starší generace modelu nasazení Azure. Vynucuje globální limit kvóty virtuálních procesorů pro virtuální počítače a škálovací sady virtuálních počítačů. Model klasického nasazení se již nedoporučuje a nyní je nahrazen modelem Resource Manager.

Další informace o těchto dvou modelech nasazení a výhodách použití Správce prostředků naleznete ve [Správci prostředků a klasickém nasazení](../../azure-resource-manager/management/deployment-models.md).

Při vytvoření nového předplatného je přiřazena výchozí kvóta virtuálních procesorů. Kdykoli má být nový virtuální počítač nasazen pomocí modelu klasického nasazení, nesmí součet nového a stávajícího využití virtuálních procesorů ve všech oblastech překročit kvótu virtuálního procesoru schválenou pro model klasického nasazení.

Další informace o kvótách najdete v [tématu Limity předplatného a služeb Azure, kvóty a omezení](../../azure-resource-manager/management/azure-subscription-service-limits.md).

Můžete požádat o zvýšení limitu kvóty virtuálního procesoru pro model klasického nasazení. Na webu Azure Portal použijte pomoc **+ podporu** nebo **kvóty Využití +** .

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-help--support"></a>Požadavek na zvýšení kvóty virtuálního procesoru řady virtuálních počítače na úrovni předplatného pomocí nápovědy + podpory

Podle následujících pokynů vytvořte žádost o podporu pomocí **nápovědy + podpory** na webu Azure Portal.

1. V nabídce [Portál Azure](https://portal.azure.com) vyberte **Nápověda + podpora**.

   ![Vyberte nápovědu + podporu na webu Azure Portal.](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. Vyberte **nový požadavek na podporu**.

   ![Vytvoření nové žádosti o podporu na webu Azure Portal](./media/resource-manager-core-quotas-request/new-support-request.png)

1. V **části Problém zvolte** **Omezení služeb a předplatného (kvóty).**

   ![Vybrat kvóty jako typ problému](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. Vyberte předplatné, jehož kvótu chcete zvýšit.

   ![Vyberte předplatné, pro které chcete zvýšit kvótu.](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. V **případě typu Kvóta**vyberte **zvýšení limitu předplatného Compute -VM (cores-vCPU)**.

   ![Vyberte typ kvóty, který chcete zvýšit.](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Vyberte **další: Řešení** pro otevření **podrobností o problému**. Chcete-li poskytnout další informace, vyberte **možnost Poskytnout podrobnosti.**

   ![Uveďte podrobnosti, které pomohou vašemu požadavku](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. V **části Podrobnosti o kvótě**vyberte **možnost Klasické** a vyberte **umístění**.

   ![Přidání podrobností včetně modelu nasazení a umístění](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. Pro **rodinu skladových položk**vyberte jednu nebo více rodin skladových položk, které chcete zvýšit.

   ![Zadejte rodinu Skladových položk, která má být větší.](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. Zadejte nové limity, které chcete v předplatném. Chcete-li odstranit řádek, zrušte výběr skladové položky ze **skupiny skladových položk** nebo vyberte ikonu zahodit "X". Po zadání kvóty pro každou rodinu skladových položk vyberte **uložit a pokračovat** v **podrobnostech kvóty** a pokračujte v žádosti o podporu.

   ![Požádat o nové limity](./media/resource-manager-core-quotas-request/new-limits-classic.png)

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usage--quotas"></a>Požadavek na zvýšení kvóty virtuálního procesoru řady virtuálních počítače na úrovni předplatného pomocí využití + kvóty

Podle následujících pokynů vytvořte žádost o podporu pomocí **využití + kvóty** na webu Azure Portal.

1. Na [webu Azure Portal](https://portal.azure.com)vyhledejte a vyberte **předplatná**.

   ![Přejděte na Předplatná na webu Azure Portal](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Vyberte předplatné, jehož kvótu chcete zvýšit.

   ![Vyberte předplatné, které chcete upravit.](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. Vyberte **možnost Využití + kvóty**.

   ![Výběr využití a kvót pro předplatné](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. V pravém horním rohu vyberte **Požádat o zvýšení**.

   ![Výběrem zvýšíte kvótu.](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. Vyberte **Limit předplatného Compute-VM (cores-vCPU) se zvyšuje** jako typ **kvóty**.

   ![Vybrat typ kvóty](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Vyberte **další: Řešení** pro otevření **podrobností o problému**. Chcete-li poskytnout další informace, vyberte **možnost Poskytnout podrobnosti.**

   ![Uveďte podrobnosti pro vaši žádost](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. V **části Podrobnosti o kvótě**vyberte **možnost Klasické** a **Umístění**.

   ![Výběr podrobností o kvótě včetně modelu nasazení a umístění](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. Vyberte jednu nebo více rodin skladových položk pro zvýšení.

   ![Vyberte rodinu skladových položk pro zvýšení](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. Zadejte nové limity, které chcete v předplatném. Chcete-li odstranit řádek, zrušte výběr skladové položky ze **skupiny skladových položk** nebo vyberte ikonu zahodit "X". Po zadání kvóty pro každou rodinu skladových položk vyberte **uložit a pokračovat** v **podrobnostech kvóty** a pokračujte v žádosti o podporu.

   ![Zadat novou kvótu](./media/resource-manager-core-quotas-request/new-limits-classic.png)

