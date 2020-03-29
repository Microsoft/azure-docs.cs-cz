---
title: Vrátit N-Nejlepší překlady - Překladatel text
titleSuffix: Azure Cognitive Services
description: Vrátit N-Nejlepší překlady pomocí překladače text API.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: swmachan
ROBOTS: NOINDEX
ms.openlocfilehash: eff25877165ac365e0af77651147fcdd1eebe294
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73837247"
---
# <a name="how-to-return-n-best-translations"></a>Jak vrátit Překlady N-Best

> [!NOTE]
> Tato metoda je zastaralé. Není k dispozici ve V3.0 překladače text API.

Metody GetTranslations() a GetTranslationsArray() rozhraní MICROSOFT Translator API obsahují volitelný logický příznak "IncludeMultipleMTAlternatives".
Metoda se vrátí až do alternativ maxTranslations, kde je delta dodávána ze seznamu N-Best motoru překladače.

Podpis je:

**Syntaxe**

| C# |
|:---|
| GetTranslationsResponse Microsoft.Translator.GetTranslations (appId, text, od, do, maxTranslations, možnosti); |

**Parametry**

| Parametr | Popis |
|:---|:---|
| appId | **Povinné** Pokud je použita hlavička Autorizace, ponechte pole appid prázdné, jiné, zadejte řetězec obsahující "Nosič" + " " + přístupový token.|
| text | **Povinné** Řetězec představující text přeložit. Velikost textu nesmí přesáhnout 10000 znaků.|
| Z | **Povinné** Řetězec představující kód jazyka textu přeložit. |
| na | **Povinné** Řetězec představující kód jazyka přeložit text do. |
| maxPřeklady | **Povinné** Int představující maximální počet překladů vrátit. |
| možnosti | **Nepovinné** A TranslateOptions objekt, který obsahuje hodnoty uvedené níže. Všechny jsou volitelné a ve výchozím nastavení se používají nejběžnější nastavení.

* Kategorie: Pouze podporované a výchozí, možnost je "obecné".
* ContentType: Pouze podporované a výchozí, volba je "text/prostý".
* Stav: Stav uživatele, který pomáhá korelovat požadavek a odpověď. Stejný obsah bude vrácen v odpovědi.
* IncludeMultipleMTAlternatives: příznak k určení, zda chcete vrátit více než jednu alternativu z modulu MT. Výchozí hodnota je false a zahrnuje pouze 1 alternativu.

## <a name="ratings"></a>Ratings
Hodnocení se uplatňují takto: Nejlepší automatický překlad má hodnocení 5.
Automaticky generované (N-Best) překlad alternativy mají hodnocení 0 a mají stupeň shody 100.

## <a name="number-of-alternatives"></a>Počet alternativ
Počet vrácených alternativ je až maxTranslations, ale může být menší.

## <a name="language-pairs"></a>Jazykové páry
Tato funkce není k dispozici pro překlady mezi zjednodušenou a tradiční čínštinou v obou směrech. Je k dispozici pro všechny ostatní jazykové páry podporované aplikací Microsoft Translator.
