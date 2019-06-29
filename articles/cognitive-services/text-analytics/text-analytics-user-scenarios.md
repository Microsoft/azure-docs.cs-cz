---
title: Ukázkové uživatelské scénáře pro rozhraní Text Analytics API
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto článku uvidíte několik běžných scénářů integrace rozhraní API pro analýzu textu služby a procesy.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 04/04/2019
ms.author: aahi
ms.openlocfilehash: d8d2aed16b5af60c44501e6d72332783a164ff91
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478392"
---
# <a name="example-user-scenarios-for-the-text-analytics-api"></a>Ukázkové uživatelské scénáře pro rozhraní Text Analytics API

Rozhraní Text Analytics API je Cloudová služba, která poskytuje pokročilé přirozeného jazyka zpracování text. Tento článek popisuje některé příklady případů použití pro rozhraní API pro integraci do obchodních řešení a procesů. 

## <a name="analyze-survey-results"></a>Analyzujte výsledky průzkumu

Získejte podrobné přehledy z výsledků zjišťování zákazníků a zaměstnanců zpracováním nezpracovaný text odpovědi pomocí analýzy mínění. Agregovat výsledky pro analýzu, zpracovat a řízení závazky.

![Obrázek popisující, jak provádět analýzu subjektivního hodnocení na zjišťování zákazníků a zaměstnanců.](media/use-cases/survey-results.svg)

## <a name="analyze-recorded-inbound-customer-calls"></a>Analýza hovorů se zákazníky zaznamenané příchozí

Extrakce poznatků z volání služby zákazníka pomocí převod textu na řeč, analýzu subjektivního hodnocení a extrakci frází klíč. Zobrazení výsledků v řídicím panelu Power BI nebo portálu k lepšímu pochopení zákazníků, zvýrazněte zákazníků služby trendy a zapojení zákazníků. Odeslání žádosti rozhraní API v dávce pro vytváření sestav, nebo v v reálném čase zásahu. Zobrazit [ukázkový kód](https://github.com/rlagh2/callcenteranalytics).

![Volání image popisující, jak automatizovat získávání informací ze služby zákazníkům pomocí analýzy mínění](media/use-cases/azure-inbound.svg)

## <a name="process-and-categorize-support-incidents"></a>Zpracování a kategorizaci incidenty podpory

Použití ke zpracování žádosti o podporu v nestrukturovaném textový formát extrakce frází klíč a rozpoznávání entit. Použijte extrahované fráze a entity ke kategorizaci požadavky pro prostředek plánování a vytváření analýz trendu.

![Obrázek popisující, jak použít ke kategorizaci ukládání neplánovaných sestav a trendů klíčových frází rozpoznávání extrakce a entity](media/use-cases/support-incidents.svg)

## <a name="monitor-your-products-social-media-feeds"></a>Sledování kanálů ze sociálních médií váš produkt

Monitorování zpětné vazby uživatelů produktu na váš produkt twitter nebo Facebookovou stránku. Data můžete analyzovat mínění zákazníků směrem k uvádění nových produktů, extrahovat klíčové fráze o funkcích a žádosti o funkce nebo adresa zákazníka stížností při jejich provádění.

![Obrázek popisující, jak monitorovat vaše produktu a společnosti zpětné vazby na sociálních médií pomocí extrakce klíčových frází](media/use-cases/social-feed.svg)

## <a name="next-steps"></a>Další postup

* [Co je API pro analýzu textu?](overview.md)
* [Odeslat požadavek na používání rozhraní Text Analytics APIC#](quickstarts/csharp.md)
