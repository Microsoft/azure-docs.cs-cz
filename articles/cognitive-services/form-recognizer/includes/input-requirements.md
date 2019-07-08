---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 816fa4b0df2fab77bcde261f30d8bad584257fd7
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592614"
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