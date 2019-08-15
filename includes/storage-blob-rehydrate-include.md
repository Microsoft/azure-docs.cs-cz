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
ms.openlocfilehash: 03674a51566ab89791725d1a51c7af12ed6949e5
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2019
ms.locfileid: "68952902"
---
Pokud chcete načíst data v archivním úložišti, musíte nejdřív změnit vrstvu příslušného objektu blob na studenou nebo horkou. Tento proces se označuje jako dosazování a může trvat hodiny, než se dokončí. Pro zajištění optimálního výkonu pro vysazování doporučujeme použít velké velikosti objektů BLOB. Současné dosazování několika malých objektů blob může dobu dokončení prodloužit. V současné době existují dva rehydratované priority, vysoká (Preview) a Standard, které lze nastavit prostřednictvím volitelné vlastnosti *x-MS-rehydratované-priorita* v rámci operace [nastavení vrstvy BLOB](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) nebo [kopie objektu BLOB](https://docs.microsoft.com/rest/api/storageservices/copy-blob) .

* **Standardní priorita**: Žádost o uvolnění bude zpracována v pořadí, v jakém byla přijata, a může trvat až 15 hodin.
* **Vysoká priorita (Preview)** : Požadavek na dehydrataci se bude upřednostňovat na standardní požadavky a může skončit za 1 hodinu. V závislosti na velikosti objektu BLOB a aktuální poptávce může být vysoká priorita delší než 1 hodina. U žádostí s vysokou prioritou je zaručeno, že bude upřednostněna priorita v rámci požadavků na standardní prioritu.

> [!NOTE]
> Standardní priorita je výchozí možnost k vysazování pro archiv. Vysoká priorita je rychlejší možnost, která bude mít za následek vyšší náklady než řešení Standard priority a je obvykle vyhrazena pro použití v situacích nouzové obnovy dat.

Během dosazování můžete zkontrolovat vlastnost *Stav archivu* objektu blob a ověřit, jestli se úroveň změnila. V závislosti na cílové vrstvě má tento stav hodnotu rehydrate-pending-to-hot nebo rehydrate-pending-to-cool. Po dokončení se vlastnost stavu archivu odebere a vlastnost *Access Tier* objektu blob odpovídá nové horké nebo studené úrovni.
