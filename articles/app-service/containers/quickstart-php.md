---
title: 'Rychlý Start: Vytvoření aplikace v PHP na platformě Linux'
description: Začněte s aplikacemi pro Linux v Azure App Service nasazením první aplikace v PHP do kontejneru Linux v App Service.
ms.assetid: 6feac128-c728-4491-8b79-962da9a40788
ms.topic: quickstart
ms.date: 05/25/2020
ms.custom: mvc, cli-validatem seodec18
ms.openlocfilehash: f0307088d274c9afee8fb8816b41a6357b4ce9f2
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/26/2020
ms.locfileid: "83848068"
---
# <a name="create-a-php-app-in-app-service-on-linux"></a>Vytvoření aplikace v PHP v App Service v systému Linux

> [!NOTE]
> Tento článek nasadí aplikaci do služby App Service v Linuxu. Pokud chcete nasadit nástroj na App Service ve _Windows_, přečtěte si téma [Vytvoření aplikace v php v Azure](../app-service-web-get-started-php.md).
>

[App Service v systému Linux](app-service-linux-intro.md) poskytuje vysoce škálovatelnou službu s automatickými opravami pro hostování webů pomocí operačního systému Linux. V tomto kurzu rychlý Start se dozvíte, jak nasadit aplikaci PHP, která Azure App Service na Linux pomocí [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).

![Ukázková aplikace spuštěná v Azure](media/quickstart-php/hello-world-in-browser.png)

Postup v tomto článku můžete použít v případě počítačů se systémem Mac, Windows nebo Linux.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

K provedení kroků v tomto kurzu Rychlý start je potřeba:

* <a href="https://git-scm.com/" target="_blank">Nainstalovat Git</a>.
* <a href="https://php.net" target="_blank">Instalace PHP</a>

## <a name="download-the-sample"></a>Stažení ukázky

V okně terminálu naklonujte spuštěním následujících příkazů ukázkovou aplikaci do místního počítače a přejděte do adresáře, který obsahuje vzorový kód.

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

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Vytvoření webové aplikace

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-php-linux-no-h.md)] 

Přejděte na web a zobrazte nově vytvořenou aplikaci s použitím integrované image. Nahraďte _ &lt; název aplikace>_ názvem vaší aplikace.

```bash
http://<app-name>.azurewebsites.net
```

Vaše nová aplikace by měla vypadat takto:

![Prázdná stránka aplikace](media/quickstart-php/app-service-web-service-created.png)

[!INCLUDE [Push to Azure](../../../includes/app-service-web-git-push-to-azure.md)] 

<pre>
Counting objects: 26, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (23/23), done.
Writing objects: 100% (26/26), 4.95 KiB | 0 bytes/s, done.
Total 26 (delta 9), reused 0 (delta 0)
remote: Deploy Async
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'df425ea6ef'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
remote: .
remote: .
remote: .
remote: Deployment successful.
remote: Deployment Logs : 'https://&lt;app-name&gt;.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/.../log'
To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
 * [new branch]      master -> master
</pre>

## <a name="browse-to-the-app"></a>Přechod do aplikace

V prohlížeči zadejte adresu nasazené aplikace.

```bash
http://<app-name>.azurewebsites.net
```

Vzorový kód PHP je spuštěný v App Service v systému Linux s integrovanou imagí.

![Ukázková aplikace spuštěná v Azure](media/quickstart-php/hello-world-in-browser.png)

**Blahopřejeme!** Nasadili jste svoji první aplikaci v PHP do služby App Service v Linuxu.

## <a name="update-locally-and-redeploy-the-code"></a>Místní aktualizace a opětovné nasazení kódu

V místním adresáři otevřete soubor `index.php`, který je součástí aplikace v PHP, a proveďte malou změnu textu v řetězci vedle `echo`:

```php
echo "Hello Azure!";
```

Potvrďte změny v Gitu a potom odešlete změny kódu do Azure.

```bash
git commit -am "updated output"
git push azure master
```

Po dokončení nasazení se vraťte do okna prohlížeče, které se otevřelo v kroku **Přechod do aplikace**, a aktualizujte zobrazení stránky.

![Aktualizovaná ukázková aplikace spuštěná ve službě Azure](media/quickstart-php/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>Správa nové aplikace Azure

Chcete-li spravovat aplikaci, kterou jste vytvořili, otevřete <a href="https://portal.azure.com" target="_blank">Azure Portal</a> .

V nabídce vlevo klikněte na **App Services**a pak klikněte na název aplikace Azure.

![Přechod do aplikace Azure na portálu](./media/quickstart-php/php-docs-hello-world-app-service-list.png)

Zobrazí se stránka s přehledem vaší aplikace. Tady můžete provádět základní úlohy správy, jako je procházení, zastavení, spuštění, restartování a odstranění.

![Stránka služby App Service na webu Azure Portal](media/quickstart-php/php-docs-hello-world-app-service-detail.png)

Levá nabídka obsahuje odkazy na různé stránky pro konfiguraci vaší aplikace. 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: aplikace PHP s MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [Konfigurace aplikace PHP](configure-language-php.md)
