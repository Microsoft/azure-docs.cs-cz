---
title: Požadavků na kontejner a doporučení
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/18/2019
ms.author: dapine
ms.openlocfilehash: 47c889a428ec2916dc8cad73e30ef5ff630372a7
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2019
ms.locfileid: "71129807"
---
> [!NOTE]
> Požadavky a doporučení jsou založené na srovnávacích testech s jednou žádostí za sekundu pomocí 8 MB naskenovaného obchodního dopisu obsahujícího 29 řádků a celkem 803 znaků.

#### <a name="readtabread"></a>[Čtení](#tab/read)

Následující tabulka popisuje minimální a doporučené přidělení prostředků pro každý kontejner pro čtení.

| Kontejner | Minimální | Doporučené |TPS<br>(Minimum, maximum)|
|-----------|---------|-------------|--|
| Čtení | 1 jádra, 8 GB paměti, 0,24 TPS | 8 jader, 16 GB paměti, 1,17 TPS | 0,24, 1,17 |

#### <a name="recognize-texttabrecognize-text"></a>[Rozpoznání textu](#tab/recognize-text)

Následující tabulka popisuje minimální a doporučené přidělení prostředků pro každý kontejner Rozpoznávání textu.

| Kontejner | Minimální | Doporučené |TPS<br>(Minimum, maximum)|
|-----------|---------|-------------|--|
| Rozpoznávání textu | 1 jádro, 8 GB paměti, 0,12 TPS | 8 jader, 16 GB paměti, 0,60 TPS | 0,12, 0,60 |

***

* Každé jádro musí mít aspoň 2,6 GHz nebo rychlejší.
* TPS-transakcí za sekundu.

Základní a paměť odpovídají `--cpus` nastavení a `--memory` , která se `docker run` používají jako součást příkazu.
