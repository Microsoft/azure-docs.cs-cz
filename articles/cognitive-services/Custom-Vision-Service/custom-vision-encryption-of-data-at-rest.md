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
ms.openlocfilehash: 85cf251db69d33f02e928eaea6a848f20ae7d923
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310287"
---
# <a name="custom-vision-encryption-of-data-at-rest"></a>Custom Vision šifrování dat v klidovém umístění

Azure Custom Vision automaticky šifruje vaše data při trvalém ukládání do cloudu. Custom Vision šifrování chrání vaše data a umožňuje vám plnit závazky zabezpečení vaší organizace a dodržování předpisů.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Klíče spravované zákazníkem jsou dostupné jenom prostředky vytvořené po 11. května 2020. Pokud chcete používat CMK s Custom Vision, budete muset vytvořit nový prostředek Custom Vision. Po vytvoření prostředku můžete k nastavení spravované identity použít Azure Key Vault.

## <a name="regional-availability"></a>Regionální dostupnost

Klíče spravované zákazníkem jsou v tuto chvíli dostupné v těchto oblastech:

* USA (střed) – jih
* USA – západ 2
* USA – východ
* USA (Gov) – Virginia

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Další kroky

* Úplný seznam služeb, které podporují CMK, najdete v tématu [klíče spravované zákazníkem pro Cognitive Services](../encryption/cognitive-services-encryption-keys-portal.md)
* [Co je Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
* [Formulář žádosti o klíč spravovaný zákazníkem Cognitive Services](https://aka.ms/cogsvc-cmk)
