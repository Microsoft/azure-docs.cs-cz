---
title: Vrátí N-Best překlady – Translator Text API
titlesuffix: Azure Cognitive Services
description: Vrátí N-Best překlady pomocí rozhraní Microsoft Translator Text API.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: a0f4e4dd5cea9c3ea7b682e6372d2ffa8726e6ef
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2018
ms.locfileid: "49646469"
---
# <a name="how-to-return-n-best-translations"></a>Jak vracet N-Best překlady

> [!NOTE]
> Tato metoda je zastaralá. Není k dispozici v V3.0 Translator Text API.

Metody GetTranslations() a GetTranslationsArray() rozhraní Microsoft Translator API zahrnují volitelný logický příznak "IncludeMultipleMTAlternatives".
Metoda vrátí až maxTranslations alternativy kde rozdílového pochází ze seznamu N-Best translator stroje.

Podpis je:

**Syntaxe**

| C# |
|:---|
| GetTranslationsResponse Microsoft.Translator.GetTranslations(appId, text, from, to, maxTranslations, options); |

**Parametry**

| Parametr | Popis |
|:---|:---|
| appId | **Vyžaduje** Pokud hlavička autorizace se používá, ponechejte tuto položku appid pole prázdné jinak zadat řetězec obsahující "Nosiče" + "" + přístupový token.|
| text | **Vyžaduje** řetězec představující text k přeložení. Velikost textu nesmí být delší než 10000 znaků.|
| od | **Vyžaduje** řetězec představující kód jazyka textu pro převod. |
| na | **Vyžaduje** řetězec představující kód jazyka můžete přeložit text do. |
| maxTranslations | **Vyžaduje** celé číslo představující maximální počet překlady se vraťte. |
| Možnosti | **Volitelné** A TranslateOptions objekt, který obsahuje níže uvedené hodnoty. Jsou nepovinné a nejběžnější nastavení ve výchozím nastavení.

* Kategorie: Jediný podporovaný a výchozí možnost je "general".
* Jediný podporovaný ContentType: A výchozí hodnota, je možnost "text/plain".
* Stav: Stav uživatele korelovat požadavku a odpovědi. Vrátí se stejným obsahem v odpovědi.
* IncludeMultipleMTAlternatives: Příznak k určení, jestli se má vrátit více než jeden modul MT alternativy. Výchozí hodnotu false a obsahuje pouze 1 alternativu.

## <a name="ratings"></a>Hodnocení
Hodnocení se použijí následujícím způsobem: nejlepší automatický překlad má hodnocení 5.
Automaticky generované alternativy překladu (N-Best) mají hodnocení 0 a shoda stupeň 100.

## <a name="number-of-alternatives"></a>Počet alternativy
Počet vrácených alternativy záleží maxTranslations, ale může být nižší.

## <a name="language-pairs"></a>Dvojice jazyků
Tato funkce není k dispozici pro překlad mezi zjednodušená a tradiční čínštiny, obou směrech. Je k dispozici pro všechny ostatní dvojice jazyků Microsoft Translatoru podporována.
