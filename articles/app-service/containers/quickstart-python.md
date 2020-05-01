---
title: 'Rychlý Start: Vytvoření aplikace Python pro Linux'
description: Začněte s aplikacemi pro Linux v Azure App Service nasazením první aplikace v Pythonu do kontejneru Linux v App Service.
ms.topic: quickstart
ms.date: 04/03/2020
ms.custom: seo-python-october2019, cli-validate
experimental: true
experiment_id: 01a9132f-eaab-4c
ms.openlocfilehash: 606758cf81d069124c67bc06c650b96d91262deb
ms.sourcegitcommit: acc558d79d665c8d6a5f9e1689211da623ded90a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2020
ms.locfileid: "82597871"
---
# <a name="quickstart-create-a-python-app-in-azure-app-service-on-linux"></a>Rychlý Start: Vytvoření aplikace v Pythonu v Azure App Service v systému Linux

V tomto rychlém startu nasadíte webovou aplikaci v Pythonu, která se [App Service v systému Linux](app-service-linux-intro.md), vysoce škálovatelná služba s automatickými opravami pro hostování webů v Azure. Použijete místní [rozhraní příkazového řádku Azure (CLI)](/cli/azure/install-azure-cli) na počítači se systémem Mac, Linux nebo Windows. Webová aplikace, kterou nakonfigurujete, využívá bezplatnou App Serviceovou úroveň, takže v tomto článku nebudete mít žádné náklady.

Pokud dáváte přednost nasazení aplikací prostřednictvím integrovaného vývojového prostředí (IDE), přečtěte si téma [nasazení aplikací Python pro App Service z Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) .
- <a href="https://www.python.org/downloads/" target="_blank">Python 3,7</a> (podporuje se taky Python 3,6)
- <a href="https://git-scm.com/downloads" target="_blank">Git</a>
- <a href="https://docs.microsoft.com/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> 2.0.80 nebo vyšší. Spuštěním příkazu `az --version` zkontrolujte svou verzi.

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

## <a name="run-the-sample"></a>Spuštění ukázky

V okně terminálu použijte následující příkazy (podle toho, jak je to vhodné pro váš operační systém) k instalaci požadovaných závislostí a spuštění integrovaného vývojového serveru. 

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

Otevřete webový prohlížeč a v `http://localhost:5000/`části použijte ukázkovou aplikaci. Aplikace zobrazí zprávu **Hello World!**.

![Místní spuštění ukázkové aplikace v Pythonu](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)

V okně terminálu ukončete webový server stisknutím **kombinace kláves CTRL +**+**C** .

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Azure CLI poskytuje mnoho pohodlných příkazů, které můžete použít z místního terminálu ke zřízení a správě prostředků Azure z příkazového řádku. Pomocí příkazů můžete provádět stejné úlohy, které jste procházeli pomocí Azure Portal v prohlížeči. Můžete také použít příkazy rozhraní příkazového řádku ve skriptech k automatizaci procesů správy.

Pokud chcete spustit příkazy Azure v rozhraní příkazového řádku Azure CLI, musíte se nejdřív `az login` přihlásit pomocí příkazu. Tento příkaz otevře prohlížeč pro shromáždění vašich přihlašovacích údajů.

```azurecli
az login
```

## <a name="deploy-the-sample"></a>Nasazení ukázky

