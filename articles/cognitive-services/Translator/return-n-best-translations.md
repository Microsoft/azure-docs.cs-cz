---
title: Vrátit N – nejlepší překlady – Translator
titleSuffix: Azure Cognitive Services
description: Vrátí N – nejlepší překlady pomocí překladatele.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: swmachan
ROBOTS: NOINDEX
ms.openlocfilehash: 1fe370070aa97befb418d27def32725bf0a148b3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "83592674"
---
# <a name="how-to-return-n-best-translations"></a>Jak vracet N-nejlepší překlady

> [!NOTE]
> Tato metoda je zastaralá. Není k dispozici v v 3.0 překladatele.

Metody gettranslations () a GetTranslationsArray () překladatele obsahují volitelný logický příznak "IncludeMultipleMTAlternatives".
Metoda vrátí až maxTranslations alternativy, kde je rozdíl dodáván z N-nejlepšího seznamu modulu Translator.

Podpis je:

**Syntax**

| C# |
|:---|
| GetTranslationsResponse Microsoft. Translator. gettranslations (appId, text, from, to, maxTranslations, Options); |

**Parametry**

| Parametr | Popis |
|:---|:---|
| appId | **Vyžaduje** se Pokud se používá autorizační hlavička, nechte pole AppID prázdné jinak zadejte řetězec, který obsahuje řetězec "nosič" + "" a přístupový token.|
| text | **Vyžaduje** se Řetězec představující text, který se má přeložit Velikost textu nesmí překročit 10000 znaků.|
| Výsledkem | **Vyžaduje** se Řetězec představující kód jazyka textu, který se má přeložit |
| na | **Vyžaduje** se Řetězec představující kód jazyka, do kterého se má text překládat. |
| maxTranslations | **Vyžaduje** se Celé číslo představující maximální počet vrácených překladů. |
| možnosti | **Volitelné** Objekt TranslateOptions, který obsahuje níže uvedené hodnoty. Jsou to všechna volitelná a výchozí nastavení pro nejběžnější nastavení.

* Category (kategorie): jedinou podporovanou a výchozí možností je "Obecné".
* ContentType: jedinou podporovanou a výchozí možnost je "text/prostý".
* Stav: stav uživatele, který umožňuje korelační požadavky a odpovědi. V odpovědi se vrátí stejný obsah.
* IncludeMultipleMTAlternatives: příznak pro určení, zda se má vrátit více než jedna alternativa z jádra MT. Výchozí hodnota je false a zahrnuje pouze 1 alternativu.

## <a name="ratings"></a>Ratings
Hodnocení se uplatní takto: nejlepší automatický překlad má hodnocení 5.
Automaticky vygenerované alternativy překladu mají hodnocení 0 a mají stupeň shody 100.

## <a name="number-of-alternatives"></a>Počet alternativ
Počet vrácených alternativ je až maxTranslations, ale může být menší.

## <a name="language-pairs"></a>Páry jazyků
Tato funkce není k dispozici pro překlady mezi zjednodušenou a tradiční čínštinou, oběma směry. Je k dispozici pro všechny ostatní podporované páry jazyků Microsoft Translator.
