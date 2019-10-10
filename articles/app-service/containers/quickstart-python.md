---
title: Vytvoření aplikace v Pythonu na platformě Linux-Azure App Service | Microsoft Docs
description: Nasaďte svou první aplikaci Hello World v jazyce Python v Azure App Service v systému Linux v řádu minut.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: cephalin
experimental: true
experiment_id: 1e304dc9-5add-4b
ms.openlocfilehash: 253e2e5015c2cb7a71386b88b4d86ac6bff6a0fe
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242464"
---
# <a name="create-a-python-app-in-azure-app-service-on-linux"></a>Vytvoření aplikace v Pythonu v Azure App Service v systému Linux

V tomto rychlém startu nasadíte jednoduchou aplikaci v Pythonu, která [App Service na Linux](app-service-linux-intro.md), která poskytuje vysoce škálovatelnou službu s automatickými opravami pro hostování webů. Použijete rozhraní příkazového řádku Azure ( [Azure CLI](/cli/azure/install-azure-cli)) prostřednictvím interaktivního Azure Cloud Shell založeného na prohlížeči, takže můžete postupovat podle pokynů k použití počítače se systémem Mac, Linux nebo Windows.

![Ukázková aplikace spuštěná v Azure](media/quickstart-python/hello-world-in-browser.png)

## <a name="prerequisites"></a>Požadované součásti

K dokončení tohoto rychlého startu:

* <a href="https://www.python.org/downloads/" target="_blank">Instalace Pythonu 3,7</a>
* <a href="https://git-scm.com/" target="_blank">Nainstalovat git</a>
* Předplatné Azure. Pokud ho ještě nemáte, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.

## <a name="download-the-sample-locally"></a>Místní stažení ukázky

V okně terminálu spuštěním následujících příkazů naklonujte ukázkovou aplikaci do místního počítače a přejděte do adresáře s ukázkovým kódem.

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
cd python-docs-hello-world
```

Úložiště obsahuje objekt *Application.py*, který oznamuje App Service, že úložiště obsahuje aplikaci v baňce. Další informace najdete v tématu [proces spuštění a přizpůsobení kontejneru](how-to-configure-python.md).

## <a name="run-the-app-locally"></a>Místní spuštění aplikace

Spusťte aplikaci místně, abyste viděli, jak by měla vypadat při nasazení do Azure. Otevřete okno terminálu a pomocí níže uvedených příkazů nainstalujte požadované závislosti a spusťte integrovaný vývojový server. 

```bash
# In Bash (for Linux or Mac)
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
FLASK_APP=application.py flask run
```
```powershell
# In Powershell (for Windows)
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
Set-Item Env:FLASK_APP ".\application.py"
flask run
```

Otevřete webový prohlížeč a přejít na ukázkovou aplikaci na adrese `http://localhost:5000/`.

Zobrazí se **Hello World.** Zpráva z ukázkové aplikace zobrazené na stránce.

![Ukázková aplikace spuštěná místně](media/quickstart-python/hello-world-in-browser.png)

V okně terminálu ukončete webový server stisknutím **kombinace kláves CTRL + C** .

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>Stažení ukázky

V Cloud Shell vytvořte adresář pro rychlý Start a pak na něj přejděte.

```bash
mkdir quickstart

cd $HOME/quickstart
```

Dále spusťte následující příkaz, který naklonuje úložiště ukázkové aplikace do adresáře pro rychlý Start.

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

Při spuštění se zobrazí podobné informace jako v následujícím příkladu:

```bash
Cloning into 'python-docs-hello-world'...
remote: Enumerating objects: 43, done.
remote: Total 43 (delta 0), reused 0 (delta 0), pack-reused 43
Unpacking objects: 100% (43/43), done.
Checking connectivity... done.
```

## <a name="create-a-web-app"></a>Vytvoření webové aplikace

Přejděte do adresáře, který obsahuje vzorový kód a spusťte příkaz `az webapp up`.

V následujícím příkladu nahraďte `<app-name>` globálně jedinečným názvem aplikace (*platné znaky jsou `a-z`, `0-9` a `-`* ).

```bash
cd python-docs-hello-world

az webapp up -n <app-name>
```

Spuštění tohoto příkazu může trvat několik minut. Při spuštění se zobrazí podobné informace jako v následujícím příkladu:

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

## <a name="browse-to-the-app"></a>Přejít k aplikaci

Vyhledejte nasazenou aplikaci pomocí webového prohlížeče.

```bash
http://<app-name>.azurewebsites.net
```

Vzorový kód Pythonu je spuštěný v App Service v systému Linux s integrovanou imagí.

![Ukázková aplikace spuštěná v Azure](media/quickstart-python/hello-world-in-browser.png)

**Blahopřejeme!** Nasadili jste svoji první aplikaci v Pythonu, abyste App Service na Linux.

## <a name="update-locally-and-redeploy-the-code"></a>Místní aktualizace a opětovné nasazení kódu

V Cloud Shell otevřete Editor Cloud Shell zadáním `code application.py`.

![Application.py kódu](media/quickstart-python/code-applicationpy.png)

 Proveďte malou změnu textu ve volání `return`:

```python
return "Hello Azure!"
```

Uložte změny a ukončete Editor. Pomocí příkazu `^S` uložte a `^Q` pro ukončení.

Znovu nasaďte aplikaci pomocí příkazu [`az webapp up`](/cli/azure/webapp#az-webapp-up) . Nahraďte název vaší aplikace `<app-name>` a zadejte umístění pro `<location-name>` (pomocí jedné z hodnot zobrazených v příkazu [`az account list-locations`](/cli/azure/appservice?view=azure-cli-latest.md#az-appservice-list-locations) ).

```bash
az webapp up -n <app-name> -l <location-name>
```

Po dokončení nasazení přepněte zpátky do okna prohlížeče, které se otevřelo v kroku **přechod do aplikace** , a aktualizujte stránku.

![Aktualizovaná ukázková aplikace spuštěná v Azure](media/quickstart-python/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>Správa nové aplikace Azure

Chcete-li spravovat aplikaci, kterou jste vytvořili, otevřete <a href="https://portal.azure.com" target="_blank">Azure Portal</a> .

V nabídce vlevo vyberte **App Services**a pak vyberte název aplikace Azure.

![Navigace na portálu do aplikace Azure](./media/quickstart-python/app-service-list.png)

Zobrazí se stránka s přehledem vaší aplikace. Tady můžete provádět základní úlohy správy, jako je procházení, zastavení, spuštění, restartování a odstranění.

![Stránka App Service v Azure Portal](media/quickstart-python/app-service-detail.png)

V nabídce vlevo najdete různé stránky pro konfiguraci vaší aplikace. 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: webová aplikace Python (Django) s PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Konfigurace aplikace v Pythonu](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Kurz: spuštění aplikace v Pythonu ve vlastním kontejneru](tutorial-custom-docker-image.md)
