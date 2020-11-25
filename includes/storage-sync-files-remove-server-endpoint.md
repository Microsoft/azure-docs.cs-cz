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
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005306"
---
Ne: odebrání koncového bodu serveru nevypadá jako restartování serveru. Odebrání a opětovné vytvoření koncového bodu serveru není skoro nikdy vhodné řešení pro opravy potíží se synchronizací, vrstvením cloudu nebo dalšími aspekty Azure File Sync. Odebrání koncového bodu serveru je destruktivní operace. Může dojít ke ztrátě dat v případě, že navrstvené soubory existují mimo obor názvů koncového bodu serveru. Další informace najdete v tématu [Proč tyto vrstvené soubory existují mimo obor názvů koncového bodu serveru](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint) . Nebo to může mít za následek nepřístupné soubory pro vrstvené soubory, které existují v rámci oboru názvů koncového bodu serveru. Tyto problémy se nebudou řešit při opětovném vytvoření koncového bodu serveru. Vrstvené soubory můžou existovat v rámci oboru názvů koncového bodu serveru i v případě, že jste nikdy nepovolili tvorbu cloudových vrstev. Z tohoto důvodu doporučujeme, abyste koncový bod serveru neodebrali, pokud nechcete přerušit použití Azure File Sync s touto konkrétní složkou nebo jste to učinili explicitně pracovníkem Microsoftu. Další informace o odebrání koncových bodů serveru najdete v tématu [Odebrání koncového bodu serveru](../articles/storage/files/storage-sync-files-server-endpoint.md#remove-a-server-endpoint).    
