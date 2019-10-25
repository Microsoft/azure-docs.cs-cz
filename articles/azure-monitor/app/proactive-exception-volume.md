---
title: Inteligentní zjišťování – neobvyklé zvýšení objemu výjimek, v Azure Application Insights | Microsoft Docs
description: Monitorování výjimek aplikace pomocí Azure Application Insights pro neobvyklé vzory ve svazku výjimek.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 12/08/2017
ms.openlocfilehash: fa7669b78caabb95b08200e83ed18ea982ce9ac9
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820602"
---
# <a name="abnormal-rise-in-exception-volume-preview"></a>Neobvyklé zvýšení objemu výjimek (Preview)

Application Insights automaticky analyzuje výjimky vyvolané ve vaší aplikaci a může upozorňovat na neobvyklé vzory v telemetrii výjimek.

Tato funkce nevyžaduje žádné speciální nastavení, kromě [Konfigurace generování sestav výjimek](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-exceptions#set-up-exception-reporting) pro vaši aplikaci. Je aktivní, když vaše aplikace vygeneruje dostatek telemetrie výjimek.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Kdy získám tento typ oznámení inteligentního zjišťování?
Tento typ oznámení se může zobrazit, pokud se v aplikaci v porovnání se směrným plánem vypočítaným během posledních sedmi dnů objeví neobvyklé zvýšení počtu výjimek konkrétního typu.
Algoritmy strojového učení se používají k detekci nárůstu počtu výjimek a přitom berou v úvahu přirozený nárůst využití aplikace.

## <a name="does-my-app-definitely-have-a-problem"></a>Má moje aplikace konečně nějaký problém?
Ne, oznámení neznamená, že vaše aplikace má jednoznačně problém. I když nadměrný počet výjimek obvykle označuje problém s aplikací, tyto výjimky mohou být neškodné a správně zpracovány vaší aplikací.

## <a name="how-do-i-fix-it"></a>Návody opravit?
Oznámení zahrnují diagnostické informace pro podporu procesu diagnostiky:
1. **Třídění.** V tomto oznámení se dozvíte, kolik uživatelů nebo kolik požadavků je ovlivněno. To vám může přispět k přiřazení priority k problému.
2. **Oboru.** Má tento problém vliv na veškerý provoz nebo jenom nějaké operace? Tyto informace lze získat z oznámení.
3. **Diagnóz.** Detekce obsahuje informace o metodě, ze které byla vyvolána výjimka, a také o typu výjimky. Můžete také použít související položky a sestavy odkazující na podpůrné informace, které vám pomohou s dalším diagnostikou problému.