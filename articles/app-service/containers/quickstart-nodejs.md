---
title: Vytvoření aplikace Node.js ve službě Azure App Service v Linuxu | Dokumentace Microsoftu
description: Během několika minut můžete nasadit svou první aplikaci Node.js Hello World ve službě Azure App Service v Linuxu.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: syntaxc4
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 05/05/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: a2643e65b74f44ee05001d5df26c7c77a430fbb2
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="create-a-nodejs-web-app-in-azure-app-service-on-linux"></a>Vytvoření webové aplikace Node.js ve službě Azure App Service v Linuxu

> [!NOTE]
> Tento článek nasadí aplikaci do služby App Service v Linuxu. Nasazení do služby App Service ve _Windows_ je popsané v tématu [Vytvoření webové aplikace v Node.js v Azure](../app-service-web-get-started-nodejs.md).
>

[App Service v Linuxu](app-service-linux-intro.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů s využitím operačního systému Linux. Tento rychlý start ukazuje, jak nasadit aplikaci Node.js do služby App Service v Linuxu s použitím integrované image. Pomocí [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) vytvoříte webovou aplikaci s použitím integrované image a pomocí Gitu do webové aplikace nasadíte kód Node.js.

![Ukázková aplikace spuštěná ve službě Azure](media/quickstart-nodejs/hello-world-in-browser.png)

Postup v tomto kurzu můžete použít v případě počítačů se systémem Mac, Windows nebo Linux. Můžete také postupovat podle [videa](#video), které pokrývá obsah tohoto článku.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

K provedení kroků v tomto kurzu Rychlý start je potřeba:

* <a href="https://git-scm.com/" target="_blank">Nainstalovat Git</a>.
* <a href="https://nodejs.org/" target="_blank">Nainstalovat Node.js a NPM</a>.

## <a name="download-the-sample"></a>Stažení ukázky

V okně terminálu na svém počítači naklonujte spuštěním následujícího příkazu úložiště ukázkové aplikace do místního počítače.

```bash
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
```

Toto okno terminálu budete používat ke spuštění všech příkazů v tomto kurzu Rychlý start.

Přejděte do adresáře, který obsahuje vzorový kód.

```bash
cd nodejs-docs-hello-world
```

## <a name="run-the-app-locally"></a>Místní spuštění aplikace

Aplikaci spustíte místně tak, že otevřete okno terminálu a pomocí skriptu `npm start` spustíte integrovaný server HTTP aplikace Node.js.

```bash
npm start
```

Otevřete webový prohlížeč a přejděte do ukázkové aplikace na adrese `http://localhost:1337`.

Na stránce se zobrazí zpráva **Hello World** z ukázkové aplikace.

![Ukázková aplikace spuštěná místně](media/quickstart-nodejs/localhost-hello-world-in-browser.png)

V okně terminálu ukončete webový server stisknutím **Ctrl + C**.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Vytvoření webové aplikace

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-nodejs-linux-no-h.md)]

Přejděte do vaší nově vytvořené webové aplikace. Nahraďte _&lt;app name>_ názvem vaší webové aplikace.

```bash
http://<app name>.azurewebsites.net
```

Vaše nová webová aplikace by měla vypadat takto:

![Prázdná stránka webové aplikace](media/quickstart-nodejs/app-service-web-service-created.png)

[!INCLUDE [Push to Azure](../../../includes/app-service-web-git-push-to-azure.md)]

```bash
Counting objects: 23, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (21/21), done.
Writing objects: 100% (23/23), 3.71 KiB | 0 bytes/s, done.
Total 23 (delta 8), reused 7 (delta 1)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'bf114df591'.
remote: Generating deployment script.
remote: Generating deployment script for node.js Web Site
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling node.js deployment.
remote: Kudu sync from: '/home/site/repository' to: '/home/site/wwwroot'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'README.md'
remote: Copying file: 'index.js'
remote: Copying file: 'package.json'
remote: Copying file: 'process.json'
remote: Deleting file: 'hostingstart.html'
remote: Ignoring: .git
remote: Using start-up script index.js from package.json.
remote: Node.js versions available on the platform are: 4.4.7, 4.5.0, 6.2.2, 6.6.0, 6.9.1.
remote: Selected node.js version 6.9.1. Use package.json file to choose a different version.
remote: Selected npm version 3.10.8
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net:443/<app_name>.git
 * [new branch]      master -> master
```

## <a name="browse-to-the-app"></a>Přechod do aplikace

V prohlížeči zadejte adresu nasazené aplikace.

```bash
http://<app_name>.azurewebsites.net
```

Vzorový kód Node.js je spuštěný ve webové aplikaci s použitím integrované image.

![Ukázková aplikace spuštěná ve službě Azure](media/quickstart-nodejs/hello-world-in-browser.png)

**Blahopřejeme!** Nasadili jste svoji první aplikaci Node.js do služby App Service v Linuxu.

## <a name="update-and-redeploy-the-code"></a>Aktualizace a opětovné nasazení kódu

V místním adresáři otevřete soubor `index.js`, který je součástí aplikace Node.js, a proveďte malou změnu textu u volání `response.end`:

```nodejs
response.end("Hello Azure!");
```

Potvrďte změny v Gitu a potom odešlete změny kódu do Azure.

```bash
git commit -am "updated output"
git push azure master
```

Po dokončení nasazení se vraťte do okna prohlížeče, které se otevřelo v kroku **Přechod do aplikace**, a stiskněte tlačítko Aktualizovat.

![Aktualizovaná ukázková aplikace spuštěná ve službě Azure](media/quickstart-nodejs/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-web-app"></a>Správa vaší nové webové aplikace Azure

Pokud chcete spravovat webovou aplikaci, kterou jste vytvořili, přejděte na web <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

V levé nabídce klikněte na **App Services** a pak klikněte na název vaší webové aplikace Azure.

![Navigace portálem k webové aplikaci Azure](./media/quickstart-nodejs/nodejs-docs-hello-world-app-service-list.png)

Zobrazí se stránka s přehledem vaší webové aplikace. Tady můžete provádět základní úlohy správy, jako je procházení, zastavení, spuštění, restartování a odstranění. 

![Stránka služby App Service na webu Azure Portal](media/quickstart-nodejs/nodejs-docs-hello-world-app-service-detail.png)

Levá nabídka obsahuje odkazy na různé stránky pro konfiguraci vaší aplikace. 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="video"></a>Video

>[!VIDEO https://www.youtube.com/embed/S9eqK7xPKqU]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Node.js s databází MongoDB](tutorial-nodejs-mongodb-app.md)
