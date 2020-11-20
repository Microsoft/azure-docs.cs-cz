---
title: Reakce modulu plug-in pro Application Insights JavaScript SDK
description: Jak nainstalovat a používat modul plug-in reakce pro Application Insights JavaScript SDK
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: 3348654a83b6d0930d10e1f58e07455623b5861d
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94981081"
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

Inicializovat připojení k Application Insights:

```javascript
// AppInsights.js
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ReactPlugin } from '@microsoft/applicationinsights-react-js';
import { createBrowserHistory } from 'history';

const browserHistory = createBrowserHistory({ basename: '' });
const reactPlugin = new ReactPlugin();
const appInsights = new ApplicationInsights({
    config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [reactPlugin],
        extensionConfig: {
          [reactPlugin.identifier]: { history: browserHistory }
        }
    }
});
appInsights.loadAppInsights();
export { reactPlugin, appInsights };
```

Zabalte komponentu pomocí funkce komponenty s vyšším pořadím, aby na ni bylo možné Application Insights:

```javascript
import React from 'react';
import { withAITracking } from '@microsoft/applicationinsights-react-js';
import { reactPlugin, appInsights } from './AppInsights';

// To instrument various React components usage tracking, apply the `withAITracking` higher-order
// component function.

class MyComponent extends React.Component {
    ...
}

export default withAITracking(reactPlugin, appInsights, MyComponent);
```

## <a name="configuration"></a>Konfigurace

| Název    | Výchozí | Popis                                                                                                    |
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

## <a name="using-react-hooks"></a>Použití nereagujících zavěšení

[Reagující zavěšení](https://reactjs.org/docs/hooks-reference.html) jsou přístup k řízení stavu a životního cyklu v reakci aplikace, aniž by se musel spoléhat na komponenty s možností reakce na třídy. Modul plug-in pro Application Insights reakci poskytuje několik integrací, které fungují podobným způsobem jako při přístupu k komponentě vyššího řádu.

### <a name="using-react-context"></a>Použití reagující kontextu

Reakce na Application Insights jsou navržené tak, aby používaly [reagující kontext](https://reactjs.org/docs/context.html) jako obsahující aspekt. Chcete-li použít kontext, proveďte inicializaci Application Insights výše a pak importujte objekt kontextu:

```javascript
import React from "react";
import { AppInsightsContext } from "@microsoft/applicationinsights-react-js";
import { reactPlugin } from "./AppInsights";

const App = () => {
    return (
        <AppInsightsContext.Provider value={reactPlugin}>
            /* your application here */
        </AppInsightsContext.Provider>
    );
};
```

Tento zprostředkovatel kontextu zpřístupní Application Insights k dispozici jako `useContext` zavěšení v rámci všech podřízených komponent.

```javascript
import React from "react";
import { useAppInsightsContext } from "@microsoft/applicationinsights-react-js";

const MyComponent = () => {
    const appInsights = useAppInsightsContext();
    
    appInsights.trackMetric("Component 'MyComponent' is in use");
    
    return (
        <h1>My Component</h1>
    );
}
export default MyComponent;
```

### `useTrackMetric`

`useTrackMetric`Zavěšení replikuje funkce `withAITracking` součásti s vyšším pořadím, aniž by bylo nutné přidat další komponentu do struktury komponent. Zavěšení přebírá dva argumenty, nejdřív je instance Application Insights (která může být získána z `useAppInsightsContext` zavěšení), a identifikátor součásti pro sledování (například jeho název).

```javascript
import React from "react";
import { useAppInsightsContext, useTrackMetric } from "@microsoft/applicationinsights-react-js";

const MyComponent = () => {
    const appInsights = useAppInsightsContext();
    const trackComponent = useTrackMetric(appInsights, "MyComponent");
    
    return (
        <h1 onHover={trackComponent} onClick={trackComponent}>My Component</h1>
    );
}
export default MyComponent;
```

Bude fungovat podobně jako součást s vyšším pořadím, ale reaguje na zavěšení událostí životního cyklu, nikoli na životní cyklus součásti. Je-li nutné spustit na určitých interakcích, je nutné, aby se tento zavěšení explicitně zadal do událostí uživatele.

### `useTrackEvent`

Tento `useTrackEvent` zavěšení se používá ke sledování libovolné vlastní události, kterou může aplikace potřebovat sledovat, jako je například kliknutí na tlačítko nebo jiné volání rozhraní API. Používá dva argumenty, první je instance Application Insights (která se dá získat z `useAppInsightsContext` zavěšení) a název události.

```javascript
import React, { useState, useEffect } from "react";
import { useAppInsightsContext, useTrackEvent } from "@microsoft/applicationinsights-react-js";

const ProductCart = () => {
    const appInsights = useAppInsightsContext();
    const trackCheckout = useTrackEvent(appInsights, "Checkout");
    const trackCartUpdate = useTrackEvent(appInsights, "Cart Updated");
    const [cart, setCart] = useState([]);
    
    useEffect(() => {
        trackCartUpdate({ cartCount: cart.length });
    }, [cart]);
    
    const performCheckout = () => {
        trackCheckout();
        // submit data
    };
    
    return (
        <div>
            <ul>
                <li>Product 1 <button onClick={() => setCart([...cart, "Product 1"])}>Add to Cart</button>
                <li>Product 2 <button onClick={() => setCart([...cart, "Product 2"])}>Add to Cart</button>
                <li>Product 3 <button onClick={() => setCart([...cart, "Product 3"])}>Add to Cart</button>
                <li>Product 4 <button onClick={() => setCart([...cart, "Product 4"])}>Add to Cart</button>
            </ul>
            <button onClick={performCheckout}>Checkout</button>
        </div>
    );
}
export default MyComponent;
```

Při použití zavěšení lze datovou datovou část poskytnout pro přidání dalších dat do události, pokud je uložena v Application Insights.

## <a name="react-error-boundaries"></a>Reakce na hranice chyb

[Hranice chyb](https://reactjs.org/docs/error-boundaries.html) poskytují způsob, jak řádně zpracovat výjimku, když k ní dojde v reakci aplikace, a pokud taková chyba nastane, je pravděpodobně nutné protokolovat výjimku. Modul plug-in reakce pro Application Insights poskytuje komponentu hranice chyby, která při výskytu automaticky protokoluje chybu.

```javascript
import React from "react";
import { reactPlugin } from "./AppInsights";
import { AppInsightsErrorBoundary } from "@microsoft/applicationinsights-react-js";

const App = () => {
    return (
        <AppInsightsErrorBoundary onError={() => <h1>I believe something went wrong</h1>} appInsights={reactPlugin}>
            /* app here */
        </AppInsightsErrorBoundary>
    );
};
```

K `AppInsightsErrorBoundary` tomu vyžaduje předání dvou vlastností, `ReactPlugin` instance vytvořená pro aplikaci a komponentu, která má být vykreslena při výskytu chyby. Pokud dojde k neošetřené chybě, `trackException` je volána s informacemi poskytnutými na hranici chyby a zobrazí se `onError` komponenta.

## <a name="sample-app"></a>Ukázková aplikace

Podívejte se na [ukázku reakce Application Insights](https://github.com/Azure-Samples/application-insights-react-demo).

## <a name="next-steps"></a>Další kroky

- Další informace o sadě JavaScript SDK naleznete v dokumentaci k [sadě sdk Application Insights JavaScript](javascript.md).
- Další informace o dotazovacím jazyku Kusto a dotazování na data v Log Analytics najdete v tématu [Přehled dotazů protokolu](../../azure-monitor/log-query/log-query-overview.md).