[`az webapp up`](/cli/azure/webapp#az-webapp-up) Příkaz vytvoří webovou aplikaci na App Service a nasadí váš kód.

Ve složce *Python-docs-Hello-World* , která obsahuje vzorový kód, spusťte následující `az webapp up` příkaz. Nahraďte `<app-name>` globálně jedinečným názvem aplikace (*platné znaky jsou `a-z`, `0-9`a `-` *).


```azurecli
az webapp up --sku F1 -n <app-name>
```
> [!CAUTION]
> Pokud používáte **Azure-CLI verze 2.5.0** , existuje regrese v `az webapp up` tom, kde určité scénáře selžou, pokud není `-l <location-name>` tento parametr zahrnutý. Tento problém se [sleduje tady](https://github.com/Azure/azure-cli/issues/13257).  
> 
>Můžete zjistit, jakou verzi Azure-CLI používáte s `az --version` příkazem.
>

`--sku F1` Argument vytvoří webovou aplikaci na cenové úrovni Free. Tento argument vynecháte místo toho, abyste místo toho použili úroveň Premium, což stojí za hodinu.

Volitelně můžete `-l <location-name>` zahrnout argument, kde `<location_name>` je oblast Azure, například **centralus**, **eastasia**, **westeurope**, **koreasouth**, **brazilsouth**, **centralindia**a tak dále. Seznam povolených oblastí pro váš účet Azure můžete načíst spuštěním [`az account list-locations`](/cli/azure/appservice?view=azure-cli-latest.md#az-appservice-list-locations) příkazu.

Úplné `az webapp up` spuštění příkazu může trvat několik minut. Při spuštění se zobrazí podobné informace jako v následujícím příkladu, kde `<app-name>` bude název, který jste zadali dříve:

<pre>
Creating Resource group 'appsvc_rg_Linux_centralus' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_centralus' ...
App service plan creation complete
Creating app '&lt;app-name&gt;' ....
Configuring default logging for the app, if not already enabled
Creating zip with contents of dir D:\Examples\python-docs-hello-world ...
Getting scm site credentials for zip deployment
Starting zip deployment. This operation can take a while to complete ...
Deployment endpoint responded with status code 202
You can launch the app at http://&lt;app-name&gt;.azurewebsites.net
{
  "URL": "http://&lt;app-name&gt;.net",
  "appserviceplan": "appsvc_asp_Linux_centralus",
  "location": "eastus",
  "name": "&lt;app-name&gt;",
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

Ve webovém prohlížeči přejděte do nasazené aplikace na adrese URL `http://<app-name>.azurewebsites.net`.

Vzorový kód Pythonu spouští kontejner Linux v App Service pomocí integrované image.

![Spuštění ukázkové aplikace v Pythonu v Azure](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**Blahopřejeme!** Nasadili jste aplikaci v Pythonu, která App Service na Linux.

## <a name="redeploy-updates"></a>Znovu nasadit aktualizace

Ve svém oblíbeném editoru kódu otevřete *Application.py* a aktualizujte `hello` funkci následujícím způsobem. Tato změna přidá `print` příkaz pro generování výstupu protokolování, se kterým pracujete v následující části. 

```python
def hello():
    print("Handling request to home page.")
    return "Hello Azure!"
```

Uložte změny a ukončete Editor. 

Znovu nasaďte aplikaci znovu pomocí `az webapp up` příkazu:

```azurecli
az webapp up
```

Tento příkaz používá hodnoty, které jsou uložené v mezipaměti v souboru *. Azure/config* , včetně názvu aplikace, skupiny prostředků a plánu App Service.

Po dokončení nasazení přepněte zpátky do okna prohlížeče, které se otevře na `http://<app-name>.azurewebsites.net` stránce, a aktualizujte stránku, která by měla upravenou zprávu zobrazit:

![Spuštění aktualizované ukázkové aplikace v Pythonu v Azure](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

> [!TIP]
> Visual Studio Code poskytuje výkonná rozšíření pro Python a Azure App Service, která zjednodušují proces nasazení webových aplikací v Pythonu na App Service. Další informace najdete v tématu [nasazení aplikací v Pythonu pro App Service z Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="stream-logs"></a>Protokoly datových proudů

Můžete přistupovat k protokolům konzoly generovaným zevnitř aplikace a kontejnerem, ve kterém je spuštěný. Protokoly obsahují výstup generovaný pomocí `print` příkazů.

Chcete-li spustit datový proud protokolů, spusťte následující příkaz:

```azurecli
az webapp log tail
```

Aktualizujte aplikaci v prohlížeči, aby se generovaly protokoly konzoly, které by měly obsahovat řádky podobné následujícímu textu. Pokud se výstup nezobrazuje hned, zkuste to znovu za 30 sekund.

<pre>
2020-04-03T22:54:04.236405938Z Handling request to home page.
2020-04-03T22:54:04.236497641Z 172.16.0.1 - - [03/Apr/2020:22:54:04 +0000] "GET / HTTP/1.1" 200 12 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4044.83 Safari/537.36 Edg/81.0.416.41"
</pre>

Soubory protokolu můžete také zkontrolovat v prohlížeči na adrese `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Pokud chcete streamování protokolů kdykoli zastavit, zadejte `Ctrl` + `C`.

## <a name="manage-the-azure-app"></a>Správa aplikace Azure

Chcete-li spravovat aplikaci, kterou jste vytvořili, otevřete <a href="https://portal.azure.com" target="_blank">Azure Portal</a> . Vyhledejte a vyberte **App Services**.

![Přejděte na App Services v Azure Portal](./media/quickstart-python/navigate-to-app-services-in-the-azure-portal.png)

Vyberte název aplikace Azure.

![Přejděte do aplikace Python v App Services Azure Portal](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

Zobrazí se stránka s přehledem vaší aplikace. Tady můžete provádět základní úlohy správy, jako je procházení, zastavení, spuštění, restartování a odstranění.

![Správa aplikace v Pythonu na stránce s přehledem v Azure Portal](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

V nabídce App Service najdete různé stránky pro konfiguraci vaší aplikace.

## <a name="clean-up-resources"></a>Vyčištění prostředků

V předchozích krocích jste vytvořili prostředky Azure ve skupině prostředků. V závislosti na vašem umístění má skupina prostředků název, jako je například "appsvc_rg_Linux_CentralUS". Pokud použijete App Service SKU jinou než bezplatnou úroveň F1, tyto prostředky účtují průběžné náklady (viz [ceny App Service](https://azure.microsoft.com/pricing/details/app-service/linux/)).

Pokud neočekáváte, že tyto prostředky budete potřebovat v budoucnu, odstraňte skupinu prostředků spuštěním následujícího příkazu, kde nahraďte `<resource-group-name>` skupinu prostředků, která je zobrazená ve výstupu `az webapp up` příkazu, například "appsvc_rg_Linux_centralus". Dokončení příkazu může trvat několik minut.

```azurecli
az group delete -n <resource-group-name>
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: webová aplikace Python (Django) s PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Přidání přihlášení uživatele do webové aplikace v Pythonu](../../active-directory/develop/quickstart-v2-python-webapp.md)

> [!div class="nextstepaction"]
> [Konfigurace aplikace v Pythonu](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Kurz: spuštění aplikace v Pythonu ve vlastním kontejneru](tutorial-custom-docker-image.md)
