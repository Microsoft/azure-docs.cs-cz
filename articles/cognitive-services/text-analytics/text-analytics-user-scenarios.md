---
title: Příklady scénářů uživatelů pro rozhraní API pro analýzu textu
titleSuffix: Azure Cognitive Services
description: V tomto článku si můžete prohlédnout některé běžné scénáře integrace rozhraní API pro analýzu textu do služeb a procesů.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 11/29/2019
ms.author: aahi
ms.openlocfilehash: 027e6ec829e9de9956451e48e5f9e1cdd749f9f7
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2019
ms.locfileid: "74689330"
---
# <a name="example-user-scenarios-for-the-text-analytics-api"></a>Příklady scénářů uživatelů pro rozhraní API pro analýzu textu

Rozhraní API pro analýzu textu je cloudová služba, která poskytuje pokročilé zpracování přirozeného jazyka nad textem. Tento článek popisuje příklady případů použití pro integraci rozhraní API do obchodních řešení a procesů. 

## <a name="analyze-survey-results"></a>Analýza výsledků průzkumu

Vykreslete přehledy ze výsledků průzkumu zákazníků a zaměstnanců zpracováním nezpracovaných textových odpovědí pomocí Analýza mínění. Agreguje výsledky pro analýzy, sledování a řízení zapojení.

![Obrázek popisující, jak provádět analýzu mínění na průzkumech zákazníků a zaměstnanců.](media/use-cases/survey-results.svg)

## <a name="analyze-recorded-inbound-customer-calls"></a>Analyzovat zaznamenaná příchozí volání zákazníků

Pomocí Převod textu na řeč, Analýza mínění a Extrakce klíčových frází extrahujte přehledy z volání služeb zákazníkům. Zobrazit výsledky v Power BI řídicím panelu nebo na portálu, abyste lépe pochopili zákazníky, mohli zvýraznit trendy zákaznických služeb a řídit zapojení zákazníků. Odešlete požadavky rozhraní API jako dávku pro vytváření sestav nebo v reálném čase pro účely intervence. Podívejte se na vzorový kód [na GitHubu](https://github.com/rlagh2/callcenteranalytics).

![Obrázek popisující, jak automatizovat získávání přehledů z hovorů zákaznických služeb pomocí analýzy mínění](media/use-cases/azure-inbound.svg)

## <a name="process-and-categorize-support-incidents"></a>Zpracování a kategorizace incidentů podpory

Pomocí Extrakce klíčových frází a rozpoznávání entit můžete zpracovat žádosti o podporu odeslané v nestrukturovaném textovém formátu. Pomocí extrahovaných frází a entit můžete kategorizovat požadavky na plánování prostředků a analýzu trendů.

![Obrázek popisující, jak použít extrakci klíčových frází a rozpoznávání entit ke kategorizaci sestav a trendů incidentů](media/use-cases/support-incidents.svg)

## <a name="monitor-your-products-social-media-feeds"></a>Monitorování informačních kanálů sociálních médií v produktu

Sledujte svůj názor na produkt uživatele na Twitteru nebo na Facebook stránkách. Pomocí dat můžete analyzovat zákaznická mínění k novým produktům, extrahovat klíčové fráze týkající se funkcí a požadavků na funkce nebo řešit reklamace zákazníků při jejich vzniku. Podívejte se na příklad [šablony Microsoft Flow](https://flow.microsoft.com/galleries/public/templates/2680d2227d074c4d901e36c66e68f6f9/run-sentiment-analysis-on-tweets-and-push-results-to-a-power-bi-dataset/).

![Obrázek popisující, jak monitorovat váš produkt a zpětnou vazbu na sociálních médiích pomocí extrakce klíčových frází](media/use-cases/social-feed.svg)

## <a name="next-steps"></a>Další kroky

* [Co je rozhraní API pro analýzu textu?](overview.md)
* [Odeslat požadavek do rozhraní API pro analýzu textu pomocí klientské knihovny](quickstarts/text-analytics-sdk.md)
