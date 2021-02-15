---
title: Kurz – Příprava webové aplikace pro komunikační služby Azure (Node.js)
titleSuffix: An Azure Communication Services tutorial
description: Zjistěte, jak vytvořit základní webovou aplikaci, která podporuje komunikační služby Azure.
author: nmurav
services: azure-communication-services
ms.author: nmurav
ms.date: 01/03/2012
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: db59a9e7693190582736b9460658f629f4f1e555
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100369624"
---
# <a name="tutorial-prepare-a-web-app-for-azure-communication-services-nodejs"></a>Kurz: Příprava webové aplikace pro komunikační služby Azure (Node.js)

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Komunikační služby Azure umožňují přidat do svých aplikací komunikaci v reálném čase. V tomto kurzu se naučíte, jak nastavit webovou aplikaci, která podporuje komunikační služby Azure. Toto je úvodní kurz určený pro nové vývojáře, kteří chtějí začít používat komunikaci v reálném čase.

Na konci tohoto kurzu budete mít nakonfigurované základní webové aplikace s klientskými knihovnami služby Azure Communication Services, pomocí kterých můžete začít vytvářet řešení pro komunikaci v reálném čase.

Nebojte se navštívit stránku [GitHubu služby Azure Communications](https://github.com/Azure/communication) , která poskytuje zpětnou vazbu.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Nastavení vývojového prostředí
> * Nastavení místního serveru
> * Přidání balíčků komunikačních služeb Azure na svůj web
> * Publikování webu na statické weby Azure

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. Podrobnosti najdete v článku o [Vytvoření účtu zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). Upozorňujeme, že bezplatný účet vám poskytne $200 kredity Azure k vyzkoušení libovolné kombinace služeb.
- [Visual Studio Code](https://code.visualstudio.com/): Toto použijeme pro úpravu kódu v místním vývojovém prostředí.
- [Webpack](https://webpack.js.org/): Tato možnost se použije k vytvoření balíčku a místně hostování vašeho kódu.
- [Node.js](https://nodejs.org/en/): Tato akce bude sloužit k instalaci a správě závislostí, jako jsou klientské knihovny Azure Communications Services a Webpack.
- [NVM a npm](https://docs.microsoft.com/windows/nodejs/setup-on-windows) ke zpracování správy verzí.
- [Azure Storage rozšíření](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) pro Visual Studio Code. Toto rozšíření je potřeba k publikování aplikace v Azure Storage. [Přečtěte si další informace o hostování statických webů v Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website)
- [Rozšíření Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Rozšíření umožňuje nasadit weby (podobně jako v předchozím příkladu), ale s možností konfigurace plně spravované průběžné integrace a průběžného doručování (CI/CD).
- [Rozšíření funkce Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) pro vytváření vlastních aplikací bez serveru. Můžete například hostovat aplikaci ověřování ve službě Azure Functions.
- Aktivní prostředek komunikační služby a připojovací řetězec. [Vytvořte prostředek služby Communications](../quickstarts/create-communication-resource.md).
- Přístupový token uživatele. Pokyny najdete v kurzu [rychlý Start přístupových tokenů](https://docs.microsoft.com/azure/communication-services/quickstarts/access-tokens?pivots=programming-language-javascript) nebo v [kurzu pro důvěryhodné služby](https://docs.microsoft.com/azure/communication-services/tutorials/trusted-service-tutorial) .


## <a name="configure-your-development-environment"></a>Nastavení vývojového prostředí

Vaše místní vývojové prostředí se nakonfiguruje takto:

:::image type="content" source="./media/step-one-pic-one.png" alt-text="Architektura vývojového prostředí":::


### <a name="install-nodejs-nvm-and-npm"></a>Instalace Node.js, NVM a npm

Node.js použijeme ke stažení a instalaci různých závislostí, které potřebujeme pro naši aplikaci na straně klienta. Použijeme ho k vygenerování statických souborů, které pak budeme hostovat v Azure, takže se nemusíte starat o konfiguraci na vašem serveru.

Vývojáři systému Windows mohou postupovat podle [tohoto NodeJS kurzu](https://docs.microsoft.com/windows/nodejs/setup-on-windows) ke konfiguraci Node, NVM a npm. 

Tento kurz jsme otestovali pomocí verze LTS 12.20.0. Po instalaci NVM použijte následující příkaz prostředí PowerShell k nasazení verze, kterou chcete použít:

```PowerShell
nvm list available
nvm install 12.20.0
nvm use 12.20.0
```

:::image type="content" source="./media/step-one-pic-two.png" alt-text="Práce s NVM pro nasazení Node.js":::

### <a name="configure-visual-studio-code"></a>Konfigurace editoru Visual Studio Code

[Visual Studio Code](https://code.visualstudio.com/) můžete stáhnout na jednu z [podporovaných platforem](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

### <a name="create-a-workspace-for-your-azure-communication-services-projects"></a>Vytvoření pracovního prostoru pro projekty služby Azure Communication Services

Vytvořte novou složku pro ukládání souborů projektu, například: `C:\Users\Documents\ACS\CallingApp` . V Visual Studio Code klikněte na soubor, přidat složku do pracovního prostoru a přidejte složku do svého pracovního prostoru.

:::image type="content" source="./media/step-one-pic-three.png" alt-text="Vytváří se nové pracoviště.":::

V levém podokně přejděte do Průzkumníku a Visual Studio Code v pracovním prostoru "bez názvu" se zobrazí složka "CallingApp".

:::image type="content" source="./media/step-one-pic-four.png" alt-text="Průzkumník":::

Můžete si klidně aktualizovat název vašeho pracovního prostoru. Verzi Node.js můžete ověřit tak, že kliknete pravým tlačítkem na složku "CallingApp" a vyberete otevřít v integrovaném terminálu.

:::image type="content" source="./media/step-one-pic-five.png" alt-text="Otevření terminálu":::

V terminálu zadejte následující příkaz, který ověří verzi node.js nainstalovanou v předchozím kroku:

```JavaScript
node --version
```

:::image type="content" source="./media/step-one-pic-six.png" alt-text="Ověřuje se verze Node.js.":::

### <a name="install-azure-extensions-for-visual-studio-code"></a>Nainstalovat rozšíření Azure pro Visual Studio Code

[Rozšíření Azure Storage](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) můžete nainstalovat buď prostřednictvím webu Visual Studio Marketplace, nebo pomocí Visual Studio Code (Zobrazit > rozšíření > Azure Storage).

:::image type="content" source="./media/step-one-pic-seven.png" alt-text="Instalace rozšíření Azure Storage 1":::

Použijte stejný postup pro rozšíření [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) a [Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) .


## <a name="set-up-a-local-webserver"></a>Nastavení místního serveru

### <a name="create-a-new-npm-package"></a>Vytvoření nového balíčku npm

Do terminálu zadejte v cestě ke složce pracovního prostoru:

``` console
npm init -y
```

Tento příkaz inicializuje nový balíček npm a přidá `package.json` do kořenové složky vašeho projektu.

:::image type="content" source="./media/step-one-pic-eight.png" alt-text="JSON balíčku":::

Další dokumentaci k příkazu npm init najdete [tady](https://docs.npmjs.com/cli/v6/commands/npm-init) .

### <a name="install-webpack"></a>Nainstalovat balíček Webpack

[Webpack](https://webpack.js.org/) umožňuje seskupit kód do statických souborů, které můžete nasadit do Azure. Má také vývojový server, který nakonfigurujeme pro použití s ukázkou volání.

Do terminálu zadejte následující pro instalaci sady Webpack:

``` Console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```

Tento kurz byl testován pomocí výše uvedených verzí. Zadání `-dev` instruuje správce balíčků, že tato závislost je určena pro účely vývoje a neměla by být součástí kódu, který Nasazujeme do Azure.

V souboru se zobrazí dva nové balíčky `package.json` jako "devDependencies". Balíčky se nainstalují do `./CallingApp/node_modules/` adresáře.

:::image type="content" source="./media/step-one-pic-ten.png" alt-text="Konfigurace sady Webpack":::

### <a name="configure-the-development-server"></a>Konfigurace vývojového serveru

Spuštění statické aplikace (jako je například `index.html` soubor) z prohlížeče používá `file://` protokol. Aby moduly npm fungovaly správně, budete potřebovat protokol HTTP pomocí nástroje Webpack jako místního vývojového serveru.

Vytvoříme dvě konfigurace: jeden pro vývoj a druhý pro produkci. Soubory připravené pro produkční prostředí budou minifikovaného, což znamená, že odebereme nepoužívané mezery a znaky. To je vhodné pro produkční scénáře, kdy by latence měla být minimalizována nebo kde by měl být kód zakódován.

Nástroj použijeme `webpack-merge` pro práci s [různými konfiguračními soubory pro Webpack](https://webpack.js.org/guides/production/) .

Pojďme začít s vývojovým prostředím. Nejdřív musíme nainstalovat `webpack merge` . V terminálu spusťte následující příkaz:

```Console
npm install --save-dev webpack-merge
```

V `package.json` souboru se zobrazí jedna další závislost přidaná do "devDependencies".

V dalším kroku musíme vytvořit nový soubor `webpack.common.js` a přidat následující kód:

```JavaScript
const path = require('path');
module.exports ={
    entry: './app.js',
    output: {
        filename:'app.js',
        path: path.resolve(__dirname, 'dist'),
    }     
}
```

Pak přidáme dva další soubory, jednu pro každou konfiguraci:

* webpack.dev.js
* webpack.prod.js

V dalším kroku musíme `webpack.dev.js` soubor upravit. Do tohoto souboru přidejte následující kód:

```JavaScript
const { merge } = require('webpack-merge');
const common = require('./webpack.common.js');

module.exports = merge(common, {
    mode: 'development',
    devtool: 'inline-source-map',
});
```
V této konfiguraci naimportujeme společné parametry z `webpack.common.js` , sloučíte tyto dva soubory, nastavíte režim na "vývoj" a nakonfigurujete SourceMap jako "inline-source-map".

Vývojový režim instruuje, že sada Webpack neminimalizuje soubory a nevytváří optimalizované provozní soubory. Podrobnou dokumentaci o režimech sady Webpack najdete [tady](https://webpack.js.org/configuration/mode/).

[Tady](https://webpack.js.org/configuration/devtool/#root)jsou uvedené možnosti pro mapování zdrojového kódu. Nastavení zdrojového mapování usnadňuje ladění v prohlížeči.

:::image type="content" source="./media/step-one-pic-11.png" alt-text="Konfigurace sady Webpack":::

Pokud chcete spustit vývojový server, v `package.json` části skripty klikněte na a přidejte následující kód:

```JavaScript
    "build:dev": "webpack-dev-server --config webpack.dev.js"
```

Váš soubor by teď měl vypadat takto:

```JavaScript
{
  "name": "CallingApp",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build:dev": "webpack-dev-server --config webpack.dev.js"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "webpack": "^4.42.0",
    "webpack-cli": "^3.3.11",
    "webpack-dev-server": "^3.10.3"
  }
}
```

Přidali jste příkaz, který lze použít z npm. 

:::image type="content" source="./media/step-one-pic-12.png" alt-text="Probíhá úprava package.js":::

### <a name="testing-the-development-server"></a>Testování vývojového serveru

 V Visual Studio Code vytvořte v projektu tři soubory:

* `index.html`
* `app.js`
* `app.css` (volitelné, to vám umožní stylovat aplikaci.)

Vložit do `index.html` :

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My first ACS application</title>
    <link rel="stylesheet" href="./app.css"/>
    <script src="./app.js" defer></script>
</head>
<body>
    <h1>Hello from ACS!</h1>
</body>
</html>
```
:::image type="content" source="./media/step-one-pic-13.png" alt-text="Soubor HTML":::

Do souboru `app.js` přidejte následující kód:

```JavaScript
alert('Hello world alert!');
console.log('Hello world console!');
```
Do souboru `app.css` přidejte následující kód:

```CSS
html {
    font-family: sans-serif;
  }
```

 :::image type="content" source="./media/step-one-pic-14.png" alt-text="App.js soubor s kódem JS":::

Po otevření této stránky by se vám měla zobrazit zpráva s výstrahou a v rámci konzoly prohlížeče.

:::image type="content" source="./media/step-one-pic-15.png" alt-text="Soubor App. CSS":::

K otestování konfigurace vývoje použijte následující příkaz terminálu:

```Console
npm run build:dev
```

Konzola vám ukáže, kde je server spuštěný. Ve výchozím nastavení je to `http://localhost:8080` . Příkaz Build: dev je příkaz, který jsme přidali do našeho `package.json` dřívějšího.

 :::image type="content" source="./media/step-one-pic-16.png" alt-text="Spuštění vývojového serveru":::
 
 V prohlížeči přejděte na adresu a měli byste vidět stránku a výstrahu, která je nakonfigurovaná v předchozích krocích.
 
  :::image type="content" source="./media/step-one-pic-17.png" alt-text="Stránka HTML":::
  
 
Když je server spuštěný, můžete změnit kód a Server a stránka HTML se automaticky znovu načítají. 

V dalším kroku přejdete do `app.js` souboru v Visual Studio Code a odstraníte `alert('Hello world alert!');` . Uložte soubor a ověřte, že výstraha zmizí z prohlížeče.

Pokud chcete server zastavit, můžete ho spustit `Ctrl+C` v terminálu. Pokud chcete server spustit, zadejte je kdykoli `npm run build:dev` .

## <a name="add-the-azure-communication-services-packages"></a>Přidat balíčky služby Azure Communication Services

Pomocí `npm install` příkazu nainstalujte pro JavaScript službu Azure Communication Services volání klientské knihovny.

```Console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

Tato akce přidá běžné a volání balíčků služby Azure Communications jako závislosti vašeho balíčku. Zobrazí se dva nové balíčky přidané do `package.json` souboru. Další informace o `npm install` příkazu najdete [tady](https://docs.npmjs.com/cli/v6/commands/npm-install).

:::image type="content" source="./media/step-one-pic-nine.png" alt-text="Instalace balíčků služby Azure Communication Services":::

Tyto balíčky poskytuje tým komunikačních služeb Azure a zahrnují knihovny pro ověřování a volání. Příkaz "--Save" signalizuje, že naše aplikace závisí na těchto balíčcích pro produkční použití, a bude obsažena v `dependencies` `package.json` souboru. Když sestavíme aplikaci pro produkční prostředí, balíčky se zahrnou do našeho produkčního kódu.


## <a name="publish-your-website-to-azure-static-websites"></a>Publikování webu na statické weby Azure

### <a name="create-a-configuration-for-production-deployment"></a>Vytvořit konfiguraci pro produkční nasazení

Přidejte následující kód do `webpack.prod.js` :

```JavaScript
const { merge } = require('webpack-merge');
 const common = require('./webpack.common.js');

 module.exports = merge(common, {
   mode: 'production',
 });
 ```

Všimněte si, že tato konfigurace bude sloučena s webpack.common.js (kde jsme zadali vstupní soubor a kam výsledky Uložit) a nastavíte režim na "produkční".
 
V `package.json` přidejte následující kód:

```JavaScript
"build:prod": "webpack --config webpack.prod.js" 
```

Soubor by měl vypadat takto:

```JavaScript
{
  "name": "CallingApp",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build:dev": "webpack-dev-server --config webpack.dev.js",
    "build:prod": "webpack --config webpack.prod.js" 
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "@azure/communication-calling": "^1.0.0-beta.3",
    "@azure/communication-common": "^1.0.0-beta.3"
  },
  "devDependencies": {
    "webpack": "^4.42.0",
    "webpack-cli": "^3.3.11",
    "webpack-dev-server": "^3.10.3",
    "webpack-merge": "^5.7.3"
  }
}
```

 :::image type="content" source="./media/step-one-pic-20.png" alt-text="Nakonfigurované soubory":::


V terminálu spusťte:

```Console
npm run build:prod
```

Příkaz vytvoří `dist` složku a statický soubor připravený pro produkční prostředí `app.js` . 

 :::image type="content" source="./media/step-one-pic-21.png" alt-text="Výrobní sestavení":::
 
 
### <a name="deploy-your-app-to-azure-storage"></a>Nasazení aplikace pro Azure Storage
 
Zkopírujte `index.html` a `app.css` do `dist` složky.

Ve `dist` složce vytvořte nový soubor a pojmenujte ho `404.html` . Zkopírujte následující kód do tohoto souboru:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="./app.css"/>
    <title>Document</title>
</head>
<body>
    <h1>The page does not exists.</h1>
</body>
</html>
```

Uložte soubor (CTRL + S).

Klikněte pravým tlačítkem a vyberte nasadit na statický web prostřednictvím Azure Storage.

:::image type="content" source="./media/step-one-pic-22.png" alt-text="Zahájení nasazování do Azure":::
 
V `Select subscription` poli vyberte přihlásit se k Azure (nebo vytvořte bezplatný účet Azure, pokud jste ještě nevytvořili předplatné).
 
:::image type="content" source="./media/step-one-pic-23.png" alt-text="Přihlášení k Azure":::
 
Vyberte `Create new Storage Account`  >  `Advanced` :

 :::image type="content" source="./media/step-one-pic-24.png" alt-text="Vytvoření skupiny účtů úložiště":::
 
 Zadejte název skupiny úložišť:
 
 :::image type="content" source="./media/step-one-pic-25.png" alt-text="Přidání názvu pro účet":::
 
V případě potřeby vytvořte novou skupinu prostředků:
 
  :::image type="content" source="./media/step-one-pic-26.png" alt-text="Vytváří se nová skupina.":::
  
  Odpověď "Ano", chcete-li povolit statické hostování webů? "
  
  :::image type="content" source="./media/step-one-pic-27.png" alt-text="Výběr možnosti pro povolení hostování statického webu":::
  
Přijměte výchozí název souboru v "zadejte název dokumentu indexu", protože jsme soubor vytvořili `index.html` .

Zadejte `404.html` pro "zadejte cestu k chybovému dokumentu 404.  
  
Vyberte umístění aplikace. Umístění, které vyberete, bude definovat, který procesor médií bude použit při budoucí volání aplikace ve skupině volání. 

Služba Azure Communication Services vybere procesor médií v závislosti na umístění aplikace.

:::image type="content" source="./media/step-one-pic-28.png" alt-text="Vybrat umístění":::
  
Počkejte na vytvoření prostředku a webu. 
 
Klikněte na Procházet k webu:

:::image type="content" source="./media/step-one-pic-29.png" alt-text="Nasazení dokončeno":::
 
Z vývojářských nástrojů v prohlížeči si můžete prohlédnout zdroj a prohlédnout si náš soubor připravený pro produkční prostředí.
 
:::image type="content" source="./media/step-one-pic-30.png" alt-text="Web":::

Přejděte na [Azure Portal](https://portal.azure.com/#home), vyberte svoji skupinu prostředků, vyberte aplikaci, kterou jste vytvořili, a přejděte na `Settings`  >  `Static website` . Vidíte, že jsou povolené statické weby a Všimněte si primárního koncového bodu, dokumentu indexu a souborů dokumentů s cestou k chybám.

:::image type="content" source="./media/step-one-pic-31.png" alt-text="Výběr statického webu":::

V části Blob service vyberte kontejnery a zobrazí se dva kontejnery, jeden pro protokoly ($logs) a obsah vašeho webu ($web).

:::image type="content" source="./media/step-one-pic-32.png" alt-text="Konfigurace kontejneru":::

Pokud přejdete na `$web` , zobrazí se vaše soubory, které jste vytvořili v aplikaci Visual Studio a nasadili do Azure. 

:::image type="content" source="./media/step-one-pic-33.png" alt-text="Nasazení":::

Aplikaci můžete kdykoli znovu nasadit z Visual Studio Code.

Nyní jste připraveni sestavit svou první webovou aplikaci Azure Communication Services.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přidání hlasových hovorů do aplikace](../quickstarts/voice-video-calling/getting-started-with-calling.md)

Můžete také chtít:

- [Přidání chatu do aplikace](../quickstarts/chat/get-started.md)
- [Vytváření tokenů přístupu uživatele](../quickstarts/access-tokens.md)
- [Informace o architektuře klientů a serverů](../concepts/client-and-server-architecture.md)
- [Další informace o ověřování](../concepts/authentication.md)
