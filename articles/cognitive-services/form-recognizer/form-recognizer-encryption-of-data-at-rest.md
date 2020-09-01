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
ms.openlocfilehash: 86b15b0059c2e3466ef65daeb53780798b3882d0
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2020
ms.locfileid: "89079247"
---
# <a name="form-recognizer-encryption-of-data-at-rest"></a>Rozpoznávání vydaných dat v klidovém formátu

Nástroj pro rozpoznávání formulářů Azure automaticky šifruje vaše data při trvalém uložení do cloudu. Šifrování ve formě rozpoznávání formulářů chrání vaše data a umožňuje splnit závazky zabezpečení a dodržování předpisů v organizaci.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Klíče spravované zákazníkem jsou dostupné jenom prostředky vytvořené po 11. května 2020. Pokud chcete používat CMK s nástrojem pro rozpoznávání formulářů, budete muset vytvořit nový prostředek pro rozpoznávání formulářů. Po vytvoření prostředku můžete k nastavení spravované identity použít Azure Key Vault.

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Další kroky

* [Formulář žádosti o klíč spravovaný zákazníkem formuláře](https://aka.ms/cogsvc-cmk)
* [Další informace o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)