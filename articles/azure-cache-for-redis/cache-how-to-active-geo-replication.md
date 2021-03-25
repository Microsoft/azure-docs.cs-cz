---
title: Konfigurace aktivní geografické replikace pro Redis instance pro podnikovou mezipaměť
description: Naučte se replikovat Azure cache pro Redis podnikové instance napříč oblastmi Azure.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: yegu
ms.openlocfilehash: 3fe3131263d3cf1984eae1692854d8d6bcd2746a
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2021
ms.locfileid: "105109484"
---
# <a name="configure-active-geo-replication-for-enterprise-azure-cache-for-redis-instances-preview"></a>Konfigurace aktivní geografické replikace pro Redis instance pro podnikovou mezipaměť (Preview)

V tomto článku se dozvíte, jak nakonfigurovat aktivní geograficky replikovanou mezipaměť Azure pomocí Azure Portal.

Aktivní geografická replikace seskupuje dvě nebo víc podnikových mezipamětí Azure pro instance Redis do jediné mezipaměti, která zahrnuje různé oblasti Azure. Všechny instance slouží jako místní primární třídy. Aplikace rozhoduje o tom, které instance se mají použít pro požadavky na čtení a zápis.

## <a name="create-or-join-an-active-geo-replication-group"></a>Vytvořit nebo připojit aktivní geografickou skupinu replikace

> [!IMPORTANT]
> Aktivní geografická replikace musí být povolená v době, kdy je vytvořená mezipaměť Azure pro Redis.
>
>

1. Na kartě **Upřesnit** v části **nové** uživatelské rozhraní pro vytváření Redis Cache vyberte **Enterprise** for **clustering Policy**.

    ![Konfigurace aktivní geografické replikace](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-not-configured.png)

1. Kliknutím na **Konfigurovat** nastavte **aktivní geografickou replikaci**.

1. Vytvořte novou replikační skupinu pro první instanci mezipaměti nebo vyberte existující ze seznamu.

    ![Propojení mezipamětí](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-new-group.png)

1. Klikněte na **Konfigurovat** a dokončete.

    ![Aktivní geografická replikace je nakonfigurovaná.](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-configured.png)

1. Počkejte, až se první mezipaměť úspěšně vytvoří. Opakujte výše uvedené kroky pro každou další instanci mezipaměti v geograficky replikační skupině.

## <a name="remove-from-an-active-geo-replication-group"></a>Odebrat z aktivní geografické replikační skupiny

Pokud chcete odebrat instanci mezipaměti z aktivní geografické replikační skupiny, jednoduše odstraňte instanci. Zbývající instance se znovu nakonfigurují automaticky.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o funkcích Azure cache pro Redis.

* [Mezipaměť Azure pro úrovně služeb Redis](cache-overview.md#service-tiers)
* [Vysoká dostupnost pro Azure cache pro Redis](cache-high-availability.md)
