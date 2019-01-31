---
title: Přehled rozhraní Web Language Model API
titleSuffix: Azure Cognitive Services
description: Rozhraní API Web Language Model v Microsoft Cognitive Services poskytuje moderní nástroje pro zpracování přirozeného jazyka.
services: cognitive-services
author: piyushbehre
manager: cgronlun
ms.service: cognitive-services
ms.subservice: web-language-model
ms.topic: overview
ms.date: 08/12/2016
ms.author: pibehre
ROBOTS: NOINDEX
ms.openlocfilehash: a4dfe5bb9651e3c5ff9706de829389a1b9df1305
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55464921"
---
# <a name="what-is-the-web-language-model-api-preview"></a>Co je rozhraní Web Language Model API? (Preview)

> [!IMPORTANT]
> Rozhraní Web Language Model ve verzi Preview je od 9. srpna 2018 vyřazené z provozu. Ke zpracování a analýze textu doporučujeme používat [moduly analýzy textu služby Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics).

Rozhraní API Microsoft Web Language Model je cloudová služba založená na REST, která poskytuje moderní nástroje pro zpracování přirozeného jazyka. Při použití tohoto rozhraní API může vaše aplikace využívat sílu velkých objemů dat prostřednictvím jazykových modelů trénovaných na webových korpusech shromážděných Bingem na americkém trhu.

Tyto vyhlazené jazykové modely na pozadí, které využívají n-gramy a podporují Markovovy řetězce až do pátého stupně, jsou trénované na těchto korpusech:

- Text obsahu webové stránky
- Text nadpisu webové stránky
- Text ukotvení webové stránky
- Text webového vyhledávacího dotazu

Rozhraní API Web Language Model podporuje čtyři operace vyhledávání:

1. Sdružená pravděpodobnost (log10) posloupnosti slov
2. Podmíněná pravděpodobnost (log10) jednoho slova vzhledem k posloupnosti předchozích slov
3. Seznam slov (dokončení), která nejpravděpodobněji následují po konkrétní posloupnosti slov
4. Dělení slov z řetězců, které neobsahují žádné mezery

## <a name="getting-started"></a>Začínáme

1. Přihlaste se k odběru služby.
2. Stáhněte si sadu [SDK](https://www.github.com/microsoft/cognitive-weblm-windows).
3. Spusťte vzorek kódu SDK.
4. Úplné podrobnosti o koncových bodech, včetně fragmentů kódu v různých jazycích, najdete v [referenčních informacích o rozhraní API](http://web.archive.org/web/20170503191852/westus.dev.cognitive.microsoft.com/docs/services/55de9ca4e597ed1fd4e2f104/operations/55de9ca4e597ed19b0de8a51).

## <a name="underlying-technology"></a>Základní technologie

Následující dokument obsahuje podrobné informace o vývoji těchto jazykových modelů a měl by být citovaný ve výzkumných publikacích, které tuto službu využívají:

- [An Overview of Microsoft Web N-gram Corpus and Applications](https://research.microsoft.com/apps/pubs/default.aspx?id=130762), NAACL-HLT 2010

Kliknutím [sem](https://academic.microsoft.com/#/search?iq=And%28Ty%3D'0'%2CRId%3D2145833060%29&q=papers%20citing%20an%20overview%20of%20microsoft%20web%20n%20gram%20corpus%20and%20applications&filters=&from=0&sort=0) zobrazíte aktuální seznam dokumentů, které tuto práci citují.
