---
title: Filtrování dat – vlastní překladač
titleSuffix: Azure Cognitive Services
description: Při odesílání dokumentů, které mají být použity pro školení vlastního systému, dokumenty projít řadou zpracování a filtrování kroky pro přípravu na školení.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 1028443eaaf6c483cd7cd57289b0dcf2a9f11902
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "68595900"
---
# <a name="data-filtering"></a>Filtrování dat

Při odesílání dokumentů, které mají být použity pro školení vlastního systému, dokumenty projít řadou zpracování a filtrování kroky pro přípravu na školení. Tyto kroky jsou vysvětleny zde. Znalost filtrování vám může pomoci pochopit počet vět zobrazený ve vlastním překladači, stejně jako kroky, které můžete podniknout při přípravě dokumentů pro školení s vlastním překladačem.

## <a name="sentence-alignment"></a>Přidružení vět
Pokud váš dokument není ve formátu XLIFF, TMX nebo ALIGN, vlastní překladač zarovná věty zdrojových a cílových dokumentů mezi sebou, větu po větě. Překladatel neprovádí zarovnání dokumentu – podle toho, jak se pojmenuje te dokumenty, a vyhledá odpovídající dokument jiného jazyka. V rámci dokumentu vlastní překladač se pokusí najít odpovídající větu v jiném jazyce. Používá značky dokumentu, jako jsou vložené značky HTML, které pomáhají s zarovnáním.  

Pokud se ve zdrojových a cílových dokumentech zobrazí velký nesoulad mezi počtem vět, dokument pravděpodobně nebyl v první řadě paralelní nebo z jiných důvodů nemohl být zarovnán. Dokument se spáruje s velkým rozdílem (>10 %) vět na každé straně vyžadují druhý pohled, aby se ujistil, že jsou skutečně paralelní. Vlastní překladač zobrazí upozornění vedle dokumentu, pokud se počet vět liší podezřele.  


## <a name="deduplication"></a>Odstranění duplicit
Vlastní překladač odebere věty, které jsou přítomny v test a ladění dokumentů z trénovacích dat.Odebrání probíhá dynamicky uvnitř trénovacího běhu, nikoli v kroku zpracování dat. Vlastní překladač hlásí počet vět na vás v přehledu projektu před takovým odstraněním.  

## <a name="length-filter"></a>Délka, filtr
* Odstraňte věty pouze s jedním slovem na obou stranách.
* Odstraňte věty s více než 100 slovy na obou stranách.Číňané, Japonci, Korejci jsou osvobozeni.
* Odeberte věty s méně než 3 znaky. Číňané, Japonci, Korejci jsou osvobozeni.
* Odstraňte věty s více než 2000 znaky pro čínštinu, japonštinu, korejštinu.
* Odstraňte věty s méně než 1 % alfa znaků.
* Odeberte položky slovníku obsahující více než 50 slov.

## <a name="white-space"></a>Prázdné místo
* Nahraďte libovolnou sekvenci nemezer, včetně tabulátorů a sekvencí CR/LF, jedním znakem mezery.
* Odebrání úvodního nebo koncového prostoru ve větě

## <a name="sentence-end-punctuation"></a>Interpunkce konce věty
Nahraďte více znaků interpunkce konce věty jednou instancí.  

## <a name="japanese-character-normalization"></a>Normalizace japonského charakteru
Převeďte písmena a číslice s plnou šířkou na znaky s poloviční šířkou.

## <a name="unescaped-xml-tags"></a>Značky XML bez řídicího kódu
Filtrování transformuje neuvozené značky na uvozené značky:
* `&lt;`se stává`&amp;lt;`
* `&gt;`se stává`&amp;gt;`
* `&amp;`se stává`&amp;amp;`

## <a name="invalid-characters"></a>Neplatné znaky
Vlastní překladač odebere věty, které obsahují znak Unicode U + FFFD. Znak U+FFFD označuje neúspěšný převod kódování.

## <a name="next-steps"></a>Další kroky

- [Trénování modelu](how-to-train-model.md) v custom translatoru.
