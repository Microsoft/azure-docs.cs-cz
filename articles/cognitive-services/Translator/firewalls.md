---
title: Překlad za firewally - Překladač Text API
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services Translator Text API můžete přeložit za firewally pomocí domény název nebo IP filtrování.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: cd7904fedd3ab3f64315cb6f98d99b8fd12254f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73837406"
---
# <a name="how-to-translate-behind-ip-firewalls-with-the-translator-text-api"></a>Jak překládat za IP firewally s Translator Text API

Překladač Text API lze přeložit za brány firewall pomocí domény název nebo IP filtrování. Upřednostňovanou metodou je filtrování názvů domén. **Nedoporučujeme spouštět** Microsoft Translator zpoza brány firewall filtrované IP adresou. Nastavení se pravděpodobně v budoucnu zlomí bez předchozího upozornění.

## <a name="translator-ip-addresses"></a>Překladač IP adres
21. srpna 2019 adresy IP adres api.cognitive.microsofttranslator.com – Microsoft Translator Text API:

* **Asie a Tichomoří:** 20.40.125.208, 20.43.88.240, 20.184.58.62, 40.90.139.163, 104.44.89.44
* **Evropa:** 40.90.138.4, 40.90.141.99, 51.105.170.64, 52.155.218.251
* **Severní Amerika:** 40.90.139.36, 40.90.139.2, 40.119.2.134, 52.224.200.129, 52.249.207.163


## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Překlad za IP firewally v volání Překladače ROZHRANÍ API](reference/v3-0-translate.md)
