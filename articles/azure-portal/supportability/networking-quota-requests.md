---
title: Zvýšení limitu sítě
description: Zvýšení limitu sítě
author: anavinahar
ms.author: anavin
ms.date: 01/23/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: e945dec2ce8514c3e4f1edecdecd13c5c43f7c75
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96745447"
---
# <a name="networking-limit-increase"></a>Zvýšení limitu sítě

K zvýšení kvóty sítě použijte [Azure Portal](https://portal.azure.com) .

Pokud chcete zobrazit aktuální využití sítě a kvótu v Azure Portal, otevřete své předplatné a pak vyberte **využití a kvóty**. Pomocí následujících možností můžete také zobrazit omezení využití a omezení sítě.

* [Použití rozhraní příkazového řádku](/cli/azure/network#az-network-list-usages)
* [PowerShell](/powershell/module/azurerm.network/get-azurermnetworkusage)
* [Rozhraní API využití sítě](/rest/api/virtualnetwork/virtualnetworks/listusage)

Můžete požádat o zvýšení pomocí podpory a **podpory** nebo **využití a kvót** na portálu.

> [!Note]
> Pokud chcete změnit výchozí velikost **předpon veřejných IP adres**, vyberte z rozevíracího seznamu **minimální délku PŘEDPONY veřejné IP adresy** .

## <a name="request-networking-quota-increase-at-subscription-level-using-help--support"></a>Požádat o zvýšení kvóty sítě na úrovni předplatného pomocí pomoci a podpory

Podle následujících pokynů vytvořte žádost o podporu pomocí **pomoci a podpory** v Azure Portal.

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a v nabídce Azure Portal vyberte **nápovědu a podpora** nebo vyhledejte a vyberte nápovědu a **podporu**.

    ![Nápověda a podpora](./media/networking-quota-request/help-plus-support.png)

1. Vyberte **Nová žádost o podporu**.

    ![Nová žádost o podporu](./media/networking-quota-request/new-support-request.png)

1. Jako **typ problému** vyberte **služby a omezení pro předplatné (kvóty)**.

    ![V rozevíracím seznamu typ problému vyberte omezení předplatného.](./media/networking-quota-request/select-quota-issue-type.png)

1. Vyberte předplatné, které potřebuje navýšit kvótu.

    ![Vybrat příspěvek odběru](./media/networking-quota-request/select-subscription-support-request.png)

1. V části **typ kvóty** vyberte **sítě**. Vyberte **Další: řešení**.

    ![Vybrat typ kvóty](./media/networking-quota-request/select-quota-type-network.png)

1. V **podrobnostech o problémech** vyberte **zadat podrobnosti** a vyplňte Další informace, které vám pomůžou zpracovat vaši žádost.

    ![Zadat podrobnosti](./media/networking-quota-request/provide-details-link.png)

1. Na panelu **Podrobnosti kvóty** vyberte model nasazení, umístění a prostředky, které chcete zahrnout do vaší žádosti.

    ![Podrobnosti o kvótě DM](./media/networking-quota-request/quota-details-network.png)

1. Zadejte nová omezení, která chcete v předplatném. Chcete-li odebrat řádek, zrušte výběr prostředku z nabídky **prostředky** nebo vyberte ikonu zahodit "x". Až zadáte kvótu pro každý prostředek, vyberte **Uložit a pokračujte** a pokračujte v vytváření žádosti o podporu.

    ![Nová omezení](./media/networking-quota-request/network-new-limits.png)

## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quotas"></a>Vyžádat zvýšení kvóty sítě na úrovni předplatného pomocí využití a kvót

Podle těchto pokynů vytvořte žádost o podporu pomocí Azure Portal a **kvóty** v.

1. V nástroji https://portal.azure.com vyhledejte a vyberte **odběry**.

    ![Předplatná](./media/networking-quota-request/search-for-suscriptions.png)

1. Vyberte předplatné, které potřebuje navýšit kvótu.

    ![Výběr předplatného](./media/networking-quota-request/select-subscription-change-quota.png)

1. Vybrat **využití a kvóty**

    ![Vybrat využití a kvóty](./media/networking-quota-request/select-usage-plus-quotas.png)

1. V pravém horním rohu vyberte **požádat o zvýšení**.

    ![Zvýšení žádosti](./media/networking-quota-request/request-increase-from-subscription.png)

1. Použijte postup od kroku 3 v části [žádost o zvýšení kvóty sítě na úrovni předplatného](#request-networking-quota-increase-at-subscription-level-using-help--support).

## <a name="about-networking-limits"></a>O omezeních sítě

Další informace o omezeních sítě najdete v [části věnované síťovým](../../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) omezením na stránce omezení stránky nebo v naší síti.
