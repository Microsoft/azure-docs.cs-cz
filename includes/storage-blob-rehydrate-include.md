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
ms.openlocfilehash: 53d2d47143c5a2cefbd50faca9a02af18ffae315
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84754674"
---
Pokud chcete načíst data v archivním úložišti, musíte nejdřív změnit vrstvu příslušného objektu blob na studenou nebo horkou. Tento proces se označuje jako dosazování a může trvat hodiny, než se dokončí. Pro zajištění optimálního výkonu pro vysazování doporučujeme použít velké velikosti objektů BLOB. Současné dosazování několika malých objektů blob může dobu dokončení prodloužit. V současné době existují dva rehydratované priority, vysoké a standardní, které lze nastavit prostřednictvím volitelné vlastnosti *x-MS-rehydratované-priority* na [úrovni objektu BLOB](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) nebo operace [kopírování objektu BLOB](https://docs.microsoft.com/rest/api/storageservices/copy-blob) .

* **Standardní priorita**: požadavek na dehydrataci se zpracuje v uvedeném pořadí a může trvat až 15 hodin.
* **Vysoká priorita**: požadavek na dehydrataci bude mít přednost před standardními požadavky a může docházet za 1 hodinu pro objekty o velikosti 10 GB. 

> [!NOTE]
> Standardní priorita je výchozí možnost k vysazování pro archiv. Vysoká priorita je rychlejší možnost, která bude mít za následek vyšší náklady než řešení Standard priority a je obvykle vyhrazena pro použití v situacích nouzové obnovy dat.
>
> V závislosti na velikosti objektu BLOB a aktuální poptávce může být vysoká priorita delší než 1 hodina. U žádostí s vysokou prioritou je zaručeno, že bude upřednostněna priorita v rámci požadavků na standardní prioritu.

Po zahájení žádosti o dehydrataci ji nelze zrušit. Během procesu opětovného nasazování bude vlastnost objektu BLOB *x-MS-Access-úrovně* nadále zobrazená jako archivní, dokud není dopředné dosazování do online úrovně. Chcete-li potvrdit stav a průběh opětovného vystavení, můžete zavolat [Get vlastnosti objektu BLOB](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties) pro kontrolu vlastností rozhraní BLOB *x-MS-Archive-status* a *x-MS-rehydratované-priority* . Stav archivu může v závislosti na cílové vrstvě pro redehydrataci přečíst "rehydratované" pending-to-Hot "nebo" rehydratované ", která čeká na chlazení. Priorita pro rehydratované bude označovat rychlost "vysoká" nebo "Standard". Po dokončení se odeberou vlastnosti stav archivu a nastavení priority a vlastnost objektu BLOB vrstvy přístupu se aktualizuje tak, aby odrážela vybranou horkou nebo studenou úroveň.
