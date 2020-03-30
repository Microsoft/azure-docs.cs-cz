---
title: Přidání panelu do uživatelského uživatelského nastavení řešení vzdáleného monitorování – Azure | Dokumenty společnosti Microsoft
description: Tento článek ukazuje, jak přidat nový panel do řídicího panelu ve webovém uživatelském uživatelském nastavení akcelerátoru řešení vzdáleného monitorování.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/05/2018
ms.topic: conceptual
ms.openlocfilehash: 3b855c3bed75945f44b55463bdacd049b7930aa7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "61447059"
---
# <a name="add-a-custom-panel-to-the-dashboard-in-the-remote-monitoring-solution-accelerator-web-ui"></a>Přidání vlastního panelu do řídicího panelu ve webovém uživatelském uživatelském uživatelském uživatelském panelu akcelerátoru řešení vzdáleného monitorování

Tento článek ukazuje, jak přidat nový panel na stránku řídicího panelu ve webovém uživatelském uživatelském nastavení akcelerátoru řešení vzdáleného monitorování. Článek popisuje:

- Jak připravit prostředí místního rozvoje.
- Jak přidat nový panel na stránku řídicího panelu ve webovém uživatelském uživatelském nastavení.

Panel příkladu v tomto článku se zobrazí na existující stránce řídicího panelu.

## <a name="prerequisites"></a>Požadavky

Chcete-li provést kroky v tomto návodu, potřebujete v místním vývojovém počítači nainstalovaný následující software:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Než začnete

Než budete pokračovat, měli byste dokončit kroky v článku Přidat vlastní stránku do článku [webového uživatelského uživatelského nastavení akcelerátoru řešení vzdáleného monitorování.](iot-accelerators-remote-monitoring-customize-page.md)

## <a name="add-a-panel"></a>Přidat panel

Chcete-li přidat panel do webového uživatelského uživatelského nastavení, musíte přidat zdrojové soubory, které panel definují, a pak upravit řídicí panel tak, aby se panel zobrazil.

### <a name="add-the-new-files-that-define-the-panel"></a>Přidání nových souborů, které definují panel

Chcete-li začít, **src/návod/komponenty/stránky/řídicí panel/panely/examplePanel** složka obsahuje soubory, které definují panel, včetně:

**examplePanel.js**

[!code-javascript[Example panel](~/remote-monitoring-webui/src/walkthrough/components/pages/dashboard/panels/examplePanel/examplePanel.js?name=panel "Example panel")]

Zkopírujte **složku src/walkthrough/components/pages/dashboard/panels/examplePanel** do složky **src/components/pages/dashboard/panels.**

Přidejte následující export do souboru **src/walkthrough/components/pages/dashboard/panels/index.js:**

```js
export * from './examplePanel';
```

### <a name="add-the-panel-to-the-dashboard"></a>Přidání panelu na řídicí panel

Upravte **src/components/pages/dashboard/dashboard.js** a přidejte panel.

Přidejte panel příkladu do seznamu importů z panelů:

```js
import {
  OverviewPanel,
  AlertsPanel,
  TelemetryPanel,
  AnalyticsPanel,
  MapPanel,
  transformTelemetryResponse,
  chartColorObjects,
  ExamplePanel
} from './panels';
```

Přidejte do mřížky v obsahu stránky následující definici buňky:

```js
          <Cell className="col-2">
            <ExamplePanel t={t} />
          </Cell>
```

## <a name="test-the-flyout"></a>Otestujte informační rámeček

Pokud webové uživatelské uživatelské prostředí ještě není spuštěno místně, spusťte v kořenovém adresáři místní kopie úložiště následující příkaz:

```cmd/sh
npm start
```

Předchozí příkaz spustí ui místně [http://localhost:3000/dashboard](http://localhost:3000/dashboard)na . Přejděte na stránku **Řídicí panel** a zobrazte nový panel.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o dostupných prostředcích, které vám pomohou přidat nebo přizpůsobit řídicí panely ve webovém uživatelském rozhraní v akcelerátoru řešení vzdáleného monitorování.

Další rámcové informace o akcelerátoru řešení vzdáleného monitorování naleznete v [tématu Architektura vzdáleného monitorování](iot-accelerators-remote-monitoring-sample-walkthrough.md).
