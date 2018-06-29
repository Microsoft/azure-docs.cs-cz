---
title: Věty a tokeny v lingvistické Analysis API | Microsoft Docs
description: Další informace o větu oddělení a tokenizaci v lingvistické API analýzy v kognitivní služby.
services: cognitive-services
author: DavidLiCIG
manager: wkwok
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: article
ms.date: 03/21/2016
ms.author: davl
ms.openlocfilehash: 78e539f365728ad540308e9cfb07af44bf6d8fe7
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2018
ms.locfileid: "37084038"
---
# <a name="sentence-separation-and-tokenization"></a>Věty oddělení a tokenizaci

## <a name="background-and-motivation"></a>Pozadí a motivace

Zadaný text textu, prvním krokem lingvistické analýzy je rozdělit ho na věty a tokeny.

### <a name="sentence-separation"></a>Věty oddělení

Na první pohled se zdá být že rozdělení text na věty je jednoduchý: právě najít end větu značek a rozdělit věty existuje.
Tyto značky jsou však často složité a nejednoznačný.

Zvažte například následující text:

> Co povídáš?!? Nebyla slyšet o ředitel společnosti "nové návrh." Je důležité pane a paní Smith.

Tento text obsahuje tři věty:

- Co povídáš?!?
- Nebyla slyšet o ředitel společnosti "nové návrh."
- Je důležité pane a paní Smith.

Všimněte si, jak jsou označené konce vět velmi různými způsoby.
První končí v kombinace otazníky a vykřičníky (někdy nazývané vykřičník s otazníkem).
Druhé končí tečkou nebo s tečkou, ale následující uvozovky by měl být vyžádány do předchozí věty.
Ve třetí větě uvidíte použití tento znak stejné období zkratky také označit.
Prohlížení právě interpunkce poskytuje sadu vhodným kandidátem, ale vyžaduje další práce k identifikaci hranice true věty.

### <a name="tokenization"></a>Tokenizaci

Dalším krokem je rozdělit tyto věty na tokeny.
Ve většině případů anglické tokeny jsou odděleny mezer.
(Hledání tokeny nebo slova je mnohem snazší v angličtině než v Čínské, většinou není použití mezery mezi slovy.
První věta může zapsat jako "Whatdidyousay?")

Existuje několik případů obtížná.
Nejprve interpunkce často (ale ne vždy) by měl rozdělit od jeho kolem kontextu.
Druhý, má Angličtina *staženiny*, jako jsou "nebylo" nebo "je", kde byly slova komprimované a orientaci na menší části. Cílem tokenizátoru je rozdělit slova posloupnost znaků.

Pojďme se vraťte k ukázkové věty z výše.
Nyní jsme jste umístili "center tečku" (&middot;) mezi každý odlišné token.

- Co &middot; nebyla &middot; jste &middot; vyslovení &middot; ?!?
- I &middot; nebyla &middot; n't &middot; uslyšíme &middot; o &middot; &middot; ředitel &middot; na &middot; " &middot; nové &middot; návrh &middot; . &middot; "
- Ho &middot; na &middot; důležité &middot; k &middot; PAN &middot; a &middot; paní. &middot; Smith &middot; .

Všimněte si, jak většina tokeny jsou slova by najít ve slovníku (například *důležité*, *ředitel*).
Ostatní výhradně obsahovat interpunkce.
Nakonec jsou více neobvyklou tokeny představují staženiny jako *n't* pro *není*, jako Přivlastňovací pád *na*atd. Tato tokenizaci umožňuje zpracovat slovo *nebyla* a fráze *nebyla* ve více konzistentní způsob, pro instanci.

## <a name="specification"></a>Specifikace

Je důležité zajistit konzistentní rozhodnutí o co zahrnuje věty a token.
Spoléháme na specifikaci z [členem této Treebank](https://catalog.ldc.upenn.edu/ldc99t42) (některé další podrobnosti jsou dostupné na ftp://ftp.cis.upenn.edu/pub/treebank/public_html/tokenization.html).
