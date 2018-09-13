---
title: Inteligentní zjišťování – snížení poměru závažnost trasování, ve službě Azure Application Insights | Dokumentace Microsoftu
description: Monitorování trasování aplikací pomocí Azure Application Insights pro neobvyklé vzory v telemetrická data trasování.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: mbullwin
ms.openlocfilehash: 41e972145d2404e46b62521c7d40d997be74a7f9
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35643217"
---
# <a name="degradation-in-trace-severity-ratio-preview"></a>Snížení výkonnosti v poměru závažnost trasování (preview)

Trasování se běžně používají v aplikacích, protože pomoci vyprávíte ke grafu příběh o co se stane, že na pozadí. Když se něco zvrtne, trasování umožňují velmi důležitý dohled nad posloupnost událostí, což vede k nežádoucímu stavu. Trasování jsou obecně nestrukturovaných, je jednou z věcí, které je možné zjistit namítají z nich – jejich úroveň závažnosti. V aplikaci prvku stabilního stavu by Očekáváme, že je poměr mezi "good" trasování (*informace* a *Verbose*) a "špatné" trasování (*upozornění*, *chyba*, a *kritický*) k zajištění stabilní. Předpokladem je, že "špatné" trasování se může stát v pravidelných intervalech do určité míry z důvodu z nejrůznějších důvodů (například přechodných síťových problémů). Ale po zahájení stále se rozšiřující skutečný problém obvykle manifesty jako prodloužením poměrný trasování "good" vs "špatné" trasování. Application Insights inteligentní zjišťování automaticky analyzuje trasování zaznamenané vaší aplikace a může vás upozorní na neobvyklé vzory v závažnost telemetrie trasování.

Tato funkce vyžaduje žádné speciální instalační program, než je konfigurace protokolování trasování pro aplikaci (v tématu Postup konfigurace naslouchacího procesu trasování protokolu pro [.NET](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-trace-logs) nebo [Java](https://docs.microsoft.com/azure/application-insights/app-insights-java-trace-logs)). Je aktivní, když vaše aplikace generuje dostatek telemetrie výjimek.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Při obdržení tohoto typu upozornění inteligentního zjišťování
Tento typ upozornění se mohou zobrazovat, pokud poměr mezi "good" trasování (protokolována s úrovní trasování *informace* nebo *Verbose*) a "špatné" trasování (protokolována s úrovní trasování *upozornění*, * Chyby, nebo *závažná chyba*) je snížení úrovně v určitý den, ve srovnání s směrný plán počítá v průběhu posledních sedmi dnů.

## <a name="does-my-app-definitely-have-a-problem"></a>Moje aplikace jednoznačně má problém?
Ne, oznámení neznamená, že vaše aplikace jednoznačně došlo k problému. I když se pokles poměr mezi "good" a "špatné" trasování může značit problém aplikace, může být neškodné tuto změnu v poměru. Například zvýšení může být z důvodu nového toku v aplikaci generování další "špatné" trasování než stávající toky).

## <a name="how-do-i-fix-it"></a>Jak ho mám opravit?
Oznámení zahrnují diagnostické informace pro podporu v procesu diagnostiky:
1. **Třídění.** Oznámení se dozvíte, jak mnoho operací se to týká. Můžete přiřadit prioritu problému.
2. **Obor.** Tento problém ovlivňuje veškerý provoz, nebo jen některé operace? Tyto informace můžete získat z oznámení.
3. **Diagnostika.** Můžete použít související položky a sestavy propojení podpůrné informace, abychom vám další diagnostice problému.


