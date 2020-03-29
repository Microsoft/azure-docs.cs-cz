---
title: Přidání služby do uživatelského uživatelského režimu řešení vzdáleného monitorování – Azure | Dokumenty společnosti Microsoft
description: Tento článek ukazuje, jak přidat novou službu do webového uživatelského uživatelského uživatelského uživatelského uživatelského uživatelského zařízení akcelerátoru řešení vzdáleného monitorování.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/02/2018
ms.topic: conceptual
ms.openlocfilehash: e44aa8ade512a6005959e795cb1d4ad861da1338
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "61447042"
---
# <a name="add-a-custom-service-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Přidání vlastní služby do webového uživatelského uživatelského uživatelského nastavení akcelerátoru řešení vzdáleného monitorování

Tento článek ukazuje, jak přidat novou službu do webového uživatelského uživatelského uživatelského uživatelského uživatelského uživatelského zařízení akcelerátoru řešení vzdáleného monitorování. Článek popisuje:

- Jak připravit prostředí místního rozvoje.
- Jak přidat novou službu do webového uživatelského uživatelského uživatelského prohlášení.

Ukázková služba v tomto článku poskytuje data pro mřížku, která přidat vlastní mřížku do článku s postupem webového uživatelského [panelu akcelerátoru řešení vzdáleného monitorování,](iot-accelerators-remote-monitoring-customize-grid.md) vám ukáže, jak přidat.

V aplikaci React služba obvykle spolupracuje s back-endovou službou. Příklady v akcelerátoru řešení vzdáleného monitorování zahrnují služby, které interagují se správcem služby IoT hub a konfiguračními mikroslužbami.

## <a name="prerequisites"></a>Požadavky

Chcete-li provést kroky v tomto návodu, potřebujete v místním vývojovém počítači nainstalovaný následující software:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Než začnete

Měli byste dokončit kroky v [přidat vlastní stránku na vzdálené monitorování řešení akcelerátoru webového](iot-accelerators-remote-monitoring-customize-page.md) uživatelského panelu, než budete pokračovat.

## <a name="add-a-service"></a>Přidat službu

Chcete-li přidat službu do webového uživatelského uživatelského nastavení, je třeba přidat zdrojové soubory, které definují službu, a upravit některé existující soubory tak, aby webové uživatelské uživatelské uživatelské nastavení bylo informováno o nové službě.

### <a name="add-the-new-files-that-define-the-service"></a>Přidání nových souborů, které definují službu

Chcete-li začít, složka **src/návod/služby** obsahuje soubory, které definují jednoduchou službu:

**exampleService.js**

[!code-javascript[Example service](~/remote-monitoring-webui/src/walkthrough/services/exampleService.js?name=service "Example service")]

Další informace o implementaci služeb najdete v [tématu Úvod do reaktivního programování, které vám chybělo](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754).

**model/exampleModels.js**

[!code-javascript[Example model](~/remote-monitoring-webui/src/walkthrough/services/models/exampleModels.js?name=models "Example model")]

Zkopírujte **exampleService.js** do složky **src/services** a zkopírujte **exampleModels.js** do složky **src/services/models.**

Chcete-li exportovat novou službu, aktualizujte soubor **index.js** ve složce **src/services:**

```js
export * from './exampleService';
```

Chcete-li exportovat nový model, aktualizujte soubor **index.js** ve složce **src/services/models:**

```js
export * from './exampleModels';
```

### <a name="set-up-the-calls-to-the-service-from-the-store"></a>Nastavení volání služby z úložiště

Chcete-li začít, složka **src/walk/store/reducers** obsahuje ukázkový reduktor:

**exampleReducer.js**

[!code-javascript[Example reducer](~/remote-monitoring-webui/src/walkthrough/store/reducers/exampleReducer.js?name=reducer "Example reducer")]

Zkopírujte **exampleReducer.js** do složky **src/store/reducers.**

Další informace o reduktoru a **eposu**najdete v [tématu redux-observable](https://redux-observable.js.org/).

### <a name="configure-the-middleware"></a>Konfigurace middlewaru

Chcete-li nakonfigurovat middleware, přidejte reduktor do souboru **rootReducer.js** ve složce **src/store:**

```js
import { reducer as exampleReducer } from './reducers/exampleReducer';

const rootReducer = combineReducers({
  ...appReducer,
  ...devicesReducer,
  ...rulesReducer,
  ...simulationReducer,
  ...exampleReducer
});
```

Přidejte eposy do souboru **rootEpics.js** ve složce **src/store:**

```js
import { epics as exampleEpics } from './reducers/exampleReducer';

// Extract the epic function from each property object
const epics = [
  ...appEpics.getEpics(),
  ...devicesEpics.getEpics(),
  ...rulesEpics.getEpics(),
  ...simulationEpics.getEpics(),
  ...exampleEpics.getEpics()
];
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o dostupných prostředcích, které vám pomohou přidat nebo přizpůsobit služby ve webovém uživatelském rozhraní v akcelerátoru řešení vzdáleného monitorování.

Nyní jste definovali službu, dalším krokem je [přidání vlastní mřížky do webového uživatelského uživatelského uživatelského nastavení akcelerátoru řešení vzdáleného monitorování,](iot-accelerators-remote-monitoring-customize-grid.md) které zobrazuje data vrácená službou.

Další rámcové informace o akcelerátoru řešení vzdáleného monitorování naleznete v [tématu Architektura vzdáleného monitorování](iot-accelerators-remote-monitoring-sample-walkthrough.md).
