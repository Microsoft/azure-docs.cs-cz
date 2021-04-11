---
title: Customer-Managed klíče pro Cognitive Services
titleSuffix: Cognitive Services
description: Naučte se používat Azure Portal ke konfiguraci klíčů spravovaných zákazníkem pomocí Azure Key Vault. Klíče spravované zákazníkem umožňují vytvářet, otáčet, zakazovat a odvolávat řízení přístupu.
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 05/28/2020
ms.author: egeaney
ms.openlocfilehash: 88a723abc606a527232b7c1949f35c1fedfdba50
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "101706839"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-for-cognitive-services"></a>Konfigurace klíčů spravovaných zákazníkem pomocí Azure Key Vault pro Cognitive Services

Proces povolení Customer-Managed klíčů s Azure Key Vault pro Cognitive Services se liší podle produktu. Použijte tyto odkazy na pokyny pro konkrétní službu:

## <a name="vision"></a>Obraz

* [Custom Vision šifrování dat v klidovém umístění](../custom-vision-service/encrypt-data-at-rest.md)
* [Šifrování neaktivních dat ve službě faceation Services](../face/encrypt-data-at-rest.md)
* [Rozpoznávání vydaných dat v klidovém formátu](../form-recognizer/encrypt-data-at-rest.md)

## <a name="language"></a>Jazyk

* [Šifrování neaktivních dat Language Understanding služby](../LUIS/encrypt-data-at-rest.md)
* [QnA Maker šifrování dat v klidovém umístění](../QnAMaker/encrypt-data-at-rest.md)
* [Šifrování dat v klidovém umístění](../translator/encrypt-data-at-rest.md)

## <a name="decision"></a>Rozhodnutí

* [Content Moderator šifrování dat v klidovém umístění](../Content-Moderator/encrypt-data-at-rest.md)
* [Individuální Šifrování neaktivních dat](../personalizer/encrypt-data-at-rest.md)

## <a name="next-steps"></a>Další kroky

* [Co je Azure Key Vault](../../key-vault/general/overview.md)?
* [Formulář žádosti o klíč Customer-Managed Cognitive Services](https://aka.ms/cogsvc-cmk)