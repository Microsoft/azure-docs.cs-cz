---
title: Řešení potíží s Azure Internet Analyzer | Microsoft Docs
description: Referenční informace k řešení potíží pro Azure Internet Analyzer.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 12/04/2019
ms.author: dibotero
ms.openlocfilehash: a7216e697680bcc049d2ceb4caec74adfc1760b0
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74897387"
---
# <a name="azure-internet-analyzer-troubleshooting"></a>Řešení potíží s Azure Internet Analyzer

Tento článek obsahuje postup pro řešení běžných problémů s Internet Analyzer.

## <a name="azure-portal"></a>Web Azure Portal
**V části přehledy výkonnostních metrik se pro tento Scorecard neshromáždila dostatečná měření.**

Poznámky:
- Data měření budou shromažďována pouze v případě, že je klientský skript profilu nástroje Internet Analyzer vložen do aplikace, která přijímá reálný uživatelský provoz. Syntetické přenosy (například testy výkonu Azure WebApp) obvykle nespouštějí vložený kód JavaScriptu, takže tento typ provozu nebude generovat žádná měření.
- Časová řada se generuje jednou za hodinu, takže budete muset počkat aspoň tuto dobu, než se data nových měření zobrazí.
- Scorecardy se generují každý den (na konci každého dne, čas UTC).
- Scorecardy se generují jenom v případě, že se pro vybranou kombinaci filtrů shromáždilo více než 100 měření (test, časové období, země atd.).

## <a name="next-steps"></a>Další kroky
Přečtěte si [Nejčastější dotazy k Internet Analyzer](internet-analyzer-faq.md)
