---
title: Detekce nevracení paměti – Azure Application Insights inteligentní zjišťování
description: Monitorujte aplikace s využitím Azure Application Insights pro potenciální nevracení paměti.
ms.topic: conceptual
ms.date: 12/12/2017
ms.openlocfilehash: 3fe58cd7d61246c5565cd89fa782c8a977f09499
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86539886"
---
# <a name="memory-leak-detection-preview"></a>Detekce nevracení paměti (Preview)

Application Insights automaticky analyzuje spotřebu paměti každého procesu ve vaší aplikaci a může upozorňovat na potenciální nevracení paměti nebo vyšší spotřebu paměti.

Tato funkce nevyžaduje žádné speciální nastavení, kromě [Konfigurace čítačů výkonu](./performance-counters.md) pro vaši aplikaci. Je aktivní, když vaše aplikace generuje dostatek paměti při vytváření telemetrie čítačů výkonu paměti (například soukromé bajty).

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Kdy získám tento typ oznámení inteligentního zjišťování?
Typické oznámení bude dodržovat konzistentní zvýšení spotřeby paměti po dlouhou dobu, v jednom nebo více procesech a/nebo jednom nebo několika počítačích, které jsou součástí vaší aplikace. Algoritmy strojového učení se používají k detekci zvýšené spotřeby paměti, která odpovídá vzoru nevrácené paměti.

## <a name="does-my-app-really-have-a-problem"></a>Má moje aplikace skutečně problém?
Ne, oznámení neznamená, že vaše aplikace má jednoznačně problém. I když vzory nevracení paměti obvykle označují problém s aplikací, tyto vzory by mohly být typické pro váš konkrétní proces, nebo by mohly mít přirozené odůvodnění podniku a můžou se ignorovat.

## <a name="how-do-i-fix-it"></a>Jak ho mám opravit?
Oznámení zahrnují diagnostické informace pro podporu v procesu analýzy diagnostiky:
1. **Třídění.** V oznámení se zobrazí množství zvýšení paměti (v GB) a časový rozsah, ve kterém se paměť zvýšila. To vám může přispět k přiřazení priority k problému.
2. **Oboru.** Kolik počítačů vykazuje vzorek nevracení paměti? Kolik výjimek bylo aktivováno během potenciální nevrácené paměti? Tyto informace lze získat z oznámení.
3. **Diagnóz.** Tato detekce obsahuje vzorek nevrácení paměti a zobrazuje spotřebu paměti procesu v průběhu času. Můžete také použít související položky a sestavy odkazující na podpůrné informace, které vám pomohou s dalším diagnostikou problému.
