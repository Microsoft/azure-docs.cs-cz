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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "77157210"
---
Mezi možnosti redundance účtu úložiště patří:

* Místně redundantní úložiště (LRS): jednoduchá strategie redundance s nízkými náklady. Data se zkopírují synchronně třikrát v rámci primární oblasti.
* Zóna – redundantní úložiště (ZRS): redundance pro scénáře vyžadující vysokou dostupnost. Data se zkopírují synchronně v rámci tří zón dostupnosti Azure v primární oblasti.
* Geograficky redundantní úložiště (GRS): meziregionální redundance pro ochranu před místními výpadky. Data se zkopírují synchronně třikrát v primární oblasti a zkopírují se asynchronně do sekundární oblasti. Pro přístup pro čtení dat v sekundární oblasti povolte geograficky redundantní úložiště s přístupem pro čtení (RA-GRS).
* Geo-Zone – redundantní úložiště (GZRS) (Preview): redundance pro scénáře vyžadující vysokou dostupnost a maximální odolnost. Data se zkopírují synchronně v rámci tří zón dostupnosti Azure v primární oblasti a pak se asynchronně zkopírovaly do sekundární oblasti. Pro přístup pro čtení dat v sekundární oblasti povolte přístup pro čtení Geo-Zone-redundantní úložiště (RA-GZRS).

Další informace o možnostech redundance v Azure Storage najdete v tématu [Azure Storage redundance](../articles/storage/common/storage-redundancy.md).
