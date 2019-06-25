---
title: Sítě navýšení limitu | Dokumentace Microsoftu
description: Zvýšení limitu sítě
author: anavinahar
ms.author: anavin
ms.date: 06/19/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 48d7e9cc4a3034e149901931f2addbc7df78e2bc
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67297279"
---
# <a name="networking-limit-increase"></a>Zvýšení limitu sítě

Chcete-li zobrazit aktuální využití sítě a kvóty, můžete navštívit **využití a kvóty** okna na webu Azure Portal. Můžete také použít využití [rozhraní příkazového řádku](https://docs.microsoft.com//cli/azure/network?view=azure-cli-latest#az-network-list-usages), [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkusage?view=azurermps-6.13.0) nebo [síťová rozhraní API využití](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/listusage) zobrazíte využití sítě a omezení.

Můžete si vyžádat navýšení prostřednictvím **Nápověda a podpora** okno nebo **využití a kvóty** okno na portálu.

## <a name="request-networking-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Požádat o zvýšení kvóty sítě na úrovni pomocí předplatného **Nápověda a podpora** okno

Postupujte podle níže uvedených pokynů a vytvořte žádost o podporu prostřednictvím Azure "Nápověda a podpora" okně k dispozici na webu Azure Portal. 

1. Z https://portal.azure.com vyberte **Nápověda a podpora**.

    ![Nápověda a podpora](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Vyberte **Nová žádost o podporu**. 

    ![Nová žádost o podporu](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. V rozevírací nabídce Typ problému zvolte **limity služby a předplatného (kvóty)** .

    ![Rozevírací nabídce Typ problému](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Vyberte předplatné, které potřebuje navýšit kvótu.

    ![Vyberte předplatné newSR](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Vyberte **sítě** v **typ kvóty** rozevíracího seznamu. 

    ![Vyberte typ kvóty](./media/networking-quota-request/select-quota-type-network.png)

6. V **podrobnosti o problému**, poskytují další informace, které pomohou proces vaši žádost kliknutím **zadání podrobností o**.

    ![Zadejte podrobnosti](./media/resource-manager-core-quotas-request/provide-details.png)

7. V **podrobnosti o kvótě** panelu, vyberte model nasazení, umístění a prostředky, které chcete požádat o zvýšení.

    ![Podrobnosti o kvótě DM](./media/networking-quota-request/quota-details-network.png)

8.  Zadejte nové limity, které chcete v rámci předplatného. Odebrat řádek, zrušte zaškrtnutí políčka prostředku z rozevíracího seznamu prostředků nebo klikněte na ikonu zrušení "x". Po zadání požadované kvóty pro každý prostředek, klikněte na **uložit a pokračovat** na panelu podrobností kvóty pokračovat ve vytváření žádosti o podporu.

    ![Nové limity](./media/networking-quota-request/network-new-limits.png)


## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quota-blade"></a>Požádat o zvýšení kvóty sítě na úrovni pomocí předplatného **využití a kvóty** okno

Postupujte podle pokynů níže použití k vytvoření žádosti o podporu prostřednictvím Azure 'využití a kvóty"okno, které jsou k dispozici na webu Azure Portal. 

1. Z https://portal.azure.com vyberte **předplatná**.

    ![Subscriptions](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Vyberte předplatné, které potřebuje navýšit kvótu.

    ![Výběr předplatného](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Vyberte **využití a kvóty**

    ![Vyberte využití a kvóty](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. V pravém horním rohu vyberte **požádat o zvýšení**.

    ![Zvýšení počtu žádostí](./media/resource-manager-core-quotas-request/request-increase.png)

5. Postupujte podle pokynů od kroku 3 z *sítě požádat o zvýšení kvóty na úrovni předplatného* části pomocí **Nápověda a podpora** části okna

## <a name="about-networking-limits"></a>Informace o omezení sítě

Další informace o omezení sítě, najdete v článku [části sítě](../azure-subscription-service-limits.md#networking-limits) stránky limity nebo naše nejčastější dotazy k omezení sítě