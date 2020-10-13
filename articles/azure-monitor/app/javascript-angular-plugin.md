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
ms.openlocfilehash: 152ba4b1c8a4e09db0bce759f5b67f577ec5d584
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91843808"
---
# <a name="angular-plugin-for-application-insights-javascript-sdk"></a>Úhlový modul plug-in pro Application Insights JavaScript SDK

Úhlový modul plug-in pro sadu Application Insights JavaScript SDK umožňuje:

- Sledování změn směrovače
- Úhlové statistiky využití komponent

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
import { Component, OnInit } from '@angular/core';
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { AngularPlugin, AngularPluginService } from '@microsoft/applicationinsights-angularplugin-js';
import { Router } from '@angular/router';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit {
    private appInsights;
    constructor(
        private router: Router,
        private angularPluginService: AngularPluginService 
    ){
        var angularPlugin = new AngularPlugin();
        this.angularPluginService.init(angularPlugin, this.router);
        this.appInsights = new ApplicationInsights({ config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [angularPlugin],
        extensionConfig: {
            [angularPlugin.identifier]: { router: this.router }
        }
        } });
    }

    ngOnInit() {
        this.appInsights.loadAppInsights();
    }
}

```

Chcete-li použít `trackMetric` metodu ke sledování úhlů použití komponenty, přidejte `AngularPluginService` jako poskytovatele v seznamu zprostředkovatelé v `app.module.ts` souboru.

```js
import { AngularPluginService } from '@microsoft/applicationinsights-angularplugin-js';

@NgModule({
    ...
  providers: [ AngularPluginService ],
})
export class AppModule { }
```

Chcete-li sledovat životnost komponenty, zavolejte `trackMetric` v metodě této `ngOnDestroy` součásti. Po zničení komponenty se aktivuje `trackMetric` událost, která pošle čas, který uživatel nechali na stránce, a název součásti.

```js
import { Component, OnDestroy, HostListener } from '@angular/core';
import { AngularPluginService } from '@microsoft/applicationinsights-angularplugin-js';

@Component({
  selector: 'app-test',
  templateUrl: './test.component.html',
  styleUrls: ['./test.component.css']
})
export class TestComponent implements OnDestroy {

  constructor(private angularPluginService: AngularPluginService) {}

  @HostListener('window:beforeunload')
  ngOnDestroy() {
    this.angularPluginService.trackMetric();
  }
}
```

## <a name="next-steps"></a>Další kroky

- Další informace o sadě JavaScript SDK naleznete v dokumentaci k [sadě SDK pro Application Insights JavaScript](javascript.md) .
- [Úhlový modul plug-in GitHubu](https://github.com/microsoft/ApplicationInsights-JS/tree/master/extensions/applicationinsights-angularplugin-js)