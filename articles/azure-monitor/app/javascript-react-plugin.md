---
title: Reakce modulu plug-in pro Application Insights JavaScript SDK
description: Jak nainstalovat a používat modul plug-in reakce pro Application Insights JavaScript SDK
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: 3a11f77384c520bed9824841269be4ad998adba4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90056196"
---
# <a name="react-plugin-for-application-insights-javascript-sdk"></a>Reakce modulu plug-in pro Application Insights JavaScript SDK

Reakce modulu plug-in pro sadu Application Insights JavaScript SDK umožňuje:

- Sledování změn tras
- Reakce na komponenty s použitím údajů

## <a name="getting-started"></a>Začínáme

Nainstalovat balíček npm:

```bash

npm install @microsoft/applicationinsights-react-js

```

## <a name="basic-usage"></a>Základní použití

```javascript
import React from 'react';
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ReactPlugin, withAITracking } from '@microsoft/applicationinsights-react-js';
import { createBrowserHistory } from "history";

const browserHistory = createBrowserHistory({ basename: '' });
var reactPlugin = new ReactPlugin();
var appInsights = new ApplicationInsights({
    config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [reactPlugin],
        extensionConfig: {
          [reactPlugin.identifier]: { history: browserHistory }
        }
    }
});
appInsights.loadAppInsights();

// To instrument various React components usage tracking, apply the `withAITracking` higher-order
// component function.

class MyComponent extends React.Component {
    ...
}

export default withAITracking(reactPlugin,appInsights, MyComponent);

```

## <a name="configuration"></a>Konfigurace

| Name    | Výchozí | Description                                                                                                    |
|---------|---------|----------------------------------------------------------------------------------------------------------------|
| historie | null    | Reagovat na historii směrovače. Další informace najdete v dokumentaci k balíčku s informacemi o [směrovači](https://reactrouter.com/web/api/history). Pokud se chcete dozvědět, jak přistupovat k objektu History mimo součásti, přečtěte si [Nejčastější dotazy k reakci – směrovač](https://github.com/ReactTraining/react-router/blob/master/FAQ.md#how-do-i-access-the-history-object-outside-of-components) .    |

### <a name="react-components-usage-tracking"></a>Reakce na sledování využití komponent

Chcete-li instrumentovat různé reakce na používání komponent, použijte `withAITracking` funkci komponenty s vyšším pořadím.

Měří čas od `ComponentDidMount` události prostřednictvím `ComponentWillUnmount` události. Aby to bylo možné přesnější, odečte čas, kdy byl uživatel nečinný `React Component Engaged Time = ComponentWillUnmount timestamp - ComponentDidMount timestamp - idle time` .

Pokud chcete zobrazit tuto metriku v Azure Portal potřebujete přejít na prostředek Application Insights, vybrat kartu metriky a nakonfigurovat prázdné grafy tak, aby zobrazovaly vlastní název metriky. doba trvání provozu komponenty (sekundy), vyberte agregace (součet, průměr atd.) metriky a použijte rozdělení "název součásti".

![Snímek obrazovky grafu zobrazující vlastní metriku "reakce na dobu trvání komponent (sekundy)" děleno "název součásti"](./media/javascript-react-plugin/chart.png)

Můžete také spustit vlastní dotazy a rozdělit Application Insights data a vygenerovat sestavy a vizualizace podle vašich požadavků. V Azure Portal přejděte na prostředek Application Insights, v horní nabídce karty Přehled vyberte Analytics a spusťte dotaz.

![Snímek obrazovky s vlastními výsledky dotazu metriky](./media/javascript-react-plugin/query.png)

> [!NOTE]
> Může trvat až 10 minut, než se nové vlastní metriky zobrazí na webu Azure Portal.

## <a name="sample-app"></a>Ukázková aplikace

Podívejte se na [ukázku reakce Application Insights](https://github.com/Azure-Samples/application-insights-react-demo).

## <a name="next-steps"></a>Další kroky

- Další informace o sadě JavaScript SDK naleznete v dokumentaci k [sadě sdk Application Insights JavaScript](javascript.md).
- Další informace o dotazovacím jazyku Kusto a dotazování na data v Log Analytics najdete v tématu [Přehled dotazů protokolu](../../azure-monitor/log-query/log-query-overview.md).
