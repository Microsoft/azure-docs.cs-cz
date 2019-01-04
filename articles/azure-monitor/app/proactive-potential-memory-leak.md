---
title: Inteligentní zjišťování – potenciální nevrácená paměť detekovaných službou Azure Application Insights | Dokumentace Microsoftu
description: Monitorování aplikací pomocí Azure Application Insights pro potenciální nevracení paměti.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: mbullwin
ms.openlocfilehash: e430b1e976ac26f7320b28d50dd39923066cfa41
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54028192"
---
# <a name="memory-leak-detection-preview"></a>Rozpoznávání nevracení paměti (preview)

Application Insights automaticky analyzuje využití paměti každého procesu ve vaší aplikaci a může vás upozorní na potenciální nevracení paměti nebo využití paměti.

Tato funkce vyžaduje žádné speciální instalační program, než [konfiguraci čítačů výkonu](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) pro vaši aplikaci. To je aktivní, pokud vaše aplikace generuje telemetrii čítače výkonu dostatek paměti (například nesdílených bajtů).

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Při obdržení tohoto typu upozornění inteligentního zjišťování
Typické oznámení bude následovat konzistentní nárůstu využití paměti po dlouhou dobu čas v jednom nebo více procesech a/nebo jeden nebo více počítačů, které jsou součástí vaší aplikace. Algoritmy strojového učení se používají pro zjištění využití zvýšení paměti, který odpovídá vzoru nevracení paměti.

## <a name="does-my-app-really-have-a-problem"></a>Moje aplikace ve skutečnosti má problém?
Ne, oznámení neznamená, že vaše aplikace jednoznačně došlo k problému. I když vzory nevracení paměti obvykle naznačují problém s aplikací, tyto vzory může být typické pro váš konkrétní proces, nebo může mít fyzické obchodní odůvodnění a můžete ignorovat.

## <a name="how-do-i-fix-it"></a>Jak ho mám opravit?
Oznámení zahrnují diagnostické informace pro podporu v procesu diagnostických analýzy:
1. **Třídění.** Oznámení se zobrazí zvýšit množství paměti (v GB) a časový rozsah, ve kterém je paměť zvýšil. Můžete přiřadit prioritu problému.
2. **Obor.** Kolik počítačů monitorujícím vzor nevracení paměti? Kolik výjimek byly aktivovaná potenciálního nevrácení paměti? Tyto informace můžete získat z oznámení.
3. **Diagnostika.** Detekce obsahuje vzor nevracení paměti, zobrazující využití paměti procesu v čase. Můžete použít také související položky a sestavy propojení podpůrné informace, abychom vám další diagnostice problému.
