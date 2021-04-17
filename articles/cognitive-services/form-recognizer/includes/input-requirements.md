---
author: laujan
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: lajanuar
ms.openlocfilehash: dfd2b32094aae06675ea8ee9157370f9d2833e91
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518217"
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
* V případě bezdohledového učení (bez popisků dat) musí data obsahovat klíče a hodnoty.
* U bezdohledového učení (bez popisků dat) se klíče musí zobrazit nad nebo nalevo od hodnot. nemůžou se zobrazit níže nebo vpravo.
