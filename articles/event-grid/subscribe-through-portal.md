---
title: Předplatná Azure Event Grid prostřednictvím portálu
description: Tento článek popisuje, jak vytvořit odběry gridu událostí pro podporované zdroje, jako je azure blob storage, pomocí portálu Azure.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 3172c92ecae094ab5d978803d2ccac7e6404a5e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721502"
---
# <a name="subscribe-to-events-through-portal"></a>Přihlásit se k odběru událostí prostřednictvím portálu

Tento článek popisuje, jak vytvořit odběry event gridprostřednictvím portálu.

## <a name="create-event-subscriptions"></a>Vytvořit odběry událostí

Chcete-li vytvořit odběr služby Event Grid pro některý z podporovaných [zdrojů událostí](event-sources.md), použijte následující kroky. Tento článek ukazuje, jak vytvořit předplatné služby Event Grid pro předplatné Azure.

1. Vyberte **Všechny služby**.

   ![Vybrat všechny služby](./media/subscribe-through-portal/select-all-services.png)

1. Vyhledejte **odběry mřížky událostí** a vyberte je z dostupných možností.

   ![Search](./media/subscribe-through-portal/search.png)

1. Vyberte **+ Odběr události**.

   ![Přidat předplatné](./media/subscribe-through-portal/add-subscription.png)

1. Vyberte typ předplatného, který chcete vytvořit. Chcete-li se například přihlásit k odběru událostí pro vaše předplatné Azure, vyberte **předplatná Azure** a cílové předplatné.

   ![Vyberte předplatné Azure](./media/subscribe-through-portal/azure-subscription.png)

1. Chcete-li se přihlásit ke všem typům událostí pro tento zdroj událostí, zaškrtněte políčko **Přihlásit se ke všem typům událostí.** V opačném případě vyberte typy událostí pro toto předplatné.

   ![Výběr typů událostí](./media/subscribe-through-portal/select-event-types.png)

1. Zadejte další podrobnosti o předplatné události, jako je například koncový bod pro zpracování událostí a název předplatného.

   ![Poskytnutí podrobností o předplatném](./media/subscribe-through-portal/provide-subscription-details.png)

1. Chcete-li povolit zásady nedoručených zpráv a přizpůsobit zásady opakování, vyberte **možnost Další funkce**.

   ![Výběr dalších funkcí](./media/subscribe-through-portal/select-additional-features.png)

1. Vyberte kontejner, který chcete použít pro ukládání událostí, které nejsou doručeny, a nastavte způsob odeslání opakovaných pokusů.

   ![Povolení nedoručených nápisů a opakování](./media/subscribe-through-portal/set-deadletter-retry.png)

1. Až budete hotovi, vyberte **Vytvořit**.

## <a name="create-subscription-on-resource"></a>Vytvořit předplatné na prostředku

Některé zdroje událostí podporují vytváření odběru událostí prostřednictvím portálového rozhraní pro tento prostředek. Vyberte zdroj události a vyhledejte **události** v levém podokně.

![Poskytnutí podrobností o předplatném](./media/subscribe-through-portal/resource-events.png)

Portál vám nabídne možnosti pro vytvoření odběru událostí, které je relevantní pro daný zdroj.

## <a name="next-steps"></a>Další kroky

* Informace o doručení události a opakování zpráv [event grid u zpráv a opakování](delivery-and-retry.md).
* Úvod do Event Gridu najdete v článku [Informace o službě Event Grid](overview.md).
* Pokud chcete rychle začít používat Event Grid, přečtěte [si tématu Vytváření a směrování vlastních událostí pomocí Azure Event Grid](custom-event-quickstart.md).
