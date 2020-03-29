---
title: Face service šifrování dat v klidovém stavu
titleSuffix: Azure Cognitive Services
description: Face service šifrování dat v klidovém stavu.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 793c21bb2341033aa51ff8c639846e57ada4bae3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372213"
---
# <a name="face-service-encryption-of-data-at-rest"></a>Face service šifrování dat v klidovém stavu

Služba Face automaticky šifruje vaše data, když je vytrvale přetrvá do cloudu. Šifrování služby Face chrání vaše data a pomáhá vám plnit závazky organizace v oblasti zabezpečení a dodržování předpisů.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Klíče spravované zákazníkem jsou k dispozici pouze na cenové úrovni E0. Chcete-li požádat o možnost používat klíče spravované zákazníkem, vyplňte a odešlete [formulář žádosti o klíč spravovaný službou Face Service](https://aka.ms/cogsvc-cmk). Bude trvat přibližně 3-5 pracovních dnů, než se dozvíte o stavu vaší žádosti. V závislosti na poptávce můžete být umístěni do fronty a schváleni, jakmile bude k dispozici místo. Po schválení pro použití CMK se službou Face budete muset vytvořit nový prostředek pro obličej a vybrat E0 jako cenovou úroveň. Po vytvoření prostředku Face s cenovou úrovní E0 můžete k nastavení spravované identity použít Azure Key Vault.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="next-steps"></a>Další kroky

* [Formulář žádosti o klíč spravované zákazníkem face service](https://aka.ms/cogsvc-cmk)
* [Další informace o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


