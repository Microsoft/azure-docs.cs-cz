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
ms.openlocfilehash: 08fe9e56298218936e700518cdd150f54d7f01de
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102425715"
---
# <a name="tutorial-prepare-a-web-app-for-azure-communication-services-nodejs"></a>Kurz: Příprava webové aplikace pro komunikační služby Azure (Node.js)

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Pomocí komunikačních služeb Azure můžete do svých aplikací přidat komunikaci v reálném čase. V tomto kurzu se naučíte, jak nastavit webovou aplikaci, která podporuje komunikační služby Azure. Toto je úvodní kurz pro nové vývojáře, kteří chtějí začít používat komunikaci v reálném čase.

Na konci tohoto kurzu budete mít webovou aplikaci, která je nakonfigurovaná s klientskými knihovnami služby Azure Communications Services. Pak můžete pomocí této aplikace začít vytvářet řešení pro komunikaci v reálném čase.

Nebojte se navštívit [stránku GitHubu služby Azure Communications](https://github.com/Azure/communication) , která poskytuje zpětnou vazbu.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Nakonfigurujte vývojové prostředí.
> * Nastavte místní webový server.
> * Přidejte na svůj web balíčky služby Azure Communication Services.
> * Publikujte svůj web na statické weby Azure.

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. Podrobnosti najdete v článku o [Vytvoření účtu zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). Bezplatný účet vám poskytne $200 kreditů Azure k vyzkoušení libovolné kombinace služeb.
- [Visual Studio Code](https://code.visualstudio.com/) pro úpravu kódu v místním vývojovém prostředí.
- [Webpack](https://webpack.js.org/) pro vytvoření balíčku a místně hostování vašeho kódu.
- [Node.js](https://nodejs.org/en/) pro instalaci a správu závislostí, jako jsou klientské knihovny Azure Communications Services a Webpack.
- [NVM a npm](/windows/nodejs/setup-on-windows) ke zpracování správy verzí.
- [Azure Storage rozšíření](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) pro Visual Studio Code. Toto rozšíření potřebujete k publikování aplikace v Azure Storage. [Přečtěte si další informace o hostování statických webů v Azure Storage](../../storage/blobs/storage-blob-static-website.md).
- [Rozšíření Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Rozšíření umožňuje nasadit weby s možností konfigurace plně spravované průběžné integrace a průběžného doručování (CI/CD).
- [Rozšíření Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) k sestavení vlastních aplikací bez serveru. Můžete například hostovat aplikaci ověřování v Azure Functions.
- Aktivní prostředek komunikační služby a připojovací řetězec. [Naučte se vytvořit prostředek komunikačních služeb](../quickstarts/create-communication-resource.md).
- Přístupový token uživatele. Pokyny najdete v tématu [rychlý Start pro vytváření a správu přístupových tokenů](../quickstarts/access-tokens.md?pivots=programming-language-javascript) nebo v [kurzu vytváření důvěryhodné ověřovací služby](./trusted-service-tutorial.md).


## <a name="configure-your-development-environment"></a>Nastavení vývojového prostředí

Vaše místní vývojové prostředí se nakonfiguruje takto:

:::image type="content" source="./media/step-one-pic-one.png" alt-text="Diagram, který znázorňuje architekturu vývojového prostředí.":::


### <a name="install-nodejs-nvm-and-npm"></a>Instalace Node.js, NVM a npm

Node.js použijeme ke stažení a instalaci různých závislostí, které potřebujeme pro naši aplikaci na straně klienta. Použijeme ho k vygenerování statických souborů, které pak budeme hostovat v Azure, takže se nemusíte starat o konfiguraci na vašem serveru.

Vývojáři systému Windows můžou pomocí [tohoto Node.js kurzu](/windows/nodejs/setup-on-windows) nakonfigurovat uzel, NVM a npm. 

Tento kurz vychází z verze LTS 12.20.0. Po instalaci NVM použijte následující příkaz prostředí PowerShell k nasazení verze, kterou chcete použít:

```PowerShell
nvm list available
nvm install 12.20.0
nvm use 12.20.0
```

:::image type="content" source="./media/step-one-pic-two.png" alt-text="Snímek obrazovky zobrazující příkazy pro nasazení verze uzlu":::

### <a name="configure-visual-studio-code"></a>Konfigurace editoru Visual Studio Code

[Visual Studio Code](https://code.visualstudio.com/) můžete stáhnout na jednu z [podporovaných platforem](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

### <a name="create-a-workspace-for-your-azure-communication-services-projects"></a>Vytvoření pracovního prostoru pro projekty služby Azure Communication Services

Vytvořte složku pro uložení souborů projektu, například: `C:\Users\Documents\ACS\CallingApp` . V Visual Studio Code vyberte **soubor**  >  **Přidat složku do pracovního prostoru** a přidejte složku do svého pracovního prostoru.

:::image type="content" source="./media/step-one-pic-three.png" alt-text="Snímek obrazovky zobrazující výběry pro přidání souboru do pracovního prostoru":::

V levém podokně přejděte do **Průzkumníka** a zobrazí se vaše `CallingApp` Složka v pracovním prostoru **bez názvu** .

:::image type="content" source="./media/step-one-pic-four.png" alt-text="Snímek obrazovky, který zobrazuje Průzkumníka a pracovní prostor bez názvu":::

Můžete si klidně aktualizovat název vašeho pracovního prostoru. Verzi Node.js můžete ověřit tak, že kliknete pravým tlačítkem na `CallingApp` složku a **v integrovaném terminálu vyberete otevřít**.

:::image type="content" source="./media/step-one-pic-five.png" alt-text="Snímek obrazovky, který zobrazuje výběr pro otevření složky v integrovaném terminálu.":::

V terminálu zadejte následující příkaz, který ověří verzi Node.js nainstalovanou v předchozím kroku:

```JavaScript
node --version
```

:::image type="content" source="./media/step-one-pic-six.png" alt-text="Snímek obrazovky, který ukazuje ověřování verze uzlu":::

### <a name="install-azure-extensions-for-visual-studio-code"></a>Nainstalovat rozšíření Azure pro Visual Studio Code

[Rozšíření Azure Storage](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) můžete nainstalovat buď prostřednictvím webu Visual Studio Marketplace, nebo prostřednictvím Visual Studio Code (**Zobrazit**  >  **rozšíření**  >  **Azure Storage**).

:::image type="content" source="./media/step-one-pic-seven.png" alt-text="Snímek obrazovky, který zobrazuje tlačítko pro instalaci rozšíření Azure Storage.":::

Použijte stejný postup pro rozšíření [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) a [Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) .


## <a name="set-up-a-local-web-server"></a>Nastavení místního webového serveru

### <a name="create-an-npm-package"></a>Vytvoření balíčku npm

Do terminálu zadejte v cestě ke složce pracovního prostoru:

``` console
npm init -y
```

Tento příkaz inicializuje nový balíček npm a přidá `package.json` do kořenové složky vašeho projektu.

:::image type="content" source="./media/step-one-pic-eight.png" alt-text="Snímek obrazovky zobrazující balíček J S O N.":::

Další dokumentaci k najdete na `npm init` [stránce s npm docs pro daný příkaz](https://docs.npmjs.com/cli/v6/commands/npm-init).

### <a name="install-webpack"></a>Nainstalovat balíček Webpack

Pomocí nástroje [Webpack](https://webpack.js.org/) můžete seskupit kód do statických souborů, které můžete nasadit do Azure. Má také vývojový server, který nakonfigurujete pro použití s ukázkou volání.

V terminálu zadejte následující příkaz pro instalaci sady Webpack:

``` Console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```

Tento kurz byl testován pomocí verzí uvedených v předchozím příkazu. Zadání `-dev` instruuje správce balíčků, že tato závislost je určena pro účely vývoje a neměla by být součástí kódu, který nasazujete do Azure.

V souboru se zobrazí dva nové balíčky `package.json` `devDependencies` . Balíčky se nainstalují do `./CallingApp/node_modules/` adresáře.

:::image type="content" source="./media/step-one-pic-ten.png" alt-text="Snímek obrazovky, který zobrazuje konfiguraci sady Webpack.":::

### <a name="configure-the-development-server"></a>Konfigurace vývojového serveru

Spuštění statické aplikace (jako je například `index.html` soubor) z prohlížeče používá `file://` protokol. Aby moduly npm fungovaly správně, budete potřebovat protokol HTTP s použitím nástroje Webpack jako místního vývojového serveru.

Vytvoříte dvě konfigurace: jeden pro vývoj a druhý pro produkci. Soubory připravené pro produkční prostředí budou minifikovaného, což znamená, že odeberete nepoužívané mezery a znaky. Tato konfigurace je vhodná pro produkční scénáře, kde by měla být minimalizována latence nebo kde by měl být kód zakódován.

Nástroj použijete `webpack-merge` pro práci s [různými konfiguračními soubory pro sadu Webpack](https://webpack.js.org/guides/production/).

Pojďme začít s vývojovým prostředím. Nejdřív je potřeba nainstalovat `webpack merge` . V terminálu spusťte následující příkaz:

```Console
npm install --save-dev webpack-merge
```

V `package.json` souboru se zobrazí jedna další závislost přidaná do `devDependencies` .

Dále vytvořte soubor s názvem `webpack.common.js` a přidejte následující kód:

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

Pak přidejte dva další soubory, jednu pro každou konfiguraci:

* `webpack.dev.js`
* `webpack.prod.js`

Nyní upravte `webpack.dev.js` soubor přidáním následujícího kódu do něj:

```JavaScript
const { merge } = require('webpack-merge');
const common = require('./webpack.common.js');

module.exports = merge(common, {
    mode: 'development',
    devtool: 'inline-source-map',
});
```
V této konfiguraci importujete společné parametry z `webpack.common.js` , sloučíte dva soubory, nastavíte režim na a `development` nakonfigurujete zdrojové mapování jako `inline-source-map` .

Vývojový režim instruuje, že sada Webpack neminimalizuje soubory a nevytváří optimalizované provozní soubory. Podrobnou dokumentaci k režimům nástroje Webpack najdete na [webové stránce režimu Webpack](https://webpack.js.org/configuration/mode/).

Možnosti zdrojového mapování jsou uvedené na [webové stránce devtool pro Webpack](https://webpack.js.org/configuration/devtool/#root). Nastavení zdrojového mapování usnadňuje ladění v prohlížeči.

:::image type="content" source="./media/step-one-pic-11.png" alt-text="Snímek obrazovky zobrazující kód pro konfiguraci sady Webpack.":::

Chcete-li spustit vývojový server, pokračujte na `package.json` a přidejte následující kód `scripts` :

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

:::image type="content" source="./media/step-one-pic-12.png" alt-text="Snímek obrazovky, který zobrazuje úpravu package.js.":::

### <a name="test-the-development-server"></a>Testování vývojového serveru

 V Visual Studio Code vytvořte v projektu tři soubory:

* `index.html`
* `app.js`
* `app.css` (volitelné, pro stylování aplikace)

Vložit tento kód do `index.html` :

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
:::image type="content" source="./media/step-one-pic-13.png" alt-text="Snímek obrazovky zobrazující soubor H T M L.":::

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
Nezapomeňte změny uložit! Neuložený soubor je v Průzkumníkovi označen bílými tečkami vedle názvů souborů.

:::image type="content" source="./media/step-one-pic-14.png" alt-text="Snímek obrazovky, který zobrazuje App.js soubor s kódem JavaScriptu":::

Když otevřete tuto stránku, měla by se zobrazit zpráva s výstrahou v konzole prohlížeče.

:::image type="content" source="./media/step-one-pic-15.png" alt-text="Snímek obrazovky, který zobrazuje soubor App. CSS.":::

K otestování konfigurace vývoje použijte následující příkaz terminálu:

```Console
npm run build:dev
```

Konzola zobrazuje, kde je server spuštěn. Ve výchozím nastavení je to `http://localhost:8080` . `build:dev`Příkaz je příkaz, který jste přidali `package.json` dříve.

:::image type="content" source="./media/step-one-pic-16.png" alt-text="Snímek obrazovky, který ukazuje spuštění vývojového serveru.":::
 
Přejděte na adresu v prohlížeči a měli byste vidět stránku a výstrahu nakonfigurovanou v předchozích krocích.
 
:::image type="content" source="./media/step-one-pic-17.png" alt-text="Snímek obrazovky se stránkou H T M L":::
  
 
I když je server spuštěný, můžete změnit kód a Server. Stránka HTML se automaticky znovu načte. 

V dalším kroku přejdete do `app.js` souboru v Visual Studio Code a odstraníte `alert('Hello world alert!');` . Uložte soubor a ověřte, že výstraha zmizí z prohlížeče.

Pokud chcete server zastavit, můžete ho spustit `Ctrl+C` v terminálu. Pokud chcete server spustit, zadejte kdykoli `npm run build:dev` .

## <a name="add-the-azure-communication-services-packages"></a>Přidat balíčky služby Azure Communication Services

Pomocí `npm install` příkazu nainstalujte pro JavaScript službu Azure Communication Services volání klientské knihovny.

```Console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

Tato akce přidá běžné a volání balíčků komunikačních služeb Azure jako závislosti vašeho balíčku. Zobrazí se dva nové balíčky přidané do `package.json` souboru. Další informace o `npm install` tomto příkazu najdete na stránce s informacemi o [npm docs](https://docs.npmjs.com/cli/v6/commands/npm-install).

:::image type="content" source="./media/step-one-pic-nine.png" alt-text="Snímek obrazovky zobrazující kód pro instalaci balíčků komunikačních služeb Azure":::

Tyto balíčky poskytuje tým komunikačních služeb Azure a zahrnují knihovny pro ověřování a volání. `--save`Příkaz signalizuje, že aplikace závisí na těchto balíčcích pro použití v produkčním prostředí a bude zahrnutá v `devDependencies` rámci `package.json` souboru. Když sestavíte aplikaci pro produkční prostředí, balíčky budou zahrnuty do kódu v produkčním prostředí.


## <a name="publish-your-website-to-azure-static-websites"></a>Publikování webu na statické weby Azure

### <a name="create-a-configuration-for-production-deployment"></a>Vytvořit konfiguraci pro produkční nasazení

Do souboru `webpack.prod.js` přidejte následující kód:

```JavaScript
const { merge } = require('webpack-merge');
const common = require('./webpack.common.js');

module.exports = merge(common, {
  mode: 'production',
});
```

Tato konfigurace bude sloučena s `webpack.common.js` (kde jste zadali vstupní soubor a kam výsledky Uložit). V konfiguraci se nastaví také režim na `production` .
 
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

:::image type="content" source="./media/step-one-pic-20.png" alt-text="Snímek obrazovky zobrazující nakonfigurované soubory":::


V terminálu spusťte příkaz:

```Console
npm run build:prod
```

Příkaz vytvoří `dist` složku a `app.js` v ní statický soubor připravený pro produkční prostředí. 

:::image type="content" source="./media/step-one-pic-21.png" alt-text="Snímek obrazovky, který ukazuje sestavení v produkčním prostředí.":::
 
 
### <a name="deploy-your-app-to-azure-storage"></a>Nasazení aplikace pro Azure Storage
 
Zkopírujte `index.html` a `app.css` do `dist` složky.

Ve `dist` složce vytvořte soubor a pojmenujte ho `404.html` . Zkopírujte následující kód do tohoto souboru:

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

Klikněte pravým tlačítkem na `dist` složku a vyberte **nasadit na statický web prostřednictvím Azure Storage**.

:::image type="content" source="./media/step-one-pic-22.png" alt-text="Snímek obrazovky s výběrem pro zahájení nasazování do Azure":::
 
V části **Vybrat předplatné** vyberte **Přihlásit se k Azure** (nebo si **vytvořte bezplatný účet Azure** , pokud jste předplatné ještě nevytvořili).
 
:::image type="content" source="./media/step-one-pic-23.png" alt-text="Snímek obrazovky zobrazující výběry pro přihlášení k Azure":::
 
Vyberte **vytvořit nový účet úložiště**  >  **Upřesnit**.

:::image type="content" source="./media/step-one-pic-24.png" alt-text="Snímek obrazovky zobrazující výběry pro vytvoření skupiny účtů úložiště":::
 
Zadejte název skupiny úložišť.
 
:::image type="content" source="./media/step-one-pic-25.png" alt-text="Snímek obrazovky, který ukazuje přidání názvu pro účet.":::
 
V případě potřeby vytvořte novou skupinu prostředků.
 
:::image type="content" source="./media/step-one-pic-26.png" alt-text="Snímek obrazovky, který zobrazuje výběr pro vytvoření nové skupiny prostředků.":::
  
Chcete **Povolit hostování statického webu?** vyberte **Ano**.

:::image type="content" source="./media/step-one-pic-27.png" alt-text="Snímek obrazovky, který zobrazuje výběr možnosti pro povolení statického hostování webu.":::
  
Pro **Zadejte název dokumentu indexu** přijměte výchozí název souboru. Soubor již byl vytvořen `index.html` .

Pro **Zadejte cestu k chybovému dokumentu 404** zadejte **404.html**.  
  
Vyberte umístění aplikace. Umístění, které vyberete, bude definovat, který procesor médií bude použit při budoucí volání aplikace ve skupině volání. 

Služba Azure Communication Services vybere procesor médií v závislosti na umístění aplikace.

:::image type="content" source="./media/step-one-pic-28.png" alt-text="Snímek obrazovky, který zobrazuje seznam umístění.":::
  
Počkejte na vytvoření prostředku a webu. 
 
Vyberte možnost **Přejít na web**.

:::image type="content" source="./media/step-one-pic-29.png" alt-text="Snímek obrazovky zobrazující zprávu o dokončení nasazení s tlačítkem pro procházení webu":::
 
Z vývojářských nástrojů v prohlížeči můžete zkontrolovat zdroj a prohlédnout si soubor, který je připravený pro produkční prostředí.
 
:::image type="content" source="./media/step-one-pic-30.png" alt-text="Snímek obrazovky se zdrojem webu se souborem":::

Otevřete [Azure Portal](https://portal.azure.com/#home), vyberte skupinu prostředků a vyberte aplikaci, kterou jste vytvořili. Pak vyberte **Nastavení**  >  **statický web**. Uvidíte, že jsou povolené statické weby. Všimněte si primárního koncového bodu, názvu dokumentu indexu a cesty k chybovému dokumentu.

:::image type="content" source="./media/step-one-pic-31.png" alt-text="Snímek obrazovky zobrazující statický výběr webu":::

V části **Blob service** vyberte **Kontejnery**. V seznamu jsou vytvořeny dva kontejnery, jeden pro protokoly ( `$logs` ) a jeden pro obsah webu ( `$web` ).

:::image type="content" source="./media/step-one-pic-32.png" alt-text="Snímek obrazovky, který zobrazuje konfiguraci kontejneru.":::

Pokud otevřete `$web` kontejner, zobrazí se soubory, které jste vytvořili v aplikaci Visual Studio a nasadili do Azure. 

:::image type="content" source="./media/step-one-pic-33.png" alt-text="Snímek obrazovky, který zobrazuje soubory nasazené do Azure":::

Aplikaci můžete kdykoli znovu nasadit z Visual Studio Code.

Nyní jste připraveni sestavit svou první webovou aplikaci Azure Communication Services.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přidání hlasových hovorů do aplikace](../quickstarts/voice-video-calling/getting-started-with-calling.md)

Můžete také chtít:

- [Přidání chatu do aplikace](../quickstarts/chat/get-started.md)
- [Vytvořit tokeny přístupu uživatele](../quickstarts/access-tokens.md)
- [Informace o architektuře klientů a serverů](../concepts/client-and-server-architecture.md)
- [Další informace o ověřování](../concepts/authentication.md)