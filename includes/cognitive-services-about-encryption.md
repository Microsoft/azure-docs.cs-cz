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
ms.openlocfilehash: 2a348827b1c992e0ef0a4592cc0b9c5c0fa0ca19
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "79372169"
---
## <a name="about-cognitive-services-encryption"></a>O šifrování Cognitive Services

Data se šifrují a dešifrují s využitím [256 šifrování AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) kompatibilního se [standardem FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) . Šifrování a dešifrování je transparentní, což znamená, že se pro vás spravuje šifrování a přístup. Vaše data jsou ve výchozím nastavení zabezpečená a nemusíte upravovat kód ani aplikace, abyste mohli využívat šifrování.

## <a name="about-encryption-key-management"></a>O správě šifrovacích klíčů

Ve výchozím nastavení používá vaše předplatné šifrovací klíče spravované Microsoftem. Pokud používáte cenovou úroveň, která podporuje klíče spravované zákazníkem, můžete zobrazit nastavení šifrování prostředku v části **šifrování** [Azure Portal](https://portal.azure.com), jak je znázorněno na následujícím obrázku.

![Zobrazit nastavení šifrování](../articles/cognitive-services/media/cognitive-services-encryption/encryptionblade.png)

U předplatných, která podporují jenom šifrovací klíče spravované Microsoftem, nebudete mít **šifrovací** oddíl.

## <a name="customer-managed-keys-with-azure-key-vault"></a>Klíče spravované zákazníkem s využitím Azure Key Vaultu

K dispozici je také možnost Spravovat předplatné s vlastními klíči. Klíče spravované zákazníkem (CMK), označované také jako Přineste si vlastní klíč (BYOK), nabízejí větší flexibilitu při vytváření, střídání, zakázání a odvolávání řízení přístupu. Můžete také auditovat šifrovací klíče používané k ochraně vašich dat.
