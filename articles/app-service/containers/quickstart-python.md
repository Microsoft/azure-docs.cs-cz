---
title: Nasazení aplikace v Pythonu ve službě Azure Web App for Containers
description: Postup nasazení image Dockeru s aplikací v Pythonu do služby Azure Web App for Containers.
keywords: azure app service, web app, python, docker, container
services: app-service
author: cephalin
manager: jeconnoc
ms.service: app-service
ms.devlang: python
ms.topic: quickstart
ms.date: 07/13/2018
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 6d328d8a3556f565e7eac8ee079bd191b7dcadef
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39433438"
---
# <a name="deploy-a-python-web-app-in-web-app-for-containers"></a>Nasazení webové aplikace v Pythonu ve službě Web App for Containers

[App Service v Linuxu](app-service-linux-intro.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů s využitím operačního systému Linux. Tento rychlý start ukazuje, jak vytvořit webovou aplikaci a pomocí vlastní image z centra Dockeru k ní nasadit jednoduchou aplikaci Flask. Webovou aplikaci vytvoříte pomocí [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

![Ukázková aplikace spuštěná ve službě Azure](media/quickstart-python/hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* <a href="https://git-scm.com/" target="_blank">Nainstalovat Git</a>.
* <a href="https://www.docker.com/community-edition" target="_blank">Nainstalovat Docker Community Edition</a>
* <a href="https://hub.docker.com/" target="_blank">Zaregistrovat si účet Centra Dockeru</a>

## <a name="download-the-sample"></a>Stažení ukázky

V okně terminálu naklonujte spuštěním následujících příkazů ukázkovou aplikaci do místního počítače a přejděte do adresáře, který obsahuje vzorový kód.

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
cd python-docs-hello-world
```

Toto úložiště obsahuje jednoduchou aplikaci Flask, kterou najdete ve složce _/app_, a soubor _Dockerfile_, který určuje tři věci:

- Použití základní image [tiangolo/uwsgi-nginx-flask:python3.6-alpine3.7](https://hub.docker.com/r/tiangolo/uwsgi-nginx-flask/).
- Kontejner musí naslouchat na portu 8000.
- Zkopírování adresáře `/app` do adresáře kontejneru `/app`.

Tato konfigurace se řídí [pokyny pro základní image](https://hub.docker.com/r/tiangolo/uwsgi-nginx-flask/).

## <a name="run-the-app-locally"></a>Místní spuštění aplikace

Spusťte aplikaci v kontejneru Dockeru.

```bash
docker build --rm -t flask-quickstart .
docker run --rm -it -p 8000:8000 flask-quickstart
```

Otevřete webový prohlížeč a přejděte do ukázkové aplikace na adrese `http://localhost:8000`.

Na stránce se zobrazí zpráva **Hello World** od ukázkové aplikace.

![Ukázková aplikace spuštěná místně](media/quickstart-python/localhost-hello-world-in-browser.png)

V okně terminálu zastavte kontejner stisknutím **Ctrl + C**.

## <a name="deploy-image-to-docker-hub"></a>Nasazení image do Centra Dockeru

Přihlaste se ke svému účtu Centra Dockeru. Po zobrazení výzvy zadejte přihlašovací údaje k Centru Dockeru.

```bash
docker login
```

Označte image a pak ji nasdílejte do nového _veřejného_ úložiště účtu Centra Dockeru s názvem `flask-quickstart`. Nahraďte *\<dockerhub_id>* svým ID účtu Centra Dockeru.

```bash
docker tag flask-quickstart <dockerhub_id>/flask-quickstart
docker push <dockerhub_id>/flask-quickstart
```

> [!NOTE]
> Příkaz `docker push` vytvoří veřejné úložiště, pokud zadané úložiště nenajde. Tento rychlý start pracuje s veřejným úložištěm v Centru Dockeru. Pokud chcete image nasdílet do privátního úložiště, budete muset později v Azure App Service nakonfigurovat své přihlašovací údaje Centra Dockeru. Viz níže v části [Vytvoření webové aplikace](#create-a-web-app).

Po nasdílení image ji můžete použít ve webové aplikaci Azure.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Vytvoření webové aplikace

Pomocí příkazu [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) vytvořte [webovou aplikaci](../app-service-web-overview.md) v plánu služby App Service `myAppServicePlan`. Nahraďte *\<app name>* globálně jedinečným názvem aplikace a nahraďte *\<dockerhub_id >* svým ID Centra Dockeru.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --deployment-container-image-name <dockerhub_id>/flask-quickstart
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
  "defaultHostName": "<app name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app name>.scm.azurewebsites.net/<app name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Pokud jste dříve použili privátní úložiště, musíte také ve službě App Service nakonfigurovat své přihlašovací údaje Centra Dockeru. Další informace získáte v tématu věnovaném [použití privátní image z Centra Dockeru](tutorial-custom-docker-image.md#use-a-private-image-from-docker-hub-optional).

### <a name="specify-container-port"></a>Zadání portu kontejneru

Jak je uvedeno v souboru _Dockerfile_, váš kontejner naslouchá na portu 8000. Aby služba App Service směrovala váš požadavek na správný port, budete muset nastavit *WEBSITES_PORT*.

Ve službě Cloud Shell spusťte příkaz [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set).


```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings WEBSITES_PORT=8000
```

## <a name="browse-to-the-app"></a>Přechod do aplikace

```bash
http://<app_name>.azurewebsites.net/
```

![Ukázková aplikace spuštěná ve službě Azure](media/quickstart-python/hello-world-in-browser.png)

> [!NOTE]
> První spuštění webové aplikace nějaký čas trvá, protože se musí stáhnout a spustit image z Centra Dockeru. Pokud se po delší době zobrazí chyba, stránku obnovte.

**Blahopřejeme!** Do služby Web App for Containers jste nasadili vlastní image Dockeru s aplikací v Pythonu.

## <a name="update-locally-and-redeploy"></a>Místní aktualizace a opětovné nasazení

Pomocí místního textového editoru otevřete soubor `app/main.py` v rámci aplikace v Pythonu a proveďte malou změnu textu vedle příkazu `return`:

```python
return 'Hello, Azure!'
```

Image znovu sestavte a nasdílejte do Centra Dockeru.

```bash
docker build --rm -t flask-quickstart .
docker tag flask-quickstart <dockerhub_id>/flask-quickstart
docker push <dockerhub_id>/flask-quickstart
```

Restartujte aplikaci ve službě Cloud Shell. Restartováním aplikace zajistí, že se použijí všechna nastavení a z registru se stáhne nejnovější kontejner.

```azurecli-interactive
az webapp restart --resource-group myResourceGroup --name <app_name>
```

Počkejte asi 15 sekund, než služba App Service stáhne aktualizovanou image. Vraťte se do okna prohlížeče, které se otevřelo v kroku **Přechod do aplikace**, a aktualizujte zobrazení stránky.

![Aktualizovaná ukázková aplikace spuštěná ve službě Azure](media/quickstart-python/hello-azure-in-browser.png)

## <a name="manage-your-azure-web-app"></a>Správa webové aplikace Azure

Přejděte na web [Azure Portal](https://portal.azure.com) k webové aplikaci, kterou jste vytvořili.

V levé nabídce klikněte na **App Services** a pak klikněte na název vaší webové aplikace Azure.

![Navigace portálem k webové aplikaci Azure](./media/quickstart-python/app-service-list.png)

Na začátku portál zobrazí stránku **Přehled** vaší webové aplikace. Tato stránka poskytuje přehled, jak si vaše aplikace stojí. Tady můžete také provést základní úlohy správy, jako je procházení, zastavení, spuštění, restartování a odstranění. Karty na levé straně stránky obsahují různé stránky konfigurace, které můžete otevřít.

![Stránka služby App Service na webu Azure Portal](./media/quickstart-python/app-service-detail.png)

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Python sh PostgreSQL](tutorial-docker-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Použití vlastní image](tutorial-custom-docker-image.md)