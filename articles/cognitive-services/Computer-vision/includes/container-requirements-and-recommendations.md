---
title: Požadavky na kontejner a doporučení
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/23/2020
ms.author: aahi
ms.openlocfilehash: 4697be519eee96778eecdf37f7b358a88ad886c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "96006899"
---
> [!NOTE]
> Požadavky a doporučení jsou založené na srovnávacích testech s jednou žádostí za sekundu pomocí 8 MB naskenovaného obchodního dopisu obsahujícího 29 řádků a celkem 803 znaků.

Následující tabulka popisuje minimální a doporučené přidělení prostředků pro každý kontejner pro čtení.

| Kontejner | Minimum | Doporučeno |
|-----------|---------|-------------|
| Přečíst 2,0 – Preview | 1 jádro, 8 GB paměti |  8 jader, 16 GB paměti |
| Přečíst 3,2 – Preview | 8 jader, 16 GB paměti | 8 jader, 24 GB paměti |

* Každé jádro musí mít aspoň 2,6 GHz nebo rychlejší.

Základní a paměť odpovídají `--cpus` `--memory` nastavení a, která se používají jako součást `docker run` příkazu.
