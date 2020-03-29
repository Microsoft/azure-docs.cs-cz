---
title: Příklad uživatelských scénářů pro rozhraní API pro analýzu textu
titleSuffix: Azure Cognitive Services
description: V tomto článku můžete zobrazit některé běžné scénáře pro integraci rozhraní API pro analýzu textu do vašich služeb a procesů.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: aahi
ms.openlocfilehash: 6847059de2a8685a56719f07a041a40456f2aa06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219244"
---
# <a name="example-user-scenarios-for-the-text-analytics-api"></a>Příklad uživatelských scénářů pro rozhraní API pro analýzu textu

Rozhraní API pro analýzu textu je cloudová služba, která poskytuje pokročilé zpracování přirozeného jazyka přes text. Tento článek popisuje některé příklady případů použití pro integraci rozhraní API do obchodních řešení a procesů. 

## <a name="analyze-survey-results"></a>Analyzovat výsledky průzkumu

Nakreslete přehledy z výsledků průzkumu zákazníků a zaměstnanců zpracováním nezpracovaných textových odpovědí pomocí analýzy mínění. Agregujte zjištění pro analýzu, sledování a řízení.

![Obrázek popisující, jak provádět analýzu mínění v průzkumech zákazníků a zaměstnanců.](media/use-cases/survey-results.svg)

## <a name="analyze-recorded-inbound-customer-calls"></a>Analýza zaznamenaných příchozích hovorů zákazníků

Extrahujte přehledy z volání služeb zákazníkům pomocí převodu textu na řeč, analýzy mínění a extrakce klíčových frází. Zobrazte výsledky na řídicím panelu Power BI nebo na portálu, abyste lépe porozuměli zákazníkům, zvýraznili trendy služeb zákazníkům a podnítite zapojení zákazníků. Odesílat požadavky rozhraní API jako dávku pro vytváření sestav nebo v reálném čase pro zásah. Podívejte se na ukázkový kód [na GitHubu](https://github.com/rlagh2/callcenteranalytics).

![Obrázek popisující, jak automatizovat získávání přehledů z volání služeb zákazníkům pomocí analýzy mínění](media/use-cases/azure-inbound.svg)

## <a name="process-and-categorize-support-incidents"></a>Zpracování a kategorizace incidentů podpory

Ke zpracování žádostí o podporu odeslaných v nestrukturovaném textovém formátu použijte extrakci klíčových frází a rozpoznávání entit. Extrahované fráze a entity slouží ke kategorizaci požadavků na plánování zdrojů a analýzu trendů.

![Obrázek popisující použití extrakce klíčových frází a rozpoznávání entit ke kategorizaci zpráv o incidentech a trendů](media/use-cases/support-incidents.svg)

## <a name="monitor-your-products-social-media-feeds"></a>Sledování zdrojů sociálních médií vašeho produktu

Sledujte zpětnou vazbu od uživatelů na stránce vašeho produktu na Twitteru nebo Facebooku. Pomocí těchto dat můžete analyzovat mínění zákazníků směrem ke spuštění nových produktů, extrahovat klíčové fráze o funkcích a požadavcích na funkce nebo řešit stížnosti zákazníků tak, jak k nim dojde. Podívejte se na příklad [šablony Microsoft Flow](https://flow.microsoft.com/galleries/public/templates/2680d2227d074c4d901e36c66e68f6f9/run-sentiment-analysis-on-tweets-and-push-results-to-a-power-bi-dataset/).

![Obrázek popisující, jak sledovat zpětnou vazbu vašeho produktu a společnosti na sociálních médiích pomocí extrakce klíčových frází](media/use-cases/social-feed.svg)

## <a name="classify-and-redact-documents-that-have-sensitive-information"></a>Klasifikovat a redigovat dokumenty s citlivými informacemi

Pomocí funkce Rozpoznávání pojmenovaných entit můžete v dokumentech identifikovat osobní a citlivé informace. Data slouží ke klasifikaci dokumentů nebo jejich redigování, aby je bylo možné bezpečně sdílet.

![Obrázek popisující použití funkce NER ke zjišťování osobních údajů a klasifikaci a redigování dokumentů](media/use-cases/sensitive-docs.jpg)

## <a name="next-steps"></a>Další kroky

* [Co je rozhraní API pro analýzu textu?](overview.md)
* [Odeslání požadavku do rozhraní API pro analýzu textu pomocí klientské knihovny](quickstarts/text-analytics-sdk.md)
