---
title: Požadavky na kontejner a doporučení
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: a441f677687789729b96011f8bf98606418ca659
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92677188"
---
> [!NOTE]
> Požadavky a doporučení jsou založené na srovnávacích testech s jednou žádostí za sekundu pomocí 8 MB naskenovaného obchodního dopisu obsahujícího 29 řádků a celkem 803 znaků.

Následující tabulka popisuje minimální a doporučené přidělení prostředků pro každý kontejner pro čtení.

| Kontejner | Minimum | Doporučeno |
|-----------|---------|-------------|
| Přečíst 2,0 – Preview | 1 jádro, 8 GB paměti |  8 jader, 16 GB paměti |
| Přečíst 3,1 – Preview | 8 jader, 16 GB paměti | 8 jader, 24 GB paměti |

* Každé jádro musí mít aspoň 2,6 GHz nebo rychlejší.

Základní a paměť odpovídají `--cpus` `--memory` nastavení a, která se používají jako součást `docker run` příkazu.
