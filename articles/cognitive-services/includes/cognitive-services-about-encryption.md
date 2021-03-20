---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.custom: include
ms.openlocfilehash: 90d5aae559a317ed509d04c8db3310ff8a5de026
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "89069901"
---
## <a name="about-cognitive-services-encryption"></a>O šifrování Cognitive Services

Data se šifrují a dešifrují s využitím [256 šifrování AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) kompatibilního se [standardem FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) . Šifrování a dešifrování je transparentní, což znamená, že se pro vás spravuje šifrování a přístup. Vaše data jsou zabezpečená ve výchozím nastavení, a abyste mohli využívat šifrování, nemusíte upravovat kód ani aplikace.

## <a name="about-encryption-key-management"></a>O správě šifrovacích klíčů

Vaše předplatné ve výchozím nastavení používá šifrovací klíče spravované Microsoftem. K dispozici je také možnost spravovat vaše předplatné s vlastními klíči nazvanými klíče spravované zákazníkem (CMK). CMK nabízí větší flexibilitu při vytváření, rotaci, zakázání a odvolávání řízení přístupu. Šifrovací klíče sloužící k ochraně vašich dat můžete také auditovat. Pokud je pro vaše předplatné nakonfigurované CMK, je k dispozici dvojité šifrování, které nabízí druhou vrstvu ochrany, a přitom vám umožní řídit šifrovací klíč pomocí Azure Key Vault.