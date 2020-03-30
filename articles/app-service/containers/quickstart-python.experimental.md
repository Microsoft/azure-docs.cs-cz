---
title: 'Úvodní příručka: Vytvoření aplikace Pythonu na Linuxu – Služba aplikací Azure'
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78246859"
---
# <a name="quickstart-create-a-python-app-in-azure-app-service-on-linux"></a>Úvodní příručka: Vytvoření aplikace Pythonu ve službě Azure App Service na Linuxu

V tomto rychlém startu nasadíte webovou aplikaci Pythonu do [služby App Service v Linuxu](app-service-linux-intro.md), vysoce škálovatelnou webhostingovou službu Azure s vlastními opravami. Místní rozhraní [příkazového řádku Azure (CLI)](/cli/azure/install-azure-cli) se používá na počítači Mac, Linux nebo Windows. Webová aplikace, kterou nakonfigurujete, používá bezplatnou úroveň služby App Service, takže v průběhu tohoto článku vám nevzniknou žádné náklady.

Pokud dáváte přednost nasazení aplikací prostřednictvím ide, najdete [v tématu Nasazení aplikací Pythonu do služby App Service z visual studia Code](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- <a href="https://www.python.org/downloads/" target="_blank">Python 3.7</a> (Podporuje se také Python 3.6)
- <a href="https://git-scm.com/downloads" target="_blank">Git</a>
- <a href="https://docs.microsoft.com/cli/azure/install-azure-cli" target="_blank">Azure CLI</a>

## <a name="download-the-sample"></a>Stažení ukázky

V okně terminálu spusťte následující příkaz a naklonujte ukázkovou aplikaci do místního počítače. 

```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

Pak jděte do této složky:

```terminal
cd python-docs-hello-world
```

Úložiště obsahuje *soubor application.py,* který informuje službu App Service, že kód obsahuje aplikaci Flask. Další informace naleznete v [tématu Kontejner spuštění procesu a vlastní nastavení](how-to-configure-python.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Azure CLI poskytuje mnoho pohodlné příkazy, které používáte z místního terminálu pro zřízení a správu prostředků Azure z příkazového řádku. Příkazy můžete použít k dokončení stejných úkolů, jako byste měli prostřednictvím portálu Azure v prohlížeči. Příkazy příkazu příkazu příkazu příkazu příkazu příkazu příkazu ve skriptech můžete také automatizovat procesy správy.

Chcete-li spustit příkazy Azure v azure cli, `az login` musíte se nejprve přihlásit pomocí příkazu. Tento příkaz otevře prohlížeč pro shromáždění vašich přihlašovacích údajů.

```azurecli
az login
```

## <a name="deploy-the-sample"></a>Nasazení ukázky

Příkaz [`az webapp up`](/cli/azure/webapp#az-webapp-up) vytvoří webovou aplikaci ve službě App Service a nasadí váš kód.

Ve složce *python-docs-hello-world,* která obsahuje ukázkový kód, spusťte následující `az webapp up` příkaz. Nahraďte `<app-name>` globálně jedinečnýnázev aplikace *(platné znaky jsou `a-z`, `0-9`a `-` *). `<location-name>` Nahraďte také oblast Azure, jako je **centralus**, **eastasia**, **westeurope**, **koreasouth**, **brazilsouth**, **centralindia**a tak dále. (Seznam povolených oblastí pro váš účet Azure můžete [`az account locations-list`](/cli/azure/appservice?view=azure-cli-latest.md#az-appservice-list-locations) načíst spuštěním příkazu.)


```azurecli
az webapp up --sku F1 -n <app-name> -l <location-name>
```

Tento příkaz může trvat několik minut dokončení spuštění. Při spuštění příkaz zobrazí podobné informace jako v následujícím příkladu:

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

Přejděte k nasazené aplikaci ve `http://<app-name>.azurewebsites.net`webovém prohlížeči na adrese URL .

Ukázkový kód Pythonu používá linuxový kontejner ve službě App Service pomocí integrované bitové kopie.

![Spuštění ukázkové aplikace Pythonu v Azure](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**Blahopřejeme!** Aplikaci Python jste nasadili do služby App Service na Linuxu.

## <a name="run-the-sample-app-locally"></a>Spuštění ukázkové aplikace místně

V okně terminálu použijte níže uvedené příkazy (podle potřeby operačního systému) k instalaci požadovaných závislostí a spuštění integrovaného vývojového serveru. 

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

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
SET FLASK_APP=application.py
flask run
```

---

Otevřete webový prohlížeč a přejděte `http://localhost:5000/`do ukázkové aplikace na adrese . Aplikace zobrazí zprávu **Hello World!**.

![Místní spuštění ukázkové aplikace Pythonu](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

V okně terminálu ukončete webový server stisknutím **klávesctrlc.**+**C**

## <a name="redeploy-updates"></a>Znovu nasadit aktualizace

V oblíbeném editoru kódu otevřete *application.py* a změňte `return` výpis na posledním řádku tak, aby odpovídal následujícímu kódu. Příkaz `print` je zde zahrnuta ke generování výstupu protokolování, se kterým pracujete v další části. 

```python
print("Handling request to home page.")
return "Hello Azure!"
```

Uložte změny a ukončete editor. 

Znovu nasadit aplikaci `az webapp up` pomocí následujícího příkazu, pomocí stejného příkazu, `<app-name>` `<location-name>` který jste použili k nasazení aplikace poprvé, nahrazení a se stejnými názvy, které jste použili dříve. 

```azurecli
az webapp up --sku F1 -n <app-name> -l <location-name>
```

Po dokončení nasazení přepněte zpět do `http://<app-name>.azurewebsites.net` okna prohlížeče a aktualizujte stránku, která by měla zobrazit upravenou zprávu:

![Spuštění aktualizované ukázkové aplikace Pythonu v Azure](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

> [!TIP]
> Visual Studio Code poskytuje výkonná rozšíření pro Python a Azure App Service, což zjednodušuje proces nasazování webových aplikací Pythonu do služby App Service. Další informace najdete [v tématu Nasazení aplikací Pythonu do služby App Service z kódu Visual Studia](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="stream-logs"></a>Protokoly datových proudů

Můžete přistupovat k protokolům konzoly generovaným z aplikace a kontejneru, ve kterém je spuštěna. Protokoly zahrnují všechny výstup `print` generované pomocí příkazů.

Nejprve zapněte protokolování kontejnerů spuštěním následujícího `<app-name>` příkazu v terminálu a nahrazením názvem aplikace a `<resource-group-name>` `az webapp up` názvem skupiny prostředků zobrazeným ve výstupu použitého příkazu (například "appsvc_rg_Linux_centralus"):

```azurecli
az webapp log config --name <app-name> --resource-group <resource-group-name> --docker-container-logging filesystem
```

Po zapnutí protokolování kontejnerů spusťte následující příkaz pro zobrazení datového proudu protokolu:

```azurecli
az webapp log tail --name <app-name> --resource-group <resource-group-name>
```

Aktualizujte aplikaci v prohlížeči, abyste vygenerovali protokoly konzoly, které by měly obsahovat řádky podobné následujícímu textu. Pokud výstup nevidíte okamžitě, zkuste to znovu za 30 sekund.

```output
2019-10-23T12:40:03.815574424Z Handling request to home page.
2019-10-23T12:40:03.815602424Z 172.16.0.1 - - [23/Oct/2019:12:40:03 +0000] "GET / HTTP/1.1" 200 12 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/78.0.3904.63 Safari/537.36 Edg/78.0.276.19"
```

Můžete také zkontrolovat soubory protokolu z `https://<app-name>.scm.azurewebsites.net/api/logs/docker`prohlížeče na adrese .

Chcete-li kdykoli zastavit streamování `Ctrl` + `C`protokolů, zadejte příkaz .

## <a name="manage-the-azure-app"></a>Správa aplikace Azure

Přejděte na <a href="https://portal.azure.com" target="_blank">portál Azure</a> a spravujte aplikaci, kterou jste vytvořili. Vyhledejte a vyberte **služby App Services**.

![Přechod na služby App Services na webu Azure Portal](./media/quickstart-python/navigate-to-app-services-in-the-azure-portal.png)

Vyberte název aplikace Azure.

![Přechod na aplikaci Python ve Službách aplikací na webu Azure Portal](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

Zobrazí se stránka Přehled aplikace. Tady můžete provádět základní úlohy správy, jako je procházení, zastavení, spuštění, restartování a odstranění.

![Správa aplikace Python na stránce Přehled na webu Azure Portal](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

Nabídka Služba App Service obsahuje různé stránky pro konfiguraci aplikace.

## <a name="clean-up-resources"></a>Vyčištění prostředků

V předchozích krocích jste vytvořili prostředky Azure ve skupině prostředků. Skupina prostředků má název jako "appsvc_rg_Linux_CentralUS" v závislosti na vaší poloze. Pokud používáte skladovou položku služby služby Aplikace než bezplatnou úroveň F1, budou tyto prostředky stát za následek průběžné náklady.

Pokud neočekáváte, že budete potřebovat tyto prostředky v budoucnu, odstraňte skupinu prostředků spuštěním následujícího příkazu, který nahradí `<resource-group-name>` skupinu prostředků zobrazenou ve výstupu příkazu, `az webapp up` například "appsvc_rg_Linux_centralus". Dokončení příkazu může trvat minutu.

```azurecli
az group delete -n <resource-group-name>
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Výuka: Webová aplikace Python (Django) s PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Konfigurace aplikace Python](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Kurz: Spuštění aplikace Pythonu ve vlastním kontejneru](tutorial-custom-docker-image.md)
