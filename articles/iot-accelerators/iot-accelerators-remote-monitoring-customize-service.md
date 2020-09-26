---
title: Přidání služby do uživatelského rozhraní řešení vzdáleného monitorování – Azure | Microsoft Docs
description: V tomto článku se dozvíte, jak přidat novou službu do webového uživatelského rozhraní akcelerátoru řešení vzdáleného monitorování.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/02/2018
ms.topic: conceptual
ms.custom: devx-track-js
ms.openlocfilehash: 6689a6d8a19dd690aa19aeecd29ead35ef6c2db1
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318343"
---
# <a name="add-a-custom-service-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Přidání vlastní služby do webového uživatelského rozhraní akcelerátoru řešení vzdáleného monitorování

V tomto článku se dozvíte, jak přidat novou službu do webového uživatelského rozhraní akcelerátoru řešení vzdáleného monitorování. Článek popisuje:

- Jak připravit místní vývojové prostředí.
- Postup přidání nové služby do webového uživatelského rozhraní

Ukázková služba v tomto článku obsahuje data pro mřížku, kterou do článku [Přidání vlastní mřížky do webového uživatelského rozhraní akcelerátoru řešení vzdáleného monitorování](iot-accelerators-remote-monitoring-customize-grid.md) ukazuje, jak přidat.

V reakci aplikace se služba obvykle komunikuje s back-end službou. Mezi příklady v akcelerátoru řešení vzdáleného monitorování patří služby, které komunikují se správcem služby IoT Hub a konfiguračními mikroslužbami.

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto průvodci, potřebujete na svém místním vývojovém počítači nainstalovaný následující software:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Než začnete

Než budete pokračovat, měli byste provést postup uvedený v článku [Přidání vlastní stránky do webového uživatelského rozhraní akcelerátoru řešení vzdáleného monitorování](iot-accelerators-remote-monitoring-customize-page.md) .

## <a name="add-a-service"></a>Přidat službu

Chcete-li přidat službu do webového uživatelského rozhraní, je třeba přidat zdrojové soubory, které definují službu, a upravit některé existující soubory, aby bylo webové uživatelské rozhraní na nové službě vědomo.

### <a name="add-the-new-files-that-define-the-service"></a>Přidejte nové soubory, které definují službu.

Chcete-li začít, složka **Src/pronávody/služby** obsahuje soubory, které definují jednoduchou službu:

**exampleService.js**

[!code-javascript[Example service](~/remote-monitoring-webui/src/walkthrough/services/exampleService.js?name=service "Example service")]

Další informace o tom, jak se služby implementují, najdete v tématu [Úvod do reaktivního programování, které jste si vyzkoušeli](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754).

**model/exampleModels.js**

[!code-javascript[Example model](~/remote-monitoring-webui/src/walkthrough/services/models/exampleModels.js?name=models "Example model")]

Zkopírujte **exampleService.js** do složky **Src/services** a zkopírujte **exampleModels.js** do složky **Src/Services/modely** .

Aktualizujte soubor **index.js** ve složce **Src/Services** pro export nové služby:

```js
export * from './exampleService';
```

Aktualizujte soubor **index.js** ve složce **Src/Services/modely** pro export nového modelu:

```js
export * from './exampleModels';
```

### <a name="set-up-the-calls-to-the-service-from-the-store"></a>Nastavení volání služby ze Storu

Chcete-li začít, složka **Src/názor/Store/reduktorů** obsahuje i redukci ukázek:

**exampleReducer.js**

[!code-javascript[Example reducer](~/remote-monitoring-webui/src/walkthrough/store/reducers/exampleReducer.js?name=reducer "Example reducer")]

Zkopírujte **exampleReducer.js** do složky **Src/Store/reduktorů** .

Další informace o nástroji pro zmenšení a **náměty**najdete v tématu [Redux-pozorovatelný](https://redux-observable.js.org/).

### <a name="configure-the-middleware"></a>Konfigurace middlewaru

Chcete-li nakonfigurovat middleware, přidejte do souboru **rootReducer.js** do složky **Src/Store** :

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

Do souboru **rootEpics.js** do složky **Src/Store** přidejte náměty:

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

V tomto článku jste se dozvěděli o dostupných zdrojích, které vám pomůžou přidat nebo přizpůsobit služby ve webovém uživatelském rozhraní v akcelerátoru řešení vzdáleného monitorování.

Nyní jste definovali službu, další krok je [Přidat vlastní mřížku do webového uživatelského rozhraní akcelerátoru řešení vzdáleného monitorování](iot-accelerators-remote-monitoring-customize-grid.md) , které zobrazuje data vrácená službou.

Další koncepční informace o akcelerátoru řešení vzdáleného monitorování najdete v tématu [architektura vzdáleného monitorování](iot-accelerators-remote-monitoring-sample-walkthrough.md).
