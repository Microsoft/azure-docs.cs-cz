---
title: Snížení míry závažnosti trasování – Azure Application Insights
description: Monitorování trasování aplikace pomocí Azure Application Insights pro neobvyklé vzory v rámci telemetrie trasování s inteligentní detekcí.
ms.topic: conceptual
ms.date: 11/27/2017
ms.openlocfilehash: 2b27860adfc1652b58fe9c51d4d0b0a6c271fc0b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86539869"
---
# <a name="degradation-in-trace-severity-ratio-preview"></a>Snížení výkonu v poměru závažnosti trasování (Preview)

Trasování se v aplikacích často používají, protože pomůžou zjistit, co se děje na pozadí. Pokud dojde k chybě, trasování poskytují zásadní přehled o posloupnosti událostí vedoucích k nepožadovanému stavu. I když jsou trasování všeobecně nestrukturovaná, existuje jedna věc, kterou je možné z nich z nich zjistit – jejich úroveň závažnosti. Ve stabilním stavu aplikace očekáváme, že poměr mezi "dobrým" trasováním (*informace* a *podrobný*) a "špatný" trasování (*Upozornění*, *Chyba* a *kritické*), aby zůstaly stabilní. Předpokladem je, že v pravidelných intervalech se můžou v určitém rozsahu vyskytnout "chybná" trasování z libovolného počtu důvodů (přechodné problémy se sítí pro instanci). Ale když skutečný problém začíná růst, obvykle se manifestuje jako zvýšení relativního podílu "špatných" trasování a "dobrých" trasování. Inteligentní zjišťování Application Insights automaticky analyzuje trasování zaznamenané aplikací a může upozorňovat na neobvyklé vzory v závažnosti vaší telemetrie trasování.

Tato funkce nevyžaduje žádné speciální nastavení, kromě konfigurace protokolování trasování pro vaši aplikaci (viz postup konfigurace naslouchacího procesu protokolu trasování pro [.NET](./asp-net-trace-logs.md) nebo [Java](./java-trace-logs.md)). Je aktivní, když vaše aplikace vygeneruje dostatek telemetrie výjimek.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Kdy získám tento typ oznámení inteligentního zjišťování?
Tento typ oznámení se může zobrazit v případě, že poměr mezi "dobrými" trasování (trasování se protokoluje na úrovni *informací* nebo *podrobných*) a "špatných" trasování (zaznamenaná úroveň *Upozornění*, *Chyba* nebo *závažná*) se v určitém dni v porovnání se směrným plánem vypočítaným během posledních sedmi dnů liší.

## <a name="does-my-app-definitely-have-a-problem"></a>Má moje aplikace konečně nějaký problém?
Ne, oznámení neznamená, že vaše aplikace má jednoznačně problém. I když snížení poměru mezi "dobrými" a "špatnými" trasování může znamenat problém s aplikací, tato změna v poměru může být neškodná. Například zvýšení může být způsobeno novým tokem v aplikaci, který emituje více "špatných" trasování než stávající toky.

## <a name="how-do-i-fix-it"></a>Jak ho mám opravit?
Oznámení zahrnují diagnostické informace pro podporu procesu diagnostiky:
1. **Třídění.** V oznámení se dozvíte, kolik operací je ovlivněno. To vám může přispět k přiřazení priority k problému.
2. **Oboru.** Má tento problém vliv na veškerý provoz nebo jenom nějaké operace? Tyto informace lze získat z oznámení.
3. **Diagnóz.** Související položky a sestavy, které se propojí s podpůrnými informacemi, můžete použít k dalšímu diagnostikování problému.
