---
title: Přidání informačního rámečku do uživatelského uživatelského zařízení řešení vzdáleného monitorování – Azure | Dokumenty společnosti Microsoft
description: Tento článek ukazuje, jak přidat nový informační rámeček na stránku ve webovém uživatelském uživatelském uživatelském uživatelském uživatelském zařízení akcelerátoru řešení vzdáleného monitorování.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/05/2018
ms.topic: conceptual
ms.openlocfilehash: ccb1a7ff6abbc68f42c7632a8ba7a392b2c48794
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "61447110"
---
# <a name="add-a-custom-flyout-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Přidání vlastního informačního rámečku do webového uživatelského uživatelského uživatelského nastavení akcelerátoru řešení vzdáleného monitorování

Tento článek ukazuje, jak přidat nový informační rámeček na stránku ve webovém uživatelském uživatelském uživatelském uživatelském uživatelském zařízení akcelerátoru řešení vzdáleného monitorování. Článek popisuje:

- Jak připravit prostředí místního rozvoje.
- Jak přidat nový informační rámeček na stránku ve webovém uživatelském uživatelském tlačítko.

Ukázkový informační rámeček v tomto článku se zobrazí na stránce s mřížkou, kterou přidáte vlastní mřížku do článku s postupem webového uživatelského [panelu akcelerátoru řešení vzdáleného monitorování,](iot-accelerators-remote-monitoring-customize-grid.md) ukazuje, jak přidat.

## <a name="prerequisites"></a>Požadavky

Chcete-li provést kroky v tomto návodu, potřebujete v místním vývojovém počítači nainstalovaný následující software:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Než začnete

Než budete pokračovat, měli byste provést kroky v následujících článcích:

- [Přidejte vlastní stránku do webového uživatelského uživatelského uživatelského uživatelského nastavení akcelerátoru řešení vzdáleného monitorování](iot-accelerators-remote-monitoring-customize-page.md).
- [Přidání vlastní služby do webového uživatelského uživatelského uživatelského nastavení akcelerátoru řešení vzdáleného monitorování](iot-accelerators-remote-monitoring-customize-service.md)
- [Přidání vlastní mřížky do webového uživatelského uživatelského uživatelského nastavení akcelerátoru řešení vzdáleného monitorování](iot-accelerators-remote-monitoring-customize-grid.md)

## <a name="add-a-flyout"></a>Přidání informačního rámečku

Chcete-li přidat informační rámeček do webového uživatelského uživatelského nastavení, je třeba přidat zdrojové soubory, které definují informační rámeček, a upravit některé existující soubory tak, aby webové uživatelské uživatelské uživatelské tlačítko bylo informováno o nové součásti.

### <a name="add-the-new-files-that-define-the-flyout"></a>Přidání nových souborů, které definují informační rámeček

Chcete-li začít, **src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout** složka obsahuje soubory, které definují informační rámeček:

**exampleFlyout.container.js**

[!code-javascript[Example flyout container](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.container.js?name=flyoutcontainer "Example flyout container")]

**exampleFlyout.js**

[!code-javascript[Example flyout](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.js?name=flyout "Example flyout")]

Zkopírujte složku **src/walkthrough/components/pages/pageWithFlyout/flyouts** do složky **src/components/pages/example.**

### <a name="add-the-flyout-to-the-page"></a>Přidání informačního rámečku na stránku

Chcete-li přidat informační rámeček, upravte **soubor src/components/pages/example/basicPage.js.**

Přidat **Btn** do importů z **komponent/shared** a přidat importy pro **svgs** a **ExampleFlyoutContainer**:

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

Přidejte **const** definice pro **closedFlyoutState** a přidejte jej do stavu v konstruktoru:

```js
const closedFlyoutState = { openFlyoutName: undefined };

export class BasicPage extends Component {
  constructor(props) {
    super(props);
    this.state = { contextBtns: null, closedFlyoutState };
  }
```

Do třídy **BasicPage** přidejte následující funkce:

```js
  closeFlyout = () => this.setState(closedFlyoutState);

  openFlyout = (name) => () => this.setState({ openFlyoutName: name });
```

Do **funkce** vykreslení přidejte následující definice **const:**

```js
    const { openFlyoutName } = this.state;

    const isExampleFlyoutOpen = openFlyoutName === 'example';
```

Přidáním tlačítka otevřete informační rámeček v místní nabídce:

```js
      <ContextMenu key="context-menu">
        {this.state.contextBtns}
        <Btn svg={svgs.reconfigure} onClick={this.openFlyout('example')}>{t('walkthrough.pageWithFlyout.open')}</Btn>
      </ContextMenu>,
```

Přidejte k obsahu stránky text a kontejner informačního rámečku:

```js
      <PageContent className="basic-page-container" key="page-content">
        {t('walkthrough.pageWithFlyout.pageBody')}
        { isExampleFlyoutOpen && <ExampleFlyoutContainer onClose={this.closeFlyout} /> }
        <RefreshBar refresh={fetchData} time={lastUpdated} isPending={isPending} t={t} />
        {!!error && <AjaxError t={t} error={error} />}
        {!error && <ExampleGrid {...gridProps} />}
      </PageContent>
```

## <a name="test-the-flyout"></a>Otestujte informační rámeček

Pokud webové uživatelské uživatelské prostředí ještě není spuštěno místně, spusťte v kořenovém adresáři místní kopie úložiště následující příkaz:

```cmd/sh
npm start
```

Předchozí příkaz spustí ui místně [http://localhost:3000/dashboard](http://localhost:3000/dashboard)na . Přejděte na stránku **Příklad** a klikněte na **Otevřít informační rámeček**.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o dostupných prostředcích, které vám pomohou přidat nebo přizpůsobit stránky ve webovém uživatelském rozhraní v akcelerátoru řešení vzdáleného monitorování.

Nyní jste definovali informační rámeček na stránce, dalším krokem je [přidání panelu do řídicího panelu v uživatelském uživatelském uživatelském uživatelském panelu akcelerátoru řešení vzdáleného monitorování](iot-accelerators-remote-monitoring-customize-panel.md).

Další rámcové informace o akcelerátoru řešení vzdáleného monitorování naleznete v [tématu Architektura vzdáleného monitorování](iot-accelerators-remote-monitoring-sample-walkthrough.md).
