---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/14/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 73c2b742ede21a4e86d717d994f8ebc4f16389c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77157210"
---
Možnosti redundance pro účet úložiště zahrnují:

* Místně redundantní úložiště (LRS): Jednoduchá, nízkonákladová redundantní strategie. Data jsou zkopírována synchronně třikrát v rámci primární oblasti.
* Zónově redundantní úložiště (ZRS): Redundance pro scénáře vyžadující vysokou dostupnost. Data se synchronně kopírují ve třech zónách dostupnosti Azure v primární oblasti.
* Geograficky redundantní úložiště (GRS): Meziregionální redundance na ochranu před regionálními výpadky. Data jsou zkopírována synchronně třikrát v primární oblasti a pak asynchronně zkopírována do sekundární oblasti. Pro přístup pro čtení dat v sekundární oblasti povolte geograficky redundantní úložiště pro čtení (RA-GRS).
* Geograficky redundantní úložiště (GZRS) (preview): Redundance pro scénáře vyžadující vysokou dostupnost a maximální odolnost. Data se synchronně kopírují přes tři zóny dostupnosti Azure v primární oblasti a pak se asynchronně zkopírují do sekundární oblasti. Pro přístup ke čtení dat v sekundární oblasti povolte geograficky redundantní úložiště pro čtení geograficky zónově redundantní (RA-GZRS).

Další informace o možnostech redundance ve službě Azure Storage najdete v [tématu Redundance Azure Storage](../articles/storage/common/storage-redundancy.md).
