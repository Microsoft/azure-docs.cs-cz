---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 4079b0cab0126a9750bbb91ca7046aa18dbe9ece
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657754"
---
Nástroj pro rozpoznávání formulářů funguje na vstupních dokumentech, které splňují tyto požadavky:

* Formát musí být JPG, PNG nebo PDF (text nebo naskenovaný). Soubory PDF vložené jako text jsou nejlepší, protože v extrakci znaků a umístění není možnost chyba.
* Pokud jsou vaše soubory PDF zamčené heslem, musíte zámek před odesláním odebrat.
* Velikost souboru musí být menší než 4 MB.
* V případě obrázků musí být rozměry v rozsahu 600 × 100 pixelů až 4200 × 4200 pixelů.
* Pokud je naskenovaný z papírových dokumentů, formuláře by měly být vysoce kvalitní kontroly.
* Text musí používat latinku (anglické znaky).
* Data musí obsahovat klíče a hodnoty.
* Klíče se mohou zobrazit nad nebo nalevo od hodnot, ale ne pod nebo napravo.

Nástroj pro rozpoznávání formulářů v současné době nepodporuje tyto typy vstupních dat:

* Složité tabulky (vnořené tabulky, sloučené záhlaví nebo buňky atd.).
* Zaškrtávací políčka nebo přepínače.
* Dokumenty PDF delší než 50 stránek.
