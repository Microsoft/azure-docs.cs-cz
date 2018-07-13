---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 05/31/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 33dff710d83bd12a8db343a89c6e4576d1397ba7
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38763073"
---
Ne: odebrání koncového bodu serveru není třeba restartování serveru. Odebrat a znovu vytvořit koncový bod serveru je téměř nikdy vhodné řešení pro řešení problémů s synchronizace, vrstvení cloudu a další aspekty Azure File Sync. Odebrání koncový bod serveru je destruktivní operace, a může vést ke ztrátě dat v případě, že vrstvené soubory existují mimo obor názvů koncového bodu serveru (viz [proč vrstvené soubory existují mimo obor názvů koncový bod serveru](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint) pro Další informace) nebo do nedostupný souborů pro vrstvené soubory existující v rámci oboru názvů koncový bod serveru. Tyto potíže nevyřeší, když se znovu vytvoří koncový bod serveru. V rámci vašeho oboru názvů koncový bod serveru můžou existovat vrstvené soubory i v případě, že nikdy neměli povoleno vrstvení cloudu. Proto doporučujeme, pokud chcete ukončit používání Azure File Sync k této konkrétní složce nebo byla explicitně pokyn k tomu pracovníkem Microsoft není odebrat koncový bod serveru. Další informace o koncových bodů odebrat server, naleznete v tématu [odebrat koncový bod serveru](../articles/storage/files/storage-sync-files-server-endpoint.md#remove-a-server-endpoint).    