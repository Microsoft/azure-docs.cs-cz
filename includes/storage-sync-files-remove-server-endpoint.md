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
ms.openlocfilehash: 2f50a1d046b7b7d3fd7507ef87f8f29096a4faa4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774553"
---
Ne: odebrání koncového bodu serveru nevypadá jako restartování serveru. Odebrání a opětovné vytvoření koncového bodu serveru není skoro nikdy vhodné řešení pro opravy potíží se synchronizací, vrstvením cloudu nebo dalšími aspekty Synchronizace souborů Azure. Odebrání koncového bodu serveru je destruktivní operace. Může dojít ke ztrátě dat v případě, že navrstvené soubory existují mimo obor názvů koncového bodu serveru. Další informace najdete v tématu [Proč tyto vrstvené soubory existují mimo obor názvů koncového bodu serveru](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint) . Nebo to může mít za následek nepřístupné soubory pro vrstvené soubory, které existují v rámci oboru názvů koncového bodu serveru. Tyto problémy se nebudou řešit při opětovném vytvoření koncového bodu serveru. Vrstvené soubory můžou existovat v rámci oboru názvů koncového bodu serveru i v případě, že jste nikdy nepovolili tvorbu cloudových vrstev. Proto doporučujeme, abyste koncový bod serveru neodebrali, pokud nechcete přestat používat Synchronizace souborů Azure s touto konkrétní složkou nebo jste na to učinili explicitně pracovníkem Microsoftu. Další informace o odebrání koncových bodů serveru najdete v tématu [Odebrání koncového bodu serveru](../articles/storage/file-sync/file-sync-server-endpoint.md#remove-a-server-endpoint).    
