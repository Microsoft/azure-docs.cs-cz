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
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 04f08965d161e35a9ae4423ad5d3cf80cb407b8a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60396689"
---
# <a name="create-a-python-app-in-azure-app-service-on-linux-preview"></a>Vytvoření aplikace v Pythonu v Azure App Service v Linuxu (Preview)

V tomto rychlém startu nasadíte jednoduchou aplikaci v Pythonu do [App Service v Linuxu](app-service-linux-intro.md), která poskytuje je vysoce škálovatelnou a automatickými opravami pro hostování webů služby. Použití rozhraní příkazového řádku Azure ( [rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli)) prostřednictvím interaktivní, založené na prohlížeči Azure Cloud Shell, takže můžete postupovat podle kroky používají počítač Mac, Linux nebo Windows.

![Ukázková aplikace spuštěná ve službě Azure](media/quickstart-python/hello-world-in-browser.png)

## <a name="prerequisites"></a>Požadavky

K provedení kroků v tomto kurzu Rychlý start je potřeba:

* <a href="https://www.python.org/downloads/" target="_blank">Nainstalovat Python 3.7</a>
* <a href="https://git-scm.com/" target="_blank">Nainstalovat Git</a>.
* Předplatné Azure. Pokud je nemáte, vytvořte [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) předtím, než začnete.

## <a name="download-the-sample-locally"></a>Místní stažení ukázky

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

## <a name="download-the-sample"></a>Stažení ukázky

Ve službě Cloud Shell vytvořte adresář rychlého startu a přejděte do něj.

```bash
mkdir quickstart

cd quickstart
```

Potom spusťte následující příkaz, pomocí kterého do tohoto adresáře naklonujete úložiště ukázkové aplikace.

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

Při spuštění příkaz zobrazí podobné informace jako v následujícím příkladu:

```bash
Cloning into 'python-docs-hello-world'...
remote: Enumerating objects: 43, done.
remote: Total 43 (delta 0), reused 0 (delta 0), pack-reused 43
Unpacking objects: 100% (43/43), done.
Checking connectivity... done.
```

## <a name="create-a-web-app"></a>Vytvoření webové aplikace

Přejděte do adresáře, který obsahuje vzorový kód a spusťte příkaz `az webapp up`.

V následujícím příkladu nahraďte `<app-name>` s jedinečným názvem aplikace.

```bash
cd python-docs-hello-world

az webapp up -n <app-name>
```

Spuštění tohoto příkazu může trvat několik minut. Při spuštění příkaz zobrazí podobné informace jako v následujícím příkladu:

```json
The behavior of this command has been altered by the following extension: webapp
Creating Resource group 'appsvc_rg_Linux_CentralUS' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_CentralUS' ...
App service plan creation complete
Creating app '<app-name>' ....
Webapp creation complete
Creating zip with contents of dir /home/username/quickstart/python-docs-hello-world ...
Preparing to deploy contents to app.
All done.
{
  "app_url": "https:/<app-name>.azurewebsites.net",
  "location": "Central US",
  "name": "<app-name>",
  "os": "Linux",
  "resourcegroup": "appsvc_rg_Linux_CentralUS ",
  "serverfarm": "appsvc_asp_Linux_CentralUS",
  "sku": "BASIC",
  "src_path": "/home/username/quickstart/python-docs-hello-world ",
  "version_detected": "-",
  "version_to_create": "python|3.7"
}
```

[!INCLUDE [AZ Webapp Up Note](../../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>Přechod do aplikace

V prohlížeči zadejte adresu nasazené aplikace.

```bash
http://<app-name>.azurewebsites.net
```

Vzorový kód Pythonu je spuštěný ve službě App Service v Linuxu s použitím integrované image.

![Ukázková aplikace spuštěná ve službě Azure](media/quickstart-python/hello-world-in-browser.png)

**Blahopřejeme!** Nasadili jste svoji první aplikaci v Pythonu do služby App Service v Linuxu.

## <a name="update-locally-and-redeploy-the-code"></a>Místní aktualizace a opětovné nasazení kódu

Ve službě Cloud Shell zadejte `code application.py` otevřete editor pro Cloud Shell.

![Application.py kódu](media/quickstart-python/code-applicationpy.png)

 Proveďte malou změnu textu ve volání na `return`:

```python
return "Hello Azure!"
```

Uložte změny a zavřete editor. K uložení použijte příkaz `^S` a k zavření příkaz `^Q`.

Opětovné nasazení aplikace pomocí [ `az webapp up` ](/cli/azure/ext/webapp/webapp?view=azure-cli-latest.md#ext-webapp-az-webapp-up) příkazu. Nahraďte názvem vaší aplikace pro `<app-name>`a zadejte umístění pro `<location-name>` (pomocí jedné z hodnot z uvedené [ `az account list-locations` ](/cli/azure/appservice?view=azure-cli-latest.md#az-appservice-list-locations) příkaz).

```bash
az webapp up -n <app-name> -l <location-name>
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
> [Kurz: Aplikace v Pythonu s PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Konfigurace aplikace v Pythonu](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Kurz: Spuštění aplikace v Pythonu do vlastního kontejneru](tutorial-custom-docker-image.md)
