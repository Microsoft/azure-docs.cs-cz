---
title: 'Rychlý Start: Vytvoření aplikace v Pythonu na platformě Linux – Azure App Service'
description: Během několika minut můžete nasadit svou první aplikaci Hello world v Pythonu ve službě Azure App Service v Linuxu.
services: app-service\web
documentationcenter: ''
author: msangapu-msft
ms.topic: quickstart
ms.date: 1/14/2020
ms.author: msangapu
ms.custom: seo-python-october2019
experimental: false
experiment_id: 01a9132f-eaab-4c
ms.openlocfilehash: 68dc36ce96737fe8395280c3a833e359084d2fee
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2020
ms.locfileid: "78246859"
---
# <a name="quickstart-create-a-python-app-in-azure-app-service-on-linux"></a>Rychlý Start: Vytvoření aplikace v Pythonu v Azure App Service v systému Linux

V tomto rychlém startu nasadíte webovou aplikaci v Pythonu, která se [App Service v systému Linux](app-service-linux-intro.md), vysoce škálovatelná služba s automatickými opravami pro hostování webů v Azure. Použijete místní [rozhraní příkazového řádku Azure (CLI)](/cli/azure/install-azure-cli) na počítači se systémem Mac, Linux nebo Windows. Webová aplikace, kterou nakonfigurujete, využívá bezplatnou App Serviceovou úroveň, takže v tomto článku nebudete mít žádné náklady.

Pokud dáváte přednost nasazení aplikací prostřednictvím integrovaného vývojového prostředí (IDE), přečtěte si téma [nasazení aplikací Python pro App Service z Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="prerequisites"></a>Předpoklady

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) .
- <a href="https://www.python.org/downloads/" target="_blank">Python 3,7</a> (podporuje se taky Python 3,6)
- <a href="https://git-scm.com/downloads" target="_blank">Git</a>
- <a href="https://docs.microsoft.com/cli/azure/install-azure-cli" target="_blank">Azure CLI</a>

## <a name="download-the-sample"></a>Stažení ukázky

V okně terminálu naklonujte ukázkovou aplikaci do místního počítače spuštěním následujícího příkazu. 

```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

Pak přejít do této složky:

```terminal
cd python-docs-hello-world
```

Úložiště obsahuje soubor *Application.py* , který oznamuje App Service, že kód obsahuje aplikaci v baňce. Další informace najdete v tématu [proces spuštění a přizpůsobení kontejneru](how-to-configure-python.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Azure CLI poskytuje mnoho pohodlných příkazů, které můžete použít z místního terminálu ke zřízení a správě prostředků Azure z příkazového řádku. Pomocí příkazů můžete provádět stejné úlohy, které jste procházeli pomocí Azure Portal v prohlížeči. Můžete také použít příkazy rozhraní příkazového řádku ve skriptech k automatizaci procesů správy.

Pokud chcete spustit příkazy Azure v rozhraní příkazového řádku Azure CLI, musíte se nejdřív přihlásit pomocí příkazu `az login`. Tento příkaz otevře prohlížeč pro shromáždění vašich přihlašovacích údajů.

```azurecli
az login
```

## <a name="deploy-the-sample"></a>Nasazení ukázky

Příkaz [`az webapp up`](/cli/azure/webapp#az-webapp-up) vytvoří webovou aplikaci v App Service a nasadí váš kód.

Ve složce *Python-docs-Hello-World* obsahující vzorový kód spusťte následující příkaz `az webapp up`. Nahraďte `<app-name>` globálně jedinečným názvem aplikace (*platné znaky jsou `a-z`, `0-9`a `-`* ). Nahraďte `<location-name>` také oblastí Azure, například **centralus**, **eastasia**, **westeurope**, **koreasouth**, **brazilsouth**, **centralindia**a tak dále. (Seznam povolených oblastí pro váš účet Azure můžete načíst spuštěním příkazu [`az account locations-list`](/cli/azure/appservice?view=azure-cli-latest.md#az-appservice-list-locations) .)


```azurecli
az webapp up --sku F1 -n <app-name> -l <location-name>
```

Tento příkaz může trvat několik minut, než se běh dokončí. Při spuštění příkaz zobrazí podobné informace jako v následujícím příkladu:

```output
The behavior of this command has been altered by the following extension: webapp
Creating Resource group 'appsvc_rg_Linux_centralus' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_centralus' ...
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
  "resourcegroup": "appsvc_rg_Linux_centralus ",
  "serverfarm": "appsvc_asp_Linux_centralus",
  "sku": "BASIC",
  "src_path": "/home/username/quickstart/python-docs-hello-world ",
  "version_detected": "-",
  "version_to_create": "python|3.7"
}
```

[!INCLUDE [AZ Webapp Up Note](../../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>Přechod do aplikace

Ve webovém prohlížeči přejděte do nasazené aplikace na adrese URL `http://<app-name>.azurewebsites.net`.

Vzorový kód Pythonu spouští kontejner Linux v App Service pomocí integrované image.

