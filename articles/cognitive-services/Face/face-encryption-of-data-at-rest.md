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
ms.openlocfilehash: 33495dd5b092cb51b3421e7204f3b529077d63b3
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/03/2020
ms.locfileid: "84309029"
---
# <a name="face-service-encryption-of-data-at-rest"></a>Šifrování neaktivních dat ve službě Face

Služba obličeje automaticky šifruje vaše data při trvalém ukládání do cloudu. Šifrování služby Face chrání vaše data a usnadňuje splnění závazků týkajících se zabezpečení a dodržování předpisů vaší organizace.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Klíče spravované zákazníkem jsou dostupné jenom v cenové úrovni E0. Chcete-li požádat o možnost použití klíčů spravovaných zákazníkem, vyplňte a odešlete [formulář žádosti o klíč spravovaný zákazníkem služby Face](https://aka.ms/cogsvc-cmk). Bude to trvat přibližně 3-5 pracovních dnů, než se vrátí na stav vaší žádosti. V závislosti na poptávce můžete být do fronty zařazené a schválené, protože místo bude k dispozici. Po schválení pro používání CMK se službou obličeje budete muset vytvořit nový prostředek pro vytváření obličeje a vybrat E0 jako cenovou úroveň. Jakmile se vytvoří prostředek obličeje s cenovou úrovní E0, můžete k nastavení spravované identity použít Azure Key Vault.

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
