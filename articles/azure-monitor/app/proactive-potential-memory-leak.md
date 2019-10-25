---
title: Inteligentní zjišťování – potenciální nevracení paměti zjištěné službou Azure Application Insights | Microsoft Docs
description: Monitorujte aplikace s využitím Azure Application Insights pro potenciální nevracení paměti.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 12/12/2017
ms.openlocfilehash: f07da754c6c9e0ad0541db12740c1d80f7f884fd
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72818807"
---
# <a name="memory-leak-detection-preview"></a>Detekce nevracení paměti (Preview)

Application Insights automaticky analyzuje spotřebu paměti každého procesu ve vaší aplikaci a může upozorňovat na potenciální nevracení paměti nebo vyšší spotřebu paměti.

Tato funkce nevyžaduje žádné speciální nastavení, kromě [Konfigurace čítačů výkonu](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) pro vaši aplikaci. Je aktivní, když vaše aplikace generuje dostatek paměti při vytváření telemetrie čítačů výkonu paměti (například soukromé bajty).

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Kdy získám tento typ oznámení inteligentního zjišťování?
Typické oznámení bude dodržovat konzistentní zvýšení spotřeby paměti po dlouhou dobu, v jednom nebo více procesech a/nebo jednom nebo několika počítačích, které jsou součástí vaší aplikace. Algoritmy strojového učení se používají k detekci zvýšené spotřeby paměti, která odpovídá vzoru nevrácené paměti.

## <a name="does-my-app-really-have-a-problem"></a>Má moje aplikace skutečně problém?
Ne, oznámení neznamená, že vaše aplikace má jednoznačně problém. I když vzory nevracení paměti obvykle označují problém s aplikací, tyto vzory by mohly být typické pro váš konkrétní proces, nebo by mohly mít přirozené odůvodnění podniku a můžou se ignorovat.

## <a name="how-do-i-fix-it"></a>Návody opravit?
Oznámení zahrnují diagnostické informace pro podporu v procesu analýzy diagnostiky:
1. **Třídění.** V oznámení se zobrazí množství zvýšení paměti (v GB) a časový rozsah, ve kterém se paměť zvýšila. To vám může přispět k přiřazení priority k problému.
2. **Oboru.** Kolik počítačů vykazuje vzorek nevracení paměti? Kolik výjimek bylo aktivováno během potenciální nevrácené paměti? Tyto informace lze získat z oznámení.
3. **Diagnóz.** Tato detekce obsahuje vzorek nevrácení paměti a zobrazuje spotřebu paměti procesu v průběhu času. Můžete také použít související položky a sestavy odkazující na podpůrné informace, které vám pomohou s dalším diagnostikou problému.
