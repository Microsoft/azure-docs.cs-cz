---
title: Předplatná Azure Event Grid prostřednictvím portálu
description: Tento článek popisuje, jak vytvořit předplatná Event Grid pro podporované zdroje, jako je Azure Blob Storage, pomocí Azure Portal.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 3172c92ecae094ab5d978803d2ccac7e6404a5e1
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721502"
---
# <a name="subscribe-to-events-through-portal"></a>Přihlášení k odběru událostí prostřednictvím portálu

Tento článek popisuje, jak vytvořit předplatná Event Grid prostřednictvím portálu.

## <a name="create-event-subscriptions"></a>Vytvoření odběrů událostí

Chcete-li vytvořit předplatné Event Grid pro některý z podporovaných [zdrojů událostí](event-sources.md), použijte následující postup. Tento článek ukazuje, jak vytvořit předplatné služby Event Grid pro předplatné Azure.

1. Vyberte **Všechny služby**.

   ![Vyberte všechny služby](./media/subscribe-through-portal/select-all-services.png)

1. Vyhledejte **Event Grid odběry** a vyberte ji z dostupných možností.

   ![Hledat](./media/subscribe-through-portal/search.png)

1. Vyberte **+ Odběr události**.

   ![Přidat předplatné](./media/subscribe-through-portal/add-subscription.png)

1. Vyberte typ předplatného, které chcete vytvořit. Pokud se například chcete přihlásit k odběru událostí předplatného Azure, vyberte **předplatná Azure** a cílové předplatné.

   ![Výběr předplatného Azure](./media/subscribe-through-portal/azure-subscription.png)

1. Chcete-li se přihlásit k odběru všech typů událostí pro tento zdroj události, zachovejte možnost přihlásit se k **odběru všech typů událostí** . V opačném případě vyberte typy událostí pro toto předplatné.

   ![Výběr typů událostí](./media/subscribe-through-portal/select-event-types.png)

1. Zadejte další podrobnosti o odběru události, například koncový bod pro zpracování událostí a název předplatného.

   ![Zadat podrobnosti předplatného](./media/subscribe-through-portal/provide-subscription-details.png)

1. Pokud chcete povolit nedoručené dopisy a přizpůsobovat zásady opakování, vyberte **Další funkce**.

   ![Výběr dalších funkcí](./media/subscribe-through-portal/select-additional-features.png)

1. Vyberte kontejner, který se má použít pro ukládání nedoručených událostí, a nastavte způsob, jakým se odesílají pokusy o opakování.

   ![Povolit nedoručené dopisy a opakovat akci](./media/subscribe-through-portal/set-deadletter-retry.png)

1. Až budete hotovi, vyberte **Vytvořit**.

## <a name="create-subscription-on-resource"></a>Vytvořit předplatné u prostředku

Některé zdroje událostí podporují vytvoření odběru událostí prostřednictvím rozhraní portálu pro daný prostředek. Vyberte zdroj události a vyhledejte **události** v levém podokně.

![Zadat podrobnosti předplatného](./media/subscribe-through-portal/resource-events.png)

Portál nabízí možnosti pro vytvoření odběru událostí, které je relevantní pro daný zdroj.

## <a name="next-steps"></a>Další kroky

* Informace o doručování událostí a opakovaných pokusů [doručování zpráv služby Event Grid a zkuste to znovu](delivery-and-retry.md).
* Úvod do Event Gridu najdete v článku [Informace o službě Event Grid](overview.md).
* Pokud chcete rychle začít používat služby Event Grid, přečtěte si téma [vytvoření a směrování vlastních událostí pomocí služby Azure Event Grid](custom-event-quickstart.md).
