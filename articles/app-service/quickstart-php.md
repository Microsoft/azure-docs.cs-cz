---
title: 'Rychlý Start: Vytvoření webové aplikace v PHP'
description: Nasaďte první Hello World PHP do Azure App Service v řádu minut. Nasadíte pomocí Gitu, což je jedním z mnoha způsobů, jak nasadit do App Service.
ms.assetid: 6feac128-c728-4491-8b79-962da9a40788
ms.topic: quickstart
ms.date: 08/01/2020
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 848b3dd60e4c8ea5ac1fae0b7632fa5a54e07dd1
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007785"
---
# <a name="create-a-php-web-app-in-azure-app-service"></a>Vytvoření webové aplikace v PHP v Azure App Service

::: zone pivot="platform-windows"  
[Azure App Service ](overview.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů.  V tomto kurzu rychlý Start se dozvíte, jak nasadit aplikaci v PHP, která se Azure App Service ve Windows.
::: zone-end  

::: zone pivot="platform-linux"
[Azure App Service ](overview.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů.  V tomto kurzu Rychlý start se dozvíte, jak nasadit aplikaci v PHP do služby Azure App Service v Linuxu.
::: zone-end  

Pomocí [Azure CLI](/cli/azure/get-started-with-azure-cli) ve službě Cloud Shell vytvoříte webovou aplikaci a pomocí Gitu do této webové aplikace nasadíte vzorový kód PHP.

![Ukázková aplikace spuštěná v Azure](media/quickstart-php/hello-world-in-browser.png)

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

![Ukázková aplikace spuštěná místně](media/quickstart-php/localhost-hello-world-in-browser.png)

V okně terminálu ukončete webový server stisknutím **Ctrl + C**.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user.md)]

::: zone pivot="platform-windows"  
[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group.md)]
::: zone-end  

::: zone pivot="platform-linux"
[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-linux.md)]
::: zone-end

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan.md)]

## <a name="create-a-web-app"></a>Vytvoření webové aplikace

V Cloud Shell v `myAppServicePlan` plánu App Service pomocí příkazu vytvořte webovou aplikaci [`az webapp create`](/cli/azure/webapp#az-webapp-create) . 

V následujícím příkladu nahraďte `<app-name>` globálně jedinečným názvem aplikace (platné znaky jsou `a-z`, `0-9` a `-`). Modul runtime je nastavený na `PHP|7.4`. Pokud chcete zobrazit všechny podporované moduly runtime, spusťte příkaz [`az webapp list-runtimes`](/cli/azure/webapp#az-webapp-list-runtimes) . 

```azurecli-interactive
# Bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.4" --deployment-local-git
# PowerShell
az --% webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.4" --deployment-local-git
```

> [!NOTE]
> Symbol stop-Analyze `(--%)` , představený v powershellu 3,0, směruje prostředí PowerShell na neinterpretaci vstupu jako příkazů nebo výrazů PowerShellu.
>

Po vytvoření webové aplikace Azure CLI zobrazí výstup podobný následujícímu příkladu:

<pre>
Local git is configured with url of 'https://&lt;username&gt;@&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;app-name&gt;.azurewebsites.net",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>

Vytvořili jste prázdnou novou webovou aplikaci s povoleným nasazením Gitu.

> [!NOTE]
> Adresa URL vzdáleného úložiště Git se zobrazuje ve vlastnosti `deploymentLocalGitUrl` ve formátu `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git`. Tuto adresu URL si uložte, protože ji budete potřebovat později.
>

Přejděte do vaší nově vytvořené webové aplikace. Nahraďte _&lt; název aplikace>_ jedinečným názvem aplikace vytvořeným v předchozím kroku.

```bash
http://<app-name>.azurewebsites.net
```

Vaše nová webová aplikace by měla vypadat takto:

![Prázdná stránka webové aplikace](media/quickstart-php/app-service-web-service-created.png)

[!INCLUDE [Push to Azure](../../includes/app-service-web-git-push-to-azure.md)] 

<pre>
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
To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
   cc39b1e..25f1805  master -> master
</pre>

## <a name="browse-to-the-app"></a>Přechod do aplikace

V prohlížeči zadejte adresu nasazené aplikace.

```
http://<app-name>.azurewebsites.net
```

Vzorový kód PHP je spuštěný ve webové aplikaci služby Azure App Service.

![Ukázková aplikace spuštěná v Azure](media/quickstart-php/hello-world-in-browser.png)

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

![Aktualizovaná ukázková aplikace spuštěná ve službě Azure](media/quickstart-php/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>Správa nové aplikace Azure

1. Pokud chcete spravovat webovou aplikaci, kterou jste vytvořili, přejděte na web <a href="https://portal.azure.com" target="_blank">Azure Portal</a>. Vyhledejte a vyberte **App Services**.

    ![Hledání App Services, Azure Portal a vytvoření webové aplikace v PHP](media/quickstart-php/navigate-to-app-services-in-the-azure-portal.png)

2. Vyberte název aplikace Azure.

    ![Přechod do aplikace Azure na portálu](./media/quickstart-php/php-docs-hello-world-app-service-list.png)

    Zobrazí se stránka s **přehledem** vaší webové aplikace. Tady můžete provádět základní úlohy správy, jako je **procházení**, **zastavení**, **restartování** a **odstranění**.

    ![Stránka služby App Service na webu Azure Portal](media/quickstart-php/php-docs-hello-world-app-service-detail.png)

    Nabídka webové aplikace nabízí různé možnosti konfigurace aplikace. 

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [PHP s MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [Konfigurace aplikace PHP](configure-language-php.md)
