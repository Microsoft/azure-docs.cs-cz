---
title: Modul plug-in inreakce pro Application Insights JavaScript SDK
description: Jak nainstalovat a používat nativní modul plug-in reakce pro Application Insights JavaScript SDK
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 0c122a21fc7149e9943825cafbed77069b7919f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100593596"
---
# <a name="native-react-plugin-for-application-insights-javascript-sdk"></a>Modul plug-in inreakce pro Application Insights JavaScript SDK

Nativní informující modul plug-in pro Application Insights JavaScript SDK shromažďuje informace o zařízení, ve výchozím nastavení tento modul plug-in automaticky shromažďuje:

- **Jedinečné ID zařízení** (označované také jako ID instalace)
- **Název modelu zařízení** (například iPhone X, Samsung Galaxy skládání, Huawei P30 pro atd.)
- **Typ zařízení** (například sluchátko, tablet atd.)

## <a name="requirements"></a>Požadavky

Musíte používat verzi >= 2.0.0 `@microsoft/applicationinsights-web` . Tento modul plug-in bude fungovat jenom v reakci – nativní aplikace. Nebude fungovat s aplikacemi, [které používají rozhraní poutavou Framework](https://docs.expo.io/), proto nebude fungovat s aplikací Create reagují v nativním režimu.

## <a name="getting-started"></a>Začínáme

Nainstalujte a propojte balíček s [informacemi o reakci-nativním zařízení](https://www.npmjs.com/package/react-native-device-info) . Udržujte `react-native-device-info` balíček v aktuálním stavu, abyste mohli shromažďovat nejnovější názvy zařízení pomocí vaší aplikace.

```zsh

npm install --save @microsoft/applicationinsights-react-native @microsoft/applicationinsights-web
npm install --save react-native-device-info
react-native link react-native-device-info

```

## <a name="initializing-the-plugin"></a>Inicializuje se modul plug-in.

Chcete-li použít tento modul plug-in, je nutné vytvořit modul plug-in a přidat ho jako `extension` do existující instance Application Insights.

```typescript
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ReactNativePlugin } from '@microsoft/applicationinsights-react-native';

var RNPlugin = new ReactNativePlugin();
var appInsights = new ApplicationInsights({
    config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [RNPlugin]
    }
});
appInsights.loadAppInsights();

```

## <a name="next-steps"></a>Další kroky

- Další informace o sadě JavaScript SDK naleznete v dokumentaci k [sadě sdk Application Insights JavaScript](javascript.md).
- Další informace o dotazovacím jazyku Kusto a dotazování na data v Log Analytics najdete v tématu [Přehled dotazů protokolu](../../azure-monitor/logs/log-query-overview.md).
