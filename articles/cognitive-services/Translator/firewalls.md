---
title: Přeložit za branami firewall – Translator Text API
titleSuffix: Azure Cognitive Services
description: Přeložte za bránou firewall IP adres pomocí Translator Text API.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 3d5c775d24c89d126962b6c4bccb4d5a572801ac
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69906767"
---
# <a name="how-to-translate-behind-ip-firewalls-with-the-translator-text-api"></a>Postup při překladu za bránou firewall IP adres pomocí Translator Text API

Translator Text API může překládat za brány firewall pomocí filtrování názvů domén nebo IP adres. Filtrování názvů domén je upřednostňovaná metoda. Nedoporučujeme spouštět Microsoft Translator za bránou firewall filtrovanou pomocí protokolu IP. Instalační program se může v budoucnu poškodit bez předchozího upozornění.

## <a name="translator-ip-addresses"></a>IP adresy překladatele
IP adresy pro api.cognitive.microsofttranslator.com – Microsoft Translator Text API od 21. srpna 2019:

* **Asie a Tichomoří:** 20.40.125.208, 20.43.88.240, 20.184.58.62, 40.90.139.163, 104.44.89.44
* **Evropským** 40.90.138.4, 40.90.141.99, 51.105.170.64, 52.155.218.251
* **Severní Amerika:** 40.90.139.36, 40.90.139.2, 40.119.2.134, 52.224.200.129, 52.249.207.163


## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Překlad za IP adresami firewallů ve vašem volání překladače rozhraní API](reference/v3-0-translate.md)
