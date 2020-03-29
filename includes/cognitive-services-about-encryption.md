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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372169"
---
## <a name="about-cognitive-services-encryption"></a>Šifrování služeb Cognitive Services

Data jsou šifrována a dešifrována pomocí [256bitového](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) šifrování AES kompatibilního se [standardem FIPS 140-2.](https://en.wikipedia.org/wiki/FIPS_140-2) Šifrování a dešifrování jsou transparentní, což znamená, že šifrování a přístup jsou spravovány za vás. Vaše data jsou ve výchozím nastavení zabezpečená a nemusíte upravovat kód nebo aplikace, abyste využili výhod šifrování.

## <a name="about-encryption-key-management"></a>O správě šifrovacích klíčů

Ve výchozím nastavení vaše předplatné používá šifrovací klíče spravované společností Microsoft. Pokud používáte cenovou úroveň, která podporuje klíče spravované zákazníkem, uvidíte nastavení šifrování pro váš prostředek v části **Šifrování** [na webu Azure Portal](https://portal.azure.com), jak je znázorněno na následujícím obrázku.

![Zobrazit nastavení šifrování](../articles/cognitive-services/media/cognitive-services-encryption/encryptionblade.png)

U předplatných, která podporují pouze šifrovací klíče spravované společností Microsoft, nebudete mít oddíl **Šifrování.**

## <a name="customer-managed-keys-with-azure-key-vault"></a>Klíče spravované zákazníkem s trezorem klíčů Azure

K dispozici je také možnost spravovat předplatné pomocí vlastních klíčů. Klíče spravované zákazníkem (CMK), označované také jako Přineste si vlastní klíč (BYOK), nabízejí větší flexibilitu při vytváření, otáčení, zakázání a odvolání ovládacích prvků přístupu. Můžete také auditovat šifrovací klíče používané k ochraně dat.
