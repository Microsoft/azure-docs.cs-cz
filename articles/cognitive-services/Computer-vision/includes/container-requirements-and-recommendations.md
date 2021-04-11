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
ms.openlocfilehash: 2a399b683dc9596d3514ce7d3be1fa2444449e2a
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284930"
---
> [!NOTE]
> Požadavky a doporučení jsou založené na srovnávacích testech s jednou žádostí za sekundu pomocí 8 MB naskenovaného obchodního dopisu obsahujícího 29 řádků a celkem 803 znaků.

Následující tabulka popisuje minimální a doporučené přidělení prostředků pro každý kontejner OCR pro čtení.

| Kontejner | Minimum | Doporučeno |
|-----------|---------|-------------|
| Přečíst 2,0 – Preview | 1 jádro, 8 GB paměti |    8 jader, 16 GB paměti |
| Přečíst 3,2 – Preview | 8 jader, 16 GB paměti | 8 jader, 24 GB paměti |

* Každé jádro musí mít aspoň 2,6 GHz nebo rychlejší.

Základní a paměť odpovídají `--cpus` `--memory` nastavení a, která se používají jako součást `docker run` příkazu.
