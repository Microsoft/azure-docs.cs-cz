---
title: Přidání panelu do řešení vzdálené monitorování uživatelské rozhraní – Azure | Dokumentace Microsoftu
description: Tento článek ukazuje, jak přidat na řídicí panel ve vzdálené monitorování řešení akcelerátoru webové uživatelské rozhraní nového panelu.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/05/2018
ms.topic: conceptual
ms.openlocfilehash: 3b855c3bed75945f44b55463bdacd049b7930aa7
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/12/2018
ms.locfileid: "49165874"
---
# <a name="add-a-custom-panel-to-the-dashboard-in-the-remote-monitoring-solution-accelerator-web-ui"></a>Přidat vlastní panel na řídicím panelu vzdáleného monitorování řešení akcelerátoru webového uživatelského rozhraní

Tento článek ukazuje, jak přidat nového panelu na stránce řídicího panelu ve vzdálené monitorování řešení akcelerátoru webového uživatelského rozhraní. Tento článek popisuje:

- Jak připravit místní vývojové prostředí.
- Postup přidání nového panelu na stránce řídicího panelu ve webovém uživatelském rozhraní.

Panel příklad v tomto článku se zobrazuje na existující stránky řídicího panelu.

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v této příručce s postupy, musíte na svém místním vývojovém počítači nainstalovaný následující software:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Než začnete

By se měla dokončit kroky v [přidat vlastní stránky pro vzdálené monitorování řešení akcelerátoru webového uživatelského rozhraní](iot-accelerators-remote-monitoring-customize-page.md) článek, než budete pokračovat.

## <a name="add-a-panel"></a>Přidat panel

Přidání panelu do webového uživatelského rozhraní, budete muset přidat zdrojové soubory, které definují panelu a pak upravit na řídicím panelu zobrazí na panelu.

### <a name="add-the-new-files-that-define-the-panel"></a>Přidat nové soubory, které definují panelu

Abyste mohli začít, **src/návod/součásti/stránek/řídicí panel/panely/examplePanel** složka obsahuje soubory, které definují panelu, včetně:

**examplePanel.js**

[!code-javascript[Example panel](~/remote-monitoring-webui/src/walkthrough/components/pages/dashboard/panels/examplePanel/examplePanel.js?name=panel "Example panel")]

Kopírování **src/návod/součásti/stránek/řídicí panel/panely/examplePanel** složku **src/součásti/stránek/řídicí panel/panelů** složky.

Přidejte následující exportu **src/walkthrough/components/pages/dashboard/panels/index.js** souboru:

```js
export * from './examplePanel';
```

### <a name="add-the-panel-to-the-dashboard"></a>Přidání panelu na řídicí panel

Upravit **src/components/pages/dashboard/dashboard.js** přidání panelu.

Příklad panelu přidáte do seznamu importů z panelů:

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

Přidejte následující definice buňky do mřížky v obsahu stránky:

```js
          <Cell className="col-2">
            <ExamplePanel t={t} />
          </Cell>
```

## <a name="test-the-flyout"></a>Testování informační rámeček

Pokud webové uživatelské rozhraní není spuštěná místně, spusťte následující příkaz v kořenové složce místní kopie úložiště:

```cmd/sh
npm start
```

Předchozí příkaz se spustí místně na uživatelské rozhraní [ http://localhost:3000/dashboard ](http://localhost:3000/dashboard). Přejděte **řídicí panel** stránku, abyste zobrazili nový panel.

## <a name="next-steps"></a>Další postup

V tomto článku jste se dozvěděli o prostředky dostupné pro umožňují přidat nebo upravit řídicí panely ve webovém uživatelském rozhraní v akcelerátoru řešení vzdáleného monitorování.

Další koncepční informace o akcelerátoru řešení vzdáleného monitorování najdete v tématu [architektura vzdáleného monitorování](iot-accelerators-remote-monitoring-sample-walkthrough.md).
