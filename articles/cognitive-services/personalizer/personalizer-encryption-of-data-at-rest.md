---
title: Šifrování neaktivních dat služby přizpůsobování
titleSuffix: Azure Cognitive Services
description: Šifrování neaktivních dat služby přizpůsobení.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 63526454bb366b214c27bddce24ed9ebc09556b3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "80071086"
---
# <a name="personalizer-service-encryption-of-data-at-rest"></a>Šifrování neaktivních dat služby přizpůsobování

Služba přizpůsobené aplikace automaticky šifruje vaše data při trvalém uložení do cloudu. Šifrování služby přizpůsobeného službou chrání vaše data a usnadňuje splnění závazků týkajících se zabezpečení a dodržování předpisů vaší organizace.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Klíče spravované zákazníkem jsou dostupné jenom v cenové úrovni E0. Chcete-li požádat o možnost použití klíčů spravovaných zákazníkem, vyplňte a odešlete [formulář žádosti o klíč spravovaný zákazníkem služby pro přizpůsobení](https://aka.ms/cogsvc-cmk). Bude to trvat přibližně 3-5 pracovních dnů, než se vrátí na stav vaší žádosti. V závislosti na poptávce můžete být do fronty zařazené a schválené, protože místo bude k dispozici. Po schválení pro použití CMK se službou pro přizpůsobování budete muset vytvořit nový prostředek pro přizpůsobení a vybrat E0 jako cenovou úroveň. Jakmile se vytvoří prostředek pro individuální nastavení s cenovou úrovní E0, můžete k nastavení spravované identity použít Azure Key Vault.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="next-steps"></a>Další kroky

* [Formulář žádosti o klíč spravovaný zákazníkem služby pro přizpůsobování](https://aka.ms/cogsvc-cmk)
* [Další informace o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
