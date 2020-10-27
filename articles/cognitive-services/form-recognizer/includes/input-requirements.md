---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: b3d24ce4dd42ea8122610943379160ca93406c21
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92548140"
---
Nástroj pro rozpoznávání formulářů funguje na vstupních dokumentech, které splňují tyto požadavky:

* Formát musí být JPG, PNG, PDF (text nebo naskenovaný) nebo TIFF. Soubory PDF vložené jako text jsou nejlepší, protože v extrakci znaků a umístění není možnost chyba.
* Velikost souboru musí být menší než 50 MB.
* Rozměry obrázku musí být mezi 50 × 50 pixelů a 10000 × 10000 pixelů.
* Rozměry PDF musí být maximálně 17 × 17 palců, které odpovídají zákonným nebo a3 velikosti papíru a menšímu.
* Pro PDF a TIFF se zpracovávají jenom první 200 stránky (s předplatným úrovně Free, zpracovávají se jenom první dvě stránky).
* Celková velikost sady školicích dat musí být 500 stránek nebo méně.
* Pokud jsou vaše soubory PDF zamčené heslem, musíte zámek před odesláním odebrat.
* Pokud je naskenovaný z papírových dokumentů, formuláře by měly být vysoce kvalitní kontroly.
* Text musí používat latinku (anglické znaky).
* V případě bezdohledového učení (bez popisků dat) musí data obsahovat klíče a hodnoty.
* U bezdohledového učení (bez popisků dat) se klíče musí zobrazit nad nebo nalevo od hodnot. nemůžou se zobrazit níže nebo vpravo.

Nástroj pro rozpoznávání formulářů v současné době nepodporuje tyto typy vstupních dat:

* Složité tabulky (vnořené tabulky, sloučené záhlaví nebo buňky atd.).
* Zaškrtávací políčka nebo přepínače.
