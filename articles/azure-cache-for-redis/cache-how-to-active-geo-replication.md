---
title: Konfigurace aktivní geografické replikace pro Redis instance pro podnikovou mezipaměť
description: Naučte se replikovat Azure cache pro Redis podnikové instance napříč oblastmi Azure.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: yegu
ms.openlocfilehash: d52998e477e44f89a35a70f7e2b5d49cbc68e386
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662249"
---
# <a name="configure-active-geo-replication-for-enterprise-azure-cache-for-redis-instances-preview"></a>Konfigurace aktivní geografické replikace pro Redis instance pro podnikovou mezipaměť (Preview)

V tomto článku se dozvíte, jak nakonfigurovat aktivní geograficky replikovanou mezipaměť Azure pomocí Azure Portal.

Aktivní geografická replikace seskupuje dvě nebo víc podnikových mezipamětí Azure pro instance Redis do jediné mezipaměti, která zahrnuje různé oblasti Azure. Všechny instance slouží jako místní primární třídy. Aplikace rozhoduje o tom, které instance se mají použít pro požadavky na čtení a zápis.

## <a name="create-or-join-an-active-geo-replication-group"></a>Vytvořit nebo připojit aktivní geografickou skupinu replikace

> [!IMPORTANT]
> Aktivní geografická replikace musí být povolená v době, kdy je vytvořená mezipaměť Azure pro Redis.
>
>

1. V novém uživatelském rozhraní pro vytváření **Redis Cache** klikněte na **Konfigurovat** a nastavte **aktivní geografickou replikaci** na kartě **Upřesnit** .

    ![Konfigurace aktivní geografické replikace](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-not-configured.png)

1. Vytvořte novou replikační skupinu pro první instanci mezipaměti nebo vyberte existující ze seznamu.

    ![Propojení mezipamětí](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-new-group.png)

1. Klikněte na **Konfigurovat** a dokončete.

    ![Propojení mezipamětí](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-configured.png)

1. Opakujte výše uvedené kroky pro každou další instanci mezipaměti v geograficky replikační skupině.

## <a name="remove-from-an-active-geo-replication-group"></a>Odebrat z aktivní geografické replikační skupiny

Pokud chcete odebrat instanci mezipaměti z aktivní geografické replikační skupiny, jednoduše odstraňte instanci. Zbývající instance se znovu nakonfigurují automaticky.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o funkcích Azure cache pro Redis.

* [Mezipaměť Azure pro úrovně služeb Redis](cache-overview.md#service-tiers)
* [Vysoká dostupnost pro Azure cache pro Redis](cache-high-availability.md)
