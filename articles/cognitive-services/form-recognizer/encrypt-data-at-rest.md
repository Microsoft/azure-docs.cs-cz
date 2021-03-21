---
title: Služba rozpoznávání formulářů zašifrování dat v klidovém formátu
titleSuffix: Azure Cognitive Services
description: Microsoft nabízí šifrovací klíče spravované Microsoftem a umožňuje také spravovat Cognitive Services předplatná s vlastními klíči, které se nazývají Customer Customer Key (CMK). Tento článek obsahuje informace o šifrování dat v klidovém formátu pro rozpoznávání formulářů a o tom, jak povolit a spravovat CMK.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: 7a8b331c1295ed19afa64e95318bfa14414e6d9f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100524406"
---
# <a name="form-recognizer-encryption-of-data-at-rest"></a>Rozpoznávání vydaných dat v klidovém formátu

Nástroj pro rozpoznávání formulářů Azure automaticky šifruje vaše data při jejich uchování do cloudu. Šifrování pomocí rozpoznávání formulářů chrání vaše data, aby bylo možné splnit závazky zabezpečení a dodržování předpisů vaší organizace.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Klíče spravované zákazníkem jsou dostupné jenom prostředky vytvořené po 11. května 2020. Pokud chcete používat CMK s nástrojem pro rozpoznávání formulářů, budete muset vytvořit nový prostředek pro rozpoznávání formulářů. Po vytvoření prostředku můžete k nastavení spravované identity použít Azure Key Vault.

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Další kroky

* [Formulář žádosti o klíč Customer-Managed pro rozpoznávání formulářů](https://aka.ms/cogsvc-cmk)
* [Další informace o Azure Key Vault](../../key-vault/general/overview.md)