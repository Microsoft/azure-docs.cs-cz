---
title: Přidat navýšením průběžné řešení vzdálené monitorování uživatelské rozhraní – Azure | Dokumentace Microsoftu
description: Tento článek ukazuje, jak přidat nové předem za běhu na stránce v vzdálené monitorování řešení akcelerátoru webového uživatelského rozhraní.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/05/2018
ms.topic: conceptual
ms.openlocfilehash: 9ba58ca887332d2ea224320951b25031cacbef0d
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49094566"
---
# <a name="add-a-custom-fly-out-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Přidat vlastní průběžné out do vzdáleného monitorování řešení akcelerátoru webového uživatelského rozhraní

Tento článek ukazuje, jak přidat nové předem za běhu na stránku ve vzdálené monitorování řešení akcelerátoru webového uživatelského rozhraní. Tento článek popisuje:

- Jak připravit místní vývojové prostředí.
- Postup přidání nové průběžné mimo stránku ve webovém uživatelském rozhraní.

V příkladu za běhu na více instancí v tomto článku se zobrazí na stránce s mřížkou, který [přidat vlastní mřížky do vzdáleného monitorování řešení akcelerátoru webového uživatelského rozhraní](iot-accelerators-remote-monitoring-customize-grid.md) článek ukazuje, jak přidat.

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v této příručce s postupy, musíte na svém místním vývojovém počítači nainstalovaný následující software:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Než začnete

Než budete pokračovat, by měl proveďte kroky v následujících článcích:

- [Přidat vlastní stránky pro vzdálené monitorování řešení akcelerátoru webového uživatelského rozhraní](iot-accelerators-remote-monitoring-customize-page.md).
- [Přidat vlastní služby do vzdáleného monitorování řešení akcelerátoru webového uživatelského rozhraní](iot-accelerators-remote-monitoring-customize-service.md)
- [Přidat vlastní mřížky do vzdáleného monitorování řešení akcelerátoru webového uživatelského rozhraní](iot-accelerators-remote-monitoring-customize-grid.md)

## <a name="add-a-fly-out"></a>Přidat za běhu out

Přidat za běhu out do webového uživatelského rozhraní, musíte přidat zdrojové soubory, které definují průběžné out a změnit některé existující soubory ve webovém uživatelském rozhraní používající nové komponenty.

### <a name="add-the-new-files-that-define-the-fly-out"></a>Přidat nové soubory, které definují mimo za běhu

Abyste mohli začít, **src/návod/součásti/stránek/pageWithFlyout/kontextové nabídky/exampleFlyout** složka obsahuje soubory, které definují průběžné out:

**exampleFlyout.container.js**

[!code-javascript[Example fly-out container](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.container.js?name=flyoutcontainer "Example fly-out container")]

**exampleFlyout.js**

[!code-javascript[Example fly-out](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.js?name=flyout "Example fly-out")]

Kopírovat **src/návod/součásti/stránek/pageWithFlyout/kontextové nabídky** složku **src/součásti/stránek/příklad** složky.

### <a name="add-the-fly-out-to-the-page"></a>Přidat vzdálené za běhu na stránku

Upravit **src/components/pages/example/basicPage.js** přidat vzdálené za běhu.

Přidat **Btn** pro importy z **součásti/shared** a přidejte importy pro **svgs** a **ExampleFlyoutContainer**:

```js
import {
  AjaxError,
  ContextMenu,
  PageContent,
  RefreshBar,
  Btn
} from 'components/shared';
import { ExampleGrid } from './exampleGrid';
import { svgs } from 'utilities';
import { ExampleFlyoutContainer } from './flyouts/exampleFlyout';
```

Přidat **const** definice **closedFlyoutState** a přidejte ho do stavu v konstruktoru:

```js
const closedFlyoutState = { openFlyoutName: undefined };

export class BasicPage extends Component {
  constructor(props) {
    super(props);
    this.state = { contextBtns: null, closedFlyoutState };
  }
```

Přidání následujících funkcí, které mají **šablonou základní** třídy:

```js
  closeFlyout = () => this.setState(closedFlyoutState);

  openFlyout = (name) => () => this.setState({ openFlyoutName: name });
```

Přidejte následující **const** definice **vykreslení** funkce:

```js
    const { openFlyoutName } = this.state;

    const isExampleFlyoutOpen = openFlyoutName === 'example';
```

Přidání tlačítka pro otevření předem za běhu do kontextové nabídky:

```js
      <ContextMenu key="context-menu">
        {this.state.contextBtns}
        <Btn svg={svgs.reconfigure} onClick={this.openFlyout('example')}>{t('walkthrough.pageWithFlyout.open')}</Btn>
      </ContextMenu>,
```

Přidáte nějaký text a rozevírací kontejner pro obsah stránky:

```js
      <PageContent className="basic-page-container" key="page-content">
        {t('walkthrough.pageWithFlyout.pageBody')}
        { isExampleFlyoutOpen && <ExampleFlyoutContainer onClose={this.closeFlyout} /> }
        <RefreshBar refresh={fetchData} time={lastUpdated} isPending={isPending} t={t} />
        {!!error && <AjaxError t={t} error={error} />}
        {!error && <ExampleGrid {...gridProps} />}
      </PageContent>
```

## <a name="test-the-fly-out"></a>Test za běhu out

Pokud webové uživatelské rozhraní není spuštěná místně, spusťte následující příkaz v kořenové složce místní kopie úložiště:

```cmd/sh
npm start
```

Předchozí příkaz se spustí místně na uživatelské rozhraní [ http://localhost:3000/dashboard ](http://localhost:3000/dashboard). Přejděte na **příklad** stránky a klikněte na tlačítko **otevřít informační rámeček**.

## <a name="next-steps"></a>Další postup

V tomto článku jste se dozvěděli o prostředky dostupné pro přidání nebo přizpůsobit stránky ve webovém uživatelském rozhraní v akcelerátoru řešení vzdáleného monitorování.

Nyní jste definovali za běhu na více instancí na stránce, dalším krokem je [přidat ve vzdálené monitorování řešení akcelerátoru webového uživatelského rozhraní na řídicí panel](iot-accelerators-remote-monitoring-customize-panel.md).

Další koncepční informace o akcelerátoru řešení vzdáleného monitorování najdete v tématu [architektura vzdáleného monitorování](iot-accelerators-remote-monitoring-sample-walkthrough.md).
