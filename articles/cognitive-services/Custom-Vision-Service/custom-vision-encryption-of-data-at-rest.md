---
title: Custom Vision šifrování dat v klidovém umístění
titleSuffix: Azure Cognitive Services
description: Custom Vision šifrování dat v klidovém umístění.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: egeaney
ms.openlocfilehash: 287344b325237843a549973ae61f569eae6dac93
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2020
ms.locfileid: "83202268"
---
# <a name="custom-vision-encryption-of-data-at-rest"></a>Custom Vision šifrování dat v klidovém umístění

Azure Custom Vision automaticky šifruje vaše data při trvalém ukládání do cloudu. Custom Vision šifrování chrání vaše data a umožňuje vám plnit závazky zabezpečení vaší organizace a dodržování předpisů.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Klíče spravované zákazníkem jsou dostupné jenom prostředky vytvořené po 11. května 2020. Pokud chcete používat CMK s Custom Vision, budete muset vytvořit nový prostředek Custom Vision. Po vytvoření prostředku můžete k nastavení spravované identity použít Azure Key Vault.

### <a name="regional-availability"></a>Regionální dostupnost

Klíče spravované zákazníkem jsou v tuto chvíli dostupné v těchto oblastech:

* USA (střed) – jih
* USA – západ 2
* USA – východ
* USA (Gov) – Virginia

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="next-steps"></a>Další kroky

* [Formulář žádosti o klíč spravovaný zákazníkem Custom Vision](https://aka.ms/cogsvc-cmk)
* [Další informace o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


