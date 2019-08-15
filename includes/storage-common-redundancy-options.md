---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/28/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5df0fe23183b53f13d6c6545e248724324434cde
ms.sourcegitcommit: df7942ba1f28903ff7bef640ecef894e95f7f335
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2019
ms.locfileid: "69029764"
---
Možnosti replikace účtu úložiště:

* [Místně redundantní úložiště (LRS)](../articles/storage/common/storage-redundancy-lrs.md): Jednoduchá strategie replikace s nízkými náklady. Data se replikují v rámci primární oblasti synchronně třikrát.
* [Redundantní úložiště zóny (ZRS)](../articles/storage/common/storage-redundancy-zrs.md): Replikace pro scénáře vyžadující vysokou dostupnost. Data se replikují synchronně v rámci tří zón dostupnosti Azure v primární oblasti.
* [Geograficky redundantní úložiště (GRS)](../articles/storage/common/storage-redundancy-grs.md): Replikace mezi různými oblastmi a ochrana proti výpadkům v oblasti regionu. Data se replikují synchronně třikrát v primární oblasti a pak se asynchronně replikují do sekundární oblasti. Pro přístup pro čtení dat v sekundární oblasti povolte geograficky redundantní úložiště s přístupem pro čtení (RA-GRS).
* [Geografická zóna – redundantní úložiště (GZRS) (Preview)](../articles/storage/common/storage-redundancy-gzrs.md): Replikace pro scénáře, které vyžadují vysokou dostupnost a maximální odolnost. Data se replikují synchronně v rámci tří zón dostupnosti Azure v primární oblasti a pak se asynchronně replikují do sekundární oblasti. Pro přístup pro čtení dat v sekundární oblasti povolte přístup pro čtení Geo-Zone-redundantní úložiště (RA-GZRS).
