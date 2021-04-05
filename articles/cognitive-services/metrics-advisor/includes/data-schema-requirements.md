---
title: Požadavky na schéma dat
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: 4c55c25621df1925b6ed6c374d8af88551eb1e46
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "96231425"
---
Poradce metriky je služba pro detekci anomálií, diagnostiku a analýzu časových řad. Jako služba využívající AI používá vaše data ke výukě používaného modelu. Služba akceptuje tabulky agregovaných dat s následujícími sloupci:

* **Measure** (povinné): jeden nebo více sloupců, které obsahují číselné hodnoty.
* **Časové razítko** (volitelné): nula nebo jeden sloupec s typem `DateTime` nebo `String` . Pokud tento sloupec není nastaven, časové razítko je nastaveno jako počáteční čas každé doby přijímání. Naformátovat časové razítko na: `yyyy-MM-ddTHH:mm:ssZ` . 
  * **Časové razítko by mělo odpovídat členitosti metriky. Například denní metrika by měla zajišťovat hodinu, minutu a sekundu v časovém razítku, které `00:00:00` je označeno jako**.
* **Dimenze** (volitelné): sloupce mohou být libovolného datového typu. Buďte opatrní při práci s velkými objemy sloupců a hodnot, abyste zabránili zpracování nadměrného počtu rozměrů.

> [!Note]
> U každé metriky by mělo být pouze jedno časové razítko na míru, které odpovídá jedné kombinaci dimenzí. Naagreguje data před zprovozněním nebo použijte dotaz k určení dat, která se mají ingestovat.