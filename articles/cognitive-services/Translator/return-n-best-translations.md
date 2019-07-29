---
title: Vrácení N-nejlepšího překladu – Translator Text API
titleSuffix: Azure Cognitive Services
description: Vrátí N – nejlepší překlady pomocí Translator Text API Microsoftu.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: swmachan
ms.openlocfilehash: 7aec6bb81f3ca99ead744767668c2e4f1d1d4d53
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68594888"
---
# <a name="how-to-return-n-best-translations"></a>Jak vracet N-nejlepší překlady

> [!NOTE]
> Tato metoda je zastaralá. Není k dispozici v v 3.0 Translator Text API.

Metody gettranslations () a GetTranslationsArray () rozhraní API služby Microsoft Translator obsahují volitelný logický příznak "IncludeMultipleMTAlternatives".
Metoda vrátí až maxTranslations alternativy, kde je rozdíl dodáván z N-nejlepšího seznamu modulu Translator.

Podpis je:

**Syntaxe**

| C# |
|:---|
| GetTranslationsResponse Microsoft. Translator. gettranslations (appId, text, from, to, maxTranslations, Options); |

**Parametry**

| Parametr | Popis |
|:---|:---|
| appId | **Vyžaduje** se Pokud se používá autorizační hlavička, nechte pole AppID prázdné jinak zadejte řetězec, který obsahuje řetězec "nosič" + "" a přístupový token.|
| text | **Vyžaduje** se Řetězec představující text, který se má přeložit Velikost textu nesmí překročit 10000 znaků.|
| from | **Vyžaduje** se Řetězec představující kód jazyka textu, který se má přeložit |
| na | **Vyžaduje** se Řetězec představující kód jazyka, do kterého se má text překládat. |
| maxTranslations | **Vyžaduje** se Celé číslo představující maximální počet vrácených překladů. |
| options | **Volitelné** Objekt TranslateOptions, který obsahuje níže uvedené hodnoty. Jsou to všechna volitelná a výchozí nastavení pro nejběžnější nastavení.

* Kategorií Pouze tato možnost je podporována a výchozí možností je "Obecné".
* Třída Pouze tato možnost je podporována a výchozí možností je "text/prostý".
* Stav: Stav uživatele, který bude pomáhat korelovat žádosti a odpověď. V odpovědi se vrátí stejný obsah.
* IncludeMultipleMTAlternatives: příznak pro určení, zda se má vrátit více než jedna alternativa z jádra MT. Výchozí hodnota je false a zahrnuje pouze 1 alternativu.

## <a name="ratings"></a>Hodnotící
Hodnocení se používá následujícím způsobem: Nejlepší automatický překlad má hodnocení 5.
Automaticky vygenerované alternativy překladu mají hodnocení 0 a mají stupeň shody 100.

## <a name="number-of-alternatives"></a>Počet alternativ
Počet vrácených alternativ je až maxTranslations, ale může být menší.

## <a name="language-pairs"></a>Páry jazyků
Tato funkce není k dispozici pro překlady mezi zjednodušenou a tradiční čínštinou, oběma směry. Je k dispozici pro všechny ostatní podporované páry jazyků Microsoft Translator.
