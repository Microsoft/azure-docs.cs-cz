---
title: 'Rychlý start: Vytvoření webové aplikace PHP'
description: Nasaďte svůj první PHP Hello World do služby Azure App Service během několika minut. Nasazujete pomocí Gitu, což je jeden z mnoha způsobů nasazení do služby App Service.
ms.assetid: 6feac128-c728-4491-8b79-962da9a40788
ms.topic: quickstart
ms.date: 08/24/2018
ms.custom: mvc, cli-validate, seodec18
ms.openlocfilehash: 2813d96fdd9fcd588b78cbfb58fe57bf58a4fe68
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80047354"
---
# <a name="create-a-php-web-app-in-azure"></a>Vytvoření webové aplikace v PHP v Azure

> [!NOTE]
> Tento článek nasadí aplikaci do služby App Service ve Windows. Nasazení do služby App Service v _Linuxu_ je popsané v tématu [Vytvoření webové aplikace v PHP ve službě App Service v Linuxu](./containers/quickstart-php.md).
>

[Azure App Service ](overview.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů.  Tento úvodní kurz ukazuje, jak nasadit aplikaci PHP do služby Azure App Service. Pomocí [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) ve službě Cloud Shell vytvoříte webovou aplikaci a pomocí Gitu do této webové aplikace nasadíte vzorový kód PHP.

![Ukázková aplikace spuštěná v Azure](media/app-service-web-get-started-php/hello-world-in-browser.png)

Tento postup můžete použít v případě počítačů se systémem Mac, Windows nebo Linux. Pokud máte nainstalované všechny požadované prostředky, zabere vám tento postup zhruba pět minut.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

K provedení kroků v tomto kurzu Rychlý start je potřeba:

* <a href="https://git-scm.com/" target="_blank">Nainstalovat Git</a>.
* <a href="https://php.net/manual/install.php" target="_blank">Instalace PHP</a>

## <a name="download-the-sample-locally"></a>Místní stažení ukázky

V okně terminálu spusťte následující příkazy. Tím se na váš místní počítač naklonuje ukázková aplikace a přejdete do adresáře se vzorovým kódem. 

```bash
git clone https://github.com/Azure-Samples/php-docs-hello-world
cd php-docs-hello-world
```

## <a name="run-the-app-locally"></a>Místní spuštění aplikace

Spusťte aplikaci místně, abyste viděli, jak by měla vypadat po nasazení do Azure. Otevřete okno terminálu a pomocí příkazu `php` spusťte integrovaný webový server PHP.

```bash
php -S localhost:8080
```

Otevřete webový prohlížeč a přejděte do ukázkové aplikace na adrese `http://localhost:8080`.

Na stránce se zobrazí zpráva **Hello World!** z ukázkové aplikace.

![Ukázková aplikace spuštěná místně](media/app-service-web-get-started-php/localhost-hello-world-in-browser.png)

V okně terminálu ukončete webový server stisknutím **Ctrl + C**.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group.md)]

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan.md)]

## <a name="create-a-web-app"></a>Vytvoření webové aplikace

V prostředí Cloud Shell vytvořte `myAppServicePlan` webovou aplikaci [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) v plánu služby App Service pomocí příkazu. 

V následujícím příkladu nahraďte `<app_name>` globálně jedinečným názvem aplikace (platné znaky jsou `a-z`, `0-9` a `-`). Modul runtime je nastavený na `PHP|7.0`. Chcete-li zobrazit všechny [`az webapp list-runtimes`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes)podporované moduly runtimes, spusťte . 

```azurecli-interactive
# Bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "PHP|7.0" --deployment-local-git
# PowerShell
az --% webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "PHP|7.0" --deployment-local-git
```

Po vytvoření webové aplikace Azure CLI zobrazí výstup podobný následujícímu příkladu:

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
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
Vytvořili jste prázdnou novou webovou aplikaci s povoleným nasazením gitu.

> [!NOTE]
> Adresa URL vzdáleného úložiště Git se zobrazuje ve vlastnosti `deploymentLocalGitUrl` ve formátu `https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git`. Tuto adresu URL si uložte, protože ji budete potřebovat později.
>

Přejděte do vaší nově vytvořené webové aplikace. Nahraďte _ &lt;název aplikace>_ jedinečným názvem aplikace vytvořeným v předchozím kroku.

```bash
http://<app name>.azurewebsites.net
```

Vaše nová webová aplikace by měla vypadat takto:

![Prázdná stránka webové aplikace](media/app-service-web-get-started-php/app-service-web-service-created.png)

[!INCLUDE [Push to Azure](../../includes/app-service-web-git-push-to-azure.md)] 

```bash
Counting objects: 2, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (2/2), 352 bytes | 0 bytes/s, done.
Total 2 (delta 1), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '25f18051e9'.
remote: Generating deployment script.
remote: Running deployment command...
remote: Handling Basic Web Site deployment.
remote: Kudu sync from: '/home/site/repository' to: '/home/site/wwwroot'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'README.md'
remote: Copying file: 'index.php'
remote: Ignoring: .git
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
   cc39b1e..25f1805  master -> master
```

## <a name="browse-to-the-app"></a>Přechod do aplikace

V prohlížeči zadejte adresu nasazené aplikace.

```
http://<app_name>.azurewebsites.net
```

Vzorový kód PHP je spuštěný ve webové aplikaci služby Azure App Service.

![Ukázková aplikace spuštěná v Azure](media/app-service-web-get-started-php/hello-world-in-browser.png)

**Blahopřejeme!** Nasadili jste svoji první aplikaci v PHP do služby App Service.

## <a name="update-locally-and-redeploy-the-code"></a>Místní aktualizace a opětovné nasazení kódu

Pomocí místního textového editoru otevřete soubor `index.php`, který je součástí PHP aplikace, a proveďte malou změnu textu v řetězci vedle `echo`:

```php
echo "Hello Azure!";
```

V místním okně terminálu potvrďte změny v Gitu a potom nasdílejte změny kódu do Azure.

```bash
git commit -am "updated output"
git push azure master
```

Po dokončení nasazení se vraťte do okna prohlížeče, které se otevřelo v kroku **Přechod do aplikace**, a aktualizujte zobrazení stránky.

![Aktualizovaná ukázková aplikace spuštěná ve službě Azure](media/app-service-web-get-started-php/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>Správa nové aplikace Azure

1. Pokud chcete spravovat webovou aplikaci, kterou jste vytvořili, přejděte na web <a href="https://portal.azure.com" target="_blank">Azure Portal</a>. Vyhledejte a vyberte **služby App Services**.

    ![Hledání služeb aplikace, portál Azure, vytvoření webové aplikace PHP](media/app-service-web-get-started-php/navigate-to-app-services-in-the-azure-portal.png)

2. Vyberte název aplikace Azure.

    ![Přechod do aplikace Azure na portálu](./media/app-service-web-get-started-php/php-docs-hello-world-app-service-list.png)

    Zobrazí se stránka **Přehled** vaší webové aplikace. Zde můžete provádět základní úlohy správy, jako **je Procházet**, **Zastavit**, **Restartovat**a **Odstranit**.

    ![Stránka služby App Service na webu Azure Portal](media/app-service-web-get-started-php/php-docs-hello-world-app-service-detail.png)

    Nabídka webové aplikace nabízí různé možnosti konfigurace aplikace. 

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [PHP s databází MySQL](app-service-web-tutorial-php-mysql.md)
