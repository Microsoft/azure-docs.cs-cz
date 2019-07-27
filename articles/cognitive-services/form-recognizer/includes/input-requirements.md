---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 17dc32f8948387b90229d3c4c07102cff98e3018
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562684"
---
Nástroj pro rozpoznávání formulářů funguje na vstupních dokumentech, které splňují tyto požadavky:

* Formát musí být JPG, PNG nebo PDF (text nebo naskenovaný). Soubory PDF vložené jako text jsou nejlepší, protože v extrakci znaků a umístění není možnost chyba.
* Pokud jsou vaše soubory PDF zamčené heslem, musíte zámek před odesláním odebrat.
* Velikost souboru musí být menší než 4 MB.
* V případě obrázků musí být rozměry v rozsahu 50 × 50 pixelů až 4200 × 4200 pixelů.
* Pokud je naskenovaný z papírových dokumentů, formuláře by měly být vysoce kvalitní kontroly.
* Text musí používat latinku (anglické znaky).
* Data musí obsahovat klíče a hodnoty.
* Klíče se mohou zobrazit nad nebo nalevo od hodnot, ale ne pod nebo napravo.

Nástroj pro rozpoznávání formulářů v současné době nepodporuje tyto typy vstupních dat:

* Složité tabulky (vnořené tabulky, sloučené záhlaví nebo buňky atd.).
* Zaškrtávací políčka nebo přepínače.
* Dokumenty PDF delší než 50 stránek.