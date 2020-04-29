---
title: Šifrování neaktivních dat ve službě Face
titleSuffix: Azure Cognitive Services
description: Služba čelí Šifrování neaktivních dat.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 793c21bb2341033aa51ff8c639846e57ada4bae3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "79372213"
---
# <a name="face-service-encryption-of-data-at-rest"></a>Šifrování neaktivních dat ve službě Face

Služba obličeje automaticky šifruje vaše data při trvalém ukládání do cloudu. Šifrování služby Face chrání vaše data a usnadňuje splnění závazků týkajících se zabezpečení a dodržování předpisů vaší organizace.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Klíče spravované zákazníkem jsou dostupné jenom v cenové úrovni E0. Chcete-li požádat o možnost použití klíčů spravovaných zákazníkem, vyplňte a odešlete [formulář žádosti o klíč spravovaný zákazníkem služby Face](https://aka.ms/cogsvc-cmk). Bude to trvat přibližně 3-5 pracovních dnů, než se vrátí na stav vaší žádosti. V závislosti na poptávce můžete být do fronty zařazené a schválené, protože místo bude k dispozici. Po schválení pro používání CMK se službou obličeje budete muset vytvořit nový prostředek pro vytváření obličeje a vybrat E0 jako cenovou úroveň. Jakmile se vytvoří prostředek obličeje s cenovou úrovní E0, můžete k nastavení spravované identity použít Azure Key Vault.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="next-steps"></a>Další kroky

* [Formulář žádosti o klíč spravovaného zákazníka na službu FACET](https://aka.ms/cogsvc-cmk)
* [Další informace o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


