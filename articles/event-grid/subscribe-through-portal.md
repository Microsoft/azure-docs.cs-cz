---
title: Předplatná Azure Event Grid prostřednictvím portálu
description: Popisuje, jak vytvořit odběry služby Event Grid prostřednictvím portálu.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: spelluru
ms.openlocfilehash: b54bc52a2feaf4646d801265ddb273c2c86158ee
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54477902"
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

1. Chcete-li povolit nedoručení a přizpůsobit zásady opakování, vyberte **další funkce**.

   ![Vyberte další funkce](./media/subscribe-through-portal/select-additional-features.png)

1. Vyberte kontejner používat k ukládání událostí, které nejsou dodány a nastavit, jak se odesílají opakovaných pokusů.

   ![Povolit nedoručení a zkuste to znovu](./media/subscribe-through-portal/set-deadletter-retry.png)

1. Až budete hotovi, vyberte **Vytvořit**.

## <a name="create-subscription-on-resource"></a>Vytvoření odběru na prostředek

Některé zdroje událostí podporují vytvoření odběru událostí prostřednictvím portálu rozhraní pro daný prostředek. Vyberte zdroj události a vyhledejte **události** v levém podokně.

![Zadejte podrobnosti předplatného](./media/subscribe-through-portal/resource-events.png)

Portál nabízí možnosti pro vytvoření odběru událostí, které se týkají tohoto zdroje.

## <a name="next-steps"></a>Další postup

* Informace o doručování událostí a opakovaných pokusů [doručování zpráv služby Event Grid a zkuste to znovu](delivery-and-retry.md).
* Úvod do Event Gridu najdete v článku [Informace o službě Event Grid](overview.md).
* Pokud chcete rychle začít používat služby Event Grid, přečtěte si téma [vytvoření a směrování vlastních událostí pomocí služby Azure Event Grid](custom-event-quickstart.md).
