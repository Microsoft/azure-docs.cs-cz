---
title: Vrátí N-nejvhodnější překlady textem Microsoft překladač rozhraní API | Microsoft Docs
description: Vrátí N-nejvhodnější překlady pomocí rozhraní API služby Microsoft překladač Text.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: 3eafe50f69ae1a6748342e64a414ecee4467d0d1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342770"
---
# <a name="how-to-return-n-best-translations"></a>Postup vrátí N-nejvhodnější překlady

> [!NOTE]
> Tato metoda je zastaralá. Není k dispozici v V3.0 rozhraní API Text překladač.

GetTranslations() a GetTranslationsArray() metod rozhraní API služby Microsoft překladač zahrnují volitelný logický příznak "IncludeMultipleMTAlternatives".
Metoda vrátí až maxTranslations alternativy kde delta pochází ze seznamu N nejvhodnější překladač stroje.

Podpis je:

**Syntaxe**

| C# |
|:---|
| GetTranslationsResponse Microsoft.Translator.GetTranslations(appId, text, from, to, maxTranslations, options); |

**Parametry**

| Parametr | Popis |
|:---|:---|
| appId | **Požadované** Pokud se používá k hlavičce autorizace, ponechejte pole appid prázdný jinak zadejte řetězec obsahující "Nosiče" + "" + přístupový token.|
| text | **Požadované** řetězec představující text přeložit. Velikost textu nesmí být delší než 10000 znaků.|
| od | **Požadované** řetězec představující kód jazyka textu přeložit. |
| na | **Požadované** řetězec představující kód jazyka přeložit text do. |
| maxTranslations | **Požadované** int představující maximální počet překladů vrátit. |
| Možnosti | **Volitelné** A TranslateOptions objekt, který obsahuje níže uvedené hodnoty. Jsou všechny volitelné a výchozí nejběžnější nastavení.

* Kategorie: Jediný podporovaný, a výchozí hodnota, je možnost "Obecné".
* Jediný podporovaný ContentType: A výchozí hodnota, je možnost "text/plain".
* Stav: Stav uživatele při correlate požadavku a odpovědi. Stejný obsah, bude vrácen v odpovědi.
* IncludeMultipleMTAlternatives: Příznak k určení, jestli se mají vracet víc než jeden z modulu MT alternativy. Výchozí je hodnota false a obsahuje pouze 1 alternativní.

## <a name="ratings"></a>Hodnocení
Hodnocení se používají takto: nejlepší automatické překlad má hodnocení 5.
Automaticky generovaný překlad alternativy (N-nejlepší) mají hodnocení 0 a mají určitý stupeň shody 100.

## <a name="number-of-alternatives"></a>Počet alternativy
Počet vrácených alternativy je až maxTranslations, ale může být nižší.

## <a name="language-pairs"></a>Dvojice jazyků
Tato funkce není k dispozici pro překlad mezi zjednodušená a tradiční čínštině, obou směrech. Je k dispozici pro všechny ostatní dvojice jazyka Microsoft Translator podporována.
