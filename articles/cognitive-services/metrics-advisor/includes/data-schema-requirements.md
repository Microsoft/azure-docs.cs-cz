---
title: Požadavky na schéma dat
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: a64bb5b28a06d9a013d59e022047f5e2841126ab
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936498"
---
Monitor metriky je služba pro detekci anomálií časových řad, diagnostiku a analýzu. Jako služba využívající AI používá vaše data ke výukě používaného modelu. Služba akceptuje tabulky agregovaných dat s následujícími sloupci:

* **Measure** (povinné): jeden nebo více sloupců, které obsahují číselné hodnoty.
* **Časové razítko** (volitelné): nula nebo jeden sloupec s typem `DateTime` nebo `String` . Pokud tento sloupec není nastaven, časové razítko je nastaveno jako počáteční čas každé doby přijímání. Naformátovat časové razítko na: `yyyy-MM-ddTHH:mm:ssZ` . 
  * **Časové razítko by mělo odpovídat členitosti metriky. Například denní metrika by měla zajišťovat hodinu, minutu a sekundu v časovém razítku, které `00:00:00` je označeno jako **.
* **Dimenze** (volitelné): sloupce mohou být libovolného datového typu. Buďte opatrní při práci s velkými objemy sloupců a hodnot, abyste zabránili zpracování nadměrného počtu rozměrů.

> [!Note]
> U každé metriky by mělo být pouze jedno časové razítko na míru, které odpovídá jedné kombinaci dimenzí. Naagreguje data před zprovozněním nebo použijte dotaz k určení dat, která se mají ingestovat.