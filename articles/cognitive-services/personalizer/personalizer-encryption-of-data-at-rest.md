---
title: Personalizace služby šifrování dat v klidovém stavu
titleSuffix: Azure Cognitive Services
description: Personalizace služby šifrování dat v klidovém stavu.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 63526454bb366b214c27bddce24ed9ebc09556b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80071086"
---
# <a name="personalizer-service-encryption-of-data-at-rest"></a>Personalizace služby šifrování dat v klidovém stavu

Služba Personalizátor automaticky šifruje vaše data, když je vytrvale přetrvá do cloudu. Šifrování služby Personalizer chrání vaše data a pomáhá vám plnit závazky v oblasti zabezpečení a dodržování předpisů vaší organizace.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Klíče spravované zákazníkem jsou k dispozici pouze na cenové úrovni E0. Chcete-li požádat o možnost používat klíče spravované zákazníkem, vyplňte a odešlete [formulář žádosti o klíč spravované zákazníkem.](https://aka.ms/cogsvc-cmk) Bude trvat přibližně 3-5 pracovních dnů, než se dozvíte o stavu vaší žádosti. V závislosti na poptávce můžete být umístěni do fronty a schváleni, jakmile bude k dispozici místo. Po schválení pro použití CMK se službou Personalizák a údržbu budete muset vytvořit nový prostředek personalikátu a vybrat E0 jako cenovou úroveň. Po vytvoření prostředku personalizátoru s cenovou úrovní E0 můžete k nastavení spravované identity použít Azure Key Vault.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="next-steps"></a>Další kroky

* [Formulář žádosti o klíč spravované zákazníkem](https://aka.ms/cogsvc-cmk)
* [Další informace o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
