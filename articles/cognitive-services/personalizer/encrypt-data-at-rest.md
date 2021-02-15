---
title: Šifrování neaktivních dat služby přizpůsobování
titleSuffix: Azure Cognitive Services
description: Microsoft nabízí šifrovací klíče spravované Microsoftem a umožňuje také spravovat Cognitive Services předplatná s vlastními klíči, které se nazývají Customer Customer Key (CMK). Tento článek obsahuje informace o šifrování dat v klidovém umístění pro přizpůsobení a o tom, jak povolit a spravovat CMK.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: 1a27199930587c1a096dd99462ebd0c9d65054ee
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2021
ms.locfileid: "100524405"
---
# <a name="personalizer-service-encryption-of-data-at-rest"></a>Šifrování neaktivních dat služby přizpůsobování

Služba přizpůsobené aplikace automaticky šifruje vaše data při trvalém uložení do cloudu. Šifrování služby přizpůsobeného službou chrání vaše data a usnadňuje splnění závazků týkajících se zabezpečení a dodržování předpisů vaší organizace.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Klíče spravované zákazníkem jsou dostupné jenom v cenové úrovni E0. Chcete-li požádat o možnost použití klíčů spravovaných zákazníkem, vyplňte a odešlete [formulář žádosti o klíč služby pro přizpůsobení Customer-Managed](https://aka.ms/cogsvc-cmk). Bude to trvat přibližně 3-5 pracovních dnů, než se vrátí na stav vaší žádosti. V závislosti na poptávce můžete být do fronty zařazené a schválené, protože místo bude k dispozici. Po schválení pro použití CMK se službou pro přizpůsobování budete muset vytvořit nový prostředek pro přizpůsobení a vybrat E0 jako cenovou úroveň. Jakmile se vytvoří prostředek pro individuální nastavení s cenovou úrovní E0, můžete k nastavení spravované identity použít Azure Key Vault.

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Další kroky

* [Formulář žádosti o klíč Customer-Managed služby přizpůsobení](https://aka.ms/cogsvc-cmk)
* [Další informace o Azure Key Vault](../../key-vault/general/overview.md)