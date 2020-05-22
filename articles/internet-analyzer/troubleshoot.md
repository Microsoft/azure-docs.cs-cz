---
title: Řešení potíží s Azure Internet Analyzer | Microsoft Docs
description: Referenční informace k řešení potíží pro Azure Internet Analyzer.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 12/04/2019
ms.author: dibotero
ms.openlocfilehash: 0c9e203d7e8be7b24c711f73e2152a7745a57dac
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745465"
---
# <a name="azure-internet-analyzer-troubleshooting"></a>Řešení potíží s Azure Internet Analyzer

Tento článek obsahuje postup pro řešení běžných problémů s Internet Analyzer.

## <a name="things-to-keep-in-mind"></a>Co je potřeba mít na paměti
- Klientský skript musí být vložen do webu **https** . Měření se nebudou shromažďovat, pokud se skript spouští na webu v prostém textu (**http://**) nebo místní (**File://**).
- Data měření budou shromažďována pouze v případě, že je klientský skript profilu nástroje Internet Analyzer vložen do aplikace, která přijímá reálný uživatelský provoz. Syntetické přenosy (například testy výkonu Azure WebApp) obvykle nespouštějí vložený kód JavaScriptu, takže tento typ provozu nebude generovat žádná měření.

## <a name="azure-portal"></a>portál Azure
**"Pro vybranou kombinaci filtru v části přehledy výkonnostních metrik nebyla vygenerována Scorecard**
- Scorecardy se generují každý den (na konci každého dne, čas UTC).
- Scorecardy se generují jenom v případě, že se pro vybranou kombinaci filtrů shromáždila více než 100 měření (test, časové období, země/oblast atd.).

**"Celkový počet měření" je nula pro jeden nebo oba koncové body v testu.**
- Časová řada a počty měření se vypočítávají jednou za hodinu, takže budete muset počkat aspoň tuto dobu, než se data nových měření zobrazí.
- Nástroj Internet Analyzer počítá pouze úspěšná měření (tj. odpovědi HTTP 200) pro jeho analýzu. Pokud je jeden nebo oba koncové body v testu nedosažitelný nebo vrací kód HTTP, který není 200, zobrazí se s nulovým celkovým počtem měření.

## <a name="next-steps"></a>Další kroky
Přečtěte si [Nejčastější dotazy k Internet Analyzer](internet-analyzer-faq.md)
