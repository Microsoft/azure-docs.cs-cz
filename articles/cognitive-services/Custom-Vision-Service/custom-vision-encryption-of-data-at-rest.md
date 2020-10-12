---
title: Custom Vision šifrování dat v klidovém umístění
titleSuffix: Azure Cognitive Services
description: Microsoft nabízí šifrovací klíče spravované Microsoftem a umožňuje také spravovat Cognitive Services předplatná s vlastními klíči, které se nazývají Customer Customer Key (CMK). Tento článek obsahuje informace o šifrování dat v klidovém umístění pro Custom Vision a o tom, jak povolit a spravovat CMK.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: 39257419f179bdce8c94f2ddb3a7cd8f5ac2d34f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89077751"
---
# <a name="custom-vision-encryption-of-data-at-rest"></a>Custom Vision šifrování dat v klidovém umístění

Azure Custom Vision automaticky šifruje vaše data při trvalém ukládání do cloudu. Custom Vision šifrování chrání vaše data a umožňuje vám plnit závazky zabezpečení vaší organizace a dodržování předpisů.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Klíče spravované zákazníkem jsou dostupné jenom prostředky vytvořené po 11. května 2020. Pokud chcete používat CMK s Custom Vision, budete muset vytvořit nový prostředek Custom Vision. Po vytvoření prostředku můžete k nastavení spravované identity použít Azure Key Vault.

## <a name="regional-availability"></a>Regionální dostupnost

Klíče spravované zákazníkem jsou v tuto chvíli dostupné v těchto oblastech:

* USA (střed) – jih
* Západní USA 2
* East US
* USA (Gov) – Virginia

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Další kroky

* Úplný seznam služeb, které podporují CMK, najdete v tématu [klíče spravované zákazníkem pro Cognitive Services](../encryption/cognitive-services-encryption-keys-portal.md)
* [Co je Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
* [Formulář žádosti o klíč Customer-Managed Cognitive Services](https://aka.ms/cogsvc-cmk)
