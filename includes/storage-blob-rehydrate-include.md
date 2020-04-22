---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/08/2020
ms.service: storage
ms.subservice: blobs
ms.topic: include
ms.reviewer: hux
ms.custom: include file
ms.openlocfilehash: fc5f4d2c10cac23600025a72fedf7fe2cec5a34e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684080"
---
Pokud chcete načíst data v archivním úložišti, musíte nejdřív změnit vrstvu příslušného objektu blob na studenou nebo horkou. Tento proces je známý jako rehydratace a může trvat hodiny. Pro optimální rehydratační výkon doporučujeme velké velikosti objektů blob. Současné dosazování několika malých objektů blob může dobu dokončení prodloužit. V současné době existují dvě priority rehydratace, Vysoká a Standardní, které lze nastavit prostřednictvím volitelné *vlastnosti x-ms rehydrate priority* na set [blob vrstvy](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) nebo [kopírování objektu blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob) operace.

* **Standardní priorita**: Žádost o rehydrataci bude zpracována v pořadí, v jakém byla přijata, a může trvat až 15 hodin.
* **Vysoká priorita**: Požadavek na rehydrataci bude upřednostněn před standardními požadavky a může skončit za méně než 1 hodinu. Vysoká priorita může trvat déle než 1 hodinu, v závislosti na velikosti objektu blob a aktuální poptávky. Požadavky s vysokou prioritou mají zaručeno, že budou mít prioritu před požadavky standardní priority.

> [!NOTE]
> Standardní priorita je výchozí možnost rehydratace pro archivaci. Vysoká priorita je rychlejší možnost, která bude stát více než standardní rehydratace priorit a je obvykle vyhrazena pro použití v situacích obnovení nouzových dat.

Jakmile je požadavek na rehydrataci zahájen, nelze jej zrušit. Během procesu rehydratace vlastnost objektu blob *x-ms přístup úrovně* bude nadále zobrazovat jako archiv, dokud rehydratace je dokončena na úroveň online. Chcete-li potvrdit stav a průběh rehydratace, můžete volat [Get Blob Properties](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties) a zkontrolovat vlastnosti objektu blob *x-ms-archive-a* vlastnosti objektu blob *s prioritou x-ms-rehydrate.* Stav archivu může číst "rehydrate-pending-to-hot" nebo "rehydrate-pending-to-cool" v závislosti na cílové úrovni rehydratace. Priorita rehydrátu bude uvádět rychlost "Vysoká" nebo "Standardní". Po dokončení jsou odebrány vlastnosti priority archivu a rehydratace priority a vlastnost objektu blob úrovně přístupu se aktualizuje tak, aby odrážela vybranou horkou nebo studenou úroveň.
