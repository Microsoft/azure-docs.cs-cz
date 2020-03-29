---
title: Šifrování dat moderátorem obsahu v klidovém stavu
titleSuffix: Azure Cognitive Services
description: Šifrování dat moderátorem obsahu v klidovém stavu.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: b41d822791f61fce274f628b87c478c3a986f4c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372158"
---
# <a name="content-moderator-encryption-of-data-at-rest"></a>Šifrování dat moderátorem obsahu v klidovém stavu

Content Moderator automaticky šifruje vaše data, když jsou v cloudu trvalá, což pomáhá splnit cíle vaší organizace v oblasti zabezpečení a dodržování předpisů.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Klíče spravované zákazníkem jsou k dispozici pouze na cenové úrovni E0. Chcete-li požádat o možnost používat klíče spravované zákazníkem, vyplňte a odešlete [formulář žádosti o klíč spravovaný zákazníkem .](https://aka.ms/cogsvc-cmk) Bude trvat přibližně 3-5 pracovních dnů, než se dozvíte o stavu vaší žádosti. V závislosti na poptávce můžete být umístěni do fronty a schváleni, jakmile bude k dispozici místo. Po schválení pro použití CMK se službou Content Moderator budete muset vytvořit nový zdroj Moderátor obsahu a vybrat E0 jako cenovou úroveň. Po vytvoření prostředku Content Moderator s cenovou úrovní E0 můžete pomocí azure key vaultu nastavit spravovanou identitu.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="enable-data-encryption-for-your-content-moderator-team"></a>Povolení šifrování dat pro svůj tým moderátorů obsahu

Informace o povolení šifrování dat pro tým pro kontrolu moderátora obsahu naleznete v [úvodním panelu: Vyzkoušet moderátorobsahu na webu](quick-start.md#create-a-review-team).  

> [!NOTE]
> S cenovou úrovní Content Moderator E0 budete muset zadat _ID prostředku._


## <a name="next-steps"></a>Další kroky

* [Formulář žádosti o klíč správce obsahu pro zákazníka](https://aka.ms/cogsvc-cmk)
* [Další informace o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
