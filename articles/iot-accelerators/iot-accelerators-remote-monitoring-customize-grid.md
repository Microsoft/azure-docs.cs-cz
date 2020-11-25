---
title: Přidání mřížky do uživatelského rozhraní řešení vzdáleného monitorování – Azure | Microsoft Docs
description: V tomto článku se dozvíte, jak přidat novou GID na stránce ve webovém uživatelském rozhraní akcelerátoru řešení vzdáleného monitorování.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/04/2018
ms.topic: conceptual
ms.custom: devx-track-js
ms.openlocfilehash: 7fc878e0c9e099b201264c1c3981c603668214d3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017759"
---
# <a name="add-a-custom-grid-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Přidání vlastní mřížky do webového uživatelského rozhraní akcelerátoru řešení vzdáleného monitorování

V tomto článku se dozvíte, jak přidat novou mřížku na stránku ve webovém uživatelském rozhraní akcelerátoru řešení vzdáleného monitorování. Článek popisuje:

- Jak připravit místní vývojové prostředí.
- Jak přidat novou mřížku na stránku ve webovém uživatelském rozhraní.

Ukázková mřížka v tomto článku zobrazuje data ze služby, kterou v článku [Přidání vlastní služby do webového uživatelského rozhraní akcelerátoru řešení vzdáleného monitorování](iot-accelerators-remote-monitoring-customize-service.md) ukazuje, jak přidat.

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto průvodci, potřebujete na svém místním vývojovém počítači nainstalovaný následující software:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Než začnete

Než budete pokračovat, měli byste provést kroky v následujících článcích:

- [Přidejte vlastní stránku do webového uživatelského rozhraní akcelerátoru řešení vzdáleného monitorování](iot-accelerators-remote-monitoring-customize-page.md).
- [Přidání vlastní služby do webového uživatelského rozhraní akcelerátoru řešení vzdáleného monitorování](iot-accelerators-remote-monitoring-customize-service.md)

## <a name="add-a-grid"></a>Přidat mřížku

Chcete-li přidat mřížku do webového uživatelského rozhraní, je nutné přidat zdrojové soubory, které definují mřížku, a upravit některé existující soubory, aby bylo možné uživatelské rozhraní Web zajímat o nové součásti.

### <a name="add-the-new-files-that-define-the-grid"></a>Přidejte nové soubory, které definují mřížku.

Chcete-li začít, složka **Src/názor/komponenty/Pages/pageWithGrid/exampleGrid** obsahuje soubory, které definují mřížku:

**exampleGrid.js**

[!code-javascript[Example grid](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithGrid/exampleGrid/exampleGrid.js?name=grid "Example grid")]

**exampleGridConfig.js**

[!code-javascript[Example grid configuration](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithGrid/exampleGrid/exampleGridConfig.js?name=gridconfig "Example grid configuration")]

Zkopírujte složku **Src/názorného/Components/Pages/pageWithGrid/exampleGrid** do složky **Src/Components/example** .

### <a name="add-the-grid-to-the-page"></a>Přidat mřížku na stránku

Upravte **Src/Components/Pages/example/basicPage.container.js** následujícím způsobem pro import definic služby:

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

Upravte **Src/Components/Pages/example/basicPage.js** následujícím způsobem přidejte mřížku:

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

Chcete-li aktualizovat testy, upravte **Src/Components/Pages/example/basicPage.test.js** následujícím způsobem:

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

## <a name="test-the-grid"></a>Otestování mřížky

Pokud webové uživatelské rozhraní ještě neběží, spusťte následující příkaz v kořenovém adresáři místní kopie úložiště:

```cmd/sh
npm start
```

Předchozí příkaz spustí uživatelské rozhraní lokálně na `http://localhost:3000/dashboard` . Přejděte na stránku s **příkladem** , kde se zobrazí data zobrazení mřížky ze služby.

## <a name="select-rows"></a>Vybrat řádky

Existují dvě možnosti, jak povolit uživateli vybrat řádky v mřížce:

### <a name="hard-select-rows"></a>Řádky s pevným výběrem

Pokud uživatel potřebuje pracovat s více řádky současně, použijte zaškrtávací políčka na řádcích:

1. Umožňuje pevný výběr řádků přidáním **checkboxColumn** k **columnDefs** , který je k mřížce k dispozici. **checkboxColumn** je definována v **/Src/Components/Shared/pcsGrid/pcsGrid.js**:

    ```js
    this.columnDefs = [
      checkboxColumn,
      exampleColumnDefs.id,
      exampleColumnDefs.description
    ];
    ```

1. Chcete-li získat přístup k vybraným položkám, získáte odkaz na vnitřní rozhraní Grid API:

    ```js
    onGridReady = gridReadyEvent => {
      this.gridApi = gridReadyEvent.api;
      // Call the onReady props if it exists
      if (isFunc(this.props.onGridReady)) {
        this.props.onGridReady(gridReadyEvent);
      }
    };
    ```

1. Zadání kontextových tlačítek stránky, když je řádek v mřížce pevně vybraný:

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

1. Po kliknutí na tlačítko kontextu získat pevně vybrané položky, na kterých se má pracovat:

    ```js
    doSomething = () => {
      //Just for demo purposes. Don't console log in a real grid.
      console.log('Hard selected rows', this.gridApi.getSelectedRows());
    };
    ```

### <a name="soft-select-rows"></a>Měkké výběr řádků

Pokud uživatel potřebuje jednat jenom na jednom řádku, nakonfigurujte odkaz na částečný výběr pro jeden nebo více sloupců v **columnDefs**.

1. V **exampleGridConfig.js** přidejte **SoftSelectLinkRenderer** jako **cellRendererFramework** pro **columnDef**.

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

1. Po kliknutí na odkaz na částečný výběr se aktivuje událost **onSoftSelectChange** . Proveďte jakoukoli akci pro tento řádek, jako je například otevření informačního rámečku podrobností. Tento příklad jednoduše zapisuje do konzoly:

    ```js
    onSoftSelectChange = (rowId, rowData) => {
      //Note: only the Id is reliable, rowData may be out of date
      const { onSoftSelectChange } = this.props;
      if (rowId) {
        //Just for demo purposes. Don't console log a real grid.
        console.log('Soft selected', rowId);
        this.setState({ softSelectedId: rowId });
      }
      if (isFunc(onSoftSelectChange)) {
        onSoftSelectChange(rowId, rowData);
      }
    }
    ```

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o dostupných zdrojích, které vám pomůžou přidávat nebo upravovat stránky ve webovém uživatelském rozhraní v akcelerátoru řešení vzdáleného monitorování.

Nyní jste definovali mřížku. dalším krokem je [Přidání vlastního informačního panelu do webového uživatelského rozhraní akcelerátoru řešení vzdáleného monitorování](iot-accelerators-remote-monitoring-customize-flyout.md) , které se zobrazí na stránce s příkladem.

Další koncepční informace o akcelerátoru řešení vzdáleného monitorování najdete v tématu [architektura vzdáleného monitorování](iot-accelerators-remote-monitoring-sample-walkthrough.md).
