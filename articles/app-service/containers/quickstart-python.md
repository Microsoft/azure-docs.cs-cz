---
title: 'Úvodní příručka: Vytvoření aplikace Python u Linuxu'
description: Začínáme s linuxovými aplikacemi ve službě Azure App Service nasazením první aplikace Pythonu do kontejneru Linuxu ve službě App Service.
ms.topic: quickstart
ms.date: 04/03/2020
ms.custom: seo-python-october2019, cli-validate
experimental: true
experiment_id: 01a9132f-eaab-4c
ms.openlocfilehash: 63daecca710e0e4d7b3326cea59c0c025c24f619
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811153"
---
# <a name="quickstart-create-a-python-app-in-azure-app-service-on-linux"></a>Úvodní příručka: Vytvoření aplikace Pythonu ve službě Azure App Service na Linuxu

V tomto rychlém startu nasadíte webovou aplikaci Pythonu do [služby App Service v Linuxu](app-service-linux-intro.md), vysoce škálovatelnou webhostingovou službu Azure s vlastními opravami. Místní rozhraní [příkazového řádku Azure (CLI)](/cli/azure/install-azure-cli) se používá na počítači Mac, Linux nebo Windows. Webová aplikace, kterou nakonfigurujete, používá bezplatnou úroveň služby App Service, takže v průběhu tohoto článku vám nevzniknou žádné náklady.

