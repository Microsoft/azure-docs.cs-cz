---
title: Prozkoumání HockeyApp dat v Azure Application Insights | Microsoft Docs
description: Analyzujte využití a výkon vaší aplikace Azure pomocí Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 03/30/2017
ms.openlocfilehash: b14cd38a1db6804a00883ded0b38511fa46c3a52
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819577"
---
# <a name="exploring-hockeyapp-data-in-application-insights"></a>Zkoumání HockeyApp dat v Application Insights

> [!NOTE]
> HockeyApp již není pro nové aplikace k dispozici. Existující nasazení HockeyApp budou fungovat i nadále. Visual Studio App Center je teď doporučenou službou od Microsoftu, která sleduje nové mobilní aplikace. [Naučte se, jak nastavit aplikace pomocí App Center a Application Insights](../../azure-monitor/learn/mobile-center-quickstart.md).

[HockeyApp](https://azure.microsoft.com/services/hockeyapp/) je služba pro monitorování živých desktopových a mobilních aplikací. Z HockeyApp můžete odeslat vlastní a sledovací telemetrii, abyste mohli monitorovat využití a pomáhat při diagnostice (Kromě získání dat o chybách). Tento proud telemetrie se dá dotazovat pomocí výkonné [analytické](../../azure-monitor/app/analytics.md) funkce služby [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Kromě toho můžete [exportovat vlastní a sledovací telemetrii](export-telemetry.md). Pokud chcete tyto funkce povolit, nastavte most, který přenáší HockeyApp vlastní data do Application Insights.

## <a name="the-hockeyapp-bridge-app"></a>Aplikace HockeyApp Bridge
HockeyApp Bridge aplikace je základní funkcí, která umožňuje přístup k HockeyApp vlastní a sledovací telemetrii v Application Insights prostřednictvím funkcí analýzy a průběžného exportu. Události vlastního a trasování shromážděné službou HockeyApp po vytvoření aplikace HockeyApp Bridge budou z těchto funkcí dostupné. Pojďme se podívat, jak nastavit jednu z těchto aplikací mostu.

V HockeyApp otevřete nastavení účtu a [tokeny API](https://rink.hockeyapp.net/manage/auth_tokens). Buď vytvořte nový token, nebo ho znovu použijte. Minimální požadovaná oprávnění jsou jen pro čtení. Pořídit kopii tokenu rozhraní API.

![Získání tokenu rozhraní API HockeyApp](./media/hockeyapp-bridge-app/01.png)

Otevřete portál Microsoft Azure a [vytvořte prostředek Application Insights](../../azure-monitor/app/create-new-resource.md ). Nastavte typ aplikace na "HockeyApp Bridge Application":

![Nový prostředek Application Insights](./media/hockeyapp-bridge-app/02.png)

Nemusíte nastavovat název – ta se automaticky nastaví z HockeyApp názvu.

Zobrazí se pole mostu HockeyApp. 

![Zadat pole mostu](./media/hockeyapp-bridge-app/03.png)

Zadejte token HockeyApp, který jste si poznamenali dříve. Tato akce naplní rozevírací nabídku "aplikace HockeyApp" všemi vašimi aplikacemi HockeyApp. Vyberte ten, který chcete použít, a vyplňte zbývající část polí. 

Otevřete nový prostředek. 

Všimněte si, že se data začnou spouštět.

![Application Insights prostředek čeká na data.](./media/hockeyapp-bridge-app/04.png)

A to je vše! Vlastní a trasovací data shromážděná v HockeyApp aplikaci od tohoto okamžiku jsou teď k dispozici také v části analýza a průběžné exportování funkcí Application Insights.

Podíváme se na každou z těchto funkcí, která je teď k dispozici.

## <a name="analytics"></a>Analýzy
Analýza je výkonný nástroj pro ad hoc dotazování na vaše data, což vám umožní diagnostikovat a analyzovat vaši telemetrii a rychle zjistit hlavní příčiny a vzory.

![Analýzy](./media/hockeyapp-bridge-app/05.png)

* [Další informace o analýze](../../azure-monitor/log-query/get-started-portal.md)

## <a name="continuous-export"></a>Průběžný export
Průběžný export umožňuje exportovat data do kontejneru Azure Blob Storage. To je velmi užitečné, pokud potřebujete uchovávat data déle, než je doba uchování, kterou momentálně nabízí Application Insights. Data v úložišti objektů blob můžete uchovávat, zpracovávat je do SQL Database nebo své preferované řešení datového skladu.

[Další informace o průběžném exportu](export-telemetry.md)

## <a name="next-steps"></a>Další kroky
* [Použití analýz na vaše data](../../azure-monitor/log-query/get-started-portal.md)

