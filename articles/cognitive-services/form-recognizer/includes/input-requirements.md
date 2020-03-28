---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 11783f8fac147e6fb2cf371ee1a588cf318a5e9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "75379420"
---
Nástroj pro rozpoznávání formulářů pracuje na vstupních dokumentech, které splňují tyto požadavky:

* Formát musí být JPG, PNG, PDF (text nebo naskenovaný) nebo TIFF. Textově vložené SOUBORY PDF jsou nejlepší, protože neexistuje žádná možnost chyby v extrakci znaků a umístění.
* Pokud jsou soubory PDF uzamčeny heslem, musíte zámek před odesláním odebrat.
* Dokumenty PDF a TIFF musí mít přibližně 200 stran a celková velikost trénovací datové sady musí být 500 stran nebo méně.
* U obrazů musí být rozměry mezi 600 x 100 pixelů a 4200 x 4200 pixelů.
* Pokud jsou formuláře skenovány z papírových dokumentů, měly by být vysoce kvalitní.
* Text musí používat latinku (anglické znaky).
* Pro učení bez dozoru (bez označených dat) musí data obsahovat klíče a hodnoty.
* Pro učení bez dozoru (bez označených dat) musí být klávesy uvedeny nad nebo vlevo od hodnot; nemohou se objevit níže ani vpravo.

Nástroj pro rozpoznávání formulářů v současné době nepodporuje tyto typy vstupních dat:

* Komplexní tabulky (vnořené tabulky, sloučená záhlaví nebo buňky a tak dále).
* Zaškrtávací políčka nebo přepínací tlačítka.
