---
title: 'Úvodní příručka: Vytvoření webové aplikace Node.js'
description: Nasaďte svůj první Node.js Hello World do služby Azure App Service během několika minut. Nasazení pomocí balíčku ZIP, což je jeden z mnoha způsobů nasazení do služby App Service.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 08/23/2019
ms.custom: mvc, devcenter, seodec18
experimental: false
experiment_id: a231f2b4-2625-4d
ms.openlocfilehash: 84953c79093ebd4e53e7e7feef5ab72b8afce002
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80047530"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Vytvoření webové aplikace Node.js ve službě Azure

> [!NOTE]
> Tento článek nasadí aplikaci do služby App Service ve Windows. Nasazení do služby App Service v _Linuxu_ je popsané v tématu [Vytvoření webové aplikace v Node.js ve službě Azure App Service v Linuxu](./containers/quickstart-nodejs.md).
>

[Azure App Service ](overview.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů.  Tento rychlý start ukazuje, jak nasadit aplikaci Node.js do služby Azure App Service. Webovou aplikaci vytvoříte pomocí [prostředí Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), ale tyto příkazy můžete spustit také místně pomocí azure [CLI](/cli/azure/install-azure-cli). Ukázkový kód Node.js nasadíte do webové aplikace pomocí příkazu [config-zip zdroje nasazení az webapp.](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip)  

![Ukázková aplikace spuštěná v Azure](media/app-service-web-get-started-nodejs-poc/hello-world-in-browser.png)

Tento postup můžete použít v případě počítačů se systémem Mac, Windows nebo Linux. Dokončení kroků trvá asi tři minuty.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>Stažení ukázky

Ve službě Cloud Shell vytvořte adresář rychlého startu a přejděte do něj.

```console
mkdir quickstart

cd $HOME/quickstart
```

Potom spusťte následující příkaz, pomocí kterého do tohoto adresáře naklonujete úložiště ukázkové aplikace.

```console
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
```

Při spuštění příkaz zobrazí podobné informace jako v následujícím příkladu:

```output
Cloning into 'nodejs-docs-hello-world'...
remote: Counting objects: 40, done.
remote: Total 40 (delta 0), reused 0 (delta 0), pack-reused 40
Unpacking objects: 100% (40/40), done.
Checking connectivity... done.
```

> [!NOTE]
> Ukázkový index index.js nastaví naslouchací port na process.env.PORT. Tato proměnná prostředí je přiřazena službou App Service.
>

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-scus.md)]

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-scus.md)]

## <a name="create-a-web-app"></a>Vytvoření webové aplikace

V prostředí Cloud Shell vytvořte `myAppServicePlan` webovou aplikaci [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) v plánu služby App Service pomocí příkazu.

V následujícím příkladu nahraďte `<app_name>` globálně jedinečným názvem aplikace (platné znaky jsou `a-z`, `0-9` a `-`).

```azurecli-interactive
# Bash and Powershell
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name>
```

Po vytvoření webové aplikace Azure CLI zobrazí výstup podobný následujícímu příkladu:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="set-nodejs-runtime"></a>Nastavení modulu runtime Node.js

Nastavte za běhu uzlu na 10.14.1. Chcete-li zobrazit všechny [`az webapp list-runtimes`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes)podporované moduly runtimes, spusťte .

```azurecli-interactive
# Bash and Powershell
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WEBSITE_NODE_DEFAULT_VERSION=10.14.1
```

Přejděte do vaší nově vytvořené webové aplikace. Nahraďte `<app_name>` jedinečným názvem aplikace.

```http
http://<app_name>.azurewebsites.net
```

Zde je to, co vaše ![nová webová aplikace by měla vypadat: Prázdný web app stránku](media/app-service-web-get-started-nodejs-poc/app-service-web-service-created.png)

## <a name="deploy-zip-file"></a>Nasazení souboru ZIP

V prostředí Cloud Shell přejděte do kořenového adresáře aplikace a vytvořte nový soubor ZIP pro ukázkový projekt.

```console
cd nodejs-docs-hello-world  

zip -r myUpdatedAppFiles.zip *.*
```

Nasaďte soubor ZIP do webové aplikace pomocí příkazu [config-zip zdroje nasazení az webapp.](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip)  

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResourceGroup --name <app_name> --src myUpdatedAppFiles.zip
```

Tento příkaz nasadí soubory a adresáře ze souboru ZIP do vaší výchozí složky aplikací služby App Service (`\home\site\wwwroot`) a restartuje aplikaci. Pokud je nakonfigurovaný nějaký vlastní proces sestavení, spustí se také. Další informace naleznete v [dokumentaci kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

## <a name="browse-to-the-app"></a>Přechod do aplikace

V prohlížeči zadejte adresu nasazené aplikace.

```http
http://<app_name>.azurewebsites.net
```

Vzorový kód Node.js je spuštěný ve webové aplikaci služby Azure App Service.

![Ukázková aplikace spuštěná v Azure](media/app-service-web-get-started-nodejs-poc/hello-world-in-browser.png)

> [!NOTE]
> V Azure App Service se aplikace spouští ve službě IIS pomocí modulu [iisnode](https://github.com/Azure/iisnode). Kvůli povolení spuštění aplikace pomocí modulu iisnode obsahuje kořenový adresář aplikace soubor web.config. Tento soubor může číst služba IIS a dokumentaci k nastavením souvisejícím s modulem iisnode najdete v [úložišti iisnode na GitHubu](https://github.com/Azure/iisnode/blob/master/src/samples/configuration/web.config).

**Blahopřejeme!** Nasadili jste svoji první aplikaci Node.js do služby App Service.

## <a name="update-and-redeploy-the-code"></a>Aktualizace a opětovné nasazení kódu

V prostředí Cloud `code index.js` Shell, zadejte otevřít editor Cloud Shell.

![Index kódu.js](media/app-service-web-get-started-nodejs-poc/code-indexjs.png)

Proveďte malou změnu textu ve volání na `response.end`:

```javascript
response.end("Hello Azure!");
```

Uložte změny a ukončete editor. K uložení použijte příkaz `^S` a k zavření příkaz `^Q`.

Vytvořte soubor ZIP a nasaďte jej pomocí příkazu [az webapp deployment source config-zip.](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip)  

```azurecli-interactive
# Bash
zip -r myUpdatedAppFiles.zip *.*

az webapp deployment source config-zip --resource-group myResourceGroup --name <app_name> --src myUpdatedAppFiles.zip
```

Vraťte se do okna prohlížeče, které se otevřelo v kroku **Přechod do aplikace**, a aktualizujte zobrazení stránky.

![Aktualizovaná ukázková aplikace spuštěná ve službě Azure](media/app-service-web-get-started-nodejs-poc/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>Správa nové aplikace Azure

Pokud chcete spravovat webovou aplikaci, kterou jste vytvořili, přejděte na web <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

V levé nabídce klikněte na **App Services**a potom klikněte na název aplikace Azure.

![Přechod do aplikace Azure na portálu](./media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-list.png)

Zobrazí se stránka s přehledem vaší webové aplikace. Tady můžete provádět základní úlohy správy, jako je procházení, zastavení, spuštění, restartování a odstranění.

![Stránka služby App Service na webu Azure Portal](media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-detail.png)

Levá nabídka obsahuje odkazy na různé stránky pro konfiguraci vaší aplikace.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Node.js s MongoDB](app-service-web-tutorial-nodejs-mongodb-app.md)
