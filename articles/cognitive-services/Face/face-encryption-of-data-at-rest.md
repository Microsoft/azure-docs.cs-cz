---
title: Šifrování neaktivních dat ve službě Face
titleSuffix: Azure Cognitive Services
description: Microsoft nabízí šifrovací klíče spravované Microsoftem a umožňuje také spravovat Cognitive Services předplatná s vlastními klíči, které se nazývají Customer Customer Key (CMK). Tento článek obsahuje informace o šifrování v klidovém umístění pro tvář a o tom, jak povolit a spravovat CMK.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: 8c6f4f7db312355b719deb434bf6a46fa55eec9d
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2020
ms.locfileid: "92912782"
---
# <a name="face-service-encryption-of-data-at-rest"></a>Šifrování neaktivních dat ve službě Face

Služba obličeje automaticky šifruje vaše data při trvalém ukládání do cloudu. Šifrování služby Face chrání vaše data a usnadňuje splnění závazků týkajících se zabezpečení a dodržování předpisů vaší organizace.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Klíče spravované zákazníkem jsou dostupné jenom v cenové úrovni E0. Chcete-li požádat o možnost použití klíčů spravovaných zákazníkem, vyplňte a odešlete [formulář žádosti o klíč služby Customer-Managed](https://aka.ms/cogsvc-cmk). Bude to trvat přibližně 3-5 pracovních dnů, než se vrátí na stav vaší žádosti. V závislosti na poptávce můžete být do fronty zařazené a schválené, protože místo bude k dispozici. Po schválení pro používání CMK se službou obličeje budete muset vytvořit nový prostředek pro vytváření obličeje a vybrat E0 jako cenovou úroveň. Jakmile se vytvoří prostředek obličeje s cenovou úrovní E0, můžete k nastavení spravované identity použít Azure Key Vault.

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Další kroky

* Úplný seznam služeb, které podporují CMK, najdete v tématu [klíče spravované zákazníkem pro Cognitive Services](../encryption/cognitive-services-encryption-keys-portal.md)
* [Co je Azure Key Vault](../../key-vault/general/overview.md)?
* [Formulář žádosti o klíč Customer-Managed Cognitive Services](https://aka.ms/cogsvc-cmk)