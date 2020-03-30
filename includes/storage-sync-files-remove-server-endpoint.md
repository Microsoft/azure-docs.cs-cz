---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/31/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: be3ce42ccdb9bedd02b8dead2426ac629fa12ef2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "72391601"
---
Ne: odebrání koncového bodu serveru není jako restartování serveru! Odebrání a opětovné vytvoření koncového bodu serveru je téměř nikdy vhodné řešení pro řešení problémů se synchronizací, vrstvením v cloudu nebo jinými aspekty Synchronizace souborů Azure. Odebrání koncového bodu serveru je destruktivní operace. Může dojít ke ztrátě dat v případě, že vrstvené soubory existují mimo obor názvů koncového bodu serveru. Další informace naleznete v [tématu Proč existují vrstvené soubory mimo obor názvů koncového bodu serveru.](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint) Nebo může mít za následek nepřístupné soubory pro vrstvené soubory, které existují v oboru názvů koncového bodu serveru. Tyto problémy se nevyřeší při opětovném vytvoření koncového bodu serveru. Vrstvené soubory mohou existovat v rámci oboru názvů koncových bodů serveru i v případě, že jste nikdy neměli povoleno vrstvení v cloudu. To je důvod, proč doporučujeme neodebírat koncový bod serveru, pokud byste chtěli přestat používat Azure File Sync s touto konkrétní složkou nebo jste byli výslovně pokyn, aby tak učinily inženýr společnosti Microsoft. Další informace o odebrání koncových bodů serveru naleznete [v tématu Odebrání koncového bodu serveru](../articles/storage/files/storage-sync-files-server-endpoint.md#remove-a-server-endpoint).    
