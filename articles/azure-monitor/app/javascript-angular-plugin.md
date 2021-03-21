---
title: Úhlový modul plug-in pro Application Insights JavaScript SDK
description: Jak nainstalovat a použít úhlový modul plug-in pro Application Insights JavaScript SDK.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: lagayhar
ms.openlocfilehash: d45d8bed328dc91dfeeabd6ce878074fa1218623
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101737014"
---
# <a name="angular-plugin-for-application-insights-javascript-sdk"></a>Úhlový modul plug-in pro Application Insights JavaScript SDK

Úhlový modul plug-in pro sadu Application Insights JavaScript SDK umožňuje:

- Sledování změn směrovače

> [!WARNING]
> Úhlový modul plug-in není kompatibilní s ECMAScript 3 (ES3).

## <a name="getting-started"></a>Začínáme

Nainstalovat balíček npm:

```bash
npm install @microsoft/applicationinsights-angularplugin-js
```

## <a name="basic-usage"></a>Základní použití

Nastavení instance Application Insights v součásti pro zadávání v aplikaci:

```js
import { Component } from '@angular/core';
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { AngularPlugin } from '@microsoft/applicationinsights-angularplugin-js';
import { Router } from '@angular/router';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
    constructor(
        private router: Router
    ){
        var angularPlugin = new AngularPlugin();
        const appInsights = new ApplicationInsights({ config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [angularPlugin],
        extensionConfig: {
            [angularPlugin.identifier]: { router: this.router }
        }
        } });
        appInsights.loadAppInsights();
    }
}
```

## <a name="next-steps"></a>Další kroky

- Další informace o sadě JavaScript SDK naleznete v dokumentaci k [sadě SDK pro Application Insights JavaScript](javascript.md) .
- [Úhlový modul plug-in GitHubu](https://github.com/microsoft/ApplicationInsights-JS/tree/master/extensions/applicationinsights-angularplugin-js)
