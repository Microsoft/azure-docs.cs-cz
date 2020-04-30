---
title: 'Rychlý Start: Vytvoření webové aplikace v Node. js'
description: Nasaďte první Hello World Node. js do Azure App Service v řádu minut. Nasadíte pomocí balíčku ZIP, který je jedním z mnoha způsobů, jak nasadit do App Service.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 08/23/2019
ms.custom: mvc, devcenter, seodec18
experimental: false
experiment_id: a231f2b4-2625-4d
ms.openlocfilehash: a185565ec125e327297883c63b1d50a2548c7853
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "82085993"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Vytvoření webové aplikace Node.js ve službě Azure

> [!NOTE]
> Tento článek nasadí aplikaci do služby App Service ve Windows. Nasazení do služby App Service v _Linuxu_ je popsané v tématu [Vytvoření webové aplikace v Node.js ve službě Azure App Service v Linuxu](./containers/quickstart-nodejs.md).
>

[Azure App Service ](overview.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů.  V tomto rychlém startu se dozvíte, jak nasadit aplikaci Node. js do Azure App Service. Webovou aplikaci vytvoříte pomocí [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), ale tyto příkazy můžete spustit i místně pomocí [Azure CLI](/cli/azure/install-azure-cli). Ukázkový kód Node. js nasadíte do webové aplikace pomocí příkazu [AZ WebApp Deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) .  

![Ukázková aplikace spuštěná v Azure](media/app-service-web-get-started-nodejs-poc/hello-world-in-browser.png)

Tento postup můžete použít v případě počítačů se systémem Mac, Windows nebo Linux. Dokončení kroků trvá přibližně tři minuty.

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
> Vzorový index. js nastaví port naslouchání na proces. env. PORT. Tato proměnná prostředí je přiřazena pomocí App Service.
>

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-scus.md)]

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-scus.md)]

## <a name="create-a-web-app"></a>Vytvoření webové aplikace

V Cloud Shell v plánu `myAppServicePlan` App Service pomocí [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) příkazu vytvořte webovou aplikaci.

V následujícím příkladu nahraďte `<app_name>` globálně jedinečným názvem aplikace (platné znaky jsou `a-z`, `0-9` a `-`).

```azurecli-interactive
# Bash and Powershell
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name>
```

Po vytvoření webové aplikace Azure CLI zobrazí výstup podobný následujícímu příkladu:

<pre>
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;app_name&gt;.azurewebsites.net",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>

### <a name="set-nodejs-runtime"></a>Nastavení modulu runtime Node.js

Nastavte modul runtime uzlu na 10.14.1. Pokud chcete zobrazit všechny podporované moduly runtime, [`az webapp list-runtimes`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes)spusťte příkaz.

```azurecli-interactive
# Bash and Powershell
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WEBSITE_NODE_DEFAULT_VERSION=10.14.1
```

Přejděte do vaší nově vytvořené webové aplikace. Nahraďte `<app_name>` jedinečným názvem aplikace.

```http
http://<app_name>.azurewebsites.net
```

Vaše nová webová aplikace by měla vypadat takto: ![prázdná stránka webové aplikace](media/app-service-web-get-started-nodejs-poc/app-service-web-service-created.png)

## <a name="deploy-zip-file"></a>Nasazení souboru ZIP

V Cloud Shell přejděte do kořenového adresáře vaší aplikace, vytvořte nový soubor ZIP pro ukázkový projekt.

```console
cd nodejs-docs-hello-world  

zip -r myUpdatedAppFiles.zip *.*
```

Pomocí příkazu [AZ WebApp Deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) nasaďte soubor zip do vaší webové aplikace.  

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResourceGroup --name <app_name> --src myUpdatedAppFiles.zip
```

Tento příkaz nasadí soubory a adresáře ze souboru ZIP do vaší výchozí složky aplikací služby App Service (`\home\site\wwwroot`) a restartuje aplikaci. Pokud je nakonfigurovaný nějaký vlastní proces sestavení, spustí se také. Další informace najdete v [dokumentaci k Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

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

V Cloud Shell zadejte `code index.js` , aby se otevřel Editor Cloud Shell.

![Index kódu. js](media/app-service-web-get-started-nodejs-poc/code-indexjs.png)

Proveďte malou změnu textu ve volání na `response.end`:

```javascript
response.end("Hello Azure!");
```

Uložte změny a ukončete Editor. K uložení použijte příkaz `^S` a k zavření příkaz `^Q`.

Vytvořte soubor ZIP a nasaďte ho pomocí příkazu [AZ WebApp Deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) .  

```azurecli-interactive
# Bash
zip -r myUpdatedAppFiles.zip *.*

az webapp deployment source config-zip --resource-group myResourceGroup --name <app_name> --src myUpdatedAppFiles.zip
```

Vraťte se do okna prohlížeče, které se otevřelo v kroku **Přechod do aplikace**, a aktualizujte zobrazení stránky.

![Aktualizovaná ukázková aplikace spuštěná ve službě Azure](media/app-service-web-get-started-nodejs-poc/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>Správa nové aplikace Azure

Pokud chcete spravovat webovou aplikaci, kterou jste vytvořili, přejděte na web <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

V nabídce vlevo klikněte na **App Services**a pak klikněte na název aplikace Azure.

![Přechod do aplikace Azure na portálu](./media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-list.png)

Zobrazí se stránka s přehledem vaší webové aplikace. Tady můžete provádět základní úlohy správy, jako je procházení, zastavení, spuštění, restartování a odstranění.

![Stránka služby App Service na webu Azure Portal](media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-detail.png)

Levá nabídka obsahuje odkazy na různé stránky pro konfiguraci vaší aplikace.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Node.js s MongoDB](app-service-web-tutorial-nodejs-mongodb-app.md)
