---
title: Přidat službu do řešení vzdálené monitorování uživatelské rozhraní – Azure | Dokumentace Microsoftu
description: Tento článek ukazuje, jak přidat nové služby do vzdáleného monitorování řešení akcelerátoru webového uživatelského rozhraní.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/02/2018
ms.topic: conceptual
ms.openlocfilehash: e44aa8ade512a6005959e795cb1d4ad861da1338
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49094565"
---
# <a name="add-a-custom-service-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Přidat vlastní služby do vzdáleného monitorování řešení akcelerátoru webového uživatelského rozhraní

Tento článek ukazuje, jak přidat nové služby do vzdáleného monitorování řešení akcelerátoru webového uživatelského rozhraní. Tento článek popisuje:

- Jak připravit místní vývojové prostředí.
- Postup přidání nové služby do webového uživatelského rozhraní.

Příklad služby v tomto článku poskytuje data pro mřížky, která [přidat vlastní mřížky do vzdáleného monitorování řešení akcelerátoru webového uživatelského rozhraní](iot-accelerators-remote-monitoring-customize-grid.md) článek ukazuje, jak přidat.

V aplikaci React služba obvykle komunikuje s back-end služby. Příklady v akcelerátoru řešení vzdáleného monitorování zahrnují služby, které pracují s IoT hub správci a konfigurací mikroslužeb.

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v této příručce s postupy, musíte na svém místním vývojovém počítači nainstalovaný následující software:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Než začnete

By se měla dokončit kroky v [přidat vlastní stránky pro vzdálené monitorování řešení akcelerátoru webového uživatelského rozhraní](iot-accelerators-remote-monitoring-customize-page.md) článek než budete pokračovat.

## <a name="add-a-service"></a>Přidat službu

Pokud chcete přidat službu do webového uživatelského rozhraní, budete muset přidat zdrojové soubory, které definují služby a změnit některé existující soubory ve webovém uživatelském rozhraní používající nové služby.

### <a name="add-the-new-files-that-define-the-service"></a>Přidat nové soubory, které definují služby

Abyste mohli začít, **src/návod/služby** složka obsahuje soubory, které definují jednoduché služby:

**exampleService.js**

[!code-javascript[Example service](~/remote-monitoring-webui/src/walkthrough/services/exampleService.js?name=service "Example service")]

Další informace o tom, jak se implementují služby najdete v tématu [Úvod do reaktivního programování jste dosud chyběla](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754).

**model/exampleModels.js**

[!code-javascript[Example model](~/remote-monitoring-webui/src/walkthrough/services/models/exampleModels.js?name=models "Example model")]

Kopírování **exampleService.js** k **src/služby** složku a Kopírovat **exampleModels.js** k **src/services/modely** složky.

Aktualizace **index.js** ve **src/služby** složku pro export nové služby:

```js
export * from './exampleService';
```

Aktualizace **index.js** soubor **src/services/modely** složku, kterou chcete exportovat nový model:

```js
export * from './exampleModels';
```

### <a name="set-up-the-calls-to-the-service-from-the-store"></a>Nastavit volání ke službě z úložiště

Jak začít, **src/návod/store/reduktorů** složka obsahuje redukční funkci vzorku:

**exampleReducer.js**

[!code-javascript[Example reducer](~/remote-monitoring-webui/src/walkthrough/store/reducers/exampleReducer.js?name=reducer "Example reducer")]

Kopírování **exampleReducer.js** k **src/store/reduktorů** složky.

Další informace o redukční funkci a **náměty**, naleznete v tématu [redux pozorovat](https://redux-observable.js.org/).

### <a name="configure-the-middleware"></a>Nakonfigurujte middleware

Pro konfiguraci middlewaru, přidejte redukční funkci k **rootReducer.js** soubor **src/úložiště** složky:

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

Přidat náměty k **rootEpics.js** soubor **src/store** složky:

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

## <a name="next-steps"></a>Další postup

V tomto článku jste se dozvěděli o prostředky dostupné pro umožňují přidat nebo upravit služby ve webovém uživatelském rozhraní v akcelerátoru řešení vzdáleného monitorování.

Nyní jste definovali služby, dalším krokem je [přidat vlastní mřížky do vzdáleného monitorování řešení akcelerátoru webového uživatelského rozhraní](iot-accelerators-remote-monitoring-customize-grid.md) , která zobrazuje data vrácené službou.

Další koncepční informace o akcelerátoru řešení vzdáleného monitorování najdete v tématu [architektura vzdáleného monitorování](iot-accelerators-remote-monitoring-sample-walkthrough.md).
