---
title: Customer-Managed klíče pro Cognitive Services
titleSuffix: Cognitive Services
description: Naučte se používat Azure Portal ke konfiguraci klíčů spravovaných zákazníkem pomocí Azure Key Vault. Klíče spravované zákazníkem umožňují vytvářet, otáčet, zakazovat a odvolávat řízení přístupu.
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 04/07/2021
ms.author: egeaney
ms.openlocfilehash: 7c7476a3ab885e9c127cbd571ad723864bf0d898
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534559"
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

## <a name="speech"></a>Řeč

* [Šifrování dat v klidovém kódování](../speech-service/speech-encryption-of-data-at-rest.md)

## <a name="decision"></a>Rozhodnutí

* [Content Moderator šifrování dat v klidovém umístění](../Content-Moderator/encrypt-data-at-rest.md)
* [Individuální Šifrování neaktivních dat](../personalizer/encrypt-data-at-rest.md)
* [Metriky Advisoru pro šifrování neaktivních dat](../metrics-advisor/encryption.md)

## <a name="next-steps"></a>Další kroky

* [Co je Azure Key Vault](../../key-vault/general/overview.md)?
* [Formulář žádosti o klíč Customer-Managed Cognitive Services](https://aka.ms/cogsvc-cmk)