---
title: Minimální dobou výpadku migrace do Azure Database pro databázi MySQL
description: Tento článek popisuje, jak provést minimální dobou výpadku migraci databáze MySQL do Azure databáze pro databázi MySQL pomocí služby Azure databáze migrace.
services: mysql
author: HJToland3
ms.author: jtoland
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 06/21/2018
ms.openlocfilehash: ecbd35bd45bd11292bbe4a032329d704858d4c77
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36293917"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Minimální dobou výpadku migrace do Azure Database pro databázi MySQL
Můžete provést migrací MySQL Azure Database pro databázi MySQL s minimálními výpadky pomocí nově přináší **průběžné synchronizace schopností** pro [služba migrace databáze Azure](https://aka.ms/get-dms) (DMS). Tato funkce omezí doba výpadku, který je spojené s prováděním.

## <a name="overview"></a>Přehled
Služba DMS provede počáteční zatížení místní databázi Azure pro databázi MySQL a pak nepřetržitě synchronizuje všechny nové transakce do Azure aplikace bez přerušení chodu. Po data zachytí na cíli Azure straně, zastavte aplikaci na chvíli stručný (minimální výpadek), počkat na poslední dávku dat (od času zastavte aplikaci, dokud nebude aplikace efektivně provést jakékoli nové přenosy dat k dispozici) pro zachycení až na cíli a aktualizujte připojovací řetězec tak, aby odkazoval na Azure. Jakmile budete hotovi, bude vaše aplikace za provozu v Azure!

![Průběžné synchronizace se službou Azure databáze migrace](./media/howto-migrate-online/ContinuousSync.png)

Migrace DMS MySQL zdrojů je aktuálně ve verzi preview. Pokud chcete vyzkoušet službu, kterou chcete migrovat úlohy MySQL, zaregistrujte si prostřednictvím Azure DMS [stránku s náhledem](https://aka.ms/dms-preview) vyjádřit váš zájem. Vaše zpětná vazba je neocenitelnou pomocí v dále zlepšit služby.

## <a name="next-steps"></a>Další postup
- Zobrazit na video [jednoduše provést migraci databáze MySQL/PostgreSQL spravované služby aplikací pro Azure](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201), který obsahuje ukázku znázorňující způsob migrace aplikací MySQL do Azure Database pro databázi MySQL.
- Zaregistrujte si omezené preview minimální dobou výpadku migrace MySQL do Azure Database pro databázi MySQL prostřednictvím Azure DMS [stránku s náhledem](https://aka.ms/dms-preview).
