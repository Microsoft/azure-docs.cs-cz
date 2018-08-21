---
title: Předplatná Azure Event Grid prostřednictvím portálu
description: Popisuje, jak vytvořit odběry služby Event Grid prostřednictvím portálu.
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: tomfitz
ms.openlocfilehash: 72eaa17e78086a4e5338bb3198ef7471c44b785f
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2018
ms.locfileid: "40234886"
---
# <a name="subscribe-to-events-through-portal"></a>Přihlášení k odběru událostí prostřednictvím portálu

Tento článek popisuje, jak vytvořit odběry služby Event Grid prostřednictvím portálu.

## <a name="create-event-subscriptions"></a>Vytvořit odběry událostí

Chcete-li vytvořit odběr Event gridu pro jakýkoli z podporovaných [zdroje událostí](event-sources.md), použijte následující postup. Tento článek ukazuje, jak vytvořit odběr Event gridu pro předplatné Azure.

1. Vyberte **Všechny služby**.

   ![Vyberte všechny služby](./media/subscribe-through-portal/select-all-services.png)

1. Vyhledejte **předplatná Event gridu** a vyberte ho z dostupných možností.

   ![Search](./media/subscribe-through-portal/search.png)

1. Vyberte **+ Odběr události**.

   ![Přidat předplatné](./media/subscribe-through-portal/add-subscription.png)

1. Vyberte typ vašeho předplatného, které chcete vytvořit. Například, přihlášení k odběru událostí předplatného Azure, vyberte **předplatná Azure** a cílové předplatné.

   ![Vyberte předplatné Azure](./media/subscribe-through-portal/azure-subscription.png)

1. Přihlásit odběr pro všechny typy událostí pro tento zdroj události a udržovat **odběru pro všechny typy událostí** zaškrtnutým políčkem. V opačném případě vyberte typy událostí pro toto předplatné.

   ![Vyberte typy událostí](./media/subscribe-through-portal/select-event-types.png)

1. Uveďte další podrobnosti o odběr události, jako je například koncový bod pro zpracování událostí a název předplatného.

   ![Zadejte podrobnosti předplatného](./media/subscribe-through-portal/provide-subscription-details.png)

## <a name="create-subscription-on-resource"></a>Vytvoření odběru na prostředek

Některé zdroje událostí podporují vytvoření odběru událostí prostřednictvím portálu rozhraní pro daný prostředek. Vyberte zdroj události a vyhledejte **události** v levém podokně.

![Zadejte podrobnosti předplatného](./media/subscribe-through-portal/resource-events.png)

Portál nabízí možnosti pro vytvoření odběru událostí, které se týkají tohoto zdroje.

## <a name="next-steps"></a>Další postup

* Informace o doručování událostí a opakovaných pokusů [doručování zpráv služby Event Grid a zkuste to znovu](delivery-and-retry.md).
* Úvod do Event Gridu najdete v článku [Informace o službě Event Grid](overview.md).
* Pokud chcete rychle začít používat služby Event Grid, přečtěte si téma [vytvoření a směrování vlastních událostí pomocí služby Azure Event Grid](custom-event-quickstart.md).
