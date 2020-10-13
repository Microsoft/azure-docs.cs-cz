---
title: Příklady scénářů uživatelů pro rozhraní API pro analýzu textu
titleSuffix: Azure Cognitive Services
description: V tomto článku si můžete prohlédnout některé běžné scénáře integrace rozhraní API pro analýzu textu do služeb a procesů.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: aahi
ms.openlocfilehash: 4f540c7e853efe370c84b7c4d0a7d74911b8cd6c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91823946"
---
# <a name="example-user-scenarios-for-the-text-analytics-api"></a>Příklady scénářů uživatelů pro rozhraní API pro analýzu textu

Rozhraní API pro analýzu textu je cloudová služba, která poskytuje pokročilé zpracování přirozeného jazyka nad textem. Tento článek popisuje příklady případů použití pro integraci rozhraní API do obchodních řešení a procesů. 

## <a name="analyze-survey-results"></a>Analýza výsledků průzkumu

Vykreslete přehledy ze výsledků průzkumu zákazníků a zaměstnanců zpracováním nezpracovaných textových odpovědí pomocí Analýza mínění. Agreguje výsledky pro analýzy, sledování a řízení zapojení.

![Obrázek popisující, jak provádět analýzu mínění na průzkumech zákazníků a zaměstnanců.](media/use-cases/survey-results.svg)

## <a name="analyze-recorded-inbound-customer-calls"></a>Analyzovat zaznamenaná příchozí volání zákazníků

Vyextrahujte přehledy z volání služeb zákazníkům pomocí převodu řeči na text, Analýza mínění a Extrakce klíčových frází. Zobrazit výsledky v Power BI řídicím panelu nebo na portálu, abyste lépe pochopili zákazníky, mohli zvýraznit trendy zákaznických služeb a řídit zapojení zákazníků. Odešlete požadavky rozhraní API jako dávku pro vytváření sestav nebo v reálném čase pro účely intervence. Podívejte se na vzorový kód [na GitHubu](https://github.com/rlagh2/callcenteranalytics).

![Obrázek popisující, jak automatizovat získávání přehledů z hovorů zákaznických služeb pomocí analýzy mínění](media/use-cases/azure-inbound.svg)

## <a name="process-and-categorize-support-incidents"></a>Zpracování a kategorizace incidentů podpory

Pomocí Extrakce klíčových frází a rozpoznávání entit můžete zpracovat žádosti o podporu odeslané v nestrukturovaném textovém formátu. Pomocí extrahovaných frází a entit můžete kategorizovat požadavky na plánování prostředků a analýzu trendů.

![Obrázek popisující, jak použít extrakci klíčových frází a rozpoznávání entit ke kategorizaci sestav a trendů incidentů](media/use-cases/support-incidents.svg)

## <a name="monitor-your-products-social-media-feeds"></a>Monitorování informačních kanálů sociálních médií v produktu

Sledujte svůj názor na produkt uživatele na Twitteru nebo na Facebook stránkách. Pomocí dat můžete analyzovat zákaznická mínění k novým produktům, extrahovat klíčové fráze týkající se funkcí a požadavků na funkce nebo řešit reklamace zákazníků při jejich vzniku. Podívejte se na příklad [šablony Microsoft Power automatizace](https://flow.microsoft.com/galleries/public/templates/2680d2227d074c4d901e36c66e68f6f9/run-sentiment-analysis-on-tweets-and-push-results-to-a-power-bi-dataset/).

![Obrázek popisující, jak monitorovat váš produkt a zpětnou vazbu na sociálních médiích pomocí extrakce klíčových frází](media/use-cases/social-feed.svg)

## <a name="classify-and-redact-documents-that-have-sensitive-information"></a>Klasifikace a redigování dokumentů, které obsahují citlivé informace

Použijte rozpoznávání pojmenovaných entit k identifikaci osobních a citlivých informací v dokumentech. Použijte data ke klasifikaci dokumentů nebo redigování, aby je bylo možné bezpečně sdílet.

![Obrázek popisující, jak používat NER k detekci osobních údajů a klasifikaci a redigování dokumentů](media/use-cases/sensitive-docs.jpg)

## <a name="perform-opinion-mining"></a>Vykonání názoru

Seskupení stanovisek souvisejících s konkrétními aspekty produktu nebo služby v prohlídekch, zpětné vazby od zákazníků nebo pokaždé, když text obsahuje stanovisko k aspektu Použijte ho k tomu, abyste vám pomohli při spuštění a vylepšení produktu, na marketingovém úsilí nebo na tom, jak váš produkt nebo služba provádí. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="media/use-cases/aspect-based-sentiment.png" alt-text="Příklad stanovisek k hotelu.":::

## <a name="next-steps"></a>Další kroky

* [Co je rozhraní API pro analýzu textu?](overview.md)
* [Odeslat požadavek do rozhraní API pro analýzu textu pomocí klientské knihovny](quickstarts/text-analytics-sdk.md)
