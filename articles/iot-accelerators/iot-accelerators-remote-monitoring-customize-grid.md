---
title: Přidat mřížku řešením vzdáleného monitorování uživatelské rozhraní – Azure | Dokumentace Microsoftu
description: Tento článek ukazuje, jak přidat nové ID skupiny na stránce v vzdálené monitorování řešení akcelerátoru webového uživatelského rozhraní.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/04/2018
ms.topic: conceptual
ms.openlocfilehash: 71f2164c9c419604c513261df7e1264060a2c374
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49094557"
---
# <a name="add-a-custom-grid-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Přidat vlastní mřížky do vzdáleného monitorování řešení akcelerátoru webového uživatelského rozhraní

V tomto článku se dozvíte, jak přidat nové mřížky na stránku ve vzdálené monitorování řešení akcelerátoru webového uživatelského rozhraní. Tento článek popisuje:

- Jak připravit místní vývojové prostředí.
- Postup přidání nové mřížky na stránku ve webovém uživatelském rozhraní.

Příklad tabulky v tomto článku se zobrazí data ze služby, který [přidat vlastní službu vzdáleného monitorování řešení akcelerátoru uživatelské rozhraní](iot-accelerators-remote-monitoring-customize-service.md) článek ukazuje, jak přidat.

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v této příručce s postupy, musíte na svém místním vývojovém počítači nainstalovaný následující software:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Než začnete

Než budete pokračovat, by měl proveďte kroky v následujících článcích:

- [Přidat vlastní stránky pro vzdálené monitorování řešení akcelerátoru webového uživatelského rozhraní](iot-accelerators-remote-monitoring-customize-page.md).
- [Přidat vlastní služby do vzdáleného monitorování řešení akcelerátoru webového uživatelského rozhraní](iot-accelerators-remote-monitoring-customize-service.md)

## <a name="add-a-grid"></a>Přidat mřížku

Pokud chcete přidat do mřížky do ve webovém uživatelském rozhraní, budete muset přidat zdrojové soubory, které definují mřížky a změnit některé existující soubory ve webovém uživatelském rozhraní používající nové komponenty.

### <a name="add-the-new-files-that-define-the-grid"></a>Přidat nové soubory, které definují mřížky

Abyste mohli začít, **src/návod/součásti/stránek/pageWithGrid/exampleGrid** složka obsahuje soubory, které definují mřížky:

**exampleGrid.js**

[!code-javascript[Example grid](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithGrid/exampleGrid/exampleGrid.js?name=grid "Example grid")]

**exampleGridConfig.js**

[!code-javascript[Example grid configuration](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithGrid/exampleGrid/exampleGridConfig.js?name=gridconfig "Example grid configuration")]

Kopírovat **src/návod/součásti/stránek/pageWithGrid/exampleGrid** složku **src/součásti/stránek/příklad** složky.

### <a name="add-the-grid-to-the-page"></a>Přidat na stránku mřížky

Upravit **src/components/pages/example/basicPage.container.js** takto k importu definic služeb:

```js
import { connect } from 'react-redux';
import { translate } from 'react-i18next';

import {
  epics as exampleEpics,
  getExamples,
  getExamplesError,
  getExamplesLastUpdated,
  getExamplesPendingStatus
} from 'store/reducers/exampleReducer';
import { BasicPage } from './basicPage';

// Pass the data
const mapStateToProps = state => ({
  data: getExamples(state),
  error: getExamplesError(state),
  isPending: getExamplesPendingStatus(state),
  lastUpdated: getExamplesLastUpdated(state)
});

// Wrap the dispatch method
const mapDispatchToProps = dispatch => ({
  fetchData: () => dispatch(exampleEpics.actions.fetchExamples())
});

export const BasicPageContainer = translate()(connect(mapStateToProps, mapDispatchToProps)(BasicPage));
```

Upravit **src/components/pages/example/basicPage.js** následovně Chcete-li přidat mřížky:

```js
// Copyright (c) Microsoft. All rights reserved.

import React, { Component } from 'react';

import {
  AjaxError,
  ContextMenu,
  PageContent,
  RefreshBar
} from 'components/shared';
import { ExampleGrid } from './exampleGrid';

import './basicPage.css';

export class BasicPage extends Component {
  constructor(props) {
    super(props);
    this.state = { contextBtns: null };
  }

  componentDidMount() {
    const { isPending, lastUpdated, fetchData } = this.props;
    if (!lastUpdated && !isPending) fetchData();
  }

  onGridReady = gridReadyEvent => this.gridApi = gridReadyEvent.api;

  onContextMenuChange = contextBtns => this.setState({ contextBtns });

  render() {
    const { t, data, error, isPending, lastUpdated, fetchData } = this.props;
    const gridProps = {
      onGridReady: this.onGridReady,
      rowData: isPending ? undefined : data || [],
      onContextMenuChange: this.onContextMenuChange,
      t: this.props.t
    };

    return [
      <ContextMenu key="context-menu">
        {this.state.contextBtns}
      </ContextMenu>,
      <PageContent className="basic-page-container" key="page-content">
        <RefreshBar refresh={fetchData} time={lastUpdated} isPending={isPending} t={t} />
        {!!error && <AjaxError t={t} error={error} />}
        {!error && <ExampleGrid {...gridProps} />}
      </PageContent>
    ];
  }
}
```

