---
title: Vytvoření aplikace v Pythonu v Linuxu – služba Azure App Service | Dokumentace Microsoftu
description: Během několika minut můžete nasadit svou první aplikaci Hello world v Pythonu ve službě Azure App Service v Linuxu.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 01/23/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: be78c91a4fb5c1e79e7b58620f65c9f17bfb4bae
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55226481"
---
# <a name="create-a-python-app-in-azure-app-service-on-linux-preview"></a>Vytvoření aplikace v Pythonu v Azure App Service v Linuxu (Preview)

[App Service v Linuxu](app-service-linux-intro.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů s využitím operačního systému Linux. Tento rychlý start ukazuje, jak nasadit aplikaci v Pythonu na integrovanou image Pythonu (verze Preview) ve službě App Service v Linuxu pomocí [Azure CLI](/cli/azure/install-azure-cli).

Postup v tomto článku můžete použít v případě počítačů se systémem Mac, Windows nebo Linux.

![Ukázková aplikace spuštěná ve službě Azure](media/quickstart-python/hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

K provedení kroků v tomto kurzu Rychlý start je potřeba:

* <a href="https://www.python.org/downloads/" target="_blank">Nainstalovat Python 3.7</a>
* <a href="https://git-scm.com/" target="_blank">Nainstalovat Git</a>.

## <a name="download-the-sample"></a>Stažení ukázky

V okně terminálu naklonujte spuštěním následujících příkazů ukázkovou aplikaci do místního počítače a přejděte do adresáře s ukázkovým kódem.

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
cd python-docs-hello-world
```

Obsahuje úložiště *application.py*, která informuje službu App Service, že úložiště obsahuje aplikaci Flask. Další informace najdete v tématu [proces spuštění kontejneru a přizpůsobení](how-to-configure-python.md).

## <a name="run-the-app-locally"></a>Místní spuštění aplikace

Spusťte aplikaci místně, abyste viděli, jak by měla vypadat po nasazení do Azure. Otevřete okno terminálu a pomocí následujících příkazů nainstalujte požadované závislosti a spusťte integrovaný vývojový server. 

```bash
# In Bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
FLASK_APP=application.py flask run

# In PowerShell
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
Set-Item Env:FLASK_APP ".\application.py"
flask run
```

Otevřete webový prohlížeč a přejděte do ukázkové aplikace na adrese `http://localhost:5000/`.

Na stránce se zobrazí zpráva **Hello World!** z ukázkové aplikace.

![Ukázková aplikace spuštěná místně](media/quickstart-python/hello-world-in-browser.png)

V okně terminálu ukončete webový server stisknutím **Ctrl + C**.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Vytvoření webové aplikace

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-python-linux-no-h.md)]

Přejděte na web a zobrazte nově vytvořenou aplikaci s použitím integrované image. Nahraďte  _&lt;název aplikace >_ názvem vaší aplikace.

```bash
http://<app_name>.azurewebsites.net
```

Zde je, jak by měla vypadat nové aplikace:

![Stránka prázdné aplikace](media/quickstart-php/app-service-web-service-created.png)

[!INCLUDE [Push to Azure](../../../includes/app-service-web-git-push-to-azure.md)] 

```bash
Counting objects: 42, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (39/39), done.
Writing objects: 100% (42/42), 9.43 KiB | 0 bytes/s, done.
Total 42 (delta 15), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'c40efbb40e'.
remote: Generating deployment script.
remote: Generating deployment script for python Web Site
.
.
.
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://user2234@cephalin-python.scm.azurewebsites.net/cephalin-python.git
 * [new branch]      master -> master
 ```

## <a name="browse-to-the-app"></a>Přechod do aplikace

V prohlížeči zadejte adresu nasazené aplikace.

```bash
http://<app_name>.azurewebsites.net
```

Vzorový kód Pythonu je spuštěný ve službě App Service v Linuxu s použitím integrované image.

![Ukázková aplikace spuštěná ve službě Azure](media/quickstart-python/hello-world-in-browser.png)

**Blahopřejeme!** Nasadili jste svoji první aplikaci v Pythonu do služby App Service v Linuxu.

## <a name="update-locally-and-redeploy-the-code"></a>Místní aktualizace a opětovné nasazení kódu

V místním úložišti otevřete soubor `application.py` a proveďte malou změnu textu na posledním řádku:

```python
return "Hello Azure!"
```

Potvrďte změny v Gitu a potom odešlete změny kódu do Azure.

```bash
git commit -am "updated output"
git push azure master
```

Po dokončení nasazení se vraťte do okna prohlížeče, které se otevřelo v kroku **Přechod do aplikace**, a aktualizujte zobrazení stránky.

![Aktualizovaná ukázková aplikace spuštěná ve službě Azure](media/quickstart-python/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>Správa nové aplikace Azure

Přejděte <a href="https://portal.azure.com" target="_blank">webu Azure portal</a> ke správě aplikace, které jste vytvořili.

V levé nabídce klikněte na tlačítko **App Services**a pak klikněte na název aplikace Azure.

![Přechod do aplikace Azure na portálu](./media/quickstart-python/app-service-list.png)

Se zobrazí stránka s přehledem vaší aplikace. Tady můžete provádět základní úlohy správy, jako je procházení, zastavení, spuštění, restartování a odstranění.

![Stránka služby App Service na webu Azure Portal](media/quickstart-python/app-service-detail.png)

Levá nabídka obsahuje odkazy na různé stránky pro konfiguraci vaší aplikace. 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Další postup

Integrovaná image v Pythonu v rámci služby App Service v Linuxu je aktuálně ve verzi Preview a je možné přizpůsobit příkaz sloužící ke spuštění vaší aplikace. Produkční aplikace v Pythonu můžete místo toho vytvářet taky s využitím vlastního kontejneru.

> [!div class="nextstepaction"]
> [Python sh PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Konfigurace vlastního spouštěcího příkazu](how-to-configure-python.md#customize-startup-command)

> [!div class="nextstepaction"]
> [Řešení potíží](how-to-configure-python.md#troubleshooting)

> [!div class="nextstepaction"]
> [Použití vlastní image](tutorial-custom-docker-image.md)
