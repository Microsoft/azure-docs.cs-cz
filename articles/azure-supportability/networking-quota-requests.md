---
title: Zvýšení limitu sítě | Microsoft Docs
description: Zvýšení limitu sítě
author: anavinahar
ms.author: anavin
ms.date: 06/19/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 5cadd0479a6ddc97cc7ee7d9b5aee1bbffba02bb
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2019
ms.locfileid: "74120961"
---
# <a name="networking-limit-increase"></a>Zvýšení limitu sítě

Pokud chcete zobrazit aktuální využití a kvótu sítě, můžete navštívit okno **používání a kvóta** v Azure Portal. K zobrazení využití a omezení sítě můžete použít také rozhraní příkazového [řádku (CLI](https://docs.microsoft.com//cli/azure/network?view=azure-cli-latest#az-network-list-usages)), [PowerShellu](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkusage?view=azurermps-6.13.0) nebo [rozhraní API pro využití sítě](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/listusage) .

Můžete požádat o zvýšení prostřednictvím okna **help + support** nebo okna **používání + kvót** na portálu.

> [!Note]
> Pokud chcete změnit výchozí velikost předpony veřejné IP adresy, v rozevíracím seznamu vyberte minimální délka předpony veřejné IP adresy.

## <a name="request-networking-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Požádat o zvýšení kvóty sítě na úrovni předplatného pomocí okna pro **pomoc a podporu**

Podle pokynů níže vytvořte žádost o podporu prostřednictvím okna "Help + podpora" v Azure, které je dostupné v Azure Portal. 

1. V https://portal.azure.comvyberte možnost **help + podpora**.

    ![Pomoc a podpora](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Vyberte **Nová žádost o podporu**. 

    ![Nová žádost o podporu](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. V rozevíracím seznamu typ problému vyberte **omezení služby a předplatné (kvóty)** .

    ![Rozevírací seznam typ problému](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Vyberte předplatné, které potřebuje navýšit kvótu.

    ![Vybrat příspěvek odběru](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Vyberte možnost **sítě** v rozevíracím seznamu **typ kvóty** . 

    ![Vybrat typ kvóty](./media/networking-quota-request/select-quota-type-network.png)

6. V **podrobnostech o problémech**zadejte další informace, které vám pomůžou zpracovat vaši žádost kliknutím na **zadat podrobnosti**.

    ![Zadat podrobnosti](./media/resource-manager-core-quotas-request/provide-details.png)

7. Na panelu **Podrobnosti kvóty** vyberte model nasazení, umístění a prostředky, u kterých chcete požádat o zvýšení.

    ![Podrobnosti o kvótě DM](./media/networking-quota-request/quota-details-network.png)

8.  Zadejte nová omezení, která chcete v předplatném. Chcete-li odebrat řádek, zrušte v rozevíracím seznamu prostředků položku prostředek nebo klikněte na ikonu zahodit "x". Až zadáte požadovanou kvótu pro každý prostředek, klikněte na **Uložit a pokračujte** na panelu Podrobnosti o kvótě a pokračujte v vytváření žádosti o podporu.

    ![Nová omezení](./media/networking-quota-request/network-new-limits.png)


## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quota-blade"></a>Žádost o zvýšení kvóty sítě na úrovni předplatného pomocí okna **využití + kvóta**

Pomocí následujících pokynů můžete vytvořit žádost o podporu prostřednictvím okna Azure využití + kvóty dostupného v Azure Portal. 

1. Z https://portal.azure.comvyberte **předplatná**.

    ![Předplatná](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Vyberte předplatné, které potřebuje navýšit kvótu.

    ![Výběr předplatného](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Vybrat **využití a kvóty**

    ![Vybrat využití a kvóty](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. V pravém horním rohu vyberte **požádat o zvýšení**.

    ![Zvýšení žádosti](./media/resource-manager-core-quotas-request/request-increase.png)

5. Postupujte podle kroků počínaje krokem č. 3 v části *žádosti o zvýšení kvóty sítě v rámci úrovně předplatného* pomocí okna **help + podpora** .

## <a name="about-networking-limits"></a>O omezeních sítě

Další informace o omezeních sítě najdete v [části sítě](../azure-subscription-service-limits.md#networking-limits) na stránce omezení – Nejčastější dotazy.
