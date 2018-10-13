---
title: Přidání vyskakovacího informačního rámečku do řešení vzdálené monitorování uživatelské rozhraní – Azure | Dokumentace Microsoftu
description: Tento článek ukazuje, jak přidat nový informační rámeček na stránce v vzdálené monitorování řešení akcelerátoru webového uživatelského rozhraní.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/05/2018
ms.topic: conceptual
ms.openlocfilehash: ccb1a7ff6abbc68f42c7632a8ba7a392b2c48794
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/12/2018
ms.locfileid: "49167422"
---
# <a name="add-a-custom-flyout-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Přidat vlastní rozevírací nabídka pro vzdálené monitorování řešení akcelerátoru webového uživatelského rozhraní

Tento článek ukazuje, jak přidat nový informační rámeček na stránku ve vzdálené monitorování řešení akcelerátoru webového uživatelského rozhraní. Tento článek popisuje:

- Jak připravit místní vývojové prostředí.
- Jak přidat nový informační rámeček stránku ve webovém uživatelském rozhraní.

Na stránce s mřížkou zobrazí informační rámeček příklad v tomto článku, který [přidat vlastní mřížky do vzdáleného monitorování řešení akcelerátoru webového uživatelského rozhraní](iot-accelerators-remote-monitoring-customize-grid.md) článek ukazuje, jak přidat.

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v této příručce s postupy, musíte na svém místním vývojovém počítači nainstalovaný následující software:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Než začnete

Než budete pokračovat, by měl proveďte kroky v následujících článcích:

- [Přidat vlastní stránky pro vzdálené monitorování řešení akcelerátoru webového uživatelského rozhraní](iot-accelerators-remote-monitoring-customize-page.md).
- [Přidat vlastní služby do vzdáleného monitorování řešení akcelerátoru webového uživatelského rozhraní](iot-accelerators-remote-monitoring-customize-service.md)
- [Přidat vlastní mřížky do vzdáleného monitorování řešení akcelerátoru webového uživatelského rozhraní](iot-accelerators-remote-monitoring-customize-grid.md)

## <a name="add-a-flyout"></a>Přidání vyskakovacího informačního rámečku

Přidání vyskakovacího informačního rámečku do webového uživatelského rozhraní, budete muset přidat zdrojové soubory, které definují informační rámeček a změnit některé existující soubory ve webovém uživatelském rozhraní používající nové komponenty.

### <a name="add-the-new-files-that-define-the-flyout"></a>Přidat nové soubory, které definují informační rámeček

Abyste mohli začít, **src/návod/součásti/stránek/pageWithFlyout/kontextové nabídky/exampleFlyout** složka obsahuje soubory, které definují vyskakovacího informačního rámečku:

**exampleFlyout.container.js**

[!code-javascript[Example flyout container](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.container.js?name=flyoutcontainer "Example flyout container")]

**exampleFlyout.js**

[!code-javascript[Example flyout](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.js?name=flyout "Example flyout")]

Kopírovat **src/návod/součásti/stránek/pageWithFlyout/kontextové nabídky** složku **src/součásti/stránek/příklad** složky.

### <a name="add-the-flyout-to-the-page"></a>Přidat na stránku informační rámeček

Upravit **src/components/pages/example/basicPage.js** přidáte informační rámeček.

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

Přidání tlačítka pro otevření informační rámeček do kontextové nabídky:

```js
      <ContextMenu key="context-menu">
        {this.state.contextBtns}
        <Btn svg={svgs.reconfigure} onClick={this.openFlyout('example')}>{t('walkthrough.pageWithFlyout.open')}</Btn>
      </ContextMenu>,
```

Přidejte nějaký text a kontejneru informační rámeček obsahu stránky:

```js
      <PageContent className="basic-page-container" key="page-content">
        {t('walkthrough.pageWithFlyout.pageBody')}
        { isExampleFlyoutOpen && <ExampleFlyoutContainer onClose={this.closeFlyout} /> }
        <RefreshBar refresh={fetchData} time={lastUpdated} isPending={isPending} t={t} />
        {!!error && <AjaxError t={t} error={error} />}
        {!error && <ExampleGrid {...gridProps} />}
      </PageContent>
```

## <a name="test-the-flyout"></a>Testování informační rámeček

Pokud webové uživatelské rozhraní není spuštěná místně, spusťte následující příkaz v kořenové složce místní kopie úložiště:

```cmd/sh
npm start
```

Předchozí příkaz se spustí místně na uživatelské rozhraní [ http://localhost:3000/dashboard ](http://localhost:3000/dashboard). Přejděte na **příklad** stránky a klikněte na tlačítko **otevřít informační rámeček**.

## <a name="next-steps"></a>Další postup

V tomto článku jste se dozvěděli o prostředky dostupné pro přidání nebo přizpůsobit stránky ve webovém uživatelském rozhraní v akcelerátoru řešení vzdáleného monitorování.

Nyní jste definovali vyskakovacího informačního rámečku na stránce, dalším krokem je [přidat ve vzdálené monitorování řešení akcelerátoru webového uživatelského rozhraní na řídicí panel](iot-accelerators-remote-monitoring-customize-panel.md).

Další koncepční informace o akcelerátoru řešení vzdáleného monitorování najdete v tématu [architektura vzdáleného monitorování](iot-accelerators-remote-monitoring-sample-walkthrough.md).
