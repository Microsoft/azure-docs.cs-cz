---
title: Přeložit za branami firewall – Překladatel
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services Translator dokáže překládat za brány firewall pomocí filtrování názvů domén nebo IP adres.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: dce41879f77d0bed44daa89c1dabbcc3f92e145e
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592521"
---
# <a name="how-to-translate-behind-ip-firewalls-with-translator"></a>Postup překladu za IP adresami firewallu pomocí překladatele

Překladatel může překládat za brány firewall pomocí filtrování podle názvu domény nebo IP adresy. Filtrování názvů domén je upřednostňovaná metoda. **Nedoporučujeme** spouštět Microsoft Translator za bránou firewall filtrovanou pomocí protokolu IP. Instalační program se může v budoucnu poškodit bez předchozího upozornění.

## <a name="translator-ip-addresses"></a>IP adresy překladatele
IP adresy pro api.cognitive.microsofttranslator.com-Translator od 21. srpna 2019:

* **Asie a Tichomoří:** 20.40.125.208, 20.43.88.240, 20.184.58.62, 40.90.139.163, 104.44.89.44
* **Evropa:** 40.90.138.4, 40.90.141.99, 51.105.170.64, 52.155.218.251
* **Severní Amerika:** 40.90.139.36, 40.90.139.2, 40.119.2.134, 52.224.200.129, 52.249.207.163

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Překlad za IP adresami firewallu v translatoru](reference/v3-0-translate.md)
