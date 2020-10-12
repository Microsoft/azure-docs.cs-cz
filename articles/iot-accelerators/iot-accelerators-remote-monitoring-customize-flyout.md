---
title: Přidání informačního rámečku do uživatelského rozhraní řešení vzdáleného monitorování – Azure | Microsoft Docs
description: V tomto článku se dozvíte, jak přidat nový informační rámeček na stránce ve webovém uživatelském rozhraní akcelerátoru řešení vzdáleného monitorování.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/05/2018
ms.topic: conceptual
ms.custom: devx-track-js
ms.openlocfilehash: afd8c5b01b9f36606bf6bd43fbed747ca83ba320
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91318411"
---
# <a name="add-a-custom-flyout-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Přidat vlastní informační rámeček do webového uživatelského rozhraní akcelerátoru řešení vzdáleného monitorování

V tomto článku se dozvíte, jak přidat nový informační rámeček na stránku ve webovém uživatelském rozhraní akcelerátoru řešení vzdáleného monitorování. Článek popisuje:

- Jak připravit místní vývojové prostředí.
- Postup přidání nového informačního panelu na stránku ve webovém uživatelském rozhraní

Vzorový informační rámeček v tomto článku se zobrazí na stránce s mřížkou, kterou článek [Přidání vlastní mřížky do webového uživatelského rozhraní akcelerátoru řešení vzdáleného monitorování](iot-accelerators-remote-monitoring-customize-grid.md) ukazuje, jak přidat.

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto průvodci, potřebujete na svém místním vývojovém počítači nainstalovaný následující software:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Než začnete

Než budete pokračovat, měli byste provést kroky v následujících článcích:

- [Přidejte vlastní stránku do webového uživatelského rozhraní akcelerátoru řešení vzdáleného monitorování](iot-accelerators-remote-monitoring-customize-page.md).
- [Přidání vlastní služby do webového uživatelského rozhraní akcelerátoru řešení vzdáleného monitorování](iot-accelerators-remote-monitoring-customize-service.md)
- [Přidání vlastní mřížky do webového uživatelského rozhraní akcelerátoru řešení vzdáleného monitorování](iot-accelerators-remote-monitoring-customize-grid.md)

## <a name="add-a-flyout"></a>Přidání informačního rámečku

Chcete-li přidat informační rámeček do webového uživatelského rozhraní, je třeba přidat zdrojové soubory, které definují informační rámeček, a změnit některé existující soubory tak, aby si webové uživatelské rozhraní věděli o nové součásti.

### <a name="add-the-new-files-that-define-the-flyout"></a>Přidejte nové soubory, které definují informační rámeček.

Chcete-li začít, složka **Src/názor/komponenty/Pages/pageWithFlyout/flyouts/exampleFlyout** obsahuje soubory, které definují informační rámeček:

**exampleFlyout.container.js**

[!code-javascript[Example flyout container](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.container.js?name=flyoutcontainer "Example flyout container")]

**exampleFlyout.js**

[!code-javascript[Example flyout](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.js?name=flyout "Example flyout")]

Zkopírujte složku **Src/názorného/Components/Pages/pageWithFlyout/flyouts** do složky **Src/Components/example** .

### <a name="add-the-flyout-to-the-page"></a>Přidání informačního panelu na stránku

Chcete-li přidat informační rámeček, upravte **Src/Components/Pages/example/basicPage.js** .

Přidejte **BTN** k importu z **komponent/Shared** a přidejte importy pro **svgs** a **ExampleFlyoutContainer**:

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

Přidejte definici **const** pro **closedFlyoutState** a přidejte ji do stavu v konstruktoru:

```js
const closedFlyoutState = { openFlyoutName: undefined };

export class BasicPage extends Component {
  constructor(props) {
    super(props);
    this.state = { contextBtns: null, closedFlyoutState };
  }
```

Do třídy **šablonou základní stránky** přidejte následující funkce:

```js
  closeFlyout = () => this.setState(closedFlyoutState);

  openFlyout = (name) => () => this.setState({ openFlyoutName: name });
```

Do funkce **Render** přidejte následující definice **const** :

```js
    const { openFlyoutName } = this.state;

    const isExampleFlyoutOpen = openFlyoutName === 'example';
```

Přidejte tlačítko pro otevření informačního panelu v místní nabídce:

```js
      <ContextMenu key="context-menu">
        {this.state.contextBtns}
        <Btn svg={svgs.reconfigure} onClick={this.openFlyout('example')}>{t('walkthrough.pageWithFlyout.open')}</Btn>
      </ContextMenu>,
```

Přidejte do obsahu stránky nějaký text a kontejner s plovoucím panelem:

```js
      <PageContent className="basic-page-container" key="page-content">
        {t('walkthrough.pageWithFlyout.pageBody')}
        { isExampleFlyoutOpen && <ExampleFlyoutContainer onClose={this.closeFlyout} /> }
        <RefreshBar refresh={fetchData} time={lastUpdated} isPending={isPending} t={t} />
        {!!error && <AjaxError t={t} error={error} />}
        {!error && <ExampleGrid {...gridProps} />}
      </PageContent>
```

## <a name="test-the-flyout"></a>Test informačního rámečku

Pokud webové uživatelské rozhraní ještě neběží, spusťte následující příkaz v kořenovém adresáři místní kopie úložiště:

```cmd/sh
npm start
```

Předchozí příkaz spustí uživatelské rozhraní lokálně na `http://localhost:3000/dashboard` . Přejděte na **příklad** stránky a klikněte na **otevřít informační rámeček**.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o dostupných zdrojích, které vám pomůžou přidávat nebo upravovat stránky ve webovém uživatelském rozhraní v akcelerátoru řešení vzdáleného monitorování.

Nyní jste na stránce definovali informační rámeček. dalším krokem je [Přidání panelu na řídicí panel ve webovém uživatelském rozhraní akcelerátoru řešení vzdáleného monitorování](iot-accelerators-remote-monitoring-customize-panel.md).

Další koncepční informace o akcelerátoru řešení vzdáleného monitorování najdete v tématu [architektura vzdáleného monitorování](iot-accelerators-remote-monitoring-sample-walkthrough.md).
