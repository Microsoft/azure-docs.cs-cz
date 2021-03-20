---
title: Předplatná Azure Event Grid prostřednictvím portálu
description: Tento článek popisuje, jak vytvořit předplatná Event Grid pro podporované zdroje, jako je Azure Blob Storage, pomocí Azure Portal.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: e80e2243c93ab38187646256f567d6ab73c40100
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95995059"
---
# <a name="subscribe-to-events-through-portal"></a>Přihlášení k odběru událostí prostřednictvím portálu

Tento článek popisuje, jak vytvořit předplatná Event Grid prostřednictvím portálu.

## <a name="create-event-subscriptions"></a>Vytvoření odběrů událostí

Chcete-li vytvořit předplatné Event Grid pro některý z podporovaných [zdrojů událostí](overview.md#event-sources), použijte následující postup. Tento článek ukazuje, jak vytvořit předplatné služby Event Grid pro předplatné Azure.

1. Vyberte **Všechny služby**.

   ![Vybrat všechny služby](./media/subscribe-through-portal/select-all-services.png)

1. Vyhledejte **Event Grid odběry** a vyberte ji z dostupných možností.

   ![Snímek obrazovky zobrazuje hledání v Azure Portal s vybranými Event Grid odběry.](./media/subscribe-through-portal/search.png)

1. Vyberte **+ Odběr události**.

   ![Přidat předplatné](./media/subscribe-through-portal/add-subscription.png)

1. Vyberte typ předplatného, které chcete vytvořit. Pokud se například chcete přihlásit k odběru událostí předplatného Azure, vyberte **předplatná Azure** a cílové předplatné.

   ![Výběr předplatného Azure](./media/subscribe-through-portal/azure-subscription.png)

1. Chcete-li se přihlásit k odběru všech typů událostí pro tento zdroj události, zachovejte možnost přihlásit se k **odběru všech typů událostí** . V opačném případě vyberte typy událostí pro toto předplatné.

   ![Výběr typů událostí](./media/subscribe-through-portal/select-event-types.png)

1. Zadejte další podrobnosti o odběru události, například koncový bod pro zpracování událostí a název předplatného.

   ![Snímek obrazovky zobrazující oddíly "Podrobnosti koncového bodu" a "Podrobnosti odběru události" se zadanou hodnotou názvu předplatného.](./media/subscribe-through-portal/provide-subscription-details.png)

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

* Pro informace o doručení a opakování události [Event Grid doručování zpráv a akci opakujte](delivery-and-retry.md).
* Úvod do Event Gridu najdete v článku [Informace o službě Event Grid](overview.md).
* Pokud chcete rychle začít používat Event Grid, přečtěte si téma [Vytvoření a směrování vlastních událostí pomocí Azure Event Grid](custom-event-quickstart.md).
