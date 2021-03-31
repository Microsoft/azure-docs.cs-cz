---
title: Model nasazení Azure Classic
description: Model nasazení Classic, který se teď nahradil modelem Správce prostředků, vynutil globální omezení vCPU kvóty pro virtuální počítače a služby Virtual Machine Scale Sets.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: b5b5e8c11bfe164aaa3539742dac8c4d267c69e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96745600"
---
# <a name="classic-deployment-model"></a>Model nasazení Classic

Model nasazení Classic je model nasazení Azure starší generace. Vynutila globální limit vCPU kvóty pro virtuální počítače a služby Virtual Machine Scale Sets. Model nasazení Classic již není doporučen a je nyní nahrazen modelem Správce prostředků.

Další informace o těchto dvou modelech nasazení a výhodách použití Správce prostředků najdete v tématu [Správce prostředků a klasické nasazení](../../azure-resource-manager/management/deployment-models.md).

Když se vytvoří nové předplatné, přiřadí se mu výchozí kvóta vCPU. Kdykoli je možné nasadit nový virtuální počítač pomocí modelu nasazení Classic, součet nového a stávajícího vCPUho využití ve všech oblastech nesmí překročit kvótu vCPU schválenou pro model nasazení Classic.

Další informace o kvótách najdete v tématu [limity, kvóty a omezení předplatného a služeb Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md).

Můžete požádat o zvýšení limitu kvóty vCPU pro model nasazení Classic. V Azure Portal použijte možnost **help + podpora** nebo **využití a kvóty** .

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-help--support"></a>Žádost o zvýšení kvóty vCPU podle řady virtuálních počítačů na úrovni předplatného s využitím pomoci a podpory

Podle následujících pokynů vytvořte žádost o podporu pomocí **pomoci a podpory** v Azure Portal.

1. V nabídce [Azure Portal](https://portal.azure.com) vyberte možnost **help + podpora**.

   ![V Azure Portal vyberte Help + support.](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. Vyberte **Nová žádost o podporu**.

   ![Vytvořit novou žádost o podporu v Azure Portal](./media/resource-manager-core-quotas-request/new-support-request.png)

1. V možnosti **typ problému** vyberte **omezení služby a předplatné (kvóty)**.

   ![Jako typ problému vyberte kvóty.](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. Vyberte předplatné, jehož kvóta se má zvýšit.

   ![Vyberte předplatné, pro které se má zvýšit kvóta.](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. V části **typ kvóty** vyberte **COMPUTE-VM (cores-vCPU) zvýšení limitu předplatného**.

   ![Vyberte typ kvóty, který se má zvýšit.](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Vyberte **Další: řešení** pro otevření **podrobností problému**. Pokud chcete zadat další informace, vyberte **zadat podrobnosti** .

   ![Zadejte podrobnosti, které vám pomůžou vaši žádost společně.](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. V **Podrobnosti kvóty** vyberte **Classic** a vyberte **umístění**.

   ![Přidat podrobnosti včetně modelu nasazení a umístění](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. Pro **rodinu SKU** vyberte jednu nebo více rodin SKU, které chcete zvýšit.

   ![Určete rodinu SKU, která se má zvýšit.](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. Zadejte nová omezení, která chcete v předplatném. Chcete-li odebrat řádek, zrušte výběr SKU z **řady SKU** nebo vyberte ikonu zahození "X". Až zadáte kvótu pro každou rodinu SKU, vyberte **Uložit a pokračovat** v **podrobnostech o kvótě** , abyste mohli pokračovat v žádosti o podporu.

   ![Požádat o nová omezení](./media/resource-manager-core-quotas-request/new-limits-classic.png)

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usage--quotas"></a>Žádost o zvýšení kvóty vCPU na řadu virtuálních počítačů na úrovni předplatného s využitím + kvót

Podle následujících pokynů vytvořte žádost o podporu pomocí Azure Portal **a kvót** v.

1. V [Azure Portal](https://portal.azure.com)vyhledejte a vyberte **předplatná**.

   ![Přejít na předplatná v Azure Portal](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Vyberte předplatné, jehož kvóta se má zvýšit.

   ![Vyberte předplatné, které chcete upravit.](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. Vyberte **využití a kvóty**.

   ![Výběr využití a kvót pro předplatné](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. V pravém horním rohu vyberte **požádat o zvýšení**.

   ![Vyberte, chcete-li zvýšit kvótu.](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. Vyberte **COMPUTE-VM (cores-vCPU) limit předplatného se zvyšuje** jako **typ kvóty**.

   ![Vybrat typ kvóty](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Vyberte **Další: řešení** pro otevření **podrobností problému**. Pokud chcete zadat další informace, vyberte **zadat podrobnosti** .

   ![Zadejte podrobnosti žádosti.](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. V **podrobnostech o kvótě** vyberte **Classic** a **umístění**.

   ![Výběr podrobností kvóty včetně modelu nasazení a umístění](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. Vyberte jednu nebo více skupin SKU pro zvýšení.

   ![Vybrat skupinu SKU pro zvýšení](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. Zadejte nová omezení, která chcete v předplatném. Chcete-li odebrat řádek, zrušte výběr SKU z **řady SKU** nebo vyberte ikonu zahození "X". Až zadáte kvótu pro každou rodinu SKU, vyberte **Uložit a pokračovat** v **podrobnostech o kvótě** , abyste mohli pokračovat v žádosti o podporu.

   ![Zadejte novou kvótu.](./media/resource-manager-core-quotas-request/new-limits-classic.png)

