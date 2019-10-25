---
title: Inteligentní zjišťování – snížení míry závažnosti trasování v Azure Application Insights | Microsoft Docs
description: Monitorování trasování aplikace pomocí Azure Application Insights pro neobvyklé vzory v telemetrii trasování.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 11/27/2017
ms.openlocfilehash: 83c1296beabaaae78289a653c6b30f6665f725c2
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820542"
---
# <a name="degradation-in-trace-severity-ratio-preview"></a>Snížení výkonu v poměru závažnosti trasování (Preview)

Trasování se v aplikacích často používají, protože pomůžou zjistit, co se děje na pozadí. Pokud dojde k chybě, trasování poskytují zásadní přehled o posloupnosti událostí vedoucích k nepožadovanému stavu. I když jsou trasování všeobecně nestrukturovaná, existuje jedna věc, kterou je možné z nich z nich zjistit – jejich úroveň závažnosti. Ve stabilním stavu aplikace očekáváme, že poměr mezi "dobrým" trasováním (*informace* a *podrobný*) a "špatný" trasování (*Upozornění*, *Chyba*a *kritické*), aby zůstaly stabilní. Předpokladem je, že v pravidelných intervalech se můžou v určitém rozsahu vyskytnout "chybná" trasování z libovolného počtu důvodů (přechodné problémy se sítí pro instanci). Ale když skutečný problém začíná růst, obvykle se manifestuje jako zvýšení relativního podílu "špatných" trasování a "dobrých" trasování. Inteligentní zjišťování Application Insights automaticky analyzuje trasování zaznamenané aplikací a může upozorňovat na neobvyklé vzory v závažnosti vaší telemetrie trasování.

Tato funkce nevyžaduje žádné speciální nastavení, kromě konfigurace protokolování trasování pro vaši aplikaci (viz postup konfigurace naslouchacího procesu protokolu trasování pro [.NET](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-trace-logs) nebo [Java](https://docs.microsoft.com/azure/application-insights/app-insights-java-trace-logs)). Je aktivní, když vaše aplikace vygeneruje dostatek telemetrie výjimek.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Kdy získám tento typ oznámení inteligentního zjišťování?
Tento typ oznámení se může zobrazit v případě, že poměr mezi "dobrými" trasování (trasování se protokoluje na úrovni *informací* nebo *podrobných*) a "špatných" trasování (trasování zaznamenaná na úrovni *varování*, *Chyba*nebo *závažná*) znamená snížení úrovně v konkrétní den v porovnání se směrným plánem vypočítaným během posledních sedmi dnů.

## <a name="does-my-app-definitely-have-a-problem"></a>Má moje aplikace konečně nějaký problém?
Ne, oznámení neznamená, že vaše aplikace má jednoznačně problém. I když snížení poměru mezi "dobrými" a "špatnými" trasování může znamenat problém s aplikací, tato změna v poměru může být neškodná. Například zvýšení může být způsobeno novým tokem v aplikaci, který emituje více "špatných" trasování než stávající toky.

## <a name="how-do-i-fix-it"></a>Návody opravit?
Oznámení zahrnují diagnostické informace pro podporu procesu diagnostiky:
1. **Třídění.** V oznámení se dozvíte, kolik operací je ovlivněno. To vám může přispět k přiřazení priority k problému.
2. **Oboru.** Má tento problém vliv na veškerý provoz nebo jenom nějaké operace? Tyto informace lze získat z oznámení.
3. **Diagnóz.** Související položky a sestavy, které se propojí s podpůrnými informacemi, můžete použít k dalšímu diagnostikování problému.