Upravit **src/components/pages/example/basicPage.test.js** následujícím způsobem se aktualizovat testy:

```js
// Copyright (c) Microsoft. All rights reserved.

import React from 'react';
import { shallow } from 'enzyme';
import 'polyfills';

import { BasicPage } from './basicPage';

describe('BasicPage Component', () => {
  it('Renders without crashing', () => {

    const fakeProps = {
      data: undefined,
      error: undefined,
      isPending: false,
      lastUpdated: undefined,
      fetchData: () => { },
      t: () => { },
    };

    const wrapper = shallow(
      <BasicPage {...fakeProps} />
    );
  });
});
```

## <a name="test-the-grid"></a>Testování mřížky

Pokud webové uživatelské rozhraní není spuštěná místně, spusťte následující příkaz v kořenové složce místní kopie úložiště:

```cmd/sh
npm start
```

Předchozí příkaz se spustí místně na uživatelské rozhraní [ http://localhost:3000/dashboard ](http://localhost:3000/dashboard). Přejděte **příklad** stránku, abyste zobrazili mřížky zobrazit data ze služby.

## <a name="select-rows"></a>Výběr řádků

Existují dvě možnosti pro povolení uživatele pro výběr řádků v tabulce:

### <a name="hard-select-rows"></a>Vybrat pevné řádky

Když uživatel potřebuje k práci s více řádky ve stejnou dobu, pomocí zaškrtávacích políček na řádky:

1. Povolit pevný výběr řádků tak, že přidáte **checkboxColumn** k **columnDefs** k dispozici do mřížky. **checkboxColumn** je definována v **/src/components/shared/pcsGrid/pcsGrid.js**:

    ```js
    this.columnDefs = [
      checkboxColumn,
      exampleColumnDefs.id,
      exampleColumnDefs.description
    ];
    ```

1. Pro přístup k vybrané položky, získejte odkaz na rozhraní API pro interní tabulky:

    ```js
    onGridReady = gridReadyEvent => {
      this.gridApi = gridReadyEvent.api;
      // Call the onReady props if it exists
      if (isFunc(this.props.onGridReady)) {
        this.props.onGridReady(gridReadyEvent);
      }
    };
    ```

1. Poskytnout kontext tlačítka na stránku při intenzivně výběru řádku v tabulce:

    ```js
    this.contextBtns = [
      <Btn key="context-btn-1" svg={svgs.reconfigure} onClick={this.doSomething()}>Button 1</Btn>,
      <Btn key="context-btn-2" svg={svgs.trash} onClick={this.doSomethingElse()}>Button 2</Btn>
    ];
    ```

    ```js
    onHardSelectChange = (selectedObjs) => {
      const { onContextMenuChange, onHardSelectChange } = this.props;
      // Show the context buttons when there are rows checked.
      if (isFunc(onContextMenuChange)) {
        onContextMenuChange(selectedObjs.length > 0 ? this.contextBtns : null);
      }
      //...
    }
    ```

1. Po kliknutí na tlačítko kontextové získáte intenzivně vybrané položky k provedení práce na:

    ```js
    doSomething = () => {
      //Just for demo purposes. Don't console log in a real grid.
      console.log('hard selected rows', this.gridApi.getSelectedRows());
    };
    ```

### <a name="soft-select-rows"></a>Obnovitelné vybrat řádky

Pokud uživatel se musí jednat o jeden řádek, nakonfigurujte obnovitelně vyberte odkaz pro jeden nebo více sloupců v **columnDefs**.

1. V **exampleGridConfig.js**, přidejte **SoftSelectLinkRenderer** jako **cellRendererFramework** pro **columnDef**.

    ```js
    export const exampleColumnDefs = {
      id: {
        headerName: 'examples.grid.name',
        field: 'id',
        sort: 'asc',
        cellRendererFramework: SoftSelectLinkRenderer
      }
    };
    ```

1. Když dojde ke kliknutí na obnovitelně vyberte odkaz, aktivuje **onSoftSelectChange** událostí. Provedení libovolné akce je požadován pro tento řádek, jako je například otevřít podrobnosti průběžné out. Tento příklad jednoduše zapisuje do konzoly:

    ```js
    onSoftSelectChange = (rowId, rowEvent) => {
      const { onSoftSelectChange } = this.props;
      const obj = (this.gridApi.getDisplayedRowAtIndex(rowId) || {}).data;
      if (obj) {
        //Just for demo purposes. Don't console log a real grid.
        console.log('Soft selected', obj);
        this.setState({ softSelectedObj: obj });
      }
      if (isFunc(onSoftSelectChange)) {
        onSoftSelectChange(obj, rowEvent);
      }
    }
    ```

## <a name="next-steps"></a>Další postup

V tomto článku jste se dozvěděli o prostředky dostupné pro přidání nebo přizpůsobit stránky ve webovém uživatelském rozhraní v akcelerátoru řešení vzdáleného monitorování.

Nyní jste definovali mřížky, dalším krokem je [přidat vlastní průběžné out do vzdáleného monitorování řešení akcelerátoru webového uživatelského rozhraní](iot-accelerators-remote-monitoring-customize-flyout.md) , který se zobrazí na příkladu stránky.

Další koncepční informace o akcelerátoru řešení vzdáleného monitorování najdete v tématu [architektura vzdáleného monitorování](iot-accelerators-remote-monitoring-sample-walkthrough.md).
