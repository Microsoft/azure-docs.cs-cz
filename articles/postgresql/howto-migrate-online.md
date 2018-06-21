---
title: Minimální dobou výpadku migrace do databáze Azure pro PostgreSQL
description: Tento článek popisuje, jak provést minimální dobou výpadku migraci databáze PostgreSQL do databáze Azure pro PostgreSQL pomocí služby Azure databáze migrace.
services: postgresql
author: HJToland3
ms.author: jtoland
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 06/21/2018
ms.openlocfilehash: 9ab5d4615a8baf763d0b7ee47bf0890124f8665c
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36292538"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql"></a>Minimální dobou výpadku migrace do databáze Azure pro PostgreSQL
Můžete provést PostgreSQL migrací databáze Azure pro PostgreSQL s minimálními výpadky pomocí nově přináší **průběžné synchronizace schopností** pro [služba migrace databáze Azure](https://aka.ms/get-dms) (DMS) . Tato funkce omezí doba výpadku, který je spojené s prováděním.

## <a name="overview"></a>Přehled
Služba DMS provede počáteční zatížení místní databázi Azure pro PostgreSQL a pak nepřetržitě synchronizuje všechny nové transakce do Azure aplikace bez přerušení chodu. Po data zachytí na cíli Azure straně, zastavte aplikaci na chvíli stručný (minimální výpadek), počkat na poslední dávku dat (od času zastavte aplikaci, dokud nebude aplikace efektivně provést jakékoli nové přenosy dat k dispozici) pro zachycení až na cíli a aktualizujte připojovací řetězec tak, aby odkazoval na Azure. Jakmile budete hotovi, bude vaše aplikace za provozu v Azure!

![Průběžné synchronizace se službou Azure databáze migrace](./media/howto-migrate-online/ContinuousSync.png)

Migrace DMS PostgreSQL zdrojů je aktuálně ve verzi preview. Pokud chcete vyzkoušet službu, kterou chcete migrovat úlohy PostgreSQL, zaregistrujte si prostřednictvím Azure DMS [stránku s náhledem](https://aka.ms/dms-preview) vyjádřit váš zájem. Vaše zpětná vazba je neocenitelnou pomocí v dále zlepšit služby.

## <a name="next-steps"></a>Další postup
- Zobrazit na video [modernizace aplikaci s Microsoft Azure](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102), který obsahuje ukázku znázorňující způsob migrace PostgreSQL aplikace do databáze Azure pro PostgreSQL.
- Zaregistrujte si omezené preview minimální dobou výpadku migrace PostgreSQL do databáze Azure pro PostgreSQL prostřednictvím Azure DMS [stránku s náhledem](https://aka.ms/dms-preview).
