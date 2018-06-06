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
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34738679"
---
Ne: odebrání serveru koncového bodu není třeba restartování serveru! Odebrání a opětovné vytvoření serveru koncového bodu je téměř žádné vhodné řešení k opravě problémů s synchronizace, cloudu vrstvení nebo další aspekty synchronizace souborů Azure. Odebrání serveru koncového bodu je destruktivní operace, a může vést ke ztrátě dat v případě, že vrstvené soubory existují mimo obor názvů koncový bod serveru (najdete v části [proč vrstvené soubory existují mimo obor názvů koncový bod serveru](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint) pro Další informace o) nebo v nepřístupných souborů pro vrstvené soubory existující v rámci oboru názvů koncový bod serveru. Tyto problémy nevyřeší, když se koncový bod serveru znovu vytvoří. Vrstvený soubory můžou existovat v rámci vašeho oboru názvů koncový bod serveru i v případě, že nikdy neměli cloudu vrstvení povoleno. Proto doporučujeme, pokud byste chtěli zastavit pomocí této konkrétní složky synchronizační souboru Azure nebo byla explicitně pokyn k tomu pracovníkem společnosti Microsoft není odebrat serveru koncového bodu. Další informace o koncových bodů odebrat server najdete v tématu [odebrat koncový bod serveru](../articles/storage/files/storage-sync-files-server-endpoint.md#remove-a-server-endpoint).    