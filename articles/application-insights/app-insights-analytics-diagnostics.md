---
title: Inteligentní diagnostiky změny výkonu webové aplikace ve službě Azure Application Insights | Microsoft Docs
description: Automatickou diagnostiku špičky nebo kroky v výkonu telemetrie z vaší webové aplikace.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 04/16/2017
ms.author: mbullwin
ms.openlocfilehash: 08b1da205797d663191dde45f8c091dba91fc573
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294363"
---
# <a name="diagnose-sudden-changes-in-your-app-telemetry"></a>Diagnostika nečekané změny v telemetrii aplikace

*Tato funkce je ve verzi preview.*

Diagnostika nečekané změny ve vaší webové aplikace výkonu a využití s jedním kliknutím! Inteligentní diagnostiky funkce je k dispozici vždy, když vytváříte graf doby zpracování v [Analytics](app-insights-analytics.md) v [Application Insights](app-insights-overview.md). Vždy, když dojde neobvyklou změnu z trend výsledků, například Špička nebo dip, identifikuje inteligentní diagnostiky vzor dimenzí a související hodnoty, které mohou vysvětlit změny. Díky tomu můžete rychle diagnostikovat problém. 

V tomto příkladu inteligentní diagnostiky nalezen se vzor přidružené změny hodnot vlastností a zvýrazňuje rozdíl mezi výsledky a bez tohoto vzoru:

![Příklad analytics diagnostiky výsledků](./media/app-insights-analytics-diagnostics/analytics-result.png)
 

## <a name="diagnose-data-changes"></a>Diagnostika změny dat

1.  Spuštění dotazu v analýzy a vykreslit ho jako graf doby zpracování. 
2.  Klikněte na libovolného bodu zvýrazněné ve špičce, pokud existuje.
 
    ![bod ve špičce](./media/app-insights-analytics-diagnostics/peak.png)

    Diagnostika trvá několik sekund zjistit vzor.

3. Na kartě diagnostiky výsledků zobrazuje vzor, který může vysvětlují nespojitost vaše data.

    ![výsledek](./media/app-insights-analytics-diagnostics/result.png)
 
    Text zobrazuje hodnoty dimenze, které se zobrazují ke korelaci s k posunu. V tomto příkladu je spojen s určité žádosti a verzí určitého prohlížeče.

    Všimněte si také dvě součásti grafu se filtr true a false. Komponentu false ukazuje beze změny trendu. Jinými slovy se nezměnila ve výsledcích telemetrie, pokud jsou vyloučeny problematické kombinace dimenzí, které má identifikovat diagnostiky. Naopak zobrazí výsledky v rámci této kombinace výrazné změny z oblasti zvýrazněná šetření. Ukazuje to, že diagnostiky nalezl kombinace vlastnosti, která vysvětluje změny.

4.  Pokud vzor je komplexní, budete muset pozastavte ukazatel myši nad **Zobrazit vše** zobrazíte dimenze.

    ![zobrazit vše](./media/app-insights-analytics-diagnostics/show-all.png)
 
5.  V případě, že diagnostiky najde žádné významné vzor k oznamování, budou uvedeny stránce žádné výsledky. V tomto okamžiku můžete změnit svůj dotaz. Například můžete zúžit časové rozmezí a přihrádkování v dotazu Analytics pro další analýzu a potenciálně lepší výsledky.

Díky znalosti, že konkrétní stránky vašeho webu došlo k problému v určitého prohlížeče, teď můžete přejít přímo na stránku problém a prozkoumat nedávné změny.

## <a name="try-the-demo"></a>Vyzkoušet ukázkovou verzi

[Kliknutím sem zobrazíte ukázka](https://analytics.applicationinsights.io/demo?q=H4sIAAAAAAAAA3VSTY%2FTQAy991dYPXWlLf0QIO2KIiGWA3duiMPsxEnMzhe2p6WIH48nVUsuGylRNPOe3%2FOzN5vFZgPfRhL4VZHPIGM%2BCdgHdESgpMjOKx0RnsgNKYuSF%2BjRaWUE7xKMGIoBgTpMSv2Z0jBxOWc1QBWEPjM4EMUCP2uc0A3x8E5HKMi%2BEQNC7oHRbIgKdJWdUk5vmr9PvdkArildit%2Fcrk0lBDjnyhBzk%2FKVxdTy0QhNY6RhDPYqdlCy9XMV96NjBZc68IH8y6Tzuf01iZxeIZ%2FI5DqMOYmaQQRXNUdz6qGb5WOdSKEXnOozHtEFK%2Bh0qnq5YQzGF9DcoinoqbcigkO0NOZRNGOZaaBkMuat5xznFOtULKhG%2BdrGlVDhy%2B8SMlsETV8dD6gTd0YrbsBrFq6U1v%2Filv4C%2FsJpRJuwUrQTZ0P7eIDOHLeD1X67e7%2Fe7dbbB9htH%2Ffbu4vQDfvhFez%2B8a1h%2F1f3VSy%2BJ4Ol1oN8X4qN0qMZWv44HJanzKFLeJIltKcRpcbomP7gbHNkdV2Xe1uqO3g%2BwzOl1c3PvbmMlC7KjKlry2GX0w4s%2FgFoo5%2BhBAMAAA%3D%3D&timespan=PT24H) na ukázková data.

## <a name="how-it-works"></a>Jak to funguje

Inteligentní diagnostiky používá nepodporovaný algoritmus učení pokročilé bez dohledu počítače na základě [DiffPatterns](app-insights-analytics-reference.md) operaci. Vypadá to candidate vzorů, které mohou vysvětlit, změny dat. Analyzuje dopad kandidáti na metriku a zobrazuje vzor, nejlépe koreluje s změnu.

## <a name="no-diagnostic-points"></a>Žádné diagnostické body?

Inteligentní diagnostiky funguje pouze pokud jsou splněny následující kritéria:

 * Inteligentní nastavení diagnostiky je zapnutá. Podívejte se do části na ikonu nastavení v Analytics.
 * Je vybrána možnost Inteligentní diagnostiky v nastavení analýzy. 
 * Časová osa: osy x grafu musí být typu `datetime`.
 * Řádek nebo oblast grafu: Diagnostics lze použít pouze tyto typy grafu. Použití `| render timechart` nebo `| render areachart` na konci tohoto dotazu; nebo vyberte řádek nebo plošný graf z rozevíracího seznamu výběru.
 * Nespojitost: Musí být významné nespojitost v datech.
 * Dostatečná body k analýze.
 * Víc než jeden shrnují klauzule dotazu.
 * Žádné projektu klauzuli, která obsahuje název definice před klauzuli summarize.

 
 ## <a name="related-articles"></a>Související články

 * [Analýza kurzu](app-insights-analytics-tour.md)
 * [Inteligentní detekce](app-insights-proactive-diagnostics.md) automaticky upozorňuje na problémy s výkonem.