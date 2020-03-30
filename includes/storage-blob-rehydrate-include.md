---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/09/2019
ms.service: storage
ms.subservice: blobs
ms.topic: include
ms.reviewer: hux
ms.custom: include file
ms.openlocfilehash: 81ffc87ce97a936e693c59bca6caf721cb8599cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "75780232"
---
Pokud chcete načíst data v archivním úložišti, musíte nejdřív změnit vrstvu příslušného objektu blob na studenou nebo horkou. Tento proces je známý jako rehydratace a může trvat hodiny. Pro optimální rehydratační výkon doporučujeme velké velikosti objektů blob. Současné dosazování několika malých objektů blob může dobu dokončení prodloužit. V současné době existují dvě priority rehydratace, High (náhled) a Standard, které lze nastavit prostřednictvím volitelné vlastnosti *x-ms rehydrate priority* v operaci [Nastavit objekt blob](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) nebo [kopírovat objekt blob.](https://docs.microsoft.com/rest/api/storageservices/copy-blob)

* **Standardní priorita**: Žádost o rehydrataci bude zpracována v pořadí, v jakém byla přijata, a může trvat až 15 hodin.
* **Vysoká priorita (náhled)**: Požadavek na rehydrataci bude upřednostněn před standardními požadavky a může skončit za méně než 1 hodinu. Vysoká priorita může trvat déle než 1 hodinu, v závislosti na velikosti objektu blob a aktuální poptávky. Požadavky s vysokou prioritou mají zaručeno, že budou mít prioritu před požadavky standardní priority.

> [!NOTE]
> Standardní priorita je výchozí možnost rehydratace pro archivaci. Vysoká priorita je rychlejší možnost, která bude stát více než standardní rehydratace priorit a je obvykle vyhrazena pro použití v situacích obnovení nouzových dat.

Jakmile je požadavek na rehydrataci zahájen, nelze jej zrušit. Během rehydratace můžete zkontrolovat vlastnost objektu blob *stav archivu* a potvrdit, zda se úroveň změnila. V závislosti na cílové vrstvě má tento stav hodnotu rehydrate-pending-to-hot nebo rehydrate-pending-to-cool. Po dokončení je vlastnost stavu archivu odebrána a vlastnost objektu blob *úrovně přístupu* odráží novou horkou nebo studenou úroveň.
