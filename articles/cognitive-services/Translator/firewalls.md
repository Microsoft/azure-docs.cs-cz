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
ms.openlocfilehash: 567e869ab9ccb2f29cd0e88ba2e44d7d1b4a296c
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595271"
---
# <a name="how-to-translate-behind-ip-firewalls-with-the-translator-text-api"></a>Postup při překladu za bránou firewall IP adres pomocí Translator Text API

Translator Text API může překládat za brány firewall pomocí filtrování názvů domén nebo IP adres. Filtrování názvů domén je upřednostňovaná metoda. Nedoporučujeme spouštět Microsoft Translator za bránou firewall filtrovanou pomocí protokolu IP. Instalační program se může v budoucnu poškodit bez předchozího upozornění.

## <a name="translator-ip-addresses"></a>IP adresy překladatele
IP adresy pro api.cognitive.microsofttranslator.com – Microsoft Translator Text API od 20. listopadu 2018:

* **Asie a Tichomoří:** 40.90.139.163, 104.44.89.44
* **Evropským** 40.90.138.4, 40.90.141.99
* **Severní Amerika:** 40.90.139.36, 40.90.139.2


## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Překlad za IP adresami firewallů ve vašem volání překladače rozhraní API](reference/v3-0-translate.md)