Pokud dáváte přednost nasazení aplikací prostřednictvím ide, najdete [v tématu Nasazení aplikací Pythonu do služby App Service z visual studia Code](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- <a href="https://www.python.org/downloads/" target="_blank">Python 3.7</a> (Podporuje se také Python 3.6)
- <a href="https://git-scm.com/downloads" target="_blank">Git</a>
- <a href="https://docs.microsoft.com/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> 2.0.80 nebo vyšší. Spuštěním příkazu `az --version` zkontrolujte svou verzi.

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

## <a name="run-the-sample"></a>Spuštění ukázky

V okně terminálu použijte níže uvedené příkazy (podle potřeby operačního systému) k instalaci požadovaných závislostí a spuštění integrovaného vývojového serveru. 

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
export FLASK_APP=application.py
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

![Místní spuštění ukázkové aplikace Pythonu](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)

V okně terminálu ukončete webový server stisknutím **klávesctrlc.**+**C**

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Azure CLI poskytuje mnoho pohodlné příkazy, které používáte z místního terminálu pro zřízení a správu prostředků Azure z příkazového řádku. Příkazy můžete použít k dokončení stejných úkolů, jako byste měli prostřednictvím portálu Azure v prohlížeči. Příkazy příkazu příkazu příkazu příkazu příkazu příkazu příkazu ve skriptech můžete také automatizovat procesy správy.

Chcete-li spustit příkazy Azure v azure cli, `az login` musíte se nejprve přihlásit pomocí příkazu. Tento příkaz otevře prohlížeč pro shromáždění vašich přihlašovacích údajů.

```azurecli
az login
```

## <a name="deploy-the-sample"></a>Nasazení ukázky

Příkaz [`az webapp up`](/cli/azure/webapp#az-webapp-up) vytvoří webovou aplikaci ve službě App Service a nasadí váš kód.

Ve složce *python-docs-hello-world,* která obsahuje ukázkový kód, spusťte následující `az webapp up` příkaz. Nahraďte `<app-name>` globálně jedinečnýnázev aplikace *(platné znaky jsou `a-z`, `0-9`a `-` *).


```azurecli
az webapp up --sku F1 -n <app-name>
```

Argument `--sku F1` vytvoří webovou aplikaci na cenové úrovni Free. Tento argument můžete vynechat a použít úroveň premium, která účtuje hodinové náklady.

Volitelně můžete zahrnout `-l <location-name>` argument, kde `<location_name>` je oblast Azure, jako je **například centralus**, **eastasia**, **westeurope**, **koreasouth**, **brazilsouth**, **centralindia**a tak dále. Seznam povolených oblastí pro váš účet Azure můžete [`az account list-locations`](/cli/azure/appservice?view=azure-cli-latest.md#az-appservice-list-locations) načíst spuštěním příkazu.

Úplné `az webapp up` spuštění příkazu může trvat několik minut. Při spuštění se zobrazí informace podobné následujícímu příkladu, kde `<app_name>` bude název, který jste zadali dříve:

<pre>
Creating Resource group 'appsvc_rg_Linux_centralus' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_centralus' ...
App service plan creation complete
Creating app '<app-name>' ....
Configuring default logging for the app, if not already enabled
Creating zip with contents of dir D:\Examples\python-docs-hello-world ...
Getting scm site credentials for zip deployment
Starting zip deployment. This operation can take a while to complete ...
Deployment endpoint responded with status code 202
You can launch the app at http://<app-name>.azurewebsites.net
{
  "URL": "http://<app-name>.net",
  "appserviceplan": "appsvc_asp_Linux_centralus",
  "location": "eastus",
  "name": "<app-name>",
  "os": "Linux",
  "resourcegroup": "appsvc_rg_Linux_centralus",
  "runtime_version": "python|3.7",
  "runtime_version_detected": "-",
  "sku": "FREE",
  "src_path": "D:\\Examples\\python-docs-hello-world"
}
</pre>

[!INCLUDE [AZ Webapp Up Note](../../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>Přechod do aplikace

Přejděte k nasazené aplikaci ve `http://<app-name>.azurewebsites.net`webovém prohlížeči na adrese URL .

Ukázkový kód Pythonu používá linuxový kontejner ve službě App Service pomocí integrované bitové kopie.

![Spuštění ukázkové aplikace Pythonu v Azure](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**Blahopřejeme!** Aplikaci Python jste nasadili do služby App Service na Linuxu.

## <a name="redeploy-updates"></a>Znovu nasadit aktualizace

Ve svém oblíbeném *application.py* editoru kódu `hello` otevřete application.py a aktualizujte funkci následujícím způsobem. Tato změna `print` přidá příkaz ke generování výstupu protokolování, se kterým pracujete v další části. 

```python
def hello():
    print("Handling request to home page.")
    return "Hello Azure!"
```

Uložte změny a ukončete editor. 

Znovu nasadit aplikaci pomocí příkazu `az webapp up` znovu:

```azurecli
az webapp up
```

Tento příkaz používá hodnoty, které jsou uloženy v mezipaměti v souboru *.azure/config,* včetně názvu aplikace, skupiny prostředků a plánu služby App Service.

Po dokončení nasazení přepněte zpět do `http://<app-name>.azurewebsites.net` okna prohlížeče a aktualizujte stránku, která by měla zobrazit upravenou zprávu:

![Spuštění aktualizované ukázkové aplikace Pythonu v Azure](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

> [!TIP]
> Visual Studio Code poskytuje výkonná rozšíření pro Python a Azure App Service, což zjednodušuje proces nasazování webových aplikací Pythonu do služby App Service. Další informace najdete [v tématu Nasazení aplikací Pythonu do služby App Service z kódu Visual Studia](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="stream-logs"></a>Protokoly datových proudů

Můžete přistupovat k protokolům konzoly generovaným z aplikace a kontejneru, ve kterém je spuštěna. Protokoly zahrnují všechny výstup `print` generované pomocí příkazů.

Chcete-li streamovat protokoly, spusťte následující příkaz:

```azurecli
az webapp log tail
```

Aktualizujte aplikaci v prohlížeči, abyste vygenerovali protokoly konzoly, které by měly obsahovat řádky podobné následujícímu textu. Pokud výstup nevidíte okamžitě, zkuste to znovu za 30 sekund.

<pre>
2020-04-03T22:54:04.236405938Z Handling request to home page.
2020-04-03T22:54:04.236497641Z 172.16.0.1 - - [03/Apr/2020:22:54:04 +0000] "GET / HTTP/1.1" 200 12 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4044.83 Safari/537.36 Edg/81.0.416.41"
</pre>

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

V předchozích krocích jste vytvořili prostředky Azure ve skupině prostředků. Skupina prostředků má název jako "appsvc_rg_Linux_CentralUS" v závislosti na vaší poloze. Pokud používáte sku služby App Service než bezplatnou úroveň F1, tyto prostředky vznikají průběžné náklady (viz [Ceny služby App Service](https://azure.microsoft.com/pricing/details/app-service/linux/)).

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
