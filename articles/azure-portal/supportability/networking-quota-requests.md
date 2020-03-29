---
title: Zvýšení limitu pro vytváření sítí | Dokumenty společnosti Microsoft
description: Zvýšení limitu sítě
author: anavinahar
ms.author: anavin
ms.date: 01/23/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9b5c7043b06172c2d4931ca1c3fd3ac5d0e80883
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76547727"
---
# <a name="networking-limit-increase"></a>Zvýšení limitu sítě

Pomocí [portálu Azure](https://portal.azure.com) zvyšte svou síťovou kvótu.

Pokud chcete zobrazit aktuální využití a kvótu sítě na webu Azure Portal, otevřete předplatné a vyberte **Využití + kvóty**. K zobrazení využití a omezení sítě můžete také použít následující možnosti.

* [Cli použití](/cli/azure/network#az-network-list-usages)
* [PowerShell](/powershell/module/azurerm.network/get-azurermnetworkusage)
* [Rozhraní API pro využití sítě](/rest/api/virtualnetwork/virtualnetworks/listusage)

Můžete požádat o zvýšení pomocí **nápovědy + podporu** nebo v **použití + kvóty** na portálu.

> [!Note]
> Chcete-li změnit výchozí velikost **předpon veřejné IP adresy**, vyberte z rozevíracího seznamu **položku Min Public IP InterNetwork Prefix Length** .

## <a name="request-networking-quota-increase-at-subscription-level-using-help--support"></a>Požádat o zvýšení kvóty pro sítě na úrovni předplatného pomocí nápovědy + podpory

Podle následujících pokynů vytvořte žádost o podporu pomocí **nápovědy + podpory** na webu Azure Portal.

1. Přihlaste se na [portál Azure](https://portal.azure.com)Portal a pak vyberte **nápovědu + podporu** z nabídky portálu Azure nebo vyhledejte a vyberte **Nápověda + podpora**.

    ![Nápověda a podpora](./media/networking-quota-request/help-plus-support.png)

1. Vyberte **nový požadavek na podporu**.

    ![Nová žádost o podporu](./media/networking-quota-request/new-support-request.png)

1. V **případě typu Problém**zvolte Omezení služeb a předplatného **(kvóty).**

    ![Výběr limitů předplatného z rozevíracího článku typu problému](./media/networking-quota-request/select-quota-issue-type.png)

1. Vyberte předplatné, které potřebuje navýšit kvótu.

    ![Vybrat předplatné novéSR](./media/networking-quota-request/select-subscription-support-request.png)

1. V části **Typ kvóty**vyberte **možnost Síť**. Vyberte **další: Řešení**.

    ![Vybrat typ kvóty](./media/networking-quota-request/select-quota-type-network.png)

1. V **části PODROBNOSTI O PROBLÉMU**vyberte Zadat **podrobnosti** a vyplňte další informace, které vám pomohou zpracovat váš požadavek.

    ![Poskytnout podrobnosti](./media/networking-quota-request/provide-details-link.png)

1. V panelu **Podrobnosti kvóty** vyberte model nasazení, umístění a prostředky, které chcete zahrnout do požadavku.

    ![Podrobnosti o kvótě DM](./media/networking-quota-request/quota-details-network.png)

1. Zadejte nové limity, které chcete v předplatném. Chcete-li řádek odebrat, zrušte výběr zdroje z nabídky **Zdroje** nebo vyberte ikonu zahodit "x". Po zadání kvóty pro každý zdroj vyberte **Uložit a pokračujte** v vytváření žádosti o podporu.

    ![Nové limity](./media/networking-quota-request/network-new-limits.png)

## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quotas"></a>Požádat o zvýšení kvóty sítě na úrovni předplatného pomocí využití + kvót

Podle těchto pokynů vytvořte žádost o podporu pomocí **využití + kvóty** na webu Azure Portal.

1. Z https://portal.azure.com, vyhledejte a vyberte **Předplatná**.

    ![Předplatná](./media/networking-quota-request/search-for-suscriptions.png)

1. Vyberte předplatné, které potřebuje navýšit kvótu.

    ![Výběr předplatného](./media/networking-quota-request/select-subscription-change-quota.png)

1. Vybrat **využití + kvóty**

    ![Výběr využití a kvót](./media/networking-quota-request/select-usage-plus-quotas.png)

1. V pravém horním rohu vyberte **Požádat o zvýšení**.

    ![Zvýšení žádosti](./media/networking-quota-request/request-increase-from-subscription.png)

1. Postupujte podle kroků počínaje krokem 3 v [request networking zvýšení kvóty na úrovni předplatného](#request-networking-quota-increase-at-subscription-level-using-help--support).

## <a name="about-networking-limits"></a>Omezení sítě

Další informace o omezeních pro vytváření sítí najdete v [části Síť](../../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) na stránce omezení nebo v nejčastějších dotazech k omezením sítě.
