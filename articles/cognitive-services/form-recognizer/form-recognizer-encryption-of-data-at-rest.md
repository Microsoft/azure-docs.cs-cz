---
title: Služba rozpoznávání formulářů zašifrování dat v klidovém formátu
titleSuffix: Azure Cognitive Services
description: Rozpoznávání formulářů v klidovém kódování.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: egeaney
ms.openlocfilehash: 782e03f572f007b8523c3ae797dd626c383a01f4
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310712"
---
# <a name="form-recognizer-encryption-of-data-at-rest"></a>Rozpoznávání vydaných dat v klidovém formátu

Nástroj pro rozpoznávání formulářů Azure automaticky šifruje vaše data při trvalém uložení do cloudu. Šifrování ve formě rozpoznávání formulářů chrání vaše data a umožňuje splnit závazky zabezpečení a dodržování předpisů v organizaci.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Klíče spravované zákazníkem jsou dostupné jenom prostředky vytvořené po 11. května 2020. Pokud chcete používat CMK s nástrojem pro rozpoznávání formulářů, budete muset vytvořit nový prostředek pro rozpoznávání formulářů. Po vytvoření prostředku můžete k nastavení spravované identity použít Azure Key Vault.

[!INCLUDE [cognitive-services-cmk](../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Další kroky

* [Formulář žádosti o klíč spravovaný zákazníkem formuláře](https://aka.ms/cogsvc-cmk)
* [Další informace o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


