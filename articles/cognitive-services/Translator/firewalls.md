---
title: Překlad za firewally – Translator Text API
titlesuffix: Azure Cognitive Services
description: Převede uzel za branách firewall protokolu IP s rozhraním Translator Text API.
services: cognitive-services
author: v-pawal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: v-jansko
ms.openlocfilehash: ebc8b7ce4ed0242f1bc62e3b97b6594cfe810374
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "58915743"
---
# <a name="how-to-translate-behind-ip-firewalls-with-the-translator-text-api"></a>Způsob převodu za branách firewall protokolu IP s rozhraním Translator Text API

Translator Text API jsou dobře převeditelné za použití názvu domény nebo filtrování protokolu IP brány firewall. Filtrování název domény je upřednostňovanou metodou. Jsme **není vhodné řešit podobné** systémem Microsoft Translatoru za IP filtrované brány firewall. Instalační program je pravděpodobnost zhroucení v budoucnosti bez předchozího upozornění.

## <a name="translator-ip-addresses"></a>Translator IP Addresses
IP adresy pro api.cognitive.microsofttranslator.com – Microsoft Translator Text API od 20. listopadu 2018:

* **Asie a Tichomoří:** 40.90.139.163, 104.44.89.44
* **Evropa:** 40.90.138.4, 40.90.141.99
* **Severní Ameriku:** 40.90.139.36, 40.90.139.2


## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Překlad za branách firewall protokolu IP v volání rozhraní Translator API](reference/v3-0-translate.md)