![Spuštění ukázkové aplikace v Pythonu v Azure](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**Blahopřejeme!** Nasadili jste aplikaci v Pythonu, která App Service na Linux.

## <a name="run-the-sample-app-locally"></a>Spustit ukázkovou aplikaci místně

V okně terminálu použijte následující příkazy (podle toho, jak je to vhodné pro váš operační systém) k instalaci požadovaných závislostí a spuštění integrovaného vývojového serveru. 

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
FLASK_APP=application.py
flask run
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
Set-Item Env:FLASK_APP ".\application.py"
flask run
```

# <a name="cmd"></a>[Přepsat](#tab/cmd)

```cmd
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
SET FLASK_APP=application.py
flask run
```

---

Otevřete webový prohlížeč a v `http://localhost:5000/`použijte ukázkovou aplikaci. Aplikace zobrazí zprávu **Hello World!** .

![Místní spuštění ukázkové aplikace v Pythonu](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

V okně terminálu ukončete webový server stisknutím klávesy **Ctrl**+**C** .

## <a name="redeploy-updates"></a>Znovu nasadit aktualizace

Ve svém oblíbeném editoru kódu otevřete *Application.py* a změňte příkaz `return` na posledním řádku tak, aby odpovídal následujícímu kódu. Zde je uveden příkaz `print`, který vygeneruje výstup protokolování, se kterým pracujete v následující části. 

```python
print("Handling request to home page.")
return "Hello Azure!"
```

Uložte změny a ukončete Editor. 

Znovu nasaďte aplikaci pomocí následujícího příkazu `az webapp up` pomocí stejného příkazu, který jste použili k nasazení aplikace poprvé, a nahraďte `<app-name>` a `<location-name>` se stejnými názvy, které jste použili dříve. 

```azurecli
az webapp up --sku F1 -n <app-name> -l <location-name>
```

Po dokončení nasazení přepněte zpátky do okna prohlížeče, které se otevře, aby se `http://<app-name>.azurewebsites.net` a aktualizovala stránku, která by měla upravenou zprávu zobrazit:

![Spuštění aktualizované ukázkové aplikace v Pythonu v Azure](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

> [!TIP]
> Visual Studio Code poskytuje výkonná rozšíření pro Python a Azure App Service, která zjednodušují proces nasazení webových aplikací v Pythonu na App Service. Další informace najdete v tématu [nasazení aplikací v Pythonu pro App Service z Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="stream-logs"></a>Protokoly datových proudů

Můžete přistupovat k protokolům konzoly generovaným zevnitř aplikace a kontejnerem, ve kterém je spuštěný. Protokoly obsahují výstup generovaný pomocí příkazů `print`.

Nejdřív zapněte protokolování kontejneru spuštěním následujícího příkazu v terminálu a nahraďte `<app-name>` názvem vaší aplikace a `<resource-group-name>` názvem skupiny prostředků, kterou jste použili ve výstupu `az webapp up` příkazu (například "appsvc_rg_Linux_centralus"):

```azurecli
az webapp log config --name <app-name> --resource-group <resource-group-name> --docker-container-logging filesystem
```

Jakmile je protokolování kontejneru zapnuté, spusťte následující příkaz, který zobrazí datový proud protokolu:

```azurecli
az webapp log tail --name <app-name> --resource-group <resource-group-name>
```

Aktualizujte aplikaci v prohlížeči, aby se generovaly protokoly konzoly, které by měly obsahovat řádky podobné následujícímu textu. Pokud se výstup nezobrazuje hned, zkuste to znovu za 30 sekund.

```output
2019-10-23T12:40:03.815574424Z Handling request to home page.
2019-10-23T12:40:03.815602424Z 172.16.0.1 - - [23/Oct/2019:12:40:03 +0000] "GET / HTTP/1.1" 200 12 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/78.0.3904.63 Safari/537.36 Edg/78.0.276.19"
```

Soubory protokolu můžete také zkontrolovat v prohlížeči na `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Pokud chcete streamování protokolů kdykoli zastavit, zadejte `Ctrl`+`C`.

## <a name="manage-the-azure-app"></a>Správa aplikace Azure

Chcete-li spravovat aplikaci, kterou jste vytvořili, otevřete <a href="https://portal.azure.com" target="_blank">Azure Portal</a> . Vyhledejte a vyberte **App Services**.

![Přejděte na App Services v Azure Portal](./media/quickstart-python/navigate-to-app-services-in-the-azure-portal.png)

Vyberte název aplikace Azure.

![Přejděte do aplikace Python v App Services Azure Portal](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

Zobrazí se stránka s přehledem vaší aplikace. Tady můžete provádět základní úlohy správy, jako je procházení, zastavení, spuštění, restartování a odstranění.

![Správa aplikace v Pythonu na stránce s přehledem v Azure Portal](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

V nabídce App Service najdete různé stránky pro konfiguraci vaší aplikace.

## <a name="clean-up-resources"></a>Vyčištění prostředků

V předchozích krocích jste vytvořili prostředky Azure ve skupině prostředků. V závislosti na vašem umístění má skupina prostředků název, jako je například "appsvc_rg_Linux_CentralUS". Pokud použijete App Service SKU jinou než bezplatnou úroveň F1, budou tyto prostředky účtovat průběžné náklady.

Pokud neočekáváte, že tyto prostředky budete potřebovat v budoucnu, odstraňte skupinu prostředků spuštěním následujícího příkazu, který nahradí `<resource-group-name>` skupinou prostředků, která se zobrazuje ve výstupu příkazu `az webapp up`, například "appsvc_rg_Linux_centralus". Dokončení příkazu může trvat několik minut.

```azurecli
az group delete -n <resource-group-name>
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: webová aplikace Python (Django) s PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Konfigurace aplikace v Pythonu](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Kurz: spuštění aplikace v Pythonu ve vlastním kontejneru](tutorial-custom-docker-image.md)
