---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 4a493d5f0d34cd4621d55c0371036c03e267c466
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2021
ms.locfileid: "105108256"
---
## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-nodejs-application"></a>Vytvoření nové aplikace Node.js

Otevřete okno terminálu nebo příkazového řádku pro svoji aplikaci vytvořte nový adresář a přejděte do něj.

```console
mkdir calling-quickstart && cd calling-quickstart
```

Spusťte `npm init -y` , chcete-li vytvořit **package.js** pro soubor s výchozími nastaveními.

```console
npm init -y
```

### <a name="install-the-package"></a>Instalace balíčku

Pomocí `npm install` příkazu nainstalujete službu Azure Communication Services, která volá sadu SDK pro JavaScript.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

Pro tento rychlý Start jsou doporučovány následující verze nástroje Webpack:

```console
"webpack": "^4.42.0",
"webpack-cli": "^3.3.11",
"webpack-dev-server": "^3.10.3"
```

`--save`Možnost vypíše knihovnu v **package.jsv** souboru jako závislost.

### <a name="set-up-the-app-framework"></a>Nastavení aplikační architektury

V tomto rychlém startu se k vytvoření sady prostředků aplikace používá Webpack. Spusťte následující příkaz pro instalaci balíčků Webpack, Webpack-CLI a Webpack-dev-Server npm a seznamte se s nimi jako se závislostmi vývoje v **package.jsna**:

```console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```

Vytvořte soubor **index.html** v kořenovém adresáři projektu. Tento soubor použijeme ke konfiguraci základního rozložení, které umožní uživateli umístit volání.
