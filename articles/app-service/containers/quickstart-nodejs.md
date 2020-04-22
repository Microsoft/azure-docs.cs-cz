---
title: 'Úvodní příručka: Vytvoření aplikace Linux Node.js'
description: Začínáme s linuxovými aplikacemi ve službě Azure App Service nasazením první aplikace Node.js do linuxového kontejneru ve službě App Service.
author: msangapu-msft
ms.author: msangapu
ms.date: 08/12/2019
ms.topic: quickstart
ms.devlang: javascript
ms.openlocfilehash: 08a246b07effb8d5f3e2473a4d7959882cf43235
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687936"
---
# <a name="create-a-nodejs-app-in-azure"></a>Vytvoření aplikace Node.js v Azure

Azure App Service  je vysoce škálovatelná služba s automatickými opravami pro hostování webů. Tento rychlý start ukazuje, jak nasadit aplikaci Node.js do služby Azure App Service.

## <a name="prerequisites"></a>Požadavky

Pokud nemáte účet Azure, [zaregistrujte si dnes](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension) bezplatný účet s kredity Azure ve výši $200 a vyzkoušejte si libovolnou kombinaci služeb.

Potřebujete [nainstalovat kód Visual Studio](https://code.visualstudio.com/) spolu s [Node.js a npm](https://nodejs.org/en/download), Správce balíčků Node.js.

Budete také muset nainstalovat [rozšíření služby Azure App Service](vscode:extension/ms-azuretools.vscode-azureappservice), které můžete použít k vytváření, správě a nasazení Linux Web Apps na platformě Azure jako služba (PaaS).

### <a name="sign-in"></a>Přihlášení

Po instalaci rozšíření se přihlaste ke svému účtu Azure. Na panelu aktivit vyberte logo Azure, které chcete zobrazit **průzkumníka AZURE APP SERVICE.** Vyberte **Přihlásit se do Azure...** a postupujte podle pokynů.

![přihlášení do Azure](./media/quickstart-nodejs/sign-in.png)

### <a name="troubleshooting"></a>Poradce při potížích

Pokud se zobrazí chyba **"Nelze najít předplatné s názvem [ID předplatného]"**, může to být proto, že jste za proxy serverem a nemůžete dosáhnout rozhraní AZURE API. Konfigurace `HTTP_PROXY` `HTTPS_PROXY` a proměnné prostředí s informacemi `export`o proxy serveru v terminálu pomocí .

```sh
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Pokud nastavení proměnných prostředí problém nenapraví, kontaktujte nás výběrem tlačítka **I narazil na** tlačítko problému níže.

### <a name="prerequisite-check"></a>Kontrola předpokladů

Než budete pokračovat, ujistěte se, že máte nainstalovány a nakonfigurovány všechny požadavky.

V kódu VS byste měli vidět e-mailovou adresu Azure na stavovém řádku a předplatné v průzkumníku **AZURE APP SERVICE.**

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=getting-started)

## <a name="create-your-nodejs-application"></a>Vytvoření aplikace Node.js

Dále vytvořte aplikaci Node.js, kterou lze nasadit do cloudu. Tento rychlý start používá generátor aplikací k rychlému vytvoření uživatelského kódu aplikace z terminálu.

> [!TIP]
> Pokud jste již dokončili [kurz Node.js](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial), můžete přeskočit na [nasazení do Azure](#deploy-to-azure).

### <a name="scaffold-a-new-application-with-the-express-generator"></a>Lešení novou aplikaci s Express Generátor

[Express](https://www.expressjs.com) je oblíbeným rámcem pro vytváření a spouštění aplikací Node.js. Pomocí nástroje [Express Generator](https://expressjs.com/en/starter/generator.html) můžete vytvořit lešení (vytvořit) novou expresní aplikaci. Express Generátor je dodáván jako npm modul a lze spustit přímo (bez instalace) `npx`pomocí npm command-line nástroj .

```bash
npx express-generator myExpressApp --view pug --git
```

Parametry `--view pug --git` říkají generátoru, aby použil modul šablony `jade` [mopslíku](https://pugjs.org/api/getting-started.html) (dříve označovaný jako ) a vytvořil `.gitignore` soubor.

Chcete-li nainstalovat všechny závislosti aplikace, přejděte do `npm install`nové složky a spusťte program .

```bash
cd myExpressApp
npm install
```

### <a name="run-the-application"></a>Spuštění aplikace

Dále se ujistěte, že aplikace běží. Z terminálu spusťte `npm start` aplikaci pomocí příkazu ke spuštění serveru.

```bash
npm start
```

Nyní otevřete prohlížeč a [http://localhost:3000](http://localhost:3000)přejděte na , kde byste měli vidět něco takového:

![Spuštění expresní aplikace](./media/quickstart-nodejs/express.png)

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-to-azure"></a>Nasazení do Azure

V této části nasadíte aplikaci Node.js pomocí Kódu VS a rozšíření služby Azure App Service. Tento rychlý start používá nejzákladnější model nasazení, kde je vaše aplikace nazipovaná a nasazená do Azure Web Appu na Linuxu.

### <a name="deploy-using-azure-app-service"></a>Nasazení pomocí služby Azure App Service

Nejprve otevřete složku aplikace v kódu VS.

```bash
code .
```

V průzkumníku **AZURE APP SERVICE** vyberte modrou šipku nahoru a nasadíte aplikaci do Azure.

![Nasazení do webové aplikace](./media/quickstart-nodejs/deploy.png)

> [!TIP]
> Můžete také nasadit z **palety příkazů** (CTRL + SHIFT + P) zadáním příkazu "nasadit do webové aplikace" a spuštěním příkazu **Azure App Service: Deploy to Web App.**

1. Vyberte aktuálně otevřený adresář . `myExpressApp`

1. Zvolte **Vytvořit novou Web App**, která se ve výchozím nastavení nasazuje do služby App Service v Linuxu.

1. Zadejte globálně jedinečný název webové aplikace a stiskněte klávesu ENTER. Platné znaky pro název aplikace jsou 'a-z', '0-9' a '-'.

1. Zvolte **verzi souboru Node.js**, doporučujeme použít soubor LTS.

    Kanál oznámení zobrazuje prostředky Azure, které se vytvářejí pro vaši aplikaci.

1. Po zobrazení výzvy k aktualizaci `npm install` konfigurace, která má být spuštěna na cílovém serveru, vyberte **možnost Ano.** Vaše aplikace se pak nasadí.

    ![Nakonfigurované nasazení](./media/quickstart-nodejs/server-build.png)

1. Po spuštění nasazení budete vyzváni k aktualizaci pracovního prostoru tak, aby pozdější nasazení automaticky zacílila na stejnou webovou aplikaci App Service. Zvolte **Ano,** abyste zajistili nasazení změn do správné aplikace.

    ![Nakonfigurované nasazení](./media/quickstart-nodejs/save-configuration.png)

> [!TIP]
> Ujistěte se, že vaše aplikace naslouchá na portu `process.env.PORT`poskytovanéport proměnné prostředí: .

### <a name="browse-the-app-in-azure"></a>Procházení aplikace v Azure

Po dokončení nasazení vyberte v řádku **procházet web** a zobrazte čerstvě nasazenou webovou aplikaci.

### <a name="troubleshooting"></a>Poradce při potížích

Pokud se zobrazí chyba **"Nemáte oprávnění k zobrazení tohoto adresáře nebo stránky."**, pak se pravděpodobně nepodařilo správně spustit aplikaci. Přejděte k další části a zobrazte výstup protokolu, abyste našli a opravili chybu. Pokud to nemůžete opravit, kontaktujte nás výběrem tlačítka **I narazil na tlačítko problému** níže. Rádi vám pomůžeme!

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="update-the-app"></a>Aktualizace aplikace

Změny můžete nasadit do této aplikace pomocí stejného procesu a výběrem stávající aplikace, nikoli vytvořením nové.

## <a name="viewing-logs"></a>Zobrazení protokolů

V této části se dozvíte, jak zobrazit (nebo "ocas") protokoly ze spuštěné aplikace App Service. Všechna volání `console.log` v aplikaci se zobrazí ve výstupním okně v kódu Sady Visual Studio.

Najděte aplikaci v průzkumníku **AZURE APP SERVICE,** klikněte na ni pravým tlačítkem myši a zvolte **Zobrazit protokoly datových proudů**.

Po zobrazení výzvy zvolte povolení protokolování a restartování aplikace. Po restartování aplikace se otevře výstupní okno VS Code s připojením k datovému proudu protokolu.

![Zobrazit protokoly datových proudů](./media/quickstart-nodejs/view-logs.png)

![Povolení protokolování a restartování](./media/quickstart-nodejs/enable-restart.png)

Po několika sekundách se zobrazí zpráva oznamující, že jste připojeni ke službě streamování protokolů. Několikrát stránku aktualizujte, abyste viděli větší aktivitu.

<pre>
2019-09-20 20:37:39.574 INFO  - Initiating warmup request to container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node
2019-09-20 20:37:55.011 INFO  - Waiting for response to warmup request for container msdocs-vscode-node_2_00ac292a. Elapsed time = 15.4373071 sec
2019-09-20 20:38:08.233 INFO  - Container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node initialized successfully and is ready to serve requests.
2019-09-20T20:38:21  Startup Request, url: /Default.cshtml, method: GET, type: request, pid: 61,1,7, SCM_SKIP_SSL_VALIDATION: 0, SCM_BIN_PATH: /opt/Kudu/bin, ScmType: None
</pre>

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>Další kroky

Gratulujeme, úspěšně jste dokončili tento rychlý start!

Dále se podívejte na další rozšíření Azure.

* [Databáze Cosmos](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Nástroje Dockeru](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Nástroje azure cli](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Nástroje Správce prostředků Azure](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Nebo je všechny získejte instalací rozšíření [Sady Node Pack pro Azure.](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack)
