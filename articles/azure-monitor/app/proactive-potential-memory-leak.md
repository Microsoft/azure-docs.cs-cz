---
title: Detekce nevracení paměti – inteligentní detekce Azure Application Insights
description: Sledujte aplikace s Azure Application Insights pro potenciální nevracení paměti.
ms.topic: conceptual
ms.date: 12/12/2017
ms.openlocfilehash: 85d138518dfb1313a810657016e9fe3143887b6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671694"
---
# <a name="memory-leak-detection-preview"></a>Detekce nevracení paměti (náhled)

Application Insights automaticky analyzuje spotřebu paměti každého procesu v aplikaci a může vás varovat před potenciálními nevracenípaměti nebo zvýšenou spotřebou paměti.

Tato funkce nevyžaduje žádné speciální nastavení, kromě [konfigurace čítačů výkonu](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) pro vaši aplikaci. Je aktivní, když vaše aplikace generuje dostatek čítače výkonu paměti telemetrie (například soukromé bajty).

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Kdy dostanu tento typ inteligentního oznámení o detekci?
Typické oznámení bude následovat konzistentní zvýšení spotřeby paměti po dlouhou dobu, v jednom nebo více procesů nebo jeden nebo více počítačů, které jsou součástí vaší aplikace. Algoritmy strojového učení se používají pro detekci zvýšené spotřeby paměti, která odpovídá vzoru nevracení paměti.

## <a name="does-my-app-really-have-a-problem"></a>Má moje aplikace opravdu nějaký problém?
Ne, oznámení neznamená, že vaše aplikace má určitě problém. Přestože vzory nevracení paměti obvykle označují problém aplikace, tyto vzory mohou být typické pro váš konkrétní proces nebo mohou mít přirozené obchodní odůvodnění a mohou být ignorovány.

## <a name="how-do-i-fix-it"></a>Jak ho mám opravit?
Oznámení obsahují diagnostické informace pro podporu v procesu diagnostické analýzy:
1. **Třídění.** Oznámení zobrazuje množství zvýšení paměti (v GB) a časový rozsah, ve kterém se zvětšila paměť. To vám může pomoci přiřadit prioritu problému.
2. **Rozsah.** Kolik strojů vystavovalo vzorek úniku paměti? Kolik výjimek bylo spuštěno během potenciálního nevracení paměti? Tyto informace lze získat z oznámení.
3. **Diagnostikovat.** Detekce obsahuje vzorek nevracení paměti, zobrazující spotřebu paměti procesu v průběhu času. Můžete také použít související položky a sestavy, které odkazují na podpůrné informace, které vám pomohou problém dále diagnostikovat.
