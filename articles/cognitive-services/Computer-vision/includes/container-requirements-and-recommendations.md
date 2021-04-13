---
title: Požadavky na kontejner a doporučení
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/09/2021
ms.author: aahi
ms.openlocfilehash: 5cbf07ab97206ae3509701a6d6b0f71e961b0bf8
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308763"
---
> [!NOTE]
> Požadavky a doporučení jsou založené na srovnávacích testech s jednou žádostí za sekundu pomocí 8 MB naskenovaného obchodního dopisu obsahujícího 29 řádků a celkem 803 znaků.

Následující tabulka popisuje minimální a doporučené přidělení prostředků pro každý kontejner OCR pro čtení.

| Kontejner | Minimum | Doporučeno |
|-----------|---------|-------------|
| Přečíst 2,0 – Preview | 1 jádro, 8 GB paměti |    8 jader, 16 GB paměti |
| Přečíst 3,2 | 8 jader, 16 GB paměti | 8 jader, 24 GB paměti |

* Každé jádro musí mít aspoň 2,6 GHz nebo rychlejší.

Základní a paměť odpovídají `--cpus` `--memory` nastavení a, která se používají jako součást `docker run` příkazu.
