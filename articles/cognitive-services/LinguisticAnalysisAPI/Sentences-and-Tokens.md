---
title: Věty a tokeny – rozhraní API pro jazykovou analýzu
titlesuffix: Azure Cognitive Services
description: Další informace o rozdělení a Tokenizace v rozhraní API pro jazykovou analýzu vět.
services: cognitive-services
author: DavidLiCIG
manager: cgronlun
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 03/21/2016
ms.author: davl
ROBOTS: NOINDEX
ms.openlocfilehash: 3b88336866909f527aa17cf77514a5ca4a4830b5
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55213442"
---
# <a name="sentence-separation-and-tokenization"></a>Rozdělení a Tokenizace vět

> [!IMPORTANT]
> Dne 9. srpna 2018 došlo k vyřazení jazykové analýzy ve verzi Preview z provozu. Ke zpracování a analýze textu doporučujeme používat [moduly analýzy textu služby Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics).

## <a name="background-and-motivation"></a>Na pozadí a motivace

Zadaný textu, jeho rozdělení na věty a tokeny je prvním krokem pro jazykovou analýzu.

### <a name="sentence-separation"></a>Oddělení větu

Na první pohled to vypadá, že text rozdělení na věty je jednoduché: stačí najít koncový věty značky a konce věty existuje.
Tyto značky jsou však často složité a nejednoznačný.

Vezměte v úvahu následující příklad:

> Co povídáš?!? Můžu žádnou odpověď o ředitel "nový návrh." Je důležité PAN a paní Smith.

Tento text obsahuje tři věty:

- Co povídáš?!?
- Můžu žádnou odpověď o ředitel "nový návrh."
- Je důležité PAN a paní Smith.

Všimněte si, jak jsou označené konce věty zcela novými způsoby.
První končí v kombinaci otazníky a vykřičník (říká se jim vykřičník s otazníkem).
Do předchozí věta by měl být vyžádány druhé končí tečkou nebo full stop, ale následující znak uvozovek.
Ve třetí věty uvidíte, jak lze pomocí tohoto stejného znak tečky označte také zkratky.
Hledání pouze na interpunkční znaménka poskytuje sadu dobrým kandidátem, ale je další práci potřebnou k identifikaci hranice true věty.

### <a name="tokenization"></a>Tokenizace

Další úlohou je rozdělit tyto věty na tokeny.
Ve většině případů anglické tokeny jsou odděleny mezer.
(Hledání tokeny nebo slova je mnohem jednodušší v anglickém jazyce než v čínštině, většinou není použití mezery mezi slovy.
První věta může být napsán jako "Whatdidyousay?")

Existuje několik případů obtížné.
Nejprve, interpunkční znaménka často (ale ne vždy) by měl dají rozdělit mimo jeho kolem kontextu.
Za druhé, má Angličtina *staženiny*, jako je "neměli" nebo "je", ve kterém byly slova komprimované a se zkracuje na menší části.
Cílem tokenizátoru je přerušit sekvence znaků slova.

Vraťme se k věty příkladu výše.
Nyní jsme jste umístili tečku"centra" (&middot;) mezi každý jedinečných token.

- Co &middot; nebyla &middot; vám &middot; Řekněme, že &middot; ?!?
- Můžu &middot; nebyla &middot; nezobrazovat &middot; slyšet &middot; o &middot; &middot; ředitel &middot; společnosti &middot; " &middot; nové &middot; návrh &middot; . &middot; "
- To &middot; společnosti &middot; důležité &middot; k &middot; PAN&middot; a &middot; Mrs. &middot; Smith &middot; .

Všimněte si, jak většina tokeny jsou slova lze najít ve slovníku (například *důležité*, *ředitel*).
Ostatní výhradně obsahovat interpunkce.
Dostupné jsou i další neobvyklé tokeny k reprezentaci staženiny jako *nezobrazovat* pro *není*, a Přivlastňovací pád jako *společnosti*.
Tato Tokenizace umožňuje slovo *neměli* a frázi *ne* více konzistentním způsobem.

## <a name="specification"></a>Specifikace

Je důležité, aby konzistentní rozhodnutí o co zahrnuje věty a token.
Spoléháme na specifikaci od [diskutují Treebank](https://catalog.ldc.upenn.edu/LDC99T42) (některé další podrobnosti najdete na adrese ftp://ftp.cis.upenn.edu/pub/treebank/public_html/tokenization.html).
