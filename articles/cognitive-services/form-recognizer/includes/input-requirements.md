---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 690219347782aad2140b0a1b0541590c5426c568
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67459725"
---
Formulář pro rozpoznávání funguje na vstupních dokumentů, které splňují tyto požadavky:

* Formát musí být JPG, PNG nebo PDF (text nebo naskenované). Vložený text PDF jsou nejvhodnější, protože nemohly žádným způsobem při extrakci znaků a umístění.
* Velikost souboru musí být menší než 4 megabajtů (MB).
* Pro Image dimenze musí být mezi 50 × 50 pixelů a 4200 x 4200 pixelů.
* Pokud prohledány z papíru dokumentů, formuláře by měl být vysoce kvalitní kontroly.
* Text, musíte použít latinské abecedy (anglické znaky).
* Data musí být vytištěna (ne rukou).
* Data musí obsahovat klíče a hodnoty.
* Klíče můžete objeví nad či hodnot, ale ne míň než vlevo nebo vpravo.

Nástroj pro rozpoznávání formuláře v současné době nepodporuje tyto typy vstupních dat:

* Komplexní tabulky (vnořené tabulky, sloučené záhlaví nebo buňky a tak dále).
* Zaškrtávací políčka nebo rádiových tlačítek.
* Dokumenty PDF je delší než 50 stránky.