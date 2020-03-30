---
title: Řešení potíží s internetovým analyzátorem Azure | Dokumenty společnosti Microsoft
description: Odkaz na řešení potíží pro Azure Internet Analyzer.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 12/04/2019
ms.author: dibotero
ms.openlocfilehash: a265278652c16b4682707470d183a02a55b9a0ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77069213"
---
# <a name="azure-internet-analyzer-troubleshooting"></a>Řešení potíží s Azure Internet Analyzer

Tento článek obsahuje postupy řešení potíží s běžnými problémy s analyzátorem Internetu.

## <a name="things-to-keep-in-mind"></a>Co je potřeba mít na paměti
- Klientský skript musí být vložen do webu **HTTPS.** Měření nebudou shromažďována, pokud je skript spuštěn na webu ve formátu prostého textu **(http://)** nebo místní **(file://).**
- Naměřená data budou shromažďována pouze v případě, že klientský skript profilu Nástroje pro analýzu Internetu byl vložen do aplikace, která přijímá skutečný uživatelský provoz. Syntetický provoz (například Azure WebApp Performance Tests) obvykle nespustí vložený kód JavaScript, takže žádné měření budou generovány tímto typem provozu.

## <a name="azure-portal"></a>portál Azure
**"Pro vybranou kombinaci filtrů nebyla vygenerována metrika přehledu výkonnostních metrik" v části Přehledy výkonnostních metrik**
- Přehledy výkonnostních metrik jsou generovány denně (na konci každého dne, čas UTC).
- Přehledy výkonnostních metrik se generují pouze v případě, že bylo shromážděno více než 100 měření pro vybranou kombinaci filtrů (test, časové období, země atd.).

**"Celkový počet měření" je nula pro jeden nebo oba koncové body v testu**
- Časové řady a počty měření se počítají jednou za hodinu, takže budete muset počkat alespoň tuto dobu, než se objeví nová naměřená data.
- Internetový analyzátor počítá pro svou analýzu pouze úspěšná měření (tj. odpovědi HTTP 200). Pokud jeden nebo oba koncové body v testu jsou nedostupné nebo vrácení non-200 HTTP kód, zobrazí se s nulovými celkovými měřeními.

## <a name="next-steps"></a>Další kroky
Přečtěte si [nejčastější dotazy k analyzátoru Internetu](internet-analyzer-faq.md)
